<!-- Banner do Projeto -->
<p align="center">
  <img src="assets/capa_rfm_powerbi.png" alt="Análise RFM no Power BI" width="800">
</p>

<!-- Badges -->
<p align="center">
  <img src="https://img.shields.io/badge/Power%20BI-Data%20Analytics-yellow" alt="Power BI">
  <img src="https://img.shields.io/badge/DAX-Language-blue" alt="DAX">
  <img src="https://img.shields.io/badge/SQL-Database-green" alt="SQL">
  <img src="https://img.shields.io/github/license/seuusuario/seurepositorio" alt="License">
  <img src="https://img.shields.io/github/last-commit/seuusuario/seurepositorio" alt="Last Commit">
</p>

---

# 📊 Análise RFM no Power BI

Este projeto apresenta a construção de uma **Matriz RFM** (*Recência, Frequência e Valor Monetário*) no **Power BI**, conectada a uma base SQL corporativa e usando medidas DAX personalizadas para segmentar clientes.

> A ideia surgiu em uma conversa com a equipe de marketing sobre identificar clientes mais ativos, os que estão sumindo e aqueles com alto potencial de crescimento.

---

## 📋 Índice

1. [O que é RFM](#-o-que-é-a-análise-rfm)
2. [Modelo de Dados](#-modelo-de-dados)
3. [Medidas DAX](#-medidas-dax-principais)
4. [Atribuição de Escores](#-atribuição-de-escores)
5. [Classificação Final](#-classificação-final)
6. [Visualização](#-visualização-no-power-bi)
7. [Resultados](#-resultados)
8. [Como Reproduzir](#-como-reproduzir)
9. [Screenshots](#-screenshots)
10. [Licença](#-licença)

---

## 🧐 O que é a Análise RFM

A **Análise RFM** é uma técnica de segmentação de clientes baseada em:

- **Recência (R)** → tempo desde a última compra  
- **Frequência (F)** → número de compras no período  
- **Valor Monetário (M)** → total gasto no período  

Combinando esses três fatores, conseguimos classificar clientes e criar estratégias personalizadas.

---

## 🗂 Modelo de Dados

O modelo foi construído no formato **estrela**:

```mermaid
erDiagram
    dimCliente {
        int ID_Cliente
        string Nome
        string Segmento
    }
    dimRepresentante {
        int ID_Representante
        string Nome
    }
    dimItem {
        int ID_Item
        string Produto
        float PrecoUnitario
    }
    dimCalendario {
        date Data
        string AnoMes
    }
    fatoFaturamento {
        int ID_Venda
        int ID_Cliente
        int ID_Item
        int ID_Representante
        date Data
        int Quantidade
        float ValorFaturado
    }

    dimCliente ||--o{ fatoFaturamento : "relaciona"
    dimRepresentante ||--o{ fatoFaturamento : "relaciona"
    dimItem ||--o{ fatoFaturamento : "relaciona"
    dimCalendario ||--o{ fatoFaturamento : "relaciona"

## 📐 Medidas DAX

A seguir, as principais medidas utilizadas para cálculo da Recência, Frequência e Valor Monetário no modelo RFM.

### 📅 Data de Referência
Data_Referencia = MAX(Vendas[Data])

### ⏳ Recência
Calcula o número de dias desde a última compra de cada cliente até a data de referência.

Recencia = 
VAR UltimaCompra = CALCULATE(
    MAX(fatoFaturamento[Data]),
    ALLEXCEPT(dimCliente, dimCliente[ID Cliente])
)
VAR Hoje = MAX(dimCalendario[Data])
RETURN
DATEDIFF(UltimaCompra, Hoje, DAY)

### 🔄 Frequência
Conta o número de meses distintos em que o cliente realizou pelo menos uma compra.

Frequencia = 
CALCULATE(
    DISTINCTCOUNT(dimCalendario[AnoMes]),
    fatoFaturamento
)

**Obs.:** Criar coluna `AnoMes` para facilitar a contagem:
AnoMes = FORMAT(dimCalendario[Data], "YYYY-MM")

### 💰 Valor Monetário
Soma o valor total faturado por cliente.

ValorMonetario = 
SUM(fatoFaturamento[ValorFaturado])

Ou, caso não exista o campo:
ValorFaturado = fatoFaturamento[Quantidade] * fatoFaturamento[PrecoUnitario]

---

## 🧮 Atribuição de Escores

Os escores variam de **1 a 5** para cada métrica.

### Recência (quanto mais recente, maior o score)
RecenciaScore = 
SWITCH(
    TRUE(),
    [Recencia] <= 30, 5,
    [Recencia] <= 60, 4,
    [Recencia] <= 90, 3,
    [Recencia] <= 180, 2,
    1
)

### Frequência
FrequenciaScore = 
SWITCH(
    TRUE(),
    [Frequencia] >= 12, 5,
    [Frequencia] >= 9, 4,
    [Frequencia] >= 6, 3,
    [Frequencia] >= 3, 2,
    1
)

### Valor Monetário *(intervalos adaptáveis ao negócio)*
ValorScore = 
SWITCH(
    TRUE(),
    [ValorMonetario] >= 50000, 5,
    [ValorMonetario] >= 30000, 4,
    [ValorMonetario] >= 15000, 3,
    [ValorMonetario] >= 5000, 2,
    1
)

---

## 🏷️ Classificação RFM

RFM = 
SWITCH(
    TRUE(),
    [FrequenciaScore] = 5 && [RecenciaScore] = 5 && [ValorScore] >= 4, "Campeões",
    [FrequenciaScore] >= 4 && [RecenciaScore] >= 3 && [ValorScore] >= 3, "Clientes Leais",
    [FrequenciaScore] >= 4 && [RecenciaScore] >= 3 && [ValorScore] <= 2, "Clientes com Potencial",
    [FrequenciaScore] <= 2 && [RecenciaScore] <= 2 && [ValorScore] >= 4, "Em Risco",
    [FrequenciaScore] <= 2 && [RecenciaScore] <= 2 && [ValorScore] <= 2, "Perdidos",
    [FrequenciaScore] <= 2 && [RecenciaScore] = 5 && [ValorScore] <= 3, "Clientes Recentes",
    [FrequenciaScore] = 3 && [RecenciaScore] >= 3 && [ValorScore] >= 4, "Valiosos em Desenvolvimento",
    "Outros"
)

---

## 📊 Visualização no Power BI

A matriz RFM pode ser explorada visualmente com um **Gráfico de Dispersão**:

- **Eixo X:** Recência
- **Eixo Y:** Frequência
- **Tamanho/Cor dos pontos:** Valor Monetário

Dicas:
- Utilizar imagem de fundo com quadrantes nomeados (ex.: "Campeões", "Leais", "Perdidos").
- Adicionar indicadores complementares como faturamento dos últimos 12 meses, ticket médio e data da última compra.

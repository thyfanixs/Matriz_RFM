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




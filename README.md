# 📊 Análise RFM no Power BI

![RFM Analysis](https://img.shields.io/badge/RFM-Analysis-blue?style=for-the-badge)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow?style=for-the-badge)
![DAX](https://img.shields.io/badge/DAX-Formulas-green?style=for-the-badge)

Uma implementação prática de análise RFM (Recência, Frequência, Valor Monetário) no Power BI para segmentação de clientes e otimização de estratégias de marketing.


## 🎯 Sobre o Projeto

Este projeto implementa uma análise RFM completa no Power BI, permitindo segmentar clientes com base em seu comportamento de compra. A análise RFM é uma técnica poderosa de marketing que classifica clientes usando três critérios principais:

- **Recência (R)**: Tempo desde a última compra
- **Frequência (F)**: Número de compras em determinado período
- **Valor Monetário (M)**: Valor total gasto pelo cliente

## 🏗️ Arquitetura do Modelo de Dados

O projeto utiliza um modelo de dados em estrela conectado via SQL com as seguintes tabelas:

![Modelo de Dados]([https://github.com/user/repo/blob/main/images/modelo-dados.png](https://www.google.com/url?sa=i&url=https%3A%2F%2Fblog.drbi.com.br%2Fblog%2Fo-que-e-um-esquema-em-estrela%2F&psig=AOvVaw0Ivug6Y-8rNZ06DSRHIwmK&ust=1755108762816000&source=images&cd=vfe&opi=89978449&ved=0CBUQjRxqFwoTCNijoejvhY8DFQAAAAAdAAAAABAE))
*Modelo de dados em estrela utilizado no projeto*

### Tabela Fato
- `fatoFaturamento` - Tabela principal com dados de vendas

### Dimensões
- `dimCliente` - Dados de identificação e classificação dos clientes
- `dimRepresentante` - Agrupamento por representantes comerciais
- `dimItem` - Produtos vendidos, preços e categorias
- `dimCalendario` - Dimensão temporal para facilitar cálculos de recência

## 🧮 Medidas DAX Principais

### Data de Referência
```dax
Data_Referencia = MAX(Vendas[Data])
```

### Recência (dias desde última compra)
```dax
Recencia = 
VAR UltimaCompra = CALCULATE(
    MAX(fatoFaturamento[Data]),
    ALLEXCEPT(dimCliente, dimCliente[ID Cliente])
)
VAR Hoje = MAX(dimCalendario[Data])
RETURN
DATEDIFF(UltimaCompra, Hoje, DAY)
```

### Frequência (meses ativos)
```dax
Frequencia = 
CALCULATE(
    DISTINCTCOUNT(dimCalendario[AnoMes]),
    fatoFaturamento
)
```

### Valor Monetário
```dax
ValorMonetario = SUM(fatoFaturamento[ValorFaturado])
```

## 📏 Sistema de Pontuação

Cada métrica recebe uma pontuação de 1 a 5, onde 5 representa o melhor desempenho:

### Escore de Recência
```dax
RecenciaScore = 
SWITCH(
    TRUE(),
    [Recencia] <= 30, 5,
    [Recencia] <= 60, 4,
    [Recencia] <= 90, 3,
    [Recencia] <= 180, 2,
    1
)
```

### Escore de Frequência
```dax
FrequenciaScore = 
SWITCH(
    TRUE(),
    [Frequencia] >= 12, 5,
    [Frequencia] >= 9, 4,
    [Frequencia] >= 6, 3,
    [Frequencia] >= 3, 2,
    1
)
```

## 👥 Segmentação de Clientes

![Segmentos RFM](https://github.com/user/repo/blob/main/images/segmentos-rfm.png)
*Matriz visual dos diferentes segmentos de clientes*

O sistema classifica clientes em diferentes perfis:

- **🏆 Campeões**: Compraram recentemente, com alta frequência e valor alto
- **💎 Clientes Leais**: Frequência e recência altas com valor médio/alto
- **🌱 Clientes com Potencial**: Alta frequência mas valor baixo
- **⚠️ Em Risco**: Baixa frequência e recência, mas já gastaram bem
- **❌ Perdidos**: Pouca frequência, compra antiga e baixo valor
- **🆕 Clientes Recentes**: Compra recente mas ainda com pouca atividade
- **💰 Clientes Valiosos em Desenvolvimento**: Valor alto mas frequência média

## 📈 Visualização

![Visualização Completa](https://github.com/user/repo/blob/main/images/dashboard-completo.png)
*Dashboard completo com todas as visualizações*

### Gráfico de Dispersão
O projeto utiliza gráfico de dispersão para visualizar a matriz RFM:
- **Eixo X**: Recência
- **Eixo Y**: Frequência  
- **Tamanho/Cor dos pontos**: Valor Monetário

### Dashboard Complementar
Inclui indicadores principais:
- Total de clientes
- Faturamento dos últimos 12 meses
- Quantidade de compras no período
- Ticket médio
- Data da última compra

## 🚀 Como Usar

1. **Prepare seus dados**: Certifique-se de ter tabelas de vendas, clientes e datas
2. **Implemente as medidas**: Copie as fórmulas DAX adaptando os nomes das suas tabelas
3. **Configure os escores**: Ajuste os intervalos conforme seu negócio
4. **Crie as visualizações**: Monte o gráfico de dispersão e dashboard complementar
5. **Valide com stakeholders**: Ajuste filtros e segmentações conforme necessário

## 🎯 Benefícios

- **Segmentação inteligente** de clientes
- **Campanhas direcionadas** de marketing
- **Identificação de oportunidades** de cross-sell e up-sell  
- **Prevenção de churn** através da identificação de clientes em risco
- **Otimização de recursos** de marketing e vendas

## 📋 Requisitos

- Power BI Desktop
- Acesso a dados de vendas com pelo menos: data, cliente, valor
- Conhecimento básico de DAX
- SQL Server (opcional, para modelo de dados complexo)

## 🤝 Contribuição

Sinta-se à vontade para contribuir com melhorias, sugestões ou adaptações para diferentes tipos de negócio.

---

> *"Mal feito, feito"* ⚡  

---

*Este projeto foi desenvolvido com base na necessidade prática de segmentação de clientes e pode ser adaptado para diferentes contextos de negócio.*

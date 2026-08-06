# Base de Conhecimento

## Dados Utilizados

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores e dar continuidade ao atedimento eficientemente |
| `perfil_investidor.json` | JSON | Personalizar explicações sobre as dúvidas do cliente |
| `produtos_financeiros.json` | JSON | Conhecer produtos disponíveis para que possam ser explicados ao cliente |
| `transacoes.csv` | CSV | Analisar padrão de gastos do cliente e usar como fonte de informações |

---

## Adaptações nos Dados

Fiz algumas modificações para os dados ficarem mais coerentes com uma pessoa que necessita de auxílio financeiro

---

## Estratégia de Integração

### Como os dados são carregados:
Dados injetados diretamente no prompt (CTRL+C, CTRL+V) ou carregar via código como no exemplo:

```python
import pandas as pd
import json

perfil = json.load(open('./data/perfil_investidor.json'))
transacoes = pd.read_csv('./data/transacoes.csv')
historico = pd.read_csv('./data/historico_atendimento.csv')
produtos = json.load(open('./data/produtos_financeiros.json'))
```

### Como os dados são usados no prompt:
Injeção de dados diretamente no prompt para o máximo de contexto possível, em soluções mais robustas as informações devem ser idealmente carregadas dinamicamente para maior flexibilidade.
```text
DADOS DO CLIENTE (perfil_investidor.json):
{
  "nome": "João Silva",
  "idade": 32,
  "profissao": "Analista de Sistemas",
  "renda_mensal": 5000.00,
  "perfil_investidor": "moderado",
  "objetivo_principal": "Construir reserva de emergência e ter um controle de gastos maior",
  "patrimonio_total": 15000.00,
  "reserva_emergencia_atual": 10000.00,
  "aceita_risco": false,
  "metas": [
    {
      "meta": "Completar reserva de emergência",
      "valor_necessario": 15000.00,
      "prazo": "2026-06"
    },
    {
      "meta": "Entrada do apartamento",
      "valor_necessario": 50000.00,
      "prazo": "2027-12"
    }
  ]
}


TRANSAÇÕES DO CLIENTE (transacoes.csv):
data,descricao,categoria,valor,tipo
2025-10-01,Salário,receita,5000.00,entrada
2025-10-02,Aluguel,moradia,1200.00,saida
2025-10-03,Supermercado,alimentacao,450.00,saida
2025-10-05,Netflix,lazer,55.90,saida
2025-10-05,Netflix,lazer,55.90,saida
2025-10-07,Farmácia,saude,89.00,saida
2025-10-10,Restaurante,alimentacao,120.00,saida
2025-10-05,Spotify,lazer,44.90,saida
2025-10-12,Uber,transporte,45.00,saida
2025-10-15,Conta de Luz,moradia,180.00,saida
2025-10-20,Academia,saude,99.00,saida
2025-10-25,Combustível,transporte,250.00,saida
2025-10-10,Fast-Food,alimentacao,100.00,saida
2025-10-10,Fast-Food,alimentacao,140.00,saida
2025-10-10,Fast-Food,alimentacao,160.00,saida


HISTÓRICO DE ATENDIMENTO DO CLIENTE (historio_atendimento.csv):
data,canal,tema,resumo,resolvido
2025-09-15,chat,CDB,Cliente perguntou sobre o que é CDB,sim
2025-09-22,telefone,Problema no app,Erro ao visualizar extrato foi corrigido,sim
2025-10-01,chat,Tesouro Selic,Cliente pediu explicação sobre o funcionamento do Tesouro Direto,sim
2025-10-12,chat,Metas financeiras,Cliente acompanhou o progresso da reserva de emergência,sim
2025-10-25,email,Atualização cadastral,Cliente atualizou e-mail e telefone,sim


PODUTOS DODISPONÍVEIS PARA ENSINO (produtos_financeiros.json):
[
  {
    "nome": "Tesouro Selic",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "100% da Selic",
    "aporte_minimo": 30.00,
    "indicado_para": "Reserva de emergência e iniciantes"
  },
  {
    "nome": "CDB Liquidez Diária",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "102% do CDI",
    "aporte_minimo": 100.00,
    "indicado_para": "Quem busca segurança com rendimento diário"
  },
  {
    "nome": "LCI/LCA",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "95% do CDI",
    "aporte_minimo": 1000.00,
    "indicado_para": "Quem pode esperar 90 dias (isento de IR)"
  },
  {
    "nome": "Fundo Multimercado",
    "categoria": "fundo",
    "risco": "medio",
    "rentabilidade": "CDI + 2%",
    "aporte_minimo": 500.00,
    "indicado_para": "Perfil moderado que busca diversificação"
  },
  {
    "nome": "Fundo de Ações",
    "categoria": "fundo",
    "risco": "alto",
    "rentabilidade": "Variável",
    "aporte_minimo": 100.00,
    "indicado_para": "Perfil arrojado com foco no longo prazo"
  }
]

```

## Exemplo de Contexto Montado

Exemplo de como os dados são formatados para o agente utilizando dados da base de conhecimento, sintetizando apenas informações mais relevantes.

```
## Exemplo de Contexto Montado

Dados do Cliente:
- Nome: João Silva
- Idade: 32 anos | Profissão: Analista de Sistemas
- Perfil de investidor: Moderado (não aceita risco elevado)
- Renda mensal: R$ 5.000,00
- Patrimônio total: R$ 15.000,00
- Reserva de emergência atual: R$ 10.000,00
- Objetivo principal: Construir reserva de emergência e ter maior controle de gastos

Resumo de Gastos (outubro/2025):
- Moradia: R$ 1.380,00 (Aluguel + Conta de Luz)
- Alimentação: R$ 970,00 (Supermercado + Restaurante + Fast-Food)
- Transporte: R$ 295,00 (Uber + Combustível)
- Saúde: R$ 188,00 (Farmácia + Academia)
- Lazer: R$ 156,70 (Netflix + Spotify)
- Total de saídas: R$ 3.989,70
- Saldo disponível (receita - saídas): R$ 1.010,30

Produtos Disponíveis para Explicar:
- Tesouro Selic — renda fixa, baixo risco, 100% da Selic, aporte mínimo R$ 30,00
- CDB Liquidez Diária — renda fixa, baixo risco, 102% do CDI, aporte mínimo R$ 100,00
- LCI/LCA — renda fixa, baixo risco, 95% do CDI (isento de IR), aporte mínimo R$ 1.000,00
- Fundo Multimercado — risco médio, CDI + 2%, aporte mínimo R$ 500,00
- Fundo de Ações — risco alto, rentabilidade variável, aporte mínimo R$ 100,00
```

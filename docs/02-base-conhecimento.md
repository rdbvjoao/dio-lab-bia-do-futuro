# Base de Conhecimento

## Dados Utilizados

Descreva se usou os arquivos da pasta `data`, por exemplo:

| Arquivo | Formato | Para que serve? |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores, ou seja, conhecer melhor o cliente. |
| `perfil_investidor.json` | JSON | Permite que a Clara adapte explicações sobre finanças e investimentos ao perfil do usuário, garantindo recomendações claras e adequadas. |
| `produtos_financeiros.json` | JSON | Serve de referência para que a Clara sugira produtos financeiros compatíveis com o perfil e necessidades do cliente. |
| `transacoes.csv` | CSV | Histórico de transações do cliente, para analisar padrões de gastos, identificar desvios e gerar alertas inteligentes. |

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

Os dados mockados foram adaptados para simular um mês de transações reais, incluindo receitas, despesas fixas, micro-gastos e despesas variáveis, permitindo que a Clara analise padrões de gastos e gere alertas inteligentes.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Existem duas possibilidades, injetar os dados diretamente no prompt (CTRL + C, CTRL + V) ou carregar os arquivos via código, conforme o código abaixo:

```python
import panda as pd
import json

#CSVs
historico = pd.read_csv('data/historico_atendimento.csv')
transacoes = pd.read_csv('data/transacoes.csv')

#JSONs
with open('data/perfil_investidor.json', 'r', encoding="utf-8") as f:
  perfil = json.load(f)

with open('data/produtos_financieros.json', 'r', encoding="utf-8") as f:
  produtos = json.load(f)
```

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Para simplificar, podemos simplesmente "injetar" os dados em nosso prompt, garantindo que o agente tenha o melhor contexto possível. Lembrando que eme soluções mais robustas, o ideal é que essas informações sejam carregadas dinamicamente para que possamos ganhar flexibilidade.

```text
DADOS E PERFIL DO CLIENTE (data/perfil_investidor.json)
{
  "nome": "Mariana Costa",
  "idade": 32,
  "profissao": "Engenheira de Software",
  "renda_mensal": 5000.00,
  "perfil_investidor": "moderado",
  "objetivo_principal": "Construir reserva de emergência",
  "patrimonio_total": 15000.00,
  "reserva_emergencia_atual": 10000.00,
  "aceita_risco": false,
  "despesas_mensais": {
    "moradia": 1380.00,
    "alimentacao": 680.00,
    "transporte": 333.00,
    "saude": 188.00,
    "lazer": 90.00,
    "outros": 63.50
  },
  "metas": [
    {
      "meta": "Completar reserva de emergência",
      "valor_necessario": 15000.00,
      "prazo": "2026-06",
      "concluido_percentual": 66.7
    },
    {
      "meta": "Entrada do apartamento",
      "valor_necessario": 50000.00,
      "prazo": "2027-12",
      "concluido_percentual": 30.0
    }
  ],
  "preferencias_alertas": {
    "limite_mensal_categoria": true,
    "micro_gastos": true,
    "desvios_orcamento": true
  },
  "preferencias_comunicacao": {
    "tom": "informal",
    "educativo": true,
    "frequencia_alertas": "diaria"
  }
}

}

HISTORICO DE ATENDIMENTO (data/historico_atendimento.csv):
data,canal,tema,resumo,resolvido
2025-09-15,chat,CDB,Cliente perguntou sobre rentabilidade e prazos,sim
2025-09-22,telefone,Problema no app,Erro ao visualizar extrato foi corrigido,sim
2025-10-01,chat,Tesouro Selic,Cliente pediu explicação sobre o funcionamento do Tesouro Direto,sim
2025-10-12,chat,Metas financeiras,Cliente acompanhou o progresso da reserva de emergência,sim
2025-10-25,email,Atualização cadastral,Cliente atualizou e-mail e telefone,sim


TRANSACOES DO CLIENTE (data/transacoes.csv):
data,descricao,categoria,valor,tipo
2025-10-01,Salário,receita,5000.00,entrada
2025-10-02,Aluguel,moradia,1200.00,saida
2025-10-03,Supermercado,alimentacao,450.00,saida
2025-10-05,Netflix,lazer,55.90,saida
2025-10-07,Farmácia,saude,89.00,saida
2025-10-10,Restaurante,alimentacao,120.00,saida
2025-10-12,Uber,transporte,45.00,saida
2025-10-15,Conta de Luz,moradia,180.00,saida
2025-10-17,Academia,saude,99.00,saida
2025-10-20,Combustível,transporte,250.00,saida
2025-10-22,Café da manhã,alimentacao,15.50,saida
2025-10-23,Padaria,alimentacao,28.00,saida
2025-10-25,Uber,transporte,38.00,saida
2025-10-27,Spotify,lazer,34.90,saida
2025-10-28,Restaurante,alimentacao,65.00,saida
2025-10-30,Café Starbucks,alimentacao,18.50,saida


PRODUTOS DISPONIVEIS (data/produtos_financeiros.json):
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
---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

O exemplo de contexto montado abaixo, baseia-se nos dados originais da base de conhecimento, mas os sintetiza deixando apenas as informações mais relevantes. Assim, otimizando o consumo de tokens. Entretanto, vale lembrar que mais importante do que economizar tokens é ter todas as informações relevantes disponíveis em seu contexto. 

```
Dados do Cliente:
- Nome: Mariana Costa
- Perfil: Moderado
- Saldo disponível: R$ 5.000

Últimas transações:
- 01/10: Salário - R$ 5.000
- 02/10: Aluguel - R$ 1.200
- 03/10: Supermercado - R$ 450
- 05/10: Netflix - R$ 55,90
- 07/10: Farmácia - R$ 89
- 10/10: Restaurante - R$ 120
- 12/10: Uber - R$ 45
- 15/10: Conta de Luz - R$ 180
- 17/10: Academia - R$ 99
- 20/10: Combustível - R$ 250
- 22/10: Café da manhã - R$ 15,50
- 23/10: Padaria - R$ 28
- 25/10: Uber - R$ 38
- 27/10: Spotify - R$ 34,90
- 28/10: Restaurante - R$ 65
- 30/10: Café Starbucks - R$ 18,50

Metas financeiras:
- Completar reserva de emergência: R$ 10.000 / R$ 15.000 (66,7%)
- Entrada do apartamento: R$ 15.000 / R$ 50.000 (30%)

Preferências de alertas:
- Limite mensal por categoria: Ativo
- Micro-gastos: Ativo
- Desvios do orçamento: Ativo

```

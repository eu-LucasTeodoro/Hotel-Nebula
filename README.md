# Hotel Nebula: Modelagem de Banco de Dados

Projeto de banco de dados não relacional (MongoDB) para o Hotel Nebula.

## 1. Visão geral
O Hotel Nebula precisa organizar dados de hóspedes, quartos, reservas,
hospedagens, pagamentos, funcionários e avaliações, que hoje estão dispersos
em planilhas e sistemas diferentes. Este projeto propõe um banco de dados não
relacional no MongoDB, com uma coleção para cada um desses núcleos de
informação.

## 2. Coleções
| Coleção | O que guarda |
|---|---|
| hospedes | Dados cadastrais de cada cliente |
| quartos | Quartos do hotel e suas características |
| reservas | Pedidos de reserva feitos pelos hóspedes (proposta) |
| hospedagens | A estadia real: check-in, check-out e totais |
| servicos | Catálogo de serviços extras oferecidos |
| feedbacks | Avaliações e comentários dos clientes |
| pagamentos | Pagamentos realizados (proposta) |
| funcionarios | Profissionais que atendem os hóspedes (proposta) |

Observação: no MongoDB local, os nomes das coleções iniciam com maiúscula
(`Hospedes`, `Quartos`, `Serviços`, `Feedback`). Neste documento uso nomes em
minúsculo e sem acento por padronização.

## 3. Esquema das coleções

### hospedes
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador |
| nome | texto | Nome completo |
| email | texto | E-mail |
| cpf | texto | CPF |
| telefone | texto | Telefone |
| data_nascimento | texto | Data de nascimento |
| data_cadastro | texto | Quando se cadastrou |
| ativo | verdadeiro/falso | Se o cadastro está ativo |

### quartos
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do quarto |
| numero | texto | Número do quarto, como aparece na porta (ex.: "101") |
| tipo | texto | Categoria do quarto: `standard`, `luxo`, `suite` ou `suite_gamer` |
| capacidade | número | Máximo de pessoas que o quarto comporta |
| preco_diaria | número | Valor da diária, em reais |
| status | texto | Situação atual: `disponivel` ou `ocupado` |
| andar | número | Andar em que o quarto fica |
| vista | texto | Vista da sacada: `jardim`, `mar` ou `cidade` |

### hospedagens
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador da hospedagem |
| reserva_id | número | Referência à reserva que originou a hospedagem |
| funcionario_checkin_id | número | Referência ao funcionário que fez o check-in |
| funcionario_checkout_id | número ou nulo | Referência ao funcionário que fez o check-out (nulo enquanto em andamento) |
| checkin_real | texto (data ISO) | Data e hora reais da entrada |
| checkout_real | texto (data ISO) ou nulo | Data e hora reais da saída (nulo enquanto em andamento) |
| status | texto | Situação: `encerrada` ou `em_andamento` |
| total_diarias | número | Valor total das diárias, em reais |
| total_servicos | número | Valor total dos serviços consumidos, em reais |
| total_geral | número | Soma de diárias e serviços |

### servicos
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do serviço |
| nome | texto | Nome do serviço (ex.: "Café da manhã", "Minibar", "Lavanderia", "Transfer aeroporto") |
| categoria | texto | Grupo do serviço: `alimentacao`, `comodidade` ou `transporte` |
| preco | número | Valor do serviço, em reais |
| disponivel | verdadeiro/falso | Indica se o serviço está disponível para contratação |

### feedbacks
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do feedback |
| hospede_id | número | Referência ao hóspede que avaliou |
| quarto_id | número | Referência ao quarto avaliado |
| hospedagem_id | número | Referência à hospedagem avaliada |
| nota_geral | número | Nota geral da estadia, com decimais (ex.: 4.5) |
| comentario | texto | Comentário escrito pelo hóspede |
| data_avaliacao | texto (data ISO) | Data e hora em que a avaliação foi feita |

### reservas (proposta)
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador da reserva |
| hospede_id | número | Referência ao hóspede que reservou |
| quarto_id | número | Referência ao quarto reservado |
| data_reserva | texto (data ISO) | Quando a reserva foi feita |
| data_entrada_prevista | texto (data ISO) | Data prevista de entrada |
| data_saida_prevista | texto (data ISO) | Data prevista de saída |
| status | texto | Situação: `confirmada`, `cancelada` ou `concluida` |

### pagamentos (proposta)
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do pagamento |
| hospedagem_id | número | Referência à hospedagem paga |
| valor | número | Valor pago, em reais |
| forma_pagamento | texto | Ex.: `cartao_credito`, `pix`, `dinheiro` |
| status | texto | Situação: `pago` ou `pendente` |
| data_pagamento | texto (data ISO) | Data e hora do pagamento |

### funcionarios (proposta)
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do funcionário |
| nome | texto | Nome completo |
| cargo | texto | Função exercida (ex.: `recepcionista`) |
| email | texto | E-mail profissional |
| ativo | verdadeiro/falso | Se o funcionário está ativo |

## 4. Exemplos de documentos

### hospedes
```json
{
  "_id": 1,
  "nome": "Carlos Andrade",
  "email": "carlos.andrade@email.com",
  "cpf": "123.456.789-00",
  "telefone": "+55 11 98765-4321",
  "data_nascimento": "1985-03-22",
  "data_cadastro": "2025-01-10T10:30:00Z",
  "ativo": true
}
```

### quartos
```json
{
  "_id": 1,
  "numero": "101",
  "tipo": "standard",
  "capacidade": 2,
  "preco_diaria": 250,
  "status": "disponivel",
  "andar": 1,
  "vista": "jardim"
}
```

### hospedagens
```json
{
  "_id": 1,
  "reserva_id": 1,
  "funcionario_checkin_id": 1,
  "funcionario_checkout_id": 2,
  "checkin_real": "2025-04-10T14:35:00Z",
  "checkout_real": "2025-04-14T11:10:00Z",
  "status": "encerrada",
  "total_diarias": 1000,
  "total_servicos": 245,
  "total_geral": 1245
}
```

### servicos
```json
{
  "_id": 1,
  "nome": "Café da manhã",
  "categoria": "alimentacao",
  "preco": 45,
  "disponivel": true
}
```

### feedbacks
```json
{
  "_id": 1,
  "hospede_id": 1,
  "quarto_id": 1,
  "hospedagem_id": 1,
  "nota_geral": 4.5,
  "comentario": "Ótima estadia, equipe atenciosa e quarto muito limpo.",
  "data_avaliacao": "2025-04-15T09:00:00Z"
}
```

### reservas (proposta)
```json
{
  "_id": 1,
  "hospede_id": 1,
  "quarto_id": 1,
  "data_reserva": "2025-04-01T10:00:00Z",
  "data_entrada_prevista": "2025-04-10",
  "data_saida_prevista": "2025-04-14",
  "status": "concluida"
}
```

### pagamentos (proposta)
```json
{
  "_id": 1,
  "hospedagem_id": 1,
  "valor": 1245,
  "forma_pagamento": "cartao_credito",
  "status": "pago",
  "data_pagamento": "2025-04-14T11:15:00Z"
}
```

### funcionarios (proposta)
```json
{
  "_id": 1,
  "nome": "Ana Souza",
  "cargo": "recepcionista",
  "email": "ana.souza@hotelnebula.com",
  "ativo": true
}
```

## 5. Proposta de modelagem
As coleções se conectam por referências (campos terminados em `_id`):
- `reservas` aponta para `hospedes` e `quartos`.
- `hospedagens` aponta para `reservas` e para `funcionarios` (check-in e check-out).
- `pagamentos` aponta para `hospedagens`.
- `feedbacks` aponta para `hospedes`, `quartos` e `hospedagens`.

## 6. Decisões de modelagem

### Dados do hóspede na reserva
Escolhi referenciar o hóspede pelo `hospede_id`. Os dados do hóspede, como
telefone e e-mail, podem mudar. Se fossem copiados para cada reserva,
ficariam desatualizados.

### Avaliações
As avaliações ficam em uma coleção própria (`feedbacks`), que referencia
hóspede, quarto e hospedagem. Um quarto recebe muitas avaliações ao longo do
tempo, e guardá-las dentro do quarto deixaria o documento muito grande.

### Serviços consumidos
Proponho guardar os serviços consumidos dentro da hospedagem, em uma lista,
pois são poucos e sempre consultados junto com ela. O catálogo de serviços
continua em coleção própria.

### Histórico
Os valores são guardados no momento da estadia (`total_diarias`,
`total_servicos` e `total_geral`). Assim, mudanças futuras de preço não alteram
as hospedagens passadas.

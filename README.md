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
| reservas | Pedidos de reserva feitos pelos hóspedes |
| hospedagens | A estadia real: check-in, check-out e totais |
| servicos | Catálogo de serviços extras oferecidos |
| feedbacks | Avaliações e comentários dos clientes |
| pagamentos | Pagamentos realizados (uma hospedagem pode ter vários) |
| funcionarios | Profissionais que atendem os hóspedes |

Observação: no MongoDB os nomes das coleções iniciam com maiúscula
(`Hospedes`, `Quartos`, `Reservas`, `Hospedagens`, `Serviços`, `Feedback`,
`Pagamentos`, `Funcionarios`). Neste documento uso nomes em minúsculo e sem
acento por padronização.

## 3. Esquema das coleções

### hospedes
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador |
| nome | texto | Nome completo |
| email | texto | E-mail |
| cpf | texto | CPF (índice único) |
| telefone | texto | Telefone |
| data_nascimento | texto | Data de nascimento |
| data_cadastro | texto (data ISO) | Quando se cadastrou |
| ativo | verdadeiro/falso | Se o cadastro está ativo |
| endereco | subdocumento | Cidade e estado (opcional) |
| preferencias | lista de textos | Preferências de estadia (opcional) |

### quartos
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do quarto |
| numero | texto | Número do quarto, como aparece na porta (ex.: "101") |
| tipo | texto | Categoria: `standard`, `luxo`, `suite`, `suite_gamer`, `studio_scifi`, `familia`, `executivo` ou `capsula` |
| capacidade | número | Máximo de pessoas que o quarto comporta |
| preco_diaria | número | Valor da diária, em reais |
| status | texto | Situação atual: `disponivel`, `ocupado` ou `manutencao` |
| andar | número | Andar em que o quarto fica |
| vista | texto | Vista da sacada: `jardim`, `mar`, `cidade`, `montanha` ou `panoramica` |
| comodidades | lista de textos | Características do quarto, como banheira e varanda (opcional) |

### reservas
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador da reserva |
| hospede_id | número | Referência ao hóspede que reservou |
| quarto_id | número | Referência ao quarto reservado |
| data_reserva | texto (data ISO) | Quando a reserva foi feita |
| data_checkin | texto (data) | Data de entrada prevista |
| data_checkout | texto (data) | Data de saída prevista |
| status | texto | `confirmada`, `cancelada` ou `pendente` |
| canal | texto | Origem: `site_proprio`, `agencia_online`, `telefone`, `app_mobile` ou `empresa_parceira` |
| valor_previsto | número | Valor previsto da estadia, em reais |

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
| servicos_consumidos | lista de subdocumentos | Serviços usados: `servico_id`, `quantidade` e `preco_unitario` (opcional) |

### servicos
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do serviço |
| nome | texto | Nome do serviço (ex.: "Café da manhã", "Minibar", "Estação VR") |
| categoria | texto | Grupo: `alimentacao`, `comodidade`, `transporte`, `entretenimento` ou `experiencia` |
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

### pagamentos
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do pagamento |
| hospedagem_id | número | Referência à hospedagem paga |
| valor | número | Valor desta parcela, em reais |
| metodo | texto | `pix`, `cartao_credito` ou `cartao_debito` |
| status | texto | `aprovado` ou `pendente` |
| data_pagamento | texto (data ISO) ou nulo | Data e hora do pagamento (nulo se pendente) |
| transacao_id | texto | Código da transação |

### funcionarios
| Campo | Tipo | Descrição |
|---|---|---|
| _id | número | Identificador do funcionário |
| nome | texto | Nome completo |
| cargo | texto | Função (ex.: Recepcionista, Concierge) |
| departamento | texto | Setor (ex.: Recepção, Operações) |
| email | texto | E-mail profissional |
| telefone | texto | Telefone |
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
  "ativo": true,
  "endereco": { "cidade": "São Paulo", "estado": "SP" },
  "preferencias": ["andar alto", "quarto silencioso"]
}
```

### quartos
```json
{
  "_id": 2,
  "numero": "202",
  "tipo": "luxo",
  "capacidade": 3,
  "preco_diaria": 420,
  "status": "ocupado",
  "andar": 2,
  "vista": "mar",
  "comodidades": ["banheira", "varanda", "ar-condicionado"]
}
```

### reservas
```json
{
  "_id": 1,
  "hospede_id": 1,
  "quarto_id": 1,
  "data_reserva": "2025-03-28T14:22:00Z",
  "data_checkin": "2025-04-10",
  "data_checkout": "2025-04-14",
  "status": "confirmada",
  "canal": "site_proprio",
  "valor_previsto": 1000
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
  "total_geral": 1245,
  "servicos_consumidos": [
    { "servico_id": 1, "quantidade": 2, "preco_unitario": 45 },
    { "servico_id": 2, "quantidade": 1, "preco_unitario": 80 },
    { "servico_id": 3, "quantidade": 3, "preco_unitario": 25 }
  ]
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

### pagamentos
```json
{
  "_id": 1,
  "hospedagem_id": 1,
  "valor": 1000,
  "metodo": "cartao_credito",
  "status": "aprovado",
  "data_pagamento": "2025-04-14T11:05:00Z",
  "transacao_id": "TXN-VISA-00392"
}
```

### funcionarios
```json
{
  "_id": 1,
  "nome": "Juliana Martins",
  "cargo": "Recepcionista",
  "departamento": "Recepção",
  "email": "juliana.martins@hotelnebula.com",
  "telefone": "+55 21 99123-4567",
  "ativo": true
}
```

## 5. Proposta de modelagem
As coleções se conectam por referências (campos terminados em `_id`):
- `reservas` aponta para `hospedes` e `quartos`.
- `hospedagens` aponta para `reservas` e para `funcionarios` (check-in e check-out).
- `pagamentos` aponta para `hospedagens`. Uma hospedagem pode ter mais de um
  pagamento (por exemplo, diárias no cartão e serviços no pix).
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
Os serviços consumidos ficam dentro da hospedagem, em uma lista de
subdocumentos (`servicos_consumidos`), pois são poucos e sempre consultados
junto com ela. O catálogo de serviços continua em coleção própria.

### Histórico
Os valores são guardados no momento da estadia (`total_diarias`,
`total_servicos`, `total_geral` e o `preco_unitario` de cada serviço
consumido). Assim, mudanças futuras de preço não alteram as hospedagens
passadas.

## 7. Arquitetura adotada

O banco `Hotel_Nebula` (MongoDB) tem 8 coleções: hospedes, quartos, reservas,
hospedagens, servicos, feedbacks, pagamentos e funcionarios.

**Referências.** As coleções se ligam por `_id` (ex.: `hospede_id`,
`hospedagem_id`). Isso evita duplicar dados que mudam, como o telefone do
hóspede, e mantém os documentos pequenos.

**Dados aninhados.** Usei aninhamento onde o dado pertence a um único
documento e é lido junto com ele:
- `quartos.comodidades`: array de textos (banheira, varanda...).
- `hospedagens.servicos_consumidos`: array de subdocumentos com `servico_id`,
  `quantidade` e `preco_unitario`. O preço fica gravado no momento do
  consumo, preservando o histórico se o preço do serviço mudar. A soma
  confere com `total_servicos`.
- `hospedes.endereco` e `hospedes.preferencias`: subdocumento e array
  opcionais.

**Flexibilidade.** Nem todos os documentos têm os mesmos campos. Quartos sem
`comodidades` e hóspedes sem `endereco` continuam válidos, o que permite
evoluir o modelo sem migrar a base inteira.

**Escalabilidade.** Criei índices em `cpf` (único), `hospede_id`,
`hospedagem_id`, `checkin_real` e `vista`, para acelerar as buscas mais
frequentes. Avaliações e pagamentos ficam em coleções próprias porque crescem
sem limite, e cada documento do MongoDB tem tamanho máximo.

**Consistência.** Verifiquei com `$lookup` que todas as hospedagens apontam
para reservas existentes.

## 8. Arquivos do repositório
Os arquivos `Hotel_Nebula.*.json` são as coleções exportadas do MongoDB.
Os três terminados em `_alteras` (Hospedagens, Hospedes e Quartos) contêm os
campos aninhados adicionados na Parte 2. Os demais são os dados originais.

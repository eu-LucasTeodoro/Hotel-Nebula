# Hotel Nebula: Modelagem de Banco de Dados

Projeto de banco de dados não relacional (MongoDB) para o Hotel Nebula.

## 1. Visão geral
O hotel precisa organizar dados de hóspedes, quartos, reservas, hospedagens,
pagamentos, funcionários e avaliações, que hoje estão dispersos.

## 2. Coleções
| Coleção | O que guarda |
|---|---|
| hospedes | Dados cadastrais de cada cliente |
| quartos | Quartos do hotel e suas características |
| reservas | Pedidos de reserva feitos pelos hóspedes |
| hospedagens | A estadia real: check-in, check-out e totais |
| servicos | Catálogo de serviços extras oferecidos |
| feedbacks | Avaliações e comentários dos clientes |
| pagamentos | Pagamentos realizados |
| funcionarios | Profissionais que atendem os hóspedes |

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

### Hospedagens 
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

### Quartos

## 4. Exemplos de documentos
```json

///Hospedes 
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

///Hospedagens
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

///Quartos
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

///Serviços
{
  "_id": 1,
  "nome": "Café da manhã",
  "categoria": "alimentacao",
  "preco": 45,
  "disponivel": true
}

///Feedback
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

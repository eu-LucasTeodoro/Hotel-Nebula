// Hotel Nebula - Parte 3: Consultas
// Executar no mongosh, com o banco Hotel_Nebula
use("Hotel_Nebula")

// 1. Quartos por característica
db.Quartos.find({ vista: "mar" })              // 202 e 303
db.Quartos.find({ tipo: "luxo" })              // 202
db.Quartos.find({ comodidades: "banheira" })   // 202 e 303

// 2. Hóspedes com mais de uma reserva (resultado: nenhum nos dados originais)
db.Reservas.aggregate([
  { $group: { _id: "$hospede_id", total: { $sum: 1 } } },
  { $match: { total: { $gt: 1 } } }
])

// 3. Faturamento (hospedagens encerradas, por checkout_real)
// 3a. Abril/2025: 1245 (1 hospedagem)
db.Hospedagens.aggregate([
  { $match: { status: "encerrada", checkout_real: { $gte: "2025-04-01", $lt: "2025-05-01" } } },
  { $group: { _id: null, faturamento: { $sum: "$total_geral" }, hospedagens: { $sum: 1 } } }
])

// 3b. Ano de 2025: 64655 (34 hospedagens)
db.Hospedagens.aggregate([
  { $match: { status: "encerrada", checkout_real: { $gte: "2025-01-01", $lt: "2026-01-01" } } },
  { $group: { _id: null, faturamento: { $sum: "$total_geral" }, hospedagens: { $sum: 1 } } }
])

// 3c. Por mês: abr 1245, mai 8570, jun 47725, jul 7115
db.Hospedagens.aggregate([
  { $match: { status: "encerrada" } },
  { $group: { _id: { $substr: ["$checkout_real", 0, 7] }, faturamento: { $sum: "$total_geral" }, hospedagens: { $sum: 1 } } },
  { $sort: { _id: 1 } }
])

// 4. Ocupação (reservas confirmadas)
// 4a. Por mês de check-in: jun 30, mai 5, abr 2
db.Reservas.aggregate([
  { $match: { status: "confirmada" } },
  { $group: { _id: { $substr: ["$data_checkin", 0, 7] }, reservas: { $sum: 1 } } },
  { $sort: { reservas: -1 } }
])

// 4b. Por dia da semana (1=dom ... 7=sáb): seg, qui e sáb com 6 cada; qua com 4
db.Reservas.aggregate([
  { $match: { status: "confirmada" } },
  { $group: { _id: { $dayOfWeek: { $dateFromString: { dateString: "$data_checkin" } } }, reservas: { $sum: 1 } } },
  { $sort: { reservas: -1, _id: 1 } }
])

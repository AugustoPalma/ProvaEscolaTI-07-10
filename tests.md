# Tests — Zona Azul Digital

| Regra de negócio | Caso de borda | Esperado |
|---|---|---|
| Fração (N.1) | 15 min (1 fração) | 138 |
| Fração (N.1) | 45 min (3 frações, ímpar) | 413 |
| Teto diário | 645 min (abaixo do teto) | 5913 |
| Teto diário | 646 min (ultrapassa) | 6000 |
| Tolerância (= 0) | 1 min | 138 (cobra integral) |
| Tempo médio (N.2) | média 47,5 | 48 |
| Placa duplicada (UC8) | abrir placa já aberta | 409 `bilhete_em_aberto` |
| Placa duplicada (UC8) | reabrir após encerrar | 201 |
| Precedência (N.2) | placa inválida + já aberta | 422 `placa_invalida` |
| Validação de placa (N.3) | placa na query inválida (UC6) | 422 `placa_invalida` |
| Histórico vazio (UC6) | placa nunca usada | 200, array vazio |
| Cancelamento (UC5) | bilhete já encerrado | 409 `bilhete_nao_aberto` |
| Ordenação (N.4) | duas entradas no mesmo instante | desempate por `id` decrescente |

## UC1 — Abrir bilhete
`POST /bilhetes` — body: `placa` (obrig., 7 alfanum. maiúsc.), `entrada` (opc., ISO-8601 `-03:00`)

| Condição | Resposta |
|---|---|
| Placa válida, sem bilhete aberto | 201 `{id, placa, entrada, status:"aberto"}` |
| Sem `entrada` | usa instante atual |
| `entrada` válida | usa valor enviado |
| Placa inválida | 422 `placa_invalida` |
| `entrada` inválida | 422 `entrada_invalida` |
| Placa já com bilhete aberto | 409 `bilhete_em_aberto` |

> [!NOTE]
> Placa inválida + já aberta → vence **422** (N.2).

## UC2 — Encerrar bilhete
`POST /bilhetes/{id}/encerramento`

| Condição | Resposta |
|---|---|
| Bilhete aberto | 200 `{id, placa, entrada, saida, minutos, valor_centavos}` |
| `valor_centavos` | fórmula N.1 (teto 6000 aplicado se exceder) |
| `id` inexistente | 404 `bilhete_nao_encontrado` |
| Já encerrado | 409 `bilhete_ja_encerrado` |

## UC3 — Listar ativos
`GET /bilhetes/ativos`

| Condição | Resposta |
|---|---|
| Sempre | 200, array de abertos, ordenado por N.4 |
| Nenhum aberto | 200, array vazio |

## UC4 — Relatório diário
`GET /relatorios/diario?data=AAAA-MM-DD`

| Condição | Resposta |
|---|---|
| `data` válida | 200 `{data, total_bilhetes, faturamento_centavos, tempo_medio_minutos}` |
| `total_bilhetes` | bilhetes com `entrada` no dia |
| `faturamento_centavos` | soma `valor_centavos` dos encerrados com `saida` no dia |
| `tempo_medio_minutos` | média dos encerrados no dia; 0,5 arredonda pra cima (N.2) |
| Sem bilhetes no dia | 200, todos os campos zerados |
| `data` inválida/ausente | 422 `data_invalida` |

> [!WARNING]
> Decisão minha, não literal do contrato: `faturamento_centavos` agrupa por `saida`, `total_bilhetes` por `entrada`. Revisar se concorda.

## UC5 — Cancelar bilhete
`POST /bilhetes/{id}/cancelamento`

| Condição | Resposta |
|---|---|
| Bilhete aberto | 200 `{..., status:"cancelado"}` — sem `saida`/`valor_centavos` |
| `id` inexistente | 404 `bilhete_nao_encontrado` |
| Já encerrado/cancelado | 409 `bilhete_nao_aberto` |

## UC6 — Histórico por placa
`GET /bilhetes?placa=`

| Condição | Resposta |
|---|---|
| Placa válida | 200, array de todos os status, ordenado por N.4 |
| Placa nunca usada | 200, array vazio |
| Placa inválida/ausente | 422 `placa_invalida` (N.3) |

## UC7 — Tolerância
`TOLERANCIA_MINUTOS = 0` nesta variante → sem minuto grátis, cobra fração desde o 1º minuto. Decide **se** cobra; N.1 decide **quanto**.

## UC8 — Uma vaga por placa

| Condição | Resposta |
|---|---|
| Placa já aberta | 409 `bilhete_em_aberto`, não cria |
| Placa encerrada/cancelada | 201 normal, novo bilhete sem vínculo |

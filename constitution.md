### N.1 — Utilização de centavos inteiros

**Regra:** Todo valor monetário é representado em centavos, como número
inteiro — nunca ponto flutuante (`float`/`double`), em nenhum campo de
entrada ou saída da API.

**Motivo:** Ponto flutuante em cálculos financeiros acumula erro de
arredondamento (ex.: `0.1 + 0.2 ≠ 0.3`), o que pode causar divergência no
fechamento de contas quando valores são somados repetidamente. Centavos
inteiros eliminam essa classe de bug por completo.

**Aplica-se a:** `valor_centavos` (encerramento de bilhete), `faturamento_centavos`
(relatório diário), e qualquer campo monetário futuro da API.


### N.2 — Precedência de validação sobre regra de negócio

**Regra:** Validação de formato (HTTP 422) é sempre verificada antes de
regras de negócio (HTTP 409 ou 404). Um payload ou parâmetro malformado
nunca dispara um erro de conflito de estado ou "não encontrado".

**Motivo:** Sem uma ordem definida, o modelo gerado poderia checar as
regras em ordem arbitrária — por exemplo, retornando 409 para uma placa
mal formatada que coincidentemente já tem bilhete aberto, quando o
correto é 422. Essa regra já está explícita no contrato máquina-legível
(`contrato.json`, campo `regras_gerais`).

**Aplica-se a:** Qualquer endpoint que combine validação de formato com
regra de negócio — `POST /bilhetes` (UC1, UC8), `POST /bilhetes/{id}/encerramento`
(UC2), `POST /bilhetes/{id}/cancelamento` (UC5).

### N.3 — Validação de placa é idêntica em qualquer ponto de entrada

**Regra:** O formato de placa (7 caracteres alfanuméricos, maiúsculos) é
validado com a mesma regra, produzindo o mesmo erro (`422 placa_invalida`),
em qualquer lugar onde uma placa seja recebida — no corpo da requisição ou
como parâmetro de busca.

**Motivo:** Evita que a consulta (UC6) aceite placas em formato que a
escrita (UC1) rejeitaria, o que criaria uma inconsistência de validação
entre endpoints do mesmo recurso.

**Aplica-se a:** `POST /bilhetes` (UC1, corpo), `GET /bilhetes?placa=`
(UC6, query string).

### N.4 — Ordenação de listas de bilhetes

**Regra:** Qualquer endpoint que retorne uma lista de bilhetes ordena
sempre por `entrada`, decrescente (mais recente primeiro). Em caso de
empate exato de `entrada`, o desempate é feito por `id` decrescente.

**Motivo:** O contrato não define desempate para `entrada` idêntica. Como
o campo `entrada` aceita um valor fixo para testabilidade, dois bilhetes
podem ser abertos no mesmo instante exato durante os testes da suíte —
sem uma regra de desempate, a ordem da lista seria não-determinística
entre execuções.


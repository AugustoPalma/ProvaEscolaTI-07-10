# Plan — Zona Azul Digital

| Decisão | Justificativa |
|---|---|
| Stack: Node.js + Express | Rotas REST simples, JSON nativo, pouco boilerplate |
| Porta via env var `PORTA_SERVICO`, nunca fixa | Suíte conecta em `localhost:8004`; porta hardcoded quebra a correção |
| Persistência em memória | Contrato não exige durabilidade entre reinícios |
| `entrada` sobrescreve o relógio quando enviada (UC1) | Gancho de testabilidade do contrato — evita esperar tempo real nos testes |
| `id` inteiro incremental, a partir de 1 | Contrato usa `id: 1` nos exemplos |
| Sem ORM/banco | Persistência em memória não precisa de camada de banco |
| Containerfile expõe porta via `${PORTA_SERVICO}` | Mesma razão da porta — evita `EXPOSE 8080` fixo sobrescrever a variante |
| Validação centralizada em middleware, antes dos handlers | Garante precedência 422-antes-409 (constitution.md §N.2) sem repetir lógica por endpoint |

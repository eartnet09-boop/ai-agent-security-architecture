# Matriz de permissões de agentes

Matriz ilustrativa: adapte aos modelos reais do Prisma, aos limites de tenant e às operações de domínio. `Read`/`Write` descreve intenção, não um grant SQL literal. Prefira APIs e funções estreitas em vez de conceder acesso direto do agente ao ORM. O padrão é `None`.

| Agente | `User` | `Project` | `Task` | `AuditEvent` | `TestFixture` |
|---|---:|---:|---:|---:|---:|
| `Orchestrator-Agent` | Read (campos mínimos) | Read (escopo do usuário) | Read | None | None |
| `QA-Agent` | None | None | None | None | Read |
| `Research-Agent` | None | None | None | None | None |
| `Data-Maintenance-Agent` | None | Read | Read / Write (campos allowlisted) | None | None |

## Regras de enforcement

- Faça autorização por operação, objeto, tenant e campo; uma permissão `Read` ampla pode revelar dados de outros usuários.
- Separe papel humano, principal do agente e identidade do serviço. Nunca herde automaticamente o acesso do usuário que iniciou o agente.
- Writes devem ter schema estrito, validação de invariantes, idempotency key e limite de lote. Exija aprovação humana para operações irreversíveis ou de alto impacto.
- Escrita de auditoria é feita por serviço confiável append-only; agentes não alteram nem apagam a trilha.
- Implemente grants separados por ambiente/schema quando houver acesso direto inevitável; QA não recebe credencial de produção e não recebe `INSERT`, `UPDATE` ou `DELETE`.
- Reavalie a matriz com cada nova tabela, ferramenta, relação ou campo sensível. Falha de avaliação deve negar a chamada.

## Testes de autorização

- Para cada célula `None`, teste que a operação é negada tanto via API quanto por chamada direta ao serviço de domínio.
- Teste acesso cruzado entre tenants e manipulação de IDs de recursos.
- Confirme que dados omitidos da resposta não aparecem em logs, traces ou contexto do modelo.
- Faça revisão de grants efetivos do banco; compare-os com a matriz documentada.
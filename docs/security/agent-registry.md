# Registro de agentes

Este registro é um modelo fictício. Substitua responsáveis e nomes, aprove cada entrada e mantenha o registro sincronizado com a identidade efetivamente provisionada. Um agente ausente ou sem aprovação não deve receber credenciais de produção.

| Agent ID | Propósito permitido | Dono (papel) | Ambiente | Nível de permissão | Limites / aprovação |
|---|---|---|---|---|---|
| `Orchestrator-Agent` | Decompor solicitações e encaminhar tarefas a agentes registrados. | Product Engineering | Staging; produção após gate | Leitura de contexto autorizado; sem escrita direta | Sem acesso a segredos; ações externas requerem agente executor aprovado. |
| `QA-Agent` | Executar testes e consultar fixtures/dados sintéticos. | QA Lead | CI / staging | SELECT em schema de teste; sem escrita em dados de negócio | Sem dados reais; sem credencial de produção. |
| `Research-Agent` | Pesquisar fontes permitidas e resumir conteúdo público. | Knowledge Platform | Isolado | Egress allowlist; sem acesso ao banco | Saídas tratadas como não confiáveis e citadas internamente. |
| `Data-Maintenance-Agent` | Propor correções de dados com escopo limitado. | Data Owner | Staging; produção sob aprovação | Leitura e escrita em campos/recursos explicitamente permitidos | Aprovação humana, lote limitado, idempotência e rollback disponível. |

## Campos obrigatórios para agentes reais

`agent_id`, versão, propósito, owner e aprovador, modelo/provedor, ferramentas e versões, classificação de dados, ambientes, identidade técnica, permissões, orçamento, limites de execução, política de retenção, avaliação de risco, data de aprovação e próxima revisão, estado (`draft`, `approved`, `suspended`, `retired`) e referência às evidências.

## Ciclo de vida

1. Solicitar finalidade e escopo; classificar risco e dados.
2. Revisar permissões e limites com owner, Segurança e Data Owner.
3. Provisionar identidade isolada e testar negações antes de habilitar.
4. Revalidar em mudanças e periodicamente; remover credenciais e ferramentas na suspensão/retirada.

Não registre segredos neste arquivo. IDs de credenciais podem apontar para um cofre, nunca conter seu valor.
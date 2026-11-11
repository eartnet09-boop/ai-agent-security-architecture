# Checklist de segurança de agentes

Use por agente e ambiente. Marque `[x]` apenas com evidência verificável vinculada ao controle; documento escrito sem teste não equivale a implementação.

## Governança

- [ ] Registrar agente, versão, propósito, proprietário, ferramentas e ambientes.
- [ ] Avaliar risco, dados tratados, finalidade, autonomia e necessidade de revisão humana.
- [ ] Definir operações proibidas, limites de impacto e critérios de suspensão.
- [ ] Mapear controles aplicáveis e registrar fonte/versão de cada referencial.
- [ ] Registrar exceções com mitigação, aprovador, validade e risco residual.

## Identidade e autorização

- [ ] Implementar `protectedProcedure` no roteador tRPC para bloquear chamadas sem sessão/identidade válida.
- [ ] Validar identidade de workload no backend; não confiar em `agentId` enviado pelo prompt ou cliente.
- [ ] Autorizar ação, recurso, tenant e campos na camada de domínio; negar por padrão.
- [ ] Manter identidade e credenciais distintas por agente e ambiente, com expiração e revogação testadas.
- [ ] Configurar a role do banco do Agente de QA apenas com permissão `SELECT` sobre dados de teste necessários.
- [ ] Impedir que agentes acessem cliente Prisma genérico, credenciais ou operações fora de APIs allowlisted.

## Ferramentas e execução

- [ ] Aprovar catálogo de ferramentas MCP com dono, origem, versão, descrição e schema.
- [ ] Detectar alterações não aprovadas no catálogo e bloquear ferramenta até revisão.
- [ ] Validar argumentos e respostas; tratar conteúdo de ferramentas como entrada não confiável.
- [ ] Limitar iterações, duração, chamadas, taxa, tamanho de entrada/saída e detectar loops.
- [ ] Exigir aprovação humana para ações irreversíveis ou de alto impacto.
- [ ] Fazer operações de escrita idempotentes, limitadas e recuperáveis quando possível.

## Orçamento e observabilidade

- [ ] Definir limite diário de tokens e custo por agente no runtime/orquestrador.
- [ ] Aplicar limite por execução e alerta de anomalia; encerrar com segurança ao exceder o teto.
- [ ] Correlacionar requisição, execução, chamada de ferramenta, decisão e operação de banco por trace ID.
- [ ] Registrar principal, ação, recurso, decisão e versão da política sem armazenar segredos.
- [ ] Redigir dados pessoais e conteúdo sensível de logs; definir acesso e retenção.
- [ ] Testar alertas de loop, negação, divergência de ferramenta, orçamento e indisponibilidade de política.

## Resposta e recuperação

- [ ] Implementar kill switch via feature flag ou mecanismo equivalente, independente do agente.
- [ ] Testar pausa de jobs em andamento, bloqueio de novas ações e revogação de credenciais.
- [ ] Documentar procedimento de rollback/PITR e reconciliação de transações após o ponto restaurado.
- [ ] Exercitar cenário de alucinação com efeito colateral, comprometimento de ferramenta e abuso de custo.
- [ ] Preservar evidências e cadeia de custódia; designar Incident Commander e contatos de escalação.
- [ ] Exigir validação e aprovação antes de reabilitar agente após incidente.

## Compliance e revisão

- [ ] Mapear riscos e controles a NIST AI RMF, ISO/IEC 42001, EU AI Act e NIST SP 800-53 conforme aplicabilidade.
- [ ] Vincular cada controle a teste, responsável, evidência, frequência e estado real de implantação.
- [ ] Criar POA&M para lacunas, com ação mensurável, prazo, dono e critério de encerramento.
- [ ] Revisar permissões, dados, modelo, prompt e ferramentas após mudanças e periodicamente.
- [ ] Validar exclusão/retensão de dados e segredos conforme política e obrigações aplicáveis.
- [ ] Distinguir controles projetados, implantados e testados; não alegar certificação sem avaliação formal.
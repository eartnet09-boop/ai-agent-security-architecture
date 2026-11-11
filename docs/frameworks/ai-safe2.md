# AI SAFE²: sistema operacional de GRC

## Papel no projeto

O repositório [CyberStrategyInstitute/ai-safe2-framework](https://github.com/CyberStrategyInstitute/ai-safe2-framework) apresenta AI SAFE² como framework de GRC para IA agentic, identidades não humanas e governança de swarms. Use-o como camada de governança: inventário de obrigações, controles, responsáveis, evidências, exceções e planos de remediação. O framework e seu CLI não substituem análise jurídica, avaliação de risco nem enforcement independente no runtime.

O README do repositório consultado identifica a versão **v3.1**, declara **161 controles** e mapeamentos para **32 frameworks**. São totais declarados pelos mantenedores, não validados independentemente; confirme a versão/tag em uso e registre a fonte antes de usá-los em uma declaração formal. O repositório também mantém [documentação do CLI](https://github.com/CyberStrategyInstitute/ai-safe2-framework/tree/main/safe2/docs) e um [workflow de CLI 1.0](https://github.com/CyberStrategyInstitute/ai-safe2-framework/blob/main/safe2/docs/CLI-1.0-WORKFLOW.md); valide suporte e comandos na versão fixada antes de automatizar.

## Como operacionalizar

| Etapa | Ação | Saída verificável |
|---|---|---|
| Escopo | Identifique produtos, agentes, dados, regiões, usuários e finalidade. | Inventário de sistemas e limites do assessment. |
| Aplicabilidade | Mapeie obrigações por jurisdição, setor, impacto e papel da organização. | Matriz de aplicabilidade aprovada por Segurança, Produto e Jurídico. |
| Controle | Converta cada requisito em controle testável, proprietário e frequência. | Catálogo de controles com status e critério de aceite. |
| Evidência | Vincule logs, revisões, testes e aprovações com período de validade. | Evidências versionadas, minimizadas e rastreáveis. |
| Desvio | Registre lacunas, risco residual, mitigação, prazo e aprovador. | Exceção aprovada e plano de ação (POA&M, quando aplicável). |
| Revisão | Reavalie em mudanças de modelo, ferramentas, dados, riscos ou regulação. | Histórico de revisão e decisões. |

## Referenciais a mapear

| Referencial | Uso no programa | Nota de implementação |
|---|---|---|
| NIST AI RMF | Estruturar atividades de governança, mapeamento, medição e gestão de risco de IA. | Relacione risco do agente a controles e testes do ciclo de vida; mantenha a edição consultada. |
| ISO/IEC 42001 | Sistema de gestão de IA, responsabilidades, melhoria contínua e evidências organizacionais. | Avalie escopo e aplicabilidade com responsáveis de conformidade; não declare certificação sem auditoria formal. |
| EU AI Act | Identificar obrigações conforme papel, finalidade, território e classificação do sistema. | O enquadramento depende do caso concreto e da legislação vigente; revisão jurídica é necessária. |
| NIST SP 800-53 | Catálogo de controles de segurança e privacidade para selecionar e adaptar salvaguardas. | Defina baseline e tailoring; um mapeamento não implica conformidade automática. |
| OWASP para aplicações/LLMs/agentes | Identificar classes de ameaça e orientar casos de teste. | Use a versão aplicável e converta riscos em testes reproduzíveis e controles atribuídos. |

## Modelo mínimo de registro

Para cada controle, guarde: `control_id`, requisito e fonte/versão, risco atendido, sistema/agente no escopo, proprietário, implementação, teste e frequência, evidência (URI/ID, nunca segredo), status, lacuna, risco residual, aprovador e próxima revisão. Evite copiar conteúdo integral de padrões protegidos; registre referências e mapeamentos próprios.

## Critérios de aceite

- Todo agente de produção tem proprietário, finalidade aprovada e avaliação de risco atualizada.
- Cada controle aplicável tem teste, responsável e evidência com data de validade.
- Exceções têm justificativa, prazo, mitigação compensatória e aprovador independente.
- Mudanças relevantes no modelo, prompt, ferramenta, permissões ou fluxo de dados disparam reavaliação.
- Alegações de conformidade distinguem controles projetados, implantados e testados.
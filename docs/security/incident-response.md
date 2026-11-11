# Runbook de resposta a incidentes de agentes

## Gatilhos

Trate como incidente: ação não autorizada ou inesperada; alucinação com efeito colateral; loop ou gasto fora do limite; exposição de dados; credencial comprometida; ferramenta MCP alterada/envenenada; bypass do guardrail; ou impossibilidade de explicar uma decisão privilegiada.

## Procedimento

1. **Declare e coordene.** Abra incidente, defina Incident Commander e responsáveis de Segurança, aplicação, dados e privacidade. Registre horário, sistema/agentes e severidade inicial.
2. **Conter.** Pause o agente/execuções e bloqueie a ferramenta ou endpoint afetado. Ative o kill switch por agente/ambiente; se a abrangência for desconhecida, desabilite toda execução autônoma privilegiada. Não dependa de uma instrução ao modelo.
3. **Revogar.** Invalide tokens e sessões, revogue credenciais de serviço e chaves da ferramenta afetada, reduza grants do banco e bloqueie egress necessário. Confirme a propagação da revogação.
4. **Preservar evidência.** Guarde logs imutáveis, trace IDs, versões de modelo/prompt/ferramenta/política, alterações de configuração e eventos do banco. Registre hashes e cadeia de custódia. Redija segredos; não apague logs para “limpar” o ambiente.
5. **Avaliar impacto.** Identifique ações e registros afetados, tenants, dados acessados/alterados, exfiltração possível e janela temporal. Preserve estado antes de qualquer reparo. Acione Jurídico/Privacidade conforme obrigação e política.
6. **Recuperar.** Escolha correção ou recuperação para um ponto no tempo (PITR) conforme o banco e o impacto. Restaure primeiro em ambiente isolado, compare alterações legítimas ocorridas após o ponto escolhido e aplique reconciliação controlada. Não faça rollback cego que descarte transações válidas.
7. **Validar e reabrir.** Corrija causa raiz, rotacione credenciais, reteste autorização, ferramenta e integridade dos dados. Reabilite gradualmente, com limites reduzidos e monitoramento reforçado; obtenha aprovação do Incident Commander e owner.
8. **Aprender.** Produza relatório de causa raiz, linha do tempo, impacto, decisões e ações preventivas. Abra POA&M com owner, prazo e evidência; atualize registro, matriz e checklist.

## Kill switch: requisitos

- Disponível fora do fluxo do agente, com autenticação forte, autorização restrita e trilha de auditoria.
- Escopo selecionável: agente, ferramenta, ambiente e, em emergência, todos os agentes com escrita.
- Efeito: negar novas chamadas privilegiadas, cancelar execuções quando suportado, revogar ou invalidar credenciais e manter leitura de diagnóstico apenas quando segura.
- Fail-closed: indisponibilidade do serviço de flag/política bloqueia a execução privilegiada; não reativa automaticamente após reinício.
- Testado periodicamente em exercício controlado, incluindo confirmação de que jobs já enfileirados também são contidos.

## Alucinação sem efeito confirmado

Pause a execução se houver risco, preserve entrada/saída e chamadas de ferramenta com minimização de dados, verifique efeitos reais nos sistemas de origem e marque a saída como não confiável. Corrija ou retire a resposta conforme o fluxo do produto; não trate uma resposta incorreta, isoladamente, como prova de incidente de dados. Se houve efeito colateral, siga o procedimento completo acima.

## Critérios de encerramento

- Escopo e impacto conhecidos ou limitações explicitadas e aprovadas.
- Credenciais afetadas revogadas e causa raiz corrigida ou risco residual aceito formalmente.
- Integridade/recuperação de dados validada por owner independente.
- Notificações regulatórias/contratuais avaliadas e executadas pelos responsáveis.
- Ações corretivas acompanhadas até reteste e evidência de fechamento.
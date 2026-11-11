# Drydock: auditoria offline e remediação

## Objetivo

Não foi identificado um repositório primário público para um **Drydock CLI de conformidade offline** que gere POA&M com base no NIST SP 800-53. A [busca de repositórios Drydock no GitHub](https://github.com/search?q=Drydock+NIST+800-53+POA%26M&type=repositories) retorna projetos homônimos, sem correspondência confirmada com esse escopo. O fluxo abaixo é um requisito de arquitetura proposto, não uma funcionalidade verificada de um produto. Não invente comandos: ao identificar o CLI correto, fixe a versão e consulte sua documentação antes de automatizá-lo.

O resultado pretendido é uma avaliação reproduzível, evidência local verificável e um plano de ação (POA&M) que relacione lacunas a controles selecionados do NIST SP 800-53. O relatório não prova conformidade por si só.

## Fluxo operacional

1. **Prepare:** obtenha binário e dependências por canal aprovado; valide assinatura/checksum e registre versão. Faça transferência para a rede isolada por processo controlado.
2. **Defina o escopo:** selecione baseline, perfil de sistema, controles aplicáveis e regras de tailoring aprovadas. Registre a edição do NIST e os parâmetros utilizados.
3. **Colete:** execute o assessment com permissões mínimas sobre configuração e evidências sanitizadas. Não inclua tokens, chaves, dados pessoais desnecessários ou conteúdo de produção sensível.
4. **Avalie:** revise achados e limitações; confirme falsos positivos e controles não testáveis manualmente. Preserve entrada, configuração e logs da execução.
5. **Gere POA&M:** para cada lacuna, registre controle, risco, causa, ação corretiva, responsável, recursos, marcos, prazo, evidência de fechamento e risco residual.
6. **Aprove e acompanhe:** proprietário do sistema aceita prioridades; segurança valida remediação com novo teste e referência à execução anterior.

## Campos mínimos do POA&M

| Campo | Descrição |
|---|---|
| ID / controle | Identificador da lacuna e controle NIST relacionado, incluindo baseline/edição. |
| Sistema e evidência | Ativo afetado e referência local rastreável, com classificação e integridade. |
| Risco e causa | Impacto, probabilidade, ameaça e razão da falha. |
| Ação / marco | Correção mensurável, dependências e marcos verificáveis. |
| Responsáveis | Dono técnico, aprovador de risco e equipe executora. |
| Datas / estado | Data de abertura, prazo, atualização e estado, com justificativa para atraso. |
| Fechamento | Teste repetido, evidência, revisor e risco residual aceito. |

## Integridade em ambiente air-gapped

- Armazene artefatos, logs, configuração e relatórios em repositório controlado com acesso mínimo.
- Gere hashes dos arquivos de entrada e saída; assine relatórios quando a infraestrutura suportar.
- Separe dados brutos de relatórios sanitizados e aplique retenção e classificação.
- Importe atualizações por fluxo aprovado, com verificação independente e trilha de custódia.
- Identifique claramente o que foi testado automaticamente, manualmente ou não foi avaliado.

## Critérios de aceite

- Outra pessoa consegue reproduzir a avaliação com a mesma versão, configuração e baseline.
- Cada achado tem responsável, prazo, evidência e critério de fechamento.
- O relatório diferencia “não aplicável”, “não testado” e “passou”.
- A remediação é retestada e o risco residual é formalmente aceito quando não corrigido.
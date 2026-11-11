# GSH: detecção e contenção no runtime

## Objetivo

Não foi identificado um repositório primário público para um framework de segurança de agentes chamado **GSH** com as capacidades de *Agentic Loop Detection* e detecção de *MCP Tool Poisoning* descritas no briefing. A [busca de repositórios GSH no GitHub](https://github.com/search?q=GSH+AI+agent+security&type=repositories) mostra homônimos sem relação confirmada. Portanto, esta página define requisitos para uma camada de detecção, não funcionalidades verificadas de um produto GSH. Se “GSH” designa um projeto específico, seu owner e repositório devem ser adicionados antes de atribuir-lhe estas capacidades.

Detecção complementa, mas não substitui, autorização preventiva. Uma chamada proibida deve ser bloqueada pelo enforcement determinístico mesmo se o detector estiver indisponível.

## Agentic Loop Detection

Um loop ocorre quando um agente repete chamadas, alterna ferramentas sem progresso, reabre tarefas concluídas ou produz uma sequência crescente de tentativas sem mudança de estado útil. Não confie apenas em uma heurística semântica do modelo.

Sinais práticos:

- Repetição da mesma ferramenta e argumentos normalizados dentro de uma janela.
- Repetição de erros ou resultados sem mudança observável de estado.
- Excesso de profundidade, iterações, duração, chamadas ou tokens por execução.
- Ciclos entre ferramentas detectados em um grafo de chamadas recente.
- Crescimento de custo ou taxa de tentativas fora do perfil histórico do agente.

Política de resposta: primeiro aplique limites duros por execução; em seguida encerre ou pause o grafo, preserve o identificador de correlação e emita evento de alerta. Use limiares por agente e tarefa, com teste contra falsos positivos. Não transforme detecção probabilística em permissão.

## MCP Tool Poisoning

Uma ferramenta MCP pode ser comprometida, substituída ou apresentar descrição/esquema malicioso que tente induzir o modelo a revelar dados, ampliar permissões ou executar ações fora da finalidade aprovada. Trate servidor, ferramenta, descrição e resposta como entradas não confiáveis.

Controles recomendados:

1. Mantenha catálogo aprovado de servidores e ferramentas, com proprietário, finalidade, versão e ambiente.
2. Fixe dependências e origens; verifique identidade do servidor e integridade de artefatos conforme a capacidade do protocolo/ambiente.
3. Compare nomes, descrições e schemas com a versão aprovada; mudanças exigem revisão e novo teste.
4. Autorize por ferramenta e operação no gateway, independentemente do texto descritivo recebido pelo modelo.
5. Valide argumentos e respostas com schemas; limite tamanho, tipos, destinos de rede e conteúdo sensível.
6. Não permita que instruções retornadas por ferramenta alterem política, identidade ou permissões do agente.
7. Registre versão/hash do catálogo, ferramenta selecionada, decisão, argumentos redigidos e resultado; não persista segredos.

## Eventos e resposta

Produza eventos estruturados para `loop_limit_exceeded`, `tool_catalog_mismatch`, `tool_denied`, `policy_unavailable` e `budget_exceeded`. Cada evento deve incluir horário, agente, execução, ferramenta, política/versão, decisão e trace ID. Redija dados pessoais e segredos antes da persistência.

Ao detectar possível comprometimento: bloqueie a ferramenta no gateway, interrompa execuções associadas, preserve evidências, revogue credenciais relevantes e siga o [runbook de incidentes](../security/incident-response.md). A retomada requer revisão da ferramenta, teste de regressão e aprovação do proprietário.
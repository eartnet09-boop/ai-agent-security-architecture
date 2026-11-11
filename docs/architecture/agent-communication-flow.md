# Fluxo de comunicação e autorização do agente

O modelo abaixo posiciona a política entre a intenção do modelo e qualquer efeito colateral. O agente propõe uma chamada; um serviço confiável autentica, autoriza, valida limites e executa. O modelo nunca decide sua própria permissão.

```mermaid
%%{init: {'theme':'base','themeVariables': {'primaryColor':'#183b56','primaryTextColor':'#ffffff','primaryBorderColor':'#62c6a5','lineColor':'#62c6a5','secondaryColor':'#263238','tertiaryColor':'#6d4c41','background':'#111827','actorBkg':'#183b56','actorTextColor':'#ffffff','actorBorder':'#62c6a5','signalColor':'#ffd166','signalTextColor':'#ffffff','labelBoxBkgColor':'#263238','labelTextColor':'#ffffff','loopTextColor':'#ffffff','noteBkgColor':'#6d4c41','noteTextColor':'#ffffff','activationBkgColor':'#14532d','activationBorderColor':'#86efac'}}}%%
sequenceDiagram
  autonumber
  actor U as Usuário
  participant FE as Frontend Next.js
  participant API as Backend tRPC
  participant ID as Provedor de identidade
  participant LG as Agente LangGraph
  participant GD as Guardrail GRITS/GSH
  participant POL as Serviço de política
  participant BUD as Orçamento / gateway de modelo
  participant LLM as Provedor LLM
  participant DB as Prisma / Neon
  participant OBS as Alertas e auditoria

  U->>FE: Envia solicitação
  FE->>API: Chamada autenticada + trace ID
  API->>ID: Verifica sessão/token e audiência
  ID-->>API: Principal verificado + claims
  API->>API: protectedProcedure valida identidade e contexto
  alt Identidade inválida ou escopo expirado
    API->>OBS: Registra tentativa negada (sem segredo)
    API-->>FE: 401/403; nenhuma execução iniciada
  else Identidade válida
    API->>LG: Inicia grafo com principal e contexto mínimo
    loop Enquanto houver próximo passo, sob limite de iterações
      LG->>GD: Solicita próximo passo do modelo
      GD->>POL: Verifica principal, modelo, limites e política vigente
      POL-->>GD: Decisão + escopo + versão da política
      alt Política permite a inferência
        GD->>BUD: Reserva teto de custo e tokens (max output)
        BUD-->>GD: Cota reservada ou negada
        alt Cota reservada
          GD->>LLM: Inferência limitada pelo teto reservado
          LLM-->>GD: Resposta + uso reportado pelo provedor
          GD->>BUD: Reconcilia uso e libera saldo não consumido
          GD-->>LG: Entrega resposta do modelo
          LG->>GD: Propõe ferramenta + argumentos estruturados
          GD->>GD: Valida schema, catálogo, destino e limites
          GD->>POL: Autoriza principal, ação, recurso e contexto
          POL-->>GD: Decisão + escopo + versão da política
          alt Ferramenta autorizada
            GD->>DB: Executa operação de domínio autorizada
            Note over GD,DB: Não entregar cliente Prisma genérico ao modelo
            DB-->>GD: Resultado mínimo necessário
            GD->>OBS: Registra decisão, ferramenta, versão e trace ID
            GD-->>LG: Retorna resultado validado e redigido
          else Ferramenta negada ou política indisponível
            GD->>OBS: Emite alerta e evento de bloqueio
            GD-->>LG: Interrompe chamada; nenhum efeito colateral
            LG-->>API: Execução encerrada com estado seguro
          end
        else Sem cota disponível
          GD->>OBS: Emite alerta de orçamento excedido
          GD-->>LG: Bloqueia inferência antes de chamar o provedor
          LG-->>API: Execução encerrada sem nova chamada ao modelo
        end
      else Política indisponível ou inferência negada
        GD->>OBS: Emite alerta e evento de bloqueio
        GD-->>LG: Bloqueia inferência
        LG-->>API: Execução encerrada com estado seguro
      end
    end
    API-->>FE: Resultado permitido ou resposta de bloqueio
  end
```

## Decisões de implementação

- `protectedProcedure` é uma fronteira de autenticação, não uma autorização completa. A camada de domínio deve verificar se aquele principal pode executar aquela ação sobre aquele recurso.
- Obtenha `agentId`, ambiente e escopos de claims assinadas ou de um registro confiável; não de campos livremente editáveis pelo usuário ou pelo LLM.
- O guardrail deve consultar uma matriz versionada e validar recurso/ação de forma determinística. Acesso a uma tabela específica é detalhe interno; prefira operações de domínio como `readOwnProject` a SQL ou acesso ORM arbitrário.
- A medição de tokens no provedor/modelo pode ser estimada antes da chamada e confirmada após a resposta. Para custo, aplique reserva, teto e reconciliação; não dependa de uma contagem declarada pelo modelo.
- Se identidade, política, catálogo ou serviço de orçamento não estiver disponível, negue ações com efeito colateral. Retornos de ferramentas são dados não confiáveis e não podem conceder novas permissões.
- Auditoria deve permitir reconstruir a decisão sem reter conteúdo sensível desnecessário: principal, ação, recurso, política/versão, decisão, execução, trace ID e timestamps.
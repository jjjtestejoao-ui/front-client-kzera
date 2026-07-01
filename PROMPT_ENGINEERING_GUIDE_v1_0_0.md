# Guia de Engenharia de Prompt

Este guia define como escrever prompts eficazes para trabalhar com Claude Code (ou qualquer assistente de IA) neste projeto. Ele reúne as práticas essenciais de um engenheiro de prompt: instruções claras, uso de Markdown, domínio de git, compreensão da estrutura do projeto, capacidade de explicar o resultado esperado, ciclo de teste/ajuste e consulta constante à documentação.

## 1. Escreva instruções claras e sem ambiguidade

- Diga **o quê**, **onde** e **por quê** — não deixe a IA adivinhar o contexto.
- Prefira frases objetivas a descrições vagas. Troque "melhore este componente" por "extraia a lógica de validação do formulário `LoginForm.tsx` para um hook `useLoginValidation`".
- Especifique restrições explícitas: linguagem, framework, estilo de código, o que **não** deve ser alterado.
- Quando o pedido tiver múltiplas etapas, numere-as. Isso reduz a chance de a IA pular um passo.

**Ruim:** "Ajusta o botão."
**Bom:** "No componente `Button.tsx`, aumente o `padding` horizontal para 16px e adicione um estado de `loading` que desabilita o clique e mostra um spinner."

## 2. Use Markdown básico para estruturar o pedido

Markdown ajuda tanto humanos quanto a IA a interpretar prioridade e hierarquia:

- `#`, `##`, `###` para títulos e seções.
- Listas (`-`, `1.`) para passos ou requisitos.
- Blocos de código (` ``` `) para trechos de código, comandos de terminal ou saídas de erro.
- **Negrito** para destacar o que é crítico; `código inline` para nomes de arquivos, variáveis e comandos.

Exemplo de pedido bem formatado:

```md
## Objetivo
Corrigir o bug de duplicidade de pedidos no checkout.

## Passos
1. Reproduzir o erro em `src/checkout/OrderService.ts`
2. Identificar a causa raiz
3. Corrigir e adicionar teste de regressão

## Critério de aceite
- Nenhum pedido duplicado ao clicar duas vezes em "Finalizar compra"
```

## 3. Entenda git (branch, commit, push)

Todo trabalho deve seguir um fluxo previsível:

- **Branch**: crie uma branch descritiva por tarefa (ex.: `feature/checkout-loading-state`, `fix/order-duplication`). Nunca trabalhe direto na branch principal.
- **Commit**: mensagens curtas, no imperativo, explicando o *porquê* da mudança, não apenas o *o quê*.
  ```
  git commit -m "Corrige duplicidade de pedido causada por duplo clique no checkout"
  ```
- **Push**: envie a branch com `git push -u origin <nome-da-branch>` e abra um Pull Request quando a tarefa estiver completa e testada.
- Nunca force push, nunca reescreva histórico compartilhado, e nunca use `--no-verify` sem justificativa explícita.

## 4. Conheça a estrutura do projeto/código

Antes de pedir uma mudança (ou de implementá-la), mapeie:

- Onde vivem os componentes, serviços, testes e configurações.
- Quais convenções de nomenclatura e organização já existem no repositório.
- Quais dependências e frameworks estão em uso (verifique `package.json`, arquivos de configuração, `CLAUDE.md` se existir).

Um bom prompt referencia caminhos reais: `src/components/Button.tsx:42`, e não descrições genéricas como "o botão do site".

## 5. Explique claramente o resultado esperado

Todo pedido de implementação deve deixar explícito:

- **Resultado final observável**: o que deve funcionar, aparecer ou mudar de comportamento.
- **Critérios de aceite**: como validar que a tarefa foi concluída (teste manual, teste automatizado, captura de tela, etc.).
- **Escopo**: o que fica de fora deliberadamente, para evitar que a IA "resolva" problemas não solicitados.

## 6. Teste, veja o que deu errado, ajuste

Engenharia de prompt é iterativa:

1. Rode o código/funcionalidade gerada.
2. Observe divergências entre o esperado e o resultado real.
3. Refine o prompt apontando exatamente o que falhou (mensagem de erro, comportamento incorreto, trecho de código problemático).
4. Repita até o critério de aceite ser atendido.

Evite reformular o pedido do zero a cada erro — aponte a falha específica e peça o ajuste incremental.

## 7. Leia a documentação do Claude Code

Antes de assumir limitações ou comportamentos, consulte a documentação oficial:

- Recursos, hooks, slash commands e configuração: https://code.claude.com/docs
- Use o comando `/help` dentro do Claude Code para ajuda contextual.
- Para reportar problemas ou dar feedback sobre a ferramenta: https://github.com/anthropics/claude-code/issues

---

### Checklist rápido antes de enviar um prompt

- [ ] O pedido é específico e sem ambiguidade?
- [ ] Usei Markdown para estruturar passos, código e critérios?
- [ ] Referenciei arquivos/caminhos reais do projeto?
- [ ] Deixei claro o resultado esperado e o critério de aceite?
- [ ] Vou testar o resultado antes de considerar a tarefa concluída?
- [ ] O fluxo de git (branch → commit → push) está definido?

---

## Prompt de persona — v1.0.0

Prompt validado por bateria de 10 testes (um por regra), sem gaps abertos.

```md
## Objetivo
Você é um assistente de engenharia de software que atua como engenheiro de prompt e desenvolvedor: direto, verifica antes de declarar algo pronto, e não assume intenção de negócio sem perguntar.

## Contexto
- Trabalha em repositórios de código reais (lê estrutura, git, arquivos de config antes de agir)
- Comunicação em português quando o usuário escrever em português
- Sem persona decorativa — comportamento, não personagem

## Regras de comportamento (obrigatórias)
1. Responda perguntas simples em 1-3 frases, sem preâmbulo
2. Dê a resposta primeiro, a justificativa depois — só se necessário
3. Antes de montar qualquer prompt ou executar uma tarefa, avalie se objetivo, contexto e critério de aceite já estão claros no pedido.
   - Se sim, siga direto — não investigue por formalidade.
   - Se algo essencial estiver faltando ou ambíguo, pergunte ao solicitante antes de produzir qualquer resultado.
4. Antes de declarar uma tarefa concluída, teste/rode/verifique — nunca declare "pronto" só por ter escrito o código
5. Nunca use "depende de vários fatores" quando é possível recomendar algo direto
6. Corte frases de transição e ressalvas desnecessárias
7. Antes de citar ou agir sobre qualquer arquivo, função ou recurso específico, verificar sua existência real (ls, grep ou leitura direta). Se não for possível verificar, marcar explicitamente como "cenário hipotético" — nunca apresentar como real sem confirmar.
8. Se o texto recebido tiver trechos incoerentes, repetidos ou fora de contexto (sinal comum de erro de transcrição de áudio), não tratar como pedido literal. Reformular o que foi entendido e confirmar com o solicitante antes de agir.
9. Antes de executar ação destrutiva ou irreversível (apagar, sobrescrever, force push, etc.), mesmo com objetivo/contexto/critério claros, confirmar com o solicitante antes de agir.
10. Se não houver como testar/verificar o resultado antes de entregar, informar isso explicitamente — nunca declarar como testado/concluído sem ter verificado de fato.

## Escopo
- Aplica-se a qualquer tarefa de código, revisão ou resposta técnica
- Não aplica a tom emocional/empático fora do necessário — objetividade tem prioridade sobre gentileza redundante

## Critério de aceite
- Nenhuma resposta a uma pergunta simples passa de 3 frases sem necessidade
- Toda entrega de código passa por alguma forma de verificação antes de ser chamada de concluída
- Nenhuma tarefa clara é bloqueada por perguntas desnecessárias; nenhuma tarefa ambígua é executada sem perguntar antes
- Nenhum arquivo/recurso é citado como real sem verificação prévia
- Ação destrutiva/irreversível nunca ocorre sem confirmação prévia
- Zero rodeio, zero repetição do que já foi perguntado

## Formato de saída
- Texto direto ou código, conforme o pedido
- Sem introdução tipo "vamos entender melhor" antes da resposta
```

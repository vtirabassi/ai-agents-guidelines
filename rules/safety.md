# Rules - Safety

## Objetivo
Definir limites para execucao segura, com reducao de risco operacional e previsibilidade de impacto.

## Obrigatorio
- Sinalizar risco e impacto antes de qualquer mudanca relevante.
- Pedir contexto adicional quando faltar informacao critica para decisao segura.
- Preferir alternativas conservadoras quando houver incerteza tecnica.
- Explicitar pre-condicoes de execucao para comandos sensiveis.

## Proibido
- Executar acao destrutiva sem confirmacao explicita.
- Sugerir procedimento inseguro sem alerta de risco.
- Prosseguir em atividade de alto risco sem plano de rollback.

## Excecoes
- Comandos destrutivos podem ser executados somente com:
  1. confirmacao explicita do solicitante,
  2. justificativa tecnica,
  3. estrategia de rollback e validacao pos-acao.

## Checklist de conformidade
- O risco foi classificado (baixo, medio, alto)?
- O impacto potencial foi comunicado antes da acao?
- Existe confirmacao explicita para acao destrutiva?
- Existe rollback definido e criterio de sucesso?

## Exemplo (aceito)
- "`rm -rf` somente apos confirmacao explicita; rollback via restauracao de backup X."

## Exemplo (nao aceito)
- "Rodar limpeza total agora" sem confirmacao e sem rollback.

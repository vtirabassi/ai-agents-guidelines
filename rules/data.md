# Rules - Data

## Objetivo
Definir uso seguro de dados com minimizacao de exposicao e tratamento padrao de informacao sensivel.

## Obrigatorio
- Tratar dados do usuario como sensiveis por padrao.
- Reduzir coleta, log e compartilhamento ao minimo necessario.
- Anonimizar PII antes de compartilhar logs, exemplos ou analises.
- Usar dados sinteticos em demos e exemplos sempre que possivel.

## Proibido
- Copiar ou expor credenciais, tokens, chaves e segredos.
- Compartilhar dados identificaveis sem necessidade tecnica.
- Mover dados para fora do repositorio sem autorizacao explicita.

## Excecoes
- Exposicao parcial de dado pode ocorrer apenas quando:
  1. indispensavel para debug,
  2. minimizada,
  3. com aprovacao explicita,
  4. com mascaramento aplicado sempre que possivel.

## Checklist de conformidade
- O dado exibido e estritamente necessario?
- PII foi removida ou mascarada?
- O exemplo pode ser substituido por dado sintetico?
- Existe autorizacao para qualquer transferencia externa?

## Exemplo (aceito)
- Compartilhar log com email mascarado (`u***@dominio.com`) e token removido.

## Exemplo (nao aceito)
- Colar trace completo com CPF, email e token em canal aberto.

# Chatbot Telegram + OpenWeather (n8n)

Workflow de chatbot no Telegram que consulta temperatura atual via OpenWeather.
O usuario envia uma cidade (ex.: `Cuiaba,MT`) e recebe a resposta no proprio Telegram.

## Entrega

- `workflow-chatbot-telegram.json`: workflow exportado do n8n.
- `README.md`: descricao do projeto, importacao, credenciais e execucao.

## Variaveis esperadas

- `OPENWEATHER_API_KEY`
- `TELEGRAM_BOT_TOKEN`

## Como importar o workflow no n8n

1. Abra o n8n.
2. Va em `Workflows` > `Import from File`.
3. Selecione `workflow-chatbot-telegram.json`.
4. Salve o workflow.

## Como inserir credenciais no n8n

### Telegram

1. Garanta que `TELEGRAM_BOT_TOKEN` esta disponivel no ambiente do n8n.
2. Crie uma credencial do tipo `Telegram API`.
3. No campo de token, use a expressao `{{$env.TELEGRAM_BOT_TOKEN}}`.
4. Associe a credencial aos nos:
   - `Telegram Trigger`
   - `Telegram Send (Success)`
   - `Telegram Send (Error)`
5. Para o trigger funcionar, o n8n precisa estar exposto em HTTPS publico (nao `localhost`).

### OpenWeather

1. Garanta que `OPENWEATHER_API_KEY` esta disponivel no ambiente do n8n.
2. O no `OpenWeather HTTP` ja usa `{{$env.OPENWEATHER_API_KEY}}` no parametro `appid`.

## Exemplo de ambiente (.env)

```env
OPENWEATHER_API_KEY=COLE_SUA_CHAVE_AQUI
TELEGRAM_BOT_TOKEN=COLE_SEU_TOKEN_AQUI
```

## Como executar o chatbot

1. Ative o workflow no n8n.
2. Envie mensagens para o bot no Telegram, por exemplo:
   - `Sao Paulo,SP`
   - `Rio de Janeiro,RJ`
   - `Curitiba,PR`
3. Retorno esperado (sucesso): temperatura atual da cidade.
4. Teste de erro: envie uma cidade invalida, por exemplo `CidadeInexistente,ZZ`.
5. Retorno esperado (erro): mensagem informando que a cidade nao foi encontrada.

## Comportamento do workflow

1. `Telegram Trigger` recebe mensagem de texto.
2. `Set (queue + chat_id)` prepara `queue` e `chat_id`.
3. `OpenWeather HTTP` consulta `https://api.openweathermap.org/data/2.5/weather`.
4. `IF (OK?)` valida resposta (`cod == 200`).
5. Fluxo de sucesso envia temperatura; fluxo de erro envia mensagem amigavel.

## Seguranca

- O `workflow-chatbot-telegram.json` foi sanitizado e nao possui segredos reais.
- Nao commitar `.env` com valores reais.

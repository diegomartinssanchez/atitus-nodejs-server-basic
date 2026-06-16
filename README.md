# nodejs-newrelic

Servidor básico em Node.js com Express para servir como base de um tutorial com New Relic.

## Objetivo desta base

Este repositório foi preparado para mostrar o fluxo mínimo de um serviço Node.js com Express e, em uma próxima etapa, receber a instrumentação do New Relic.

Nesta versão, não há integração do agente no código. O foco desta documentação é explicar como configurar o New Relic e como as métricas seriam enviadas quando a instrumentação for adicionada.

## Requisitos

- Node.js 18 ou superior
- npm

## Instalação

```bash
npm install
```

## Executar

```bash
npm start
```

Por padrão, o servidor sobe em `http://localhost:3000`. Se quiser usar outra porta, defina a variável `PORT`:

```bash
PORT=4000 npm start
```

## Endpoints

- `GET /` - resposta simples para confirmar que o servidor está no ar
- `GET /health` - retorno de health check em JSON

## Teste rápido

```bash
curl http://localhost:3000/
curl http://localhost:3000/health
```

## Configurar o New Relic

Para instrumentar uma aplicação Node.js com New Relic, o fluxo comum é este:

1. Criar uma conta no New Relic e obter a sua licença.
2. Instalar o agente oficial do Node.js.
3. Configurar o nome da aplicação e a chave de licença.
4. Iniciar a aplicação com o agente carregado antes do servidor.

### 1. Instalar o agente

```bash
npm install newrelic
```

### 2. Criar o arquivo de configuração

No projeto real, o agente usa um arquivo de configuração chamado `newrelic.js` na raiz do repositório. Esse arquivo normalmente contém o nome da aplicação, a licença e outras opções de coleta.

Exemplo de campos importantes:

```javascript
exports.config = {
	app_name: ['nodejs-newrelic'],
	license_key: 'SUA_LICENSE_KEY_AQUI',
	logging: {
		level: 'info'
	}
};
```

### 3. Configurar variáveis de ambiente

Você também pode fornecer parte da configuração por variáveis de ambiente.

```bash
export NEW_RELIC_APP_NAME="nodejs-newrelic"
export NEW_RELIC_LICENSE_KEY="SUA_LICENSE_KEY_AQUI"
```

### 4. Carregar o agente antes da aplicação

Quando a instrumentação for adicionada ao servidor, o agente precisa ser carregado antes de qualquer outro código da aplicação. O padrão é deixar o require do New Relic como a primeira linha do arquivo de entrada principal.

Exemplo conceitual:

```javascript
require('newrelic');
const express = require('express');
```

## Como as métricas são enviadas

O agente do New Relic envia automaticamente várias métricas da aplicação, como:

- tempo de resposta dos endpoints
- taxa de erro
- uso de CPU e memória em nível de aplicação
- chamadas externas e banco de dados, quando instrumentadas

Além disso, você pode publicar métricas personalizadas quando quiser acompanhar um comportamento específico do sistema.

### Métrica personalizada

Exemplo conceitual de envio de métrica personalizada:

```javascript
const newrelic = require('newrelic');

newrelic.recordMetric('Custom/HealthCheck', 1);
```

### Evento personalizado

Em cenários mais avançados, você pode enviar eventos personalizados:

```javascript
const newrelic = require('newrelic');

newrelic.recordCustomEvent('AppEvent', {
	endpoint: '/health',
	status: 'ok'
});
```

## Fluxo recomendado para este repositório

Quando a instrumentação for implementada, o fluxo esperado neste projeto será:

1. Inicializar o agente do New Relic no arquivo de entrada do servidor.
2. Subir a aplicação com `npm start`.
3. Acessar os endpoints `/` e `/health`.
4. Conferir no painel do New Relic se as métricas e transações apareceram.

## Observação

Esta documentação não altera o comportamento atual do servidor. Ela serve como guia para a próxima etapa, quando a instrumentação do New Relic for adicionada ao código.
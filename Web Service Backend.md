
Server-side online processing of a visitor good.
## Technologies

- Node.js
	- [express.js](https://expressjs.com/) as an HTTP/REST listener framework

## Principles

- [The Twelve-Factor App](https://12factor.net/)
	- one codebase tracked in revision control, many deploys
	- explicitly declare and isolate dependencies
	- store config in the environment
	- treat backing services as attached resources
	- strictly separate build and run stages
	- execute the app as one or more stateless processes
	- export services via port binding
	- scale out via the process model
	- maximize robustness with fast startup and graceful shutdown
	- keep development, staging, and production as similar as possible
	- treat logs as event streams
	- run admin/management tasks as one-off processes

## Killer features

- updated RSS-feed (Russian: обновляемая RSS-лента)
- support administrative and database connections via AF_UNIX

### AF_UNIX

From https://habr.com/ru/companies/yandex/articles/488588/#comment_21303466:

> Ну уж называть AF_UNIX костылем… Имхо, сейчас это очень недооцененная технология. По факту он: 
> 
> 1. Производительнее любого другого стека, так как ни при каких случаях не покидает пределов хоста.  
> 2. Безопаснее, так как к нему невозможно получить доступ извне. Ни прослушать, ни взломать вас через него не смогут.  
> 3. Имеет встроенное разграничение доступа на уровне fs. Мало иметь доступ к системе, чтобы получить доступ к сокету нужно еще и исполнять код от имени пользователя/группы, которым этот доступ разрешен.  
>
> Для любого взаимодействия внутри машины AF_UNIX будет предпочтительнее, чем AF_INET6/AF_INET. Это просто протоколы с разными целями.

## Node.js examples

Responsibility distribution in `index.js`:

```javascript
/* index.js - a start module of a REST API server
 *
 * Copyright (c) 2021 Oleg Iarygin <oleg@arhadthedev.net>
 *
 * Distributed under the MIT software license; see the accompanying
 * file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
 */

'use strict';

function bind_rest_entrypoints(server) {
}

const express = require('express');

const server = express();
bind_rest_entrypoints(server);

server.listen(process.env.PORT);
```

Docker container configuration:

```Dockerfile
FROM node:16-slim
LABEL org.opencontainers.image.title="eshost-online"
LABEL org.opencontainers.image.description="Eshost-as-a-service; runs your code on various ES/JS engines and compares outputs"
LABEL org.opencontainers.image.authors="oleg@arhadthedev.net"
LABEL org.opencontainers.image.url="https://github.com/arhadthedev/eshost-online#readme"
LABEL org.opencontainers.image.source="https://github.com/arhadthedev/eshost-online"
LABEL org.opencontainers.image.licenses="MIT"
```

```Dockerfile
# Base
FROM heroku/nodejs

# Deployment of local code and assets
RUN npm install
COPY app /

# Third party workers for a backend
RUN ESVU_PATH=. npm install -g esvu && esvu --engines=all && npm uninstall -g esvu

# Runtime
ENTRYPOINT ['node', 'index.js']

backend_web/Dockerfile

# Base
FROM heroku/nodejs

# Deployment of local code and assets
RUN npm install
COPY app /

# Runtime
ENTRYPOINT ['npm', 'start']
```

Docker base container size:

- alpine: 2.69MB
- 16-alpine: 38.12MB
- 16.11-slim (Ubuntu-based): 60.72MB

Docker tips and tricks:
- `ENTRYPOINT` should be defined when using the container as an executable ([source](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact))


## .Net.Core aka [.NET 5 and newer](https://en.wikipedia.org/wiki/.NET)

Development:
- [# Walkthrough: Create an MSBuild project file from scratch](https://learn.microsoft.com/en-us/visualstudio/msbuild/walkthrough-creating-an-msbuild-project-file-from-scratch)
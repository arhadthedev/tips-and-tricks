
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
## Examples

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
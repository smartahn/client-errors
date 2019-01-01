# client-errors

Client (4xx) error classes including status codes and messages for each error in Node server.

## Installation

```sh
$ npm install client-errors
```

## Example

### Baic Usage

```js
const { UnauthorizedError } = require('client-errors');

throw new UnauthorizedError(); // default message: 'The user is not authorized'
throw new UnauthorizedError('you are a bad guy!'); // custom message: 'you are a bad guy'

```

### Usage of Sending Status Code

```js
const express = require('express');
const router = express.Router();
const { UnauthorizedError, BadRequestError } = require('client-errors');
const mongoose = require('mongoose');

router.put('/items/:id', updateItem);

function updateItem(req, res) {
    mongoose.model('Item').findById(req.params.id).then(item => {
        if (!item) throw new BadRequestError('item does not exist');
        if (item.ownedBy !== req.user.id) throw new UnauthorizedError('invalid access to this item');

        item.set(res.body);
        item.save().then(res.json);

    }).catch(err => {
    	let status, message;
        if (err.statusCode) {
        	status = err.statusCode;
        	message = err.message;
        } else {
        	status = 422;
        	message = err;
        }
        res.status(status).send({ message });
    });
}
```

### [MIT Licensed](LICENSE)
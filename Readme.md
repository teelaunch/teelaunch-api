
# Teelaunch API

Teelaunch is the only swag service provider ("SSP") for crowdfunding platforms & projects.


## Quick Start

**1.** Sign up for a free Teelaunch developer account.

**2.** Create a new app and receive your API key and secret.

**3.** Integrate with our API with your respective wrapper:

* Node (npm) <https://github.com/teelaunch/teelaunch-node>
* Ruby (gem) <https://github.com/teelaunch/teelaunch-ruby>
* Python (pip) <https://github.com/teelaunch/teelaunch-python>
* PHP (pear) <https://github.com/teelaunch/teelaunch-php>

**4.** Create a new project.

Request:

```bash
curl -u key:secret -H 'Content-Type: application/json' -X POST -d @project.json https://teelaunch.com/api/v1/projects
```

> Here's how your local `project.json` might look:

```json
{
  "name": "My Awesome Project",
  "url": "http://www.indiegogo.com/projects/my-awesome-project"
}
```

Response:

```json
{
  "project_id": "26988293",
  "name": "My Awesome Project",
  "url": "http://www.indiegogo.com/projects/my-awesome-project"
}
```

**5.** Create a new reward for the project.

Request:

```bash
curl -u key:secret -H 'Content-Type: application/json' -X POST -d @reward.json https://teelaunch.com/api/v1/rewards
```

> Here's how your local `reward.json` might look:

```json
{
  "project_id": "26988293",
  "type": "t-shirt",
  "description": "Official T-Shirt",
  "design": {
    "colors": 1,
    "url": "https://www.dropbox.com/s/c3x34pi4pumsw8p/teelaunch_prod_rev1.pdf"
  },
  "garment": {
    "brand": "American Apparel",
    "color": "Royal Blue"
  }
}
```

Response:

```json
{
  "reward_id": "15892039",
  "project_id": "26988293",
  "type": "t-shirt",
  "description": "Official T-Shirt",
  "design": {
    "colors": 1,
    "url": "https://www.dropbox.com/s/c3x34pi4pumsw8p/teelaunch_prod_rev1.pdf"
  },
  "garment": {
    "brand": "American Apparel",
    "color": "Royal Blue"
  },
  "options": {
    "size": "What size t-shirt do you wear?"
  }
}
```

**6.** Create a new order based on the reward (e.g. when the project is successfully crowdfunded).

Request:

```bash
curl -u key:secret -H 'Content-Type: application/json' -X POST -d @order.json https://teelaunch.com/api/v1/orders
```

> Here's how your local `order.json` might look:

```json
{
  "reward_id": "15892039",
  "quantity": 100,
  "fulfillment": false
}
```

Response (if we don't handle fulfillment):

```json
{
  "order_id": "32897340",
  "reward_id": "15892039",
  "status": "draft",
  "quantity": 100,
  "fulfillment": false,
  "price": 5.54,
  "tax": 0,
  "shipping": {
    "carrier": "UPS",
    "method": "Ground",
    "amount": 25
  },
  "discount": 0,
  "total": 579
}
```

Response (if we handle fulfillment):

```json
{
  "order_id": "32897340",
  "reward_id": "15892039",
  "status": "draft",
  "quantity": 100,
  "fulfillment": true,
  "price": 6.29,
  "tax": 0,
  "shipping": {
    "carrier": "Teelaunch",
    "method": "Fulfillment",
    "amount": 0
  },
  "discount": 0,
  "total": 654
}
```

> Note that `price` increased for fulfillment fee and `shipping.amount` will increase or decrease based off total postage incurred for recipient backers receiving the reward (see below).

**7.** Create backers (required for fulfillment).

Request:

```bash
curl -u key:secret -H 'Content-Type: application/json' -X POST -d @backers.json https://teelaunch.com/api/v1/backers
```

> Here's how your local `backers.json` might look for creating an individual backer:

```json
{
  "name": {
    "first": "Darth",
    "last": "Vader"
  },
  "email": "darth.vader@galacticempire.com",
  "address": {
    "street1": "1110 Gorgas Ave",
    "city": "San Francisco",
    "state": "CA",
    "zip": "94129"
  }
}
```

Response:

```json
{
  "backer_id": "51891230",
  "name": {
    "first": "Darth",
    "last": "Vader"
  },
  "email": "darth.vader@galacticempire.com",
  "address": {
    "street1": "1110 Gorgas Ave",
    "city": "San Francisco",
    "state": "CA",
    "zip": "94129"
  }
}
```

> Here's how your local `backers.json` might look for creating multiple backers:

```json
[
  {
    "name": {
      "first": "Luke",
      "last": "Skywalker"
    },
    "email": "luke.skywalker@rebelalliance.com",
    "address": {
      "street1": "1110 Gorgas Ave",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94129"
    }
  },
  {
    "name": {
      "first": "Han",
      "last": "Solo"
    },
    "email": "han.solo@rebelalliance.com",
    "address": {
      "street1": "1110 Gorgas Ave",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94129"
    }
  }
]
```

Response:

```json
[
  {
    "backer_id": "94309634",
    "name": {
      "first": "Luke",
      "last": "Skywalker"
    },
    "email": "luke.skywalker@rebelalliance.com",
    "address": {
      "street1": "1110 Gorgas Ave",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94129"
    }
  },
  {
    "backer_id": "10123015",
    "name": {
      "first": "Han",
      "last": "Solo"
    },
    "email": "han.solo@rebelalliance.com",
    "address": {
      "street1": "1110 Gorgas Ave",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94129"
    }
  }
]
```

**8.** Add recipients of backers to the order (required for fulfillment).

Request:

```bash
curl -u key:secret -H 'Content-Type: application/json' -X POST -d @recipients.json https://teelaunch.com/api/v1/recipients
```

> Here's how your local `recipients.json` might look for creating an individual recipient:

```json
{
  "order_id": "32897340",
  "backer_id": "51891230",
  "options": {
    "size": "M"
  },
  "survey": false
}
```

> If you don't provide all required `options` (e.g. `options.size`) and if `"survey": true`, then we'll survey the recipient by email.

Response:

```json
{
  "recipient_id": "58912385",
  "order_id": "32897340",
  "backer_id": "51891230",
  "options": {
    "size": "M"
  },
  "survey": false,
  "shipping": {
    "tracking_id": null,
    "status": "Draft",
    "carrier": "USPS",
    "method": "First Class",
    "amount": 2.07
  }
}
```

> Here's how your local `recipients.json` might look for creating multiple recipients:

```json
[
  {
    "order_id": "32897340",
    "backer_id": "94309634",
    "options": {
      "size": "M"
    },
    "survey": false
  },
  {
    "order_id": "32897340",
    "backer_id": "10123015",
    "options": {
      "size": "L"
    },
    "survey": false
  }
]
```

Response:

```json
[
  {
    "recipient_id": "18985912",
    "order_id": "32897340",
    "backer_id": "94309634",
    "options": {
      "size": "M"
    },
    "survey": false,
    "shipping": {
      "tracking_id": null,
      "status": "Draft",
      "carrier": "USPS",
      "method": "First Class",
      "amount": 2.07
    }
  },
  {
    "recipient_id": "23985729",
    "order_id": "32897340",
    "backer_id": "10123015",
    "options": {
      "size": "L"
    },
    "survey": false,
    "shipping": {
      "tracking_id": null,
      "status": "Draft",
      "carrier": "USPS",
      "method": "First Class",
      "amount": 2.31
    }
  }
]
```

**9.** Update order status to "ready" and receive an email confirmation for payment.

Request:

```bash
curl -u key:secret -H 'Content-Type: application/json' -X PUT -d @order-update.json https://teelaunch.com/api/v1/orders/32897340
```

> Here's how your local `order-update.json` might look:

```json
{
  "status": "ready"
}
```

Response:

```json
{
  "order_id": "32897340",
  "reward_id": "15892039",
  "status": "ready",
  "quantity": 100,
  "fulfillment": false,
  "price": 5.54,
  "tax": 0,
  "shipping": {
    "carrier": "UPS",
    "method": "Ground",
    "amount": 25
  },
  "discount": 0,
  "total": 579
}
```

**10.** Payment method charged, order shipped, and tracking notifications emailed to recipient(s).

> Sit back and relax mon'


## Resources <sup>v1</sup>

Prefix all paths with `/api/v1` (e.g. `/projects` becomes `/api/v1/projects`)

### Projects

<table>
  <thead>
    <tr>
      <th>Path</th>
      <th>Method</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/projects</td>
      <td>GET</td>
      <td>List projects</td>
    </tr>
    <tr>
      <td>/projects</td>
      <td>POST</td>
      <td>Create project</td>
    </tr>
    <tr>
      <td>/projects/:id</td>
      <td>GET</td>
      <td>Read project</td>
    </tr>
    <tr>
      <td>/projects/:id</td>
      <td>PUT</td>
      <td>Update project</td>
    </tr>
    <tr>
      <td>/projects/:id</td>
      <td>DELETE</td>
      <td>Delete project</td>
    </tr>
  </tbody>
</table>

### Rewards

<table>
  <thead>
    <tr>
      <th>Path</th>
      <th>Method</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/rewards</td>
      <td>GET</td>
      <td>List rewards</td>
    </tr>
    <tr>
      <td>/rewards</td>
      <td>POST</td>
      <td>Create reward</td>
    </tr>
    <tr>
      <td>/rewards/:id</td>
      <td>GET</td>
      <td>Read reward</td>
    </tr>
    <tr>
      <td>/rewards/:id</td>
      <td>PUT</td>
      <td>Update reward</td>
    </tr>
    <tr>
      <td>/rewards/:id</td>
      <td>DELETE</td>
      <td>Delete reward</td>
    </tr>
  </tbody>
</table>

### Orders

<table>
  <thead>
    <tr>
      <th>Path</th>
      <th>Method</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/orders</td>
      <td>GET</td>
      <td>List orders</td>
    </tr>
    <tr>
      <td>/orders</td>
      <td>POST</td>
      <td>Create order</td>
    </tr>
    <tr>
      <td>/orders/:id</td>
      <td>GET</td>
      <td>Read order</td>
    </tr>
    <tr>
      <td>/orders/:id</td>
      <td>PUT</td>
      <td>Update order</td>
    </tr>
    <tr>
      <td>/orders/:id</td>
      <td>DELETE</td>
      <td>Delete order</td>
    </tr>
  </tbody>
</table>

### Backers

<table>
  <thead>
    <tr>
      <th>Path</th>
      <th>Method</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/backers</td>
      <td>GET</td>
      <td>List backers</td>
    </tr>
    <tr>
      <td>/backers</td>
      <td>POST</td>
      <td>Create backer</td>
    </tr>
    <tr>
      <td>/backers/:id</td>
      <td>GET</td>
      <td>Read backer</td>
    </tr>
    <tr>
      <td>/backers/:id</td>
      <td>PUT</td>
      <td>Update backer</td>
    </tr>
    <tr>
      <td>/backers/:id</td>
      <td>DELETE</td>
      <td>Delete backer</td>
    </tr>
  </tbody>
</table>

### Recipients

<table>
  <thead>
    <tr>
      <th>Path</th>
      <th>Method</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/recipients</td>
      <td>GET</td>
      <td>List recipients</td>
    </tr>
    <tr>
      <td>/recipients</td>
      <td>POST</td>
      <td>Create recipient</td>
    </tr>
    <tr>
      <td>/recipients/:id</td>
      <td>GET</td>
      <td>Read recipient</td>
    </tr>
    <tr>
      <td>/recipients/:id</td>
      <td>PUT</td>
      <td>Update recipient</td>
    </tr>
    <tr>
      <td>/recipients/:id</td>
      <td>DELETE</td>
      <td>Delete recipient</td>
    </tr>
  </tbody>
</table>

### Pricing

<table>
  <thead>
    <tr>
      <th>Path</th>
      <th>Method</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/pricing/:type</td>
      <td>GET</td>
      <td>Read pricing</td>
    </tr>
  </tbody>
</table>

### Tracking

<table>
  <thead>
    <tr>
      <th>Path</th>
      <th>Method</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>/tracking/:id</td>
      <td>GET</td>
      <td>Read tracking</td>
    </tr>
  </tbody>
</table>


## Examples

Reference to the `/examples` folder in your language's API wrapper (see links above).

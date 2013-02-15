
# Teelaunch API

Teelaunch is the only swag service provider ("SSP") for crowdfunding platforms & projects.

**Active work in progress, open to feedback and suggestions**

## Index

* [Overview](#overview)
* [Support](#support)
* [Quick Start](#quick-start)
  * [Steps](#steps)
  * [Guide](#guide)
* [Resources](#resources)
  * [Projects](#projects)
  * [Rewards](#rewards)
  * [Backers](#backers)
  * [Items](#items)
  * [Products](#products)
  * [Verification](#verification)
  * [Packages](#packages)
  * [Tracking](#tracking)
  * [Rates](#rates)
  * [Labels](#labels)
* [Examples](#examples)

## Overview

**1.** Sign up for a free Teelaunch developer account.

**2.** Create a new app and receive your API key and secret.

**3.** Integrate our RESTful API with your DSL wrapper:

* Node (npm) <https://github.com/teelaunch/teelaunch-node>
* Ruby (gem) <https://github.com/teelaunch/teelaunch-ruby>
* Python (pip) <https://github.com/teelaunch/teelaunch-python>
* PHP (pear) <https://github.com/teelaunch/teelaunch-php>

See [Quick Start](#quick-start) for an example implementation.


## Support

Join us in `#teelaunch` on `irc.freenode.net`.  You can also email <support@teelaunch.com> or file an [Issue](/issues).


## Quick Start

### Steps

1. Create a new project
2. Create a new reward for the project
3. Create backer(s)
4. Add reward items to be delivered to backer(s)
5. Update reward status to "ready"

Here's what happens next...

* We'll review the reward after your approval and mark `reward.status` to "production".
* We fulfill your reward with a quick turnaround time of 5-10 days.
* Backers receive automated emails (e.g. "Your reward is on its way").

### Guide

**1.** Create a new project

Request:

```bash
curl https://teelaunch.com/api/v1/projects \
     -u key:secret \
     -X POST \
     -H "Content-Type: application/json" \
     -d @project.json
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
  "id": "26988293",
  "name": "My Awesome Project",
  "url": "http://www.indiegogo.com/projects/my-awesome-project",
  "created": "2013-02-14T22:14:44.820Z",
  "updated": "2013-02-14T22:14:44.820Z"
}
```

**2.** Create a new reward for the project.

Request:

```bash
curl https://teelaunch.com/api/v1/rewards \
     -u key:secret \
     -X POST \
     -H "Content-Type: application/json" \
     -d @reward.json
```

> Before you can prepare `reward.json`, you need to know what product you want to offer as a reward (e.g. an American Apparel t-shirt).  To do this, you need to query the [Products](#products) resource to determine the proper `product_id`.  In the example below, a `product_id` of "51236345" is for an American Apparel short-sleeved tee.  An easy way to integrate this on your front-end is to simply list all products in a drop-down menu with their respective `id` values.  Example given below...

```html
<label for="product-id">
  Select a Reward Type:
  <select name="product_id" id="product-id">
    <option value="51236345">American Apparel 4.3oz T-Shirt (#2001)</option>
    <option value="51236723">Gildan 5.3 oz T-Shirt (#5000)</option>
    <option value="15123124">Hanes 5.2 oz T-Shirt (#5280)</option>
  </select>
</label>
```

> Here's how your local `reward.json` might look after you know the `product_id`:

```json
{
  "project_id": "26988293",
  "product_id": "51236345",
  "description": "Official T-Shirt",
  "options": {
    "color": "Royal Blue",
    "artwork": {
      "url": "https://www.dropbox.com/s/c3x34pi4pumsw8p/teelaunch_prod_rev1.pdf",
      "num_colors": 1
    }
  }
}
```

> Note that `reward.product_id` can be found using using the [Products](#products) resource.

Response:

```json
{
  "id": "15892039",
  "project_id": "26988293",
  "product_id": "51236345",
  "status": "draft",
  "description": "Official T-Shirt",
  "options": {
    "color": "Royal Blue",
    "artwork": {
      "url": "https://www.dropbox.com/s/c3x34pi4pumsw8p/teelaunch_prod_rev1.pdf",
      "num_colors": 1
    }
  },
  "fields": [ "size", "address" ],
  "created": "2013-02-14T22:14:44.820Z",
  "updated": "2013-02-14T22:14:44.820Z"
}
```

> Note that you can't modify pre-populated (required) fields such as "size" and "address".  In the future we may allow you to create custom fields that would allow you to request answers to in email surveys.

**3.** Create backer(s)

Request:

```bash
curl https://teelaunch.com/api/v1/backers \
     -u key:secret \
     -X POST \
     -H "Content-Type: application/json" \
     -d @backer.json
```

> Here's how your local `backer.json` might look for creating an individual backer:

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
  "id": "51891230",
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
  },
  "created": "2013-02-14T22:14:44.820Z",
  "updated": "2013-02-14T22:14:44.820Z"
}
```

> Here's how your local `backer.json` might look for creating multiple backers:

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
    "id": "94309634",
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
    },
    "created": "2013-02-14T22:14:44.820Z",
    "updated": "2013-02-14T22:14:44.820Z"
  },
  {
    "id": "10123015",
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
    },
    "created": "2013-02-14T22:14:44.820Z",
    "updated": "2013-02-14T22:14:44.820Z"
  }
]
```

**4.** Add reward items to be delivered to backer(s)

> (e.g. with reward's product options, such as shirt size)

Request:

```bash
curl https://teelaunch.com/api/v1/items \
     -u key:secret \
     -X POST \
     -H "Content-Type: application/json" \
     -d @item.json
```

> Here's how your local `item.json` might look for creating an individual item:

```json
{
  "reward_id": "15892039",
  "backer_id": "51891230",
  "survey": false,
  "fields": {
    "size": "M"
  }
}
```

> Note that `item.fields` is an object requiring values for `reward.fields` specified above (e.g. "size" and "address").

> If you don't provide `item.fields.address` and the respective `backer.address` exists, then `item.options.address` will be pre-populated.  This is especially helpful for   Note that you can however pass a new `item.fields.address` or update the `backer.address` first.

> If you don't provide all required `item.options` (e.g. `item.options.size` or `item.options.address`) and if `item.survey` is `true`, then we'll survey the backer by email to get values for `item.options`.

Response:

```json
{
  "id": "58912385",
  "reward_id": "15892039",
  "backer_id": "51891230",
  "survey": false,
  "options": {
    "size": "M",
    "address": {
      "street1": "1110 Gorgas Ave",
      "city": "San Francisco",
      "state": "CA",
      "zip": "94129"
    }
  },
  "created": "2013-02-14T22:14:44.820Z",
  "updated": "2013-02-14T22:14:44.820Z"
}
```

> Here's how your local `item.json` might look for creating multiple line items:

```json
[
  {
    "reward_id": "15892039",
    "backer_id": "94309634",
    "survey": false,
    "options": {
      "size": "M"
    }
  },
  {
    "reward_id": "15892039",
    "backer_id": "10123015",
    "survey": false,
    "options": {
      "size": "L"
    }
  }
]
```

Response:

```json
[
  {
    "id": "18985912",
    "reward_id": "15892039",
    "backer_id": "94309634",
    "survey": false,
    "options": {
      "size": "M",
      "address": {
        "street1": "1110 Gorgas Ave",
        "city": "San Francisco",
        "state": "CA",
        "zip": "94129"
      }
    },
    "created": "2013-02-14T22:14:44.820Z",
    "updated": "2013-02-14T22:14:44.820Z"
  },
  {
    "id": "23985729",
    "reward_id": "15892039",
    "backer_id": "10123015",
    "survey": false,
    "options": {
      "size": "L",
      "address": {
        "street1": "1110 Gorgas Ave",
        "city": "San Francisco",
        "state": "CA",
        "zip": "94129"
      }
    },
    "created": "2013-02-14T22:14:44.820Z",
    "updated": "2013-02-14T22:14:44.820Z"
  }
]
```

**5.** Update reward status to "ready"

> You will receive an email confirmation for approval within 24 hours.  It will also contain an attachment of the product proof (e.g. JPG/PDF).

Request:

```bash
curl https://teelaunch.com/api/v1/rewards/15892039 \
     -u key:secret \
     -X PUT \
     -d "{\n  \"status\": \"ready\"\n}"
```

Response:

```json
{
  "id": "15892039",
  "project_id": "26988293",
  "product_id": "51236345",
  "status": "ready",
  "description": "Official T-Shirt",
  "options": {
    "color": "Royal Blue",
    "artwork": {
      "url": "https://www.dropbox.com/s/c3x34pi4pumsw8p/teelaunch_prod_rev1.pdf",
      "num_colors": 1
    }
  },
  "fields": [ "size", "address" ],
  "created": "2013-02-14T22:14:44.820Z",
  "updated": "2013-02-14T22:18:00.820Z"
}
```


## Resources <sup>v1</sup>

Prefix all paths with `/api/v1` (e.g. `/projects` becomes `/api/v1/projects`)

### Projects

| Path          | Method | Description    |
| ------------- |:------:| -------------- |
| /projects     | GET    | List projects  |
| /projects     | POST   | Create project |
| /projects/:id | GET    | Read project   |
| /projects/:id | PUT    | Update project |
| /projects/:id | DELETE | Delete project |

#### List projects

TODO

#### Create project

TODO

#### Read project

TODO

#### Update project

TODO

#### Delete project

TODO

### Rewards

| Path         | Method | Description   |
| ------------ |:------:| ------------- |
| /rewards     | GET    | List rewards  |
| /rewards     | POST   | Create reward |
| /rewards/:id | GET    | Read reward   |
| /rewards/:id | PUT    | Update reward |
| /rewards/:id | DELETE | Delete reward |

#### List rewards

TODO

#### Create reward

TODO

#### Read reward

TODO

#### Update reward

TODO

#### Delete reward

TODO

### Backers

| Path         | Method | Description   |
| ------------ |:------:| ------------- |
| /backers     | GET    | List backers  |
| /backers     | POST   | Create backer |
| /backers/:id | GET    | Read backer   |
| /backers/:id | PUT    | Update backer |
| /backers/:id | DELETE | Delete backer |

#### List backers

TODO

#### Create backer

TODO

#### Read backer

TODO

#### Update backer

TODO

#### Delete backer

TODO

### Items

| Path       | Method | Description |
| ---------- |:------:| ----------- |
| /items     | GET    | List items  |
| /items     | POST   | Create item |
| /items/:id | GET    | Read item   |
| /items/:id | PUT    | Update item |
| /items/:id | DELETE | Delete item |

#### List items

TODO

#### Create item

TODO

#### Read item

TODO

#### Update item

TODO

#### Delete item

TODO

### Products

| Path          | Method | Description                 |
| ------------- |:------:| --------------------------- |
| /products     | GET    | List products               |
| /products/:id | GET    | Read product                |

#### List products

TODO

#### Read product

TODO

### Verification

| Path          | Method  | Description       |
| ------------- |:-------:| ----------------- |
| /verification | GET     | Read verification |

#### Read verification

TODO

### Tracking

| Path          | Method | Description   |
| ------------- |:------:| ------------- |
| /tracking/:id | GET    | Read tracking |

#### Read tracking

TODO

> If you need to track a package with `label.tracking_code` (this field is populated for an item upon shipment)...

Request:

```bash
curl https://teelaunch.com/api/v1/tracking/32897340 \
     -u key:secret
```

Response:

```json
```

### Rates

| Path       | Method | Description |
| ---------- |:------:| ----------- |
| /rates/:id | GET    | Read rates  |

#### Read Rates

Request:

```bash
curl https://teelaunch.com/api/v1/postage/rates \
     -u key:secret \
     -d 'to=94129' \
     -d 'from=27601' \
     -d 'length=9' \
     -d 'width=9' \
     -d 'height=1' \
     -d 'weight=7.4'
```

Response:

```json
{
  "rates": [
    {
      "carrier": "USPS",
      "rate": "2.51",
      "service": "First"
    },
    {
      "carrier": "USPS",
      "rate": "6.84",
      "service": "Priority"
    },
    {
      "carrier": "USPS",
      "rate": "29.06",
      "service": "Express"
    },
    {
      "carrier": "USPS",
      "rate": "2.52",
      "service": "LibraryMail"
    },
    {
      "carrier": "USPS",
      "rate": "2.66",
      "service": "MediaMail"
    },
    {
      "carrier": "USPS",
      "rate": "6.07",
      "service": "ParcelSelect"
    },
    {
      "carrier": "USPS",
      "rate": "7.19",
      "service": "StandardPost"
    },
    {
      "carrier": "USPS",
      "rate": "4.73",
      "service": "CriticalMail"
    }
  ]
}
```

### Labels

| Path        | Method | Description  |
| ----------- |:------:| ------------ |
| /labels     | GET    | List labels  |
| /labels     | POST   | Create label |
| /labels/:id | GET    | Read label   |

#### List labels

TODO

#### Create label

Request:

```bash
curl https://teelaunch.com/api/v1/labels \
     -u key:secret \
     -X POST \
     -H "Content-Type: application/json" \
     -d @label.json
```

> Here's how your local `label.json` might look:

```json
{
  "to": {
    "name": {
      "first": "Ben",
      "last": "Kenobi"
    },
    "street1": "1110 Gorgas Ave",
    "city": "San Francisco",
    "state": "CA",
    "zip": "94129",
  },
  "from": {
    "name": {
      "first": "C3PO",
      "last": "R2D2"
    },
    "street1": "1110 Gorgas Ave",
    "city": "San Francisco",
    "state": "CA",
    "zip": "94129"
  },
  "length": 9,
  "width": 12,
  "height": 1,
  "weight": 7:
  "carrier": "USPS",
  "method": "First Class"
}
```

Response:

TODO

```json
```

#### Read label

TODO


## Examples

Reference to the `/examples` folder in your language's API wrapper (see links above).

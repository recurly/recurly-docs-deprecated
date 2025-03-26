---
title: Quick-start guide
excerpt: >-
  A short, simple guide to quickly authenticate with Recurly, choose a client
  library, and create a plan for billing customers.
deprecated: false
hidden: false
metadata:
  robots: index
---
#### Metadata Description

<br />

# Overview

This Quick Start Guide walks you through setting up your Recurly API integration in five steps:

<Cards columns={3}>
  <Card title="Step 1: Obtain your private API key" icon="fa-key">
    Generate your private API key in Recurly’s Admin Dashboard under <em>Integrations > API Keys</em>. You’ll use this key to authenticate all API requests.
  </Card>

  <Card title="Step 2: Choose a client library" icon="fa-code">
    Recurly provides official client libraries for Ruby, Node.js, Python, Java, C#, and PHP. Pick one to simplify development and ensure compatibility.
  </Card>

  <Card title="Step 3: Create a client instance" icon="fa-cog">
    In your chosen language, create a new <strong>Client</strong> object using your private API key. All API calls go through this client instance.
  </Card>

  <Card title="Step 4: Define a plan" icon="fa-file-alt">
    Use the <strong>createPlan</strong> method to build subscription plans. Configure details like currency, price, and billing frequency.
  </Card>

  <Card title="Step 5: Verify in Recurly Admin" icon="fa-check">
    Log in to the Recurly Admin UI to confirm your plan and test that all steps have been completed successfully.
  </Card>
</Cards>

### Prerequisites & limitations

* A valid Recurly account with API access
* Basic familiarity with RESTful APIs and JSON
* Access to a compatible programming environment (Ruby, Node.js, Python, Java, C#, or PHP)

***

# Definition

**Quick Start Guide**: A high-level tutorial for connecting to Recurly’s API, authenticating with a private API key, and creating an initial subscription plan. This is the foundation for more advanced tasks like account creation, subscription management, and invoicing.

## Step 1: Authentication keys

Recurly authenticates your API requests using your private API key. You can generate and retrieve private API keys in the [Recurly Admin Dashboard](https://app.recurly.com/go/integrations/api_keys).

## Step 2: Choose a client library

Recurly offers a number of client libraries to integrate to the API. We suggest using\
one of these official clients as it will make support, onboarding, and security much easier.

Check out the [Ruby API docs](https://www.rubydoc.info/github/recurly/recurly-client-ruby/),\
or see the source on [GitHub](https://github.com/recurly/recurly-client-ruby).

**Add to your`Gemfile`**:

```ruby
gem 'recurly', '~> 4.0'
```

**Or install from the command line**:

```ruby
gem install recurly
```

Check out the [Js API docs](https://recurly.github.io/recurly-client-node/),\
or see the source on [GitHub](https://github.com/recurly/recurly-client-node).

**Install via[npm](https://www.npmjs.com/package/recurly):**

```
npm install recurly --save
```

**Or add directly to dependencies in`package.json`**:

```json
{
  "recurly": "^4.0.0"
}
```

Check out the [Python API docs](https://recurly-client-python.readthedocs.io/en/latest/),\
or see the source on [GitHub](https://github.com/recurly/recurly-client-python/tree/4_0_0).

**Add to your requirements.txt**:

```
recurly~=4.0
```

**Or install via pip**

```
pip install --upgrade recurly
```

See the source on [GitHub](https://github.com/recurly/recurly-client-java)\
and release details on [Maven Central](https://search.maven.org/artifact/com.recurly.v3/api-client/4.4.0/jar).

**As a Maven dependency:**

```xml
<dependency>
  <groupId>com.recurly.v3</groupId>
  <artifactId>api-client</artifactId>
  <version>4.0.0</version>
</dependency>
```

**In Gradle:**

```
implementation 'com.recurly.v3:api-client:4.4.0'
```

See the source on [GitHub](https://github.com/recurly/recurly-client-dotnet).

**Install via the`dotnet` tool:**

```
dotnet add package Recurly --version 4.*
```

**Or manually insert into your`.csproj` file**:

```xml
<ItemGroup>
  <PackageReference Include="Recurly" Version="4.*" />
  <!-- ... -->
</ItemGroup>
```

See the source on [GitHub](https://github.com/recurly/recurly-client-php).

**Install via the`composer` tool**:

```
composer install recurly/recurly-client
```

**Or manually add to your`composer.json`**:

```json
{
    "require": {
        "recurly/recurly-client": "^4"
    }
}

```

## Step 3: Create a client instance

Your integration starts by creating an instance of the Client class. The client\
represents a connection to Recurly's API. Every operation in the API exists
as a method on this object. Creating a client only requires the private API key.

```ruby
require 'recurly'
@client = Recurly::Client.new(
  api_key: '<your private api key>'
)
```
```js
const recurly = require('recurly')
const client = new recurly.Client('<your private api key>')
```
```python
import recurly
client = recurly.Client('<your private api key>')
```
```java
// Put this import on the top of your file
import com.recurly.v3.Client;

final Client client = new Client("<your private api key>");
```
```csharp
// Add this on the top of your file
using Recurly;

var client = new Recurly.Client("<your private api key>");
```
```php
$client = new \Recurly\Client("<your private api key>");
```

## Step 4: Create a plan

A plan tells Recurly how often and how much to charge your customers.\
Plans can be created with free trials, optional products (called add-ons), setup fees, and more.

Plans are typically created in the [admin interface](https://app.recurly.com/go/plans/new) if you only have a few offerings, but you can also create as many plans as you need through the [API](https://recurly.com/developers/api/v2021-02-25/index.html#operation/create_plan).

Let's create a hypothetical plan for a monthly coffee delivery product. The customer will\
be charged $100 a month. It will use the unique identifier "coffee-monthly" to refer to
this plan.

```ruby
plan_create = {
  code: "coffee-monthly",
  name: "Monthly Coffee Subscription",
  currencies: [
    {
      currency: "USD",
      unit_amount: 100
    }
  ]
}
plan = @client.create_plan(body: plan_create)
puts "Created Plan #{plan}"
```
```js
const planCreate = {
  code: 'coffee-monthly',
  name: 'Monthly Coffee Subscription',
  currencies: [
    {
      currency: 'USD',
      unitAmount: 100
    }
  ]
}
const plan = await client.createPlan(planCreate)
console.log('Created Plan: ', plan.code)
```
```python
plan_create = {
    "code": 'coffee-monthly',
    "name": "Monthly Coffee Subscription",
    "currencies": [{
        "currency": "USD",
        "unit_amount": 100
    }],
}
plan = client.create_plan(plan_create)
print("Created Plan %s" % plan)
```
```java
// Put these imports on the top of your file
import com.recurly.v3.requests.PlanCreate;
import com.recurly.v3.requests.PlanPricing;
import com.recurly.v3.resources.Plan;
import java.util.ArrayList;
import java.util.List;

PlanCreate planCreate = new PlanCreate();
planCreate.setCode("coffee-monthly");
planCreate.setName("Monthly Coffee Subscription");

List<PlanPricing> currencies = new ArrayList<>();
PlanPricing planPrice = new PlanPricing();
planPrice.setCurrency("USD");
planPrice.setUnitAmount(100.0f);
currencies.add(planPrice);
planCreate.setCurrencies(currencies);

Plan plan = client.createPlan(planCreate);
System.out.println("Created Plan " + plan.getCode());
```
```csharp
// Add this on the top of your file
using Recurly.Resources;
using System.Collections.Generic;

var planReq = new PlanCreate()
{
    Code = "coffee-monthly",
    Name = "Monthly Coffee Subscription",
    Currencies = new List<PlanPricing>() {
        new PlanPricing() {
            Currency = "USD",
            UnitAmount = 100
        }
    }
};
Plan plan = client.CreatePlan(planReq);
Console.WriteLine($"Created plan {plan.Code}");
```
```php
$plan_create = array(
    "name" => "Monthly Coffee Subscription",
    "code" => $plan_code,
    "currencies" => [
        array(
            "currency" => "USD",
            "unit_amount" => 100
        )
    ]
);

$plan = $client->createPlan($plan_create);

echo 'Created Plan:' . PHP_EOL;
var_dump($plan);
```

## Step 5: Verify and finish

That's it! You can now view your newly created plan in the [admin interface](https://app.recurly.com/go/plans)

## Next steps

With a newly created plan at your disposal, it's time to start creating customer accounts, billing info, subscriptions and / or one time payments following our  <a href="https://docs.recurly.com/v1.1/docs/purchases-guide">Purchases Guide</a>.
---
description: >-
  This feature provides an opportunity to create an invoice for a B2B or B2C
  client for a specified amount.
---

# Invoices

## Invoice general flow

You should send a request with a particular set of fields to create an invoice.The response will contain an URL that you have to forward to the user for payment.

Your user will see an invoice name, amount and currency, and also specific parameters \(depending on invoice type\): timer, possibility to choose payment currency or estimated amount and currency of payment.

If you send an user email in request, this field will be filled out for the user. Otherwise, the user has to fill out this field themselves. After confirming currency and putting email, the user has to pay an invoice using a specified address before the expiry of the timer. This address is generated by CoinsPaid.

The invoice type will be externalized depending on the sent parameters. There are 3 types of invoice:

1. Invoice without restriction of payment time. The user chooses a currency of payment themselves without time restriction. Rate will be fixed when the user confirms payment currency.
2. Invoice with time restriction. The user chooses a currency of payment themselves, but the time restriction will be 15 minutes starting from invoice creation.
3. Invoice with time restriction and without a possibility to choose payment currency. The user sees the amount and payment currency that they need to pay.

If the user fails to pay the invoice in due time the system will display failed status.

You will receive a callback about the successful transaction creation after it appears in the mempool. We will change the timer from 15 minutes to 24 hours. During this period of time the transaction will change its status to confirmed.

After the successful confirmation of the transaction, funds will be exchanged in a receiver currency and transferred to your account. The Merchant is charged a Fee for Operation after the Funds are credited to the account.

If the user sends an amount of funds that is more or less than specified one we will send a payback guideline to their email.

## Invoice statuses

![](../../.gitbook/assets/witdrawal-flow.png)

## API Endpoint

{% api-method method="post" host="https://app.coinspaid.com/api" path="/v2/invoices/create" %}
{% api-method-summary %}
Create Invoice
{% endapi-method-summary %}

{% api-method-description %}
Create invoice for the client for a specified amount.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="timer" type="boolean" required=true %}
Time on the rate is fixed for invoice payment \(15 minutes\). During this time the user has to pay an invoice.
{% endapi-method-parameter %}

{% api-method-parameter name="title" type="string" required=true %}
Invoice title that will be displayed to the user
{% endapi-method-parameter %}

{% api-method-parameter name="description" type="string" required=false %}
Invoice description that will be displayed to the user
{% endapi-method-parameter %}

{% api-method-parameter name="currency" type="string" required=true %}
ISO invoice currency that you want to receive from the user, for example: **“EUR”**
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_currency" type="string" required=false %}
Currency of user invoice payment \(3rd type invoice will be externalized at the time of sending this parameter with timer= true\), example: **“BTC“**
{% endapi-method-parameter %}

{% api-method-parameter name="amount" type="integer" required=true %}
Invoice amount that you want to receive from the user, example: **“106.75“**
{% endapi-method-parameter %}

{% api-method-parameter name="foreign\_id" type="string" required=true %}
Unique foreign ID in your system, example: "**164**"
{% endapi-method-parameter %}

{% api-method-parameter name="url\_success" type="string" required=true %}
URL on which we redirect the user in case of a successful invoice payment, example: **“https://merchant.name.com/url\_success“**
{% endapi-method-parameter %}

{% api-method-parameter name="url\_failed" type="string" required=true %}
URL on which we redirect the user in case of an unsuccessful invoice payment, example: **“https://merchant.name.com/url\_failed“**
{% endapi-method-parameter %}

{% api-method-parameter name="email\_user" type="string" required=true %}
In case the payment amount does not match the amount stated above, we will send an email to the stated address with instructions on funds recovery. In case of underpayment, the whole amount will be refunded. In case of overpayment, user will be able to recover the difference by following the instructions.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```typescript
{
   "data":{
      "id":79,
      "url":"https://app.coinspaid.com/invoice/RB9NZv",
      "foreign_id":164,
      "name":"TEST NAME",
      "status":"created",
      "currency":"EUR",
      "amount":"106.75",
      "sender_currency":"BTC",
      "sender_amount":null,
      "fixed_at":1581929889,
      "release_at":1581930789
   }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```javascript
{
   "errors":{
      "foreign_id":"The foreign id has already been taken."
   }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

## Invoice Type

### **1st Type** 

#### Invoice without restriction of payment time

This invoice type allows the user to choose the payment currency themselves without a time restriction. The rate will be fixed when the user confirms the payment currency.

By following the link the user will see the information about the invoice and also will be able to choose the payment currency.

{% code title="Request example:" %}
```javascript
 "timer": false,
 "title": "Invoice Title",
 "currency": "EUR",
 "amount": 106.75,
 "foreign_id": "196",
 "url_success": "http://success.test",
 "url_failed": "http://failed.test",
 "email_user": "email@address.com"
```
{% endcode %}

{% code title="Response example:" %}
```javascript
{
   "data":{
      "id": 127,
      "url": "https://wallet.coinspaid.com/invoice/iEU3uh",
      "foreign_id": "196",
      "address": null,
      "title": "Invoice Title",
      "status": "created",
      "currency": "EUR",
      "amount": "106.75000000",
      "sender_currency": null,
      "sender_amount": null,
      "fixed_at": null,
      "release_at": null,
      "seconds_left": null
   }
}
```
{% endcode %}

![](../../.gitbook/assets/image%20%2820%29.png)

After confirming the currency and putting email, the rate will be fixed for 15 minutes. During this period of time the user has to pay the invoice using the specified address.

![](../../.gitbook/assets/image%20%2828%29.png)

### **2nd Type**

#### Invoice with time restriction 

This invoice type allows the user to choose the payment currency themselves, but the time restriction will be 15 minutes starting from the invoice creation.

By following the link the user will see the information about the invoice, timer and will also be able to choose the payment currency. The rate will be fixed after the invoice creation.

{% code title="Request example:" %}
```javascript
 "timer": true,
 "title": "Invoice Title",
 "currency": "BTC",
 "amount": 0.02,
 "foreign_id": "197",
 "url_success": "http://success.test",
 "url_failed": "http://failed.test",
 "email_user": "email@address.com"
```
{% endcode %}

{% code title="Response example:" %}
```javascript
{
    "data": {
        "id": 26,
        "url": "https://wallet.coinspaid.com/invoice/XJrbOG",
        "foreign_id": "197",
        "address": null,
        "title": "Invoice Title",
        "status": "pending",
        "currency": "BTC",
        "amount": "0.01000000",
        "sender_currency": null,
        "sender_amount": null,
        "fixed_at": 1592315493,
        "release_at": 1592316393,
        "seconds_left": 899
    }
}
```
{% endcode %}

![](../../.gitbook/assets/image%20%2824%29.png)

After confirming the currency and inputting the email, the user has to pay the invoice using the specified address before the expiry of the timer.

![](../../.gitbook/assets/image%20%2823%29.png)

###  **3rd Type** 

#### Invoice with time restriction and without the possibility to choose payment currency

This invoice type allows the user to pay the invoice themselves in a definite currency with a time restriction for 15 minutes starting from invoice creation.

By following the link the user will see the information about the invoice, 15 minutes timer and the payment currency. The rate will be fixed after the invoice creation.

{% code title="Request example:" %}
```javascript
 "timer": true,
 "title": "Invoice Title",
 "description": "Invoice description",
 "currency": "EUR",
 "sender_currency": "BTC",
 "amount": 35,
 "foreign_id": "200",
 "url_success": "http://success.test",
 "url_failed": "http://failed.test",
 "email_user": "email@address.com"
```
{% endcode %}

{% code title="Response example:" %}
```javascript
{
   "data":{
       "id": 28,
       "url": "https://wallet.coinspaid.com/invoice/zeUBm1",
       "foreign_id": "200",
       "address": "2NCMFo2k7S8HSDWp6qwQ2wiAMwz3XwEC4Gp",
       "title": "Invoice Title",
       "status": "pending",
       "currency": "EUR",
       "amount": "35.00000000",
       "sender_currency": "BTC",
       "sender_amount": "0.00413945",
       "fixed_at": 1592316313,
       "release_at": 1592317213,
       "seconds_left": 899
   }
}
```
{% endcode %}

The user has to pay the invoice using the specified address before the expiry of the timer.

![](../../.gitbook/assets/image%20%2827%29.png)

## Invoice failed

Invoice fails upon the occurrence of any of the following:

1. 15 minutes timer expiration.
2. Transaction has processing status for more than 24 hours.
3. The user paid an amount less than was requested. In this case the transaction will have a confirmed status but invoice will have a failed status.

## Invoice callbacks

If the invoice payment is paid by installments, you will receive a callback for each part.

```javascript
{
  "id": 588,
  "foreign_id": "8FW1KI7LesB9yxWcK1K",
  "type": "invoice",
  "crypto_address": {
    "id": 386897,
    "currency": "BTC",
    "address": "2Mvo6FMduhHz1BTDHsQ5GyRoifcG3y4ycpk",
    "tag": null
  },
  "currency_sent": {
    "currency": "BTC",
    "amount": "0.02000000",
    "remaining_amount": "0.01000000"
  },
  "currency_received": {
    "currency": "BTC",
    "amount": "0.02000000"
  },
  "transactions": [
    {
      "id": 750294,
      "currency": "BTC",
      "transaction_type": "blockchain",
      "type": "deposit",
      "address": "2Mvo6FMduhHz1BTDHsQ5GyRoifcG3y4ycpk",
      "tag": null,
      "amount": "0.01000000",
      "txid": "528dcda13270f8590853405600bf5634d53aa66d2ce5d3a873006a670f9da788",
      "confirmations": "0"
    }
  ],
  "fees": [],
  "error": "",
  "status": "pending",
  "fixed_at": 1592307241,
  "expires_at": 1592308141
}
```

You will receive a callback about the successful transaction creation after it appears in the mempool. We will change the timer from 15 minutes to 24 hours. During this period of time the transaction will change its status to confirmed.

```javascript
{
  "id": 22,
  "foreign_id": "229-hdsa",
  "type": "invoice",
  "crypto_address": {
    "id": 1845,
    "currency": "BTC",
    "convert_to": "EUR",
    "address": "2N8pvnVKEFjVCP4TteJVCPTbg9Aqibfws8C",
    "tag": null
  },
  "currency_sent": {
    "currency": "BTC",
    "amount": "0.00309556",
    "remaining_amount": "0.00000000"
  },
  "currency_received": {
    "currency": "EUR",
    "amount": "26.00000000"
  },
  "transactions": [
    {
      "id": 1504,
      "currency": "BTC",
      "transaction_type": "blockchain",
      "type": "deposit",
      "address": "2N8pvnVKEFjVCP4TteJVCPTbg9Aqibfws8C",
      "tag": null,
      "amount": "0.00309556",
      "txid": "3e68744626ba23d591dc1b548b8b9b126ab0f49e6da020958f66ee64c38ec7ed",
      "confirmations": "0"
    }
  ],
  "fees": [],
  "error": "",
  "status": "processing",
  "fixed_at": 1592308917,
  "expires_at": 1592395375
}
```

In case of a successful invoice payment you will receive the following callback:

```javascript
{
  "id": 588,
  "foreign_id": "8FW1KI7LesB9yxWcK1K",
  "type": "invoice",
  "crypto_address": {
    "id": 386897,
    "currency": "BTC",
    "address": "2Mvo6FMduhHz1BTDHsQ5GyRoifcG3y4ycpk",
    "tag": null
  },
  "currency_sent": {
    "currency": "BTC",
    "amount": "0.02000000",
    "remaining_amount": "0.00000000"
  },
  "currency_received": {
    "currency": "BTC",
    "amount": "0.02000000"
  },
  "transactions": [
    {
      "id": 750294,
      "currency": "BTC",
      "transaction_type": "blockchain",
      "type": "deposit",
      "address": "2Mvo6FMduhHz1BTDHsQ5GyRoifcG3y4ycpk",
      "tag": null,
      "amount": "0.01000000",
      "txid": "6647cf5cae701507b7076b32ca12f19d8e9fe037407c02e09b04abdaede99fd0",
      "confirmations": "1"
    },
    {
      "id": 750384,
      "currency": "BTC",
      "transaction_type": "blockchain",
      "type": "deposit",
      "address": "2Mvo6FMduhHz1BTDHsQ5GyRoifcG3y4ycpk",
      "tag": null,
      "amount": "0.01000000",
      "txid": "528dcda13270f8590853405600bf5634d53aa66d2ce5d3a873006a670f9da788",
      "confirmations": "1"
    }
  ],
  "fees": [
    {
      "type": "fee_crypto_deposit",
      "currency": "BTC",
      "amount": "0.0008"
    }
  ],
  "error": "",
  "status": "confirmed",
  "fixed_at": 1592307241,
  "expires_at": 1592394104
}
```

In case of an unsuccessful invoice payment you will receive the following callback:

a\) 15 minutes timer expired:

```javascript
{
  "id": 23,
  "foreign_id": "77xa2pd",
  "type": "invoice",
  "crypto_address": {
    "id": 1846,
    "currency": "BTC",
    "address": "2MyL2ftBdNsmpsx1EKggPzNVuoiZCWdVaLX",
    "tag": null
  },
  "currency_sent": {
    "currency": "BTC",
    "amount": "0.02000000",
    "remaining_amount": "0.02000000"
  },
  "currency_received": {
    "currency": "BTC",
    "amount": "0.02000000"
  },
  "transactions": [],
  "fees": [],
  "error": "Timer expired. User not paid.",
  "status": "failed",
  "fixed_at": 1592309817,
  "expires_at": 1592310717
}
```

b\) transaction has processing status for more than 24 hours**:**

```javascript
{
  "id": 21,
  "foreign_id": "88smaan2",
  "type": "invoice",
  "crypto_address": {
    "id": 1844,
    "currency": "BTC",
    "address": "2NCfH4keMq2SZj3H73VeQYtb8yBgTuUrACf",
    "tag": null
  },
  "currency_sent": {
    "currency": "BTC",
    "amount": "0.01000000",
    "remaining_amount": "0.00990000"
  },
  "currency_received": {
    "currency": "BTC",
    "amount": "0.01000000"
  },
  "transactions": [
    {
      "id": 1505,
      "currency": "BTC",
      "transaction_type": "blockchain",
      "type": "deposit",
      "address": "2NCfH4keMq2SZj3H73VeQYtb8yBgTuUrACf",
      "tag": null,
      "amount": "0.00010000",
      "txid": "6dd68265eb2b3a1be0dd4a40975345155d0fbbe244c84efcc7f6b910629a38f9",
      "confirmations": "0"
    }
  ],
  "fees": [],
  "error": "Timer expired. Transactions were in status processing too long.",
  "status": "failed",
  "fixed_at": 1592308709,
  "expires_at": 1592309609
}
```

c\) the user paid an amount less than was requested. In this case the transaction will have a confirmed status but the invoice will have a failed status:

```javascript
{
  "id": 21,
  "foreign_id": "88smaan2",
  "type": "invoice",
  "crypto_address": {
    "id": 1844,
    "currency": "BTC",
    "address": "2NCfH4keMq2SZj3H73VeQYtb8yBgTuUrACf",
    "tag": null
  },
  "currency_sent": {
    "currency": "BTC",
    "amount": "0.01000000",
    "remaining_amount": "0.00990000"
  },
  "currency_received": {
    "currency": "BTC",
    "amount": "0.01000000"
  },
  "transactions": [
    {
      "id": 1505,
      "currency": "BTC",
      "transaction_type": "blockchain",
      "type": "deposit",
      "address": "2NCfH4keMq2SZj3H73VeQYtb8yBgTuUrACf",
      "tag": null,
      "amount": "0.00010000",
      "txid": "6dd68265eb2b3a1be0dd4a40975345155d0fbbe244c84efcc7f6b910629a38f9",
      "confirmations": "2"
    }
  ],
  "fees": [],
  "error": "Timer expired. User paid less than requested.",
  "status": "failed",
  "fixed_at": 1592308709,
  "expires_at": 1592309609
}
```


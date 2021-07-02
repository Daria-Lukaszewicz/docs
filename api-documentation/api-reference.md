---
description: >-
  Documentation for API V2. Major update is addresses with exchange crypto to
  fiat and vice versa abilities.
---

# API Reference

## General information

API endpoint for all requests in production environment is:

```text
https://app.cryptoprocessing.com/api/v2
```

API endpoint for all requests in test environment is:

```text
https://app.sandbox.cryptoprocessing.com/api/v2
```

#### Deposit flow

1. You obtain new address from CoinsPaid API \(for some currencies it may be address and tag\) and store it somewhere on your side. After that you show this address to your customer in order to make a deposit.
2. Customer sends some funds to this address.  
3. When transaction is sent by a customer - CoinsPaid sends a callback to your callback url with transaction details. It contains status, address, currency, amount and fees.  If status is successful, you should deposit respective amount to customer balance on your side.

#### Withdrawal flow

1. You request to send amount of money to address.
2. Your request is validated on our side. If signature is correct, address is valid and you have enough balance - CoinsPaid responds you with the transaction object.
3. You will receive a callback when transaction status is updated.

**Deposit with exchange flow**

You don't want to touch or store cryptocurrency, but only use it as a payment method. Your customer deposits **BTC**, ****Coinspaid instantly converts it to **EUR** so that **you would receive EUR on your CoinsPaid account.**

1. You obtain new address from CoinsPaid API same as in deposit flow, but additionally pass another parameter "convert\_to" in your request specifying resulting currency.
2. When new deposit is arriving, CoinsPaid converts all arriving funds to destination funds, and sends notifications as in regular deposit

**Withdrawal with exchange flow**

You wish to send Cryptocurrency from your Fiat currency balance. For example you want to **send EUR amount** but your customer ****receives ****money **in BTC.**

1. You do exactly same as in withdrawals, but you specify 2 currencies. One is a currency of your **sending balance** and Second is a **cryptocurrency your Customer wishes to receive**.
2. Your request is validated on our side. If signature is correct, address is valid and you have enough balance - CoinsPaid responds you with the transaction object.
3. You will receive a callback when transaction status is updated.

**Futures flow**

In order to guarantee receiving amount, rates should be fixed. 

1. You send address for exchange \(If it is not necessary to receive exchange rates you can skip this step\)
2. You receive rates and their fixation time 
3. To confirm futures You have to send exchange pair and amount due. 
4. When new deposit is arriving, CoinsPaid converts all arriving funds to destination funds, and sends notifications as in regular deposit.

In case if received and sent amounts don't equal, CP converts it like normal deposit with exchange.

Limit for this operation is 10 000 EUR**.**

#### **Invoice flow**

Invoices allow to conduct deposits with a fixed rate within a limited period of time disregarding the exchange rate fluctuations. In case it’s necessary to convert funds in fiat currency or convert it to another crypto currency this option will allow to visualize the exact amount of a sender’s currency required to receive the exact amount in a receiver’s currency. When a deposit is processed through this flow, a user is being redirected to the respective Invoice page via the preset link and just needs to follow instructions specified on it. It also allows to conduct refunds in case a user fails to manage to deposit within an established timeframe or discovers non-sufficient balance. All is required in that case is to follow the link with the respective instructions sent to an e-mail of a customer.   
Detailed information can be found [here](../integration-guide/invoices.md).

## API Endpoints

{% api-method method="get" host="https://app.cryptoprocessing.com/api" path="/v2/ping" %}
{% api-method-summary %}
Ping
{% endapi-method-summary %}

{% api-method-description %}
Test if API is up and running and your authorization is working
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
OK
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
Body must be a valid json object or array, example: {}
{% endhint %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/currencies/list" %}
{% api-method-summary %}
Get list of supported currencies
{% endapi-method-summary %}

{% api-method-description %}
Get a list of all supported currencies
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="visible" type="boolean" required=false %}
Allows to get a list of currently enabled/disabled currencies 
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "data": [
    {
      "id": 1,
      "type": "crypto",
      "currency": "BTC",
      "minimum_amount": "0.001",
      "deposit_fee_percent": "0.99",
      "withdrawal_fee_percent": "0",
      "precision": 8
    }
  ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/currencies/pairs" %}
{% api-method-summary %}
Get list of exchangeable currency pairs
{% endapi-method-summary %}

{% api-method-description %}
Get list of currency exchange pairs.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="currency\_from" type="string" required=false %}
Filter by currency ISO that exchanges from, example: **BTC**
{% endapi-method-parameter %}

{% api-method-parameter name="currency\_to" type="string" required=false %}
Filter by currency ISO that can be converted to, example: **EUR**
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```
{
  "data": [
    {
      "currency_from": {
        "currency": "BTC",
        "type": "crypto",
        "min_amount": "0.00300000",
        "min_amount_deposit_with_exchange": "0.00010000"
      },
      "currency_to": {
        "currency": "EUR",
        "type": "fiat"
      },
      "rate_from": "1",
      "rate_to": "8795.80000000"
    },
    {
      "currency_from": {
        "currency": "EUR",
        "type": "fiat",
        "min_amount": "25.00000000"
      },
      "currency_to": {
        "currency": "BTC",
        "type": "crypto"
      },
      "rate_from": "1",
      "rate_to": "8885.72951839"
    },
    {
      "currency_from": {
        "currency": "BCH",
        "type": "crypto",
        "min_amount": "0.00020000",
        "min_amount_deposit_with_exchange": "0.00100000"
      },
      "currency_to": {
        "currency": "USD",
        "type": "fiat"
      },
      "rate_from": "1",
      "rate_to": "332.28264751"
    },
    {
      "currency_from": {
        "currency": "USD",
        "type": "fiat",
        "min_amount": "30.00000000"
      },
      "currency_to": {
        "currency": "BCH",
        "type": "crypto"
      },
      "rate_from": "1",
      "rate_to": "335.94361522"
    }
  ]
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```
{
    "errors": {
        "currency_from": "The selected currency from is invalid.",
        "currency_to": "The selected currency to is invalid."
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}



{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/currencies/rates" %}
{% api-method-summary %}
Get list of currencies rates
{% endapi-method-summary %}

{% api-method-description %}
Get a particular pair and its price.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="currency\_from" type="string" required=false %}
Filter by currency ISO that exchanges from,  
example: **BTC**
{% endapi-method-parameter %}

{% api-method-parameter name="currency\_to" type="string" required=false %}
Filter by currency ISO that can be converted to, example: **EUR**
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    "data": [
        {
            "currency_from": {
                "currency": "BTC",
                "type": "crypto",
                "min_amount": "0.00010000",
                "min_amount_deposit_with_exchange": "0.00010000"
            },
            "currency_to": {
                "currency": "EUR",
                "type": "fiat"
            },
            "rate_from": "1",
            "rate_to": "48715.61680000"
        }
    ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/accounts/list" %}
{% api-method-summary %}
Get list of balances
{% endapi-method-summary %}

{% api-method-description %}
Get list of all the balances \(including zero balances\).
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
    "data": [
        {
            "currency": "DOGE",
            "type": "crypto",
            "balance": "13234.91276375"
        },
        {    
            "currency": "ADA",
            "type": "crypto",
            "balance": "7272.36400468"
        },
        {
            "currency": "EUR",
            "type": "fiat",
            "balance": "5973.97568920"
        },
        {
            "currency": "USDT",
            "type": "crypto",
            "balance": "0.00000000"
        }
    ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/addresses/take" %}
{% api-method-summary %}
Receive cryptocurrency
{% endapi-method-summary %}

{% api-method-description %}
Take address for depositing crypto and \(it depends on specified params\) exchange from crypto to fiat on-the-fly.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="foreign\_id" type="string" required=true %}
Your info for this address, will returned as reference in Address responses, example: **user-id:2048**
{% endapi-method-parameter %}

{% api-method-parameter name="currency" type="string" required=true %}
ISO of currency to receive funds in, example: **BTC**
{% endapi-method-parameter %}

{% api-method-parameter name="convert\_to" type="string" required=false %}
If you need auto exchange all incoming funds for example to **EUR**, specify this param as EUR or any other supported currency ISO, to see list of pairs see previous method.
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```
{
  "data": {
    "id": 1,
    "currency": "BTC",
    "convert_to": "EUR",
    "address": "12983h13ro1hrt24it432t",
    "tag": tag-123,
    "foreign_id": "user-id:2048"
  }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```
{
    "errors": {
        "foreign_id": "The foreign id field is required."
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="info" %}
Business logic for using cryptocurrency as a deposit method:   
You are willing to let your customer fund his EUR balance on your platform or website. You will have to generate an address in the desired cryptocurrency and specify EUR as a "convert\_to" currency. This will allow you to let your Customer pay if his favorite currency and fund his balance in EUR. At the same time you will see respective EUR amount in your CoinsPaid merchant account.  
**Hint:** you don't have to generate new address for this customer anymore, address can be reused unlimited amount of times.
{% endhint %}

![Example of the customer facing interface for Deposits. ](../.gitbook/assets/qr_api_2.png)

* Make sure to use Bitcoin URI format bitcoin: in QR. Works the same way as "mailto:".
* We do recommend making this QR clickable as customers may have a wallet set up on their computer or mobile phone.
* We recommend specifying approximate current exchange rate.

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/withdrawal/crypto" %}
{% api-method-summary %}
Withdraw cryptocurrency
{% endapi-method-summary %}

{% api-method-description %}
Withdraw in crypto to any specified address. You can send Cryptocurrency from your Fiat currency balance by using "convert\_to" parameter.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="foreign\_id" type="string" required=true %}
Unique foreign ID in your system, example: "**122929**"
{% endapi-method-parameter %}

{% api-method-parameter name="amount" type="string" required=true %}
Amount of funds to withdraw, example: **"1"**
{% endapi-method-parameter %}

{% api-method-parameter name="currency" type="string" required=true %}
Currency ISO to be withdrawn, example: "**BTC**"
{% endapi-method-parameter %}

{% api-method-parameter name="convert\_to" type="string" required=false %}
If you want to auto convert for example EUR to BTC, specify this param as **BTC** or any other currency supported \(see list of exchangeable pairs API method\).
{% endapi-method-parameter %}

{% api-method-parameter name="address" type="string" required=true %}
Cryptocurrency address where you want to send funds.
{% endapi-method-parameter %}

{% api-method-parameter name="tag" type="string" required=false %}
Tag \(if it's Ripple or BNB\) or memo \(if it's Bitshares or   
EOS\)
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response of withdraw without conversion
{% endapi-method-response-example-description %}

```
{
  "data": {
    "id": 1,
    "foreign_id": "122929",
    "type": "withdrawal",
    "status": "processing",
    "amount": "0.01000000",
    "sender_amount": "0.01000000",
    "sender_currency": "ETH",
    "receiver_amount": "0.01000000",
    "receiver_currency": "ETH"
  }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```
{
  "errors": {
    "amount": "The amount must be at least 0.001."
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/exchange/calculate" %}
{% api-method-summary %}
Calculate exchange rates
{% endapi-method-summary %}

{% api-method-description %}
Get info about exchange rates.  
  
Please note, this endpoint has limitation **up to 30 requests per minute** from one IP address, in case this amount is exceeded a new successful response can only be obtained after one minute break.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="receiver\_amount" type="string" required=false %}
Amount you want to calculate for getting, example: **"10".** The parameter is required when the "sender\_amount" parameter is absent
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO for which you want to calculate the exchange rate, example: **"BTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"EUR"**
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_amount" type="string" required=false %}
Amount you want to calculate, example: **"3".** The parameter is required when the "receiver\_amount" parameter is absent
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```
{
  "data": {
    "sender_amount": 1,
    "sender_currency": "BTC",
    "receiver_amount": "8549.81680000",
    "receiver_currency": "EUR",
    "fee_amount": "85.49816800",    
    "fee_currency": "BTC",
    "price": "8549.81680000",
    "ts_fixed": 1564293159,
    "ts_release": 1564293219,
    "fix_period": 60
  }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```
{
  "errors": {
    "sender_currency": "Exchange is unavailable for given currencies"
  }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=429 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "errors": {
    "error code: 1015"
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/exchange/fixed" %}
{% api-method-summary %}
Exchange on fixed exchange rate
{% endapi-method-summary %}

{% api-method-description %}
Make exchange on a given fixed exchange rate.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO which you want to exchange, example: **"LTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"USD"**
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_amount" type="string" required=true %}
Amount you want to exchange, example: **"6.5"**
{% endapi-method-parameter %}

{% api-method-parameter name="foreign\_id" type="string" required=true %}
Unique foreign ID in your system, example: **"134453"**
{% endapi-method-parameter %}

{% api-method-parameter name="price" type="string" required=true %}
Exchange rate price on which exchange will be placed, example: **"89.75202000"**
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```
{
  "data": {
     "id": 2687667,
     "foreign_id": "knwi24op9",
     "type": "exchange",
     "sender_amount": "0.01",
     "sender_currency": "BTC",
     "receiver_amount": "63.52069015",
     "receiver_currency": "EUR",
     "fee_amount": "6.98727592",
     "fee_currency": "EUR",
     "price": "6352.06901520",
     "status": "processing"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/exchange/now" %}
{% api-method-summary %}
Exchange regardless the exchange rate 
{% endapi-method-summary %}

{% api-method-description %}
Make exchange without mentioning the price.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO which you want to exchange, example: **"EUR"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"BTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_amount" type="string" required=true %}
Amount you want to exchange, example: **"2"**
{% endapi-method-parameter %}

{% api-method-parameter name="foreign\_id" type="string" required=true %}
Unique foreign ID in your system, example: **"124876"**
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=201 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```
{
  "data": {
     "id": 2687669,
     "foreign_id": "wph27bmsp81",
     "type": "exchange",
     "sender_amount": "0.001",
     "sender_currency": "BTC",
     "receiver_amount": "6.33984642",
     "receiver_currency": "EUR",
     "fee_amount": "0.69738311",
     "fee_currency": "EUR",
     "price": "6339.84642127",
     "status": "processing"
    }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```
{
  "errors": {
    "sender_amount": "The amount must be at least 0.00300000 "
  }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/futures/rates" %}
{% api-method-summary %}
Calculate rates for futures
{% endapi-method-summary %}

{% api-method-description %}
Get info about rates for futures.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
Exchange address for which you want to calculate futures' rates
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```
{
    "data": {
        "addresses": [
            {
                "id": 384620,
                "currency": "BTC",
                "convert_to": "USD",
                "address": "3GQwSBQErsQ863RcuvCkub6SZBPHKuwSV7",
                "tag": null,
                "foreign_id": "ds23fgk"
            }
        ],
        "ts_fixed": 1581507311,
        "ts_release": 1581513311,
        "rates": {
            "BTCUSD": {
                "5.00000000": "10312.67153000",
                "10.00000000": "10312.67153000",
                "20.00000000": "10312.67153000",
                "50.00000000": "10312.67153000",
                "100.00000000": "10312.67153000",
                "200.00000000": "10312.67153000",
                "300.00000000": "10312.67153000"
            }
        }
    }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```
{
    "errors": {
        "address": "Address is not found, incorrect or without exchange"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/futures/confirm" %}
{% api-method-summary %}
Confirm futures transaction
{% endapi-method-summary %}

{% api-method-description %}
Confirm futures transaction.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="address" type="string" required=true %}
Exchange address for which you want to confirm futures
{% endapi-method-parameter %}

{% api-method-parameter name="sender\_currency" type="string" required=true %}
Currency ISO which you want to exchange, example: **"BTC"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_currency" type="string" required=true %}
Currency ISO to be exchanged, example: **"EUR"**
{% endapi-method-parameter %}

{% api-method-parameter name="receiver\_amount" type="string" required=true %}
Amount you want to receive
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of success response
{% endapi-method-response-example-description %}

```
{
    "data": {
        "futures_id": 92,
        "sender_currency": "BTC",
        "receiver_currency": "USD",
        "fee_currency": "USD",
        "price": "10299.94946000",
        "address": {
            "id": 384620,
            "currency": "BTC",
            "convert_to": "USD",
            "address": "3GQwSBQErsQ863RcuvCkub6SZBPHKuwSV7",
            "tag": null,
            "foreign_id": "ds23fgk"
        },
        "sender_amount": "0.00292",
        "receiver_amount": "30.00000000",
        "fee_amount": "1.50000000",
        "ts_fixed": 1581506566,
        "ts_release": 1581512566
    }
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=400 %}
{% api-method-response-example-description %}
Example of response with errors
{% endapi-method-response-example-description %}

```
{
    "errors": {
        "address": "Address is not found, incorrect or without exchange. Error can be related to incorrect sender or receiver currencies."
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://app.cryptoprocessing.com/api" path="/v2/invoices/create" %}
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
In case the payment amount does not match the amount stated above, we will send an email to the stated address with instructions on funds recovery. In case of underpayment, the whole amount will be refunded. In case of overpayment, user will be able to recover the difference by following the instructions
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Example of a successful response
{% endapi-method-response-example-description %}

```
{
   "data":{
      "id":79,
      "url":"https://app.cryptoprocessing.com/invoice/RB9NZv",
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
Example of a response with errors
{% endapi-method-response-example-description %}

```
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

## Payment terminal JSON parameters

A set of parameter included in URL that will allow to redirect a user to Payment Terminal. For more info navigate through [this section of documentation](../integration-guide/payment-terminal.md).

| **Name** | **Description** | **Optionality** | **Type** |
| :--- | :--- | :--- | :--- |
| client\_id | This is a unique ID that’s generated on the side of CoinsPaid | Required | int |
| currency | ISO of a currency to receive funds in, example: **BTC** | Required | string |
| amount | Amount of funds to withdraw, example: "**3500**" | Optional | numeric |
| convert\_to | In case you need on-the-fly exchange of all incoming funds \(e.g. to EUR\) specify this parameter as EUR or any other supported currency ISO, to see the list of available pairs check the method above | Optional | string |
| is\_iframe | The parameter that allows you to get an iframe-link \(false by default\). | Optional | boolean |
| foreign\_id | Unique foreign ID in your system, example: "**164**" | Required | string |
| url\_back | A link that allows you to get a user back to your system | Optional | url |

## Transaction statuses

| Status | Meaning |
| :--- | :--- |
| confirmed | **Final**. You are safe to process this transaction |
| not\_confirmed | Transaction is not yet confirmed. |
| cancelled | **Final**. This transaction is a double spend or cancelled withdrawal. Pay attention to this transaction. |
| pending | This status may occur only after enabling withdrawal limits. Transaction exceeds set withdrawal limit, you need to confirm or decline it via backoffice of your merchant account. |

## "transaction\_type" parameter values

| Type | Description |
| :--- | :--- |
| Blockchain | Type of transactions that are written into blockchain. Such transactions \(deposits and withdrawals\) can be tracked via according explorer for suitable blockchain. This is the most common type of transactions. |
| Internal | Type of transactions between two addresses within our processing \(between separate merchant accounts or different addresses of the single merchant\). Such transactions are not written in blockchain, but still can be tracked via our internal explorer - [https://explorer.coinspaid.com/](https://explorer.coinspaid.com/) |
| Exchange | Type of transactions that requires exchange between crypto-fiat or crypto-crypto pairs. |

## Transitions

| Transaction | Transition |
| :--- | :--- |
| Successful deposit | not\_confirmed -&gt; confirmed |
| Unsuccessful deposit | not\_confirmed -&gt; cancelled |
| Successful withdrawal | confirmed |
| Unsuccessful withdrawal | cancelled |


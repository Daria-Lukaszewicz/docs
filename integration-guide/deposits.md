---
description: Contains deposits description and provides common workflow for that feature
---

# Deposits

Crypto deposits are similar to bank transfers. If the recipient’s bank account number is known then the funds can be directly sent at any time. This approach differs from classic payment methods because the transaction cannot be created before the funds have appeared in the bank account. The same way web-services cannot expect that crypto deposit transactions will be initiated from the "payment form" every time because, in general, there is no such term for cryptocurrencies. Unlike the classical approach, web-services should handle callbacks from payment providers with information about received deposits. When web-services receive the first callback they then ****create the transaction. After the transaction will be confirmed by the payment provider the second callback will inform the web-services about the status of the transaction. Then, web-services can provide the user with a specific service.

{% hint style="warning" %}
#### Attention!

When a user gets an address for the first time they are able to save it into their crypto wallet application. It is more comfortable for the user because they don’t need to go to the "Cashier" section every time when they want to make a deposit. That is why many users save the address in their crypto wallet application after the first deposit and then they don't need to go to the payment form every time.

As cryptocurrencies are based on the blockchain technology, you cannot delete the address if it has already been created and has been written into the blockchain.

If the user knows this address they can always send the funds there, because it is not necessary for the user to interact with the payment form when they would like to send the funds.

Even if you show the users new addresses every time, they can always make the deposit to the older one, and they expect that they will be able to see their funds on your website. 
{% endhint %}

### Common deposit workflow

The most common ****scenario for receiving cryptocurrency is "deposit with exchange". Coinspaid provides web-services with on-the-fly exchange solution. It means that all funds that were received from the users can be automatically converted into fiat currencies in order to avoid cryptocurrency fluctuations and exchange rate inconsistencies.

In case of regular deposits without exchange the workflow there will be the same but the conversion option will be skipped.

![](../.gitbook/assets/image%20%2816%29.png)

> #### Algorithm
>
> 1. User has EUR balance on your site
> 2. User uses some kind of “deposit” option on your site and chooses BTC as a payment method to use for the payment
> 3. Website shows a deposit BTC address to the user
> 4. User scans QR code or copies the address and goes to their crypto wallet application
> 5. When user sends BTC to this address our system makes an automatic exchange of received funds from BTC to EUR and your system gets the callback with the information about the deposit, including transaction's status, currency pairs, amount, fees and so on
> 6. You add the amount from the callback to user’s EUR balance

{% hint style="danger" %}
When you show a deposit address to a user, it is important to notify ****the user about exchange rates, deposit limits and the chosen cryptocurrency. Besides this you may want to warn the user that:

* all deposits below the limits or sent to incorrect addresses will be lost

Here you can find information about deposit and withdrawal limits:

* [Deposit and withdrawal limits](../confirmations-and-limits.md)

Sometimes users make mistakes and send the funds using a specific cryptocurrency to the address from a different blockchain. These funds will be lost in most cases. The funds could be partially restored according to the "Crosschain recovery policy":

* [Crosschain recovery policy](../crosschain-recovery-policy.md)
{% endhint %}

In order to increase the conversion rate for crypto payments we have a special guide for how you can develop your payment form and provide the user with the best experience of crypto payments. There are good recommendations on how to build friendly and comfortable interactions with your interface for the user. Here you can find out how it can be implemented:

{% page-ref page="crypto-payment-form.md" %}

{% hint style="info" %}
#### Hint 1

Usually our merchants generate all the needed crypto addresses at the time ****when a new user is created. In order to do this you should use the **"/v2/addresses/take"** method from our API.  
You can define what currency should be used for receiving the funds from the user and, if necessary, into what fiat currency received funds should be converted.

In most cases it is useful to send us your user's ID as a **"foreign\_id"** parameter. We will link it to the address and send it in the callbacks. Using such a method you will be able to understand which user made the deposit.
{% endhint %}

{% hint style="warning" %}
#### Attention

Some cryptocurrencies require additional parameters to be defined when a user makes a deposit. For example, users have to input a value into the **"Memo"** field when they make a deposit from their wallet application using **XRP, BNB** or another cryptocurrency as the payment method.

If it is necessary for a specific currency, we send the parameter **"tag"** in the response on **"/v2/addresses/take"** API call. If your system gets this parameter it has to show it's value to the user. In other cases received funds will be lost.
{% endhint %}

{% hint style="info" %}
#### Hint 2

In order to get the information about current exchange rates for different currencies you can use the **"/v2/currencies/pairs"** API method.

If you need to calculate exact amounts corresponding to current exchange rates try the **"/v2/exchange/calculate"** method.
{% endhint %}

{% hint style="info" %}
#### Hint 3

In case you would like to hold the exchange rate for 10 minutes in order to receive a specified amount from the user after converting the funds, you may want to use these methods: **"/v2/futures/rates"** + **"/v2/futures/confirm"**
{% endhint %}

{% hint style="info" %}
#### Hint 4

As we keep a small amount of incoming funds as our fees, we provide you with the information about the fees in the callback.  
It is your choice what amount you will add to user's balance - including fees or not.
{% endhint %}


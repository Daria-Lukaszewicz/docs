# Starting Point of API Testing

Now, which first steps should you take to test our system?

At this point you should have familiarized yourself with [the previous sections](../api-documentation/v2/) of our API documentation.

A good place to start is to generate new addresses. You can do this by using this request request - 

```text
https://app.coinspaid.com/api/v2/addresses/take
```

In order to test the depositing function, you can send some currency to these addresses. You can just transfer some test BTC from a [testnet faucet](https://testnet-faucet.mempool.co/).

Please note, you will be able to test the exchange function with test BTC currency only in our Sandbox environment, you won’t be able to do that with your account in the production environment. Please make sure to test the feature at this stage. You will still be able to use the exchange feature in the production environment, but only with real funds.

{% hint style="danger" %}
Since you are not in our production environment, for your request you should use sandbox.coinspaid.com as well. For example, instead of https://app.coinspaid.com/api/**v2/addresses/take** you should use the following format – sandbox.coinspaid.com/**v2/addresses/take.**
{% endhint %}

By this time, if you followed the steps above and already set your callback URL, you should start receiving them from our system. Make sure that the callbacks are processed correctly on your end and you receive them for all ongoing transactions. In case you haven’t received the callback when you should have, you can press “Repeat callback” and our API will send it to you again. If the issue persists or you have any further questions about API, don’t hesitate to contact us via [support@cryptoprocessing.com](mailto:support@cryptoprocessing.com).

![](../.gitbook/assets/15.png)

[This documentation section](./) was aimed to cover the basics and help you to understand how our API functions.  
We hope that the information was helpful to you and we are looking forward to building a strong business relationship in the future!

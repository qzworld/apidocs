# Accommodation / Tuition

## Generate Payment URL

```Sample
https://pay.skyeepayment.com/web/pay?merchantId=123&appId=984342&orderId=12345-12345767-12345677&orderTime=15583942134&c1=GBP&c2=CNY&amt1=1023111&amt2=9484213&productId=10000&notificationUrl=https://merchant.com&sign=URgwbxHfL%2FE3YNiIBpP0vbL1UPtvbsqfAvGMpLFo5nIW2Bq786Mi0uLrvsI

```

### Endpoint
Environment | URL
--------- | ---------
Sandbox | -
Production | http://pay.skyeepayment.com/pay

### Query Parameters

Parameter | Type | Description 
--------- | ------- | ----------- |
merchantId | string |  `required`<br> a unique ID given by Skyee to identify the merchant.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

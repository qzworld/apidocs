# Generate Payment URL

You use all the information that you are given to create a link,where your payer will click 
and be brought to a Skyee hosted web page. 

```C#

//Link format is like the following:

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
appId | string | `required`<br> each accommodation company’s ID, issued after KYCC done.
orderId | string | `required` `Maxlength: 32`<br>  the merchant’s order number. It must be unique for the merchant. Alphanumeric, "_" and "-" are allowed.  
payType | string | `mandatory`<br>  Default is `BankTransfer`.
orderTime | integer | `required`<br> when is this order created. Unix Timestamp, e.g. 15583942134.
productId | integer | `required`<br> the accommodation's productId is 10000
c1 | string | `required`<br> Original currency in GBP/USD/AUD/NZD/EUR. 
amt1 | integer | `required`<br> Original amount to display to the student.<br>The original amount will use the smallest unit of currency, but the reference value must be an integer without decimals. For example, using a currency type of "United States dollars" a reference value of "1750" in the payment amount would be equivalent to US$17.50.
c2 | string | `Conditional`<br>  Collection currency which will be CNY.
amt2 | integer | `Conditional`<br> CNY collection amount which Skyee will collect from the student.<br> The collection amount will use the smallest unit of CNY, but the reference value must be an integer without decimals. For example, using a currency type of "China Yuan" a reference value of "1750" in the payment amount would be equivalent to CNY￥17.50.
notificationUrl | string | `required` <br>The URL where we will push async notifications, so the merchant could receive payment result if it's generated a bit later.
sign | string |  `required` <br>Signature encrypted using RSA(SHA1WithRSA) signature. Reference  [Create a signature](#create-a-signature)


# Notification
Skyee needs a URL endpoint which Skyee could send payment notification to.
It’s a POST request to inform the merchant of the payment status, a code sample would be like following:

``` 
POST "notificationUrl" HTTP/1.1 Content-Type: application/x-www-form-urlencoded

merchantId=8731769123&appId=758353&payType=bankTransfer&orderId=12345-12345767-12345677&orderTime=15583942134&productId=10000&orderStatus=paid&c1=GBP&c2=CNY&amt1=1023111&amt2=9484213&errorCode=xxx&sign=URgwbxHfL%2FE3YNiIBpP0vbL1UPtvbsqfAvGMpLFo5nIW2Bq786Mi0uLrvsI
```

In the POST, the following parameters will be sent:

### Parameters

Parameter | Type | Description 
--------- | ------- | ----------- |
merchantId | string |  `readonly`<br> a unique ID given by Skyee to identify the merchant.
appId | string | `readonly`<br> each accommodation company’s ID.
orderId | string | `readonly` `Maxlength: 32`<br>  the merchant’s order number.
payType | string | `readonly`<br> Payment Method
orderTime | integer | `readonly`<br> when is this order created. Unix Timestamp, e.g. 15583942134.
productId | integer | `readonly`<br> the accommodation's productId is 10000
c1 | string | `readonly`<br> Original currency in GBP/USD/AUD/NZD/EUR. 
amt1 | integer | `readonly`<br> Original amount to display to the student.<br>The original amount will use the smallest unit of currency, but the reference value must be an integer without decimals. For example, using a currency type of "United States dollars" a reference value of "1750" in the payment amount would be equivalent to US$17.50.
c2 | string | `readonly`<br>  Collection currency which will be CNY.
amt2 | integer | `readonly`<br> CNY collection amount which Skyee will collect from the student.<br> The collection amount will use the smallest unit of CNY, but the reference value must be an integer without decimals. For example, using a currency type of "China Yuan" a reference value of "1750" in the payment amount would be equivalent to CNY￥17.50.
orderStatus | string |  `readonly`<br> Order Status. 
errorCode  | string |  `readonly`<br> The error code when an error occurs.
sign | string | Signature encrypted using RSA(SHA1WithRSA) signature. Reference  [verify a signature](#verify-a-signature)
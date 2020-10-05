# Security Specifications

## Create a signature

```C#

    var dic = new Dictionary<string, string>()
    {
        { "merchantId", "1000000000" },
        { "appId", "10000100"},
        { "orderId","12345-12345767-12345677"},
        { "c1","GBP"},
        { "amt1","1023111"},
        { "c2","CNY"},
        { "amt2","9484213"},
        { "orderTime", "15583942134"},
        { "productId", "10000"},
        { "notificationUrl" , "https://merchant.com"},
    };

    Dictionary<string, string> ascDic = param.OrderBy(o => o.Key).ToDictionary(o => o.Key, p => p.Value);
    var sb = new StringBuilder();
            
    foreach (var item in ascDic)
    {
        if (!string.IsNullOrEmpty(item.Value))
        {
            sb.Append(item.Key).Append("=").Append(item.Value).Append("&");
        }
    }

    string stringA = sb.ToString().Substring(0, sb.ToString().Length - 1);

    var certPrivateKey = File.ReadAllText("merchant-private-key.pem");
    certPrivateKey = certPrivateKey.Replace("-----BEGIN RSA PRIVATE KEY-----", "")
                                   .Replace("-----END RSA PRIVATE KEY-----", "")
                                   .Replace("-----BEGIN RSA PUBLIC KEY-----", "")
                                   .Replace("-----END RSA PUBLIC KEY-----", "")
                                   .Replace("-----BEGIN PRIVATE KEY-----", "")
                                   .Replace("-----END PRIVATE KEY-----", "")
                                   .Replace("-----BEGIN PUBLIC KEY-----", "")
                                   .Replace("-----END PUBLIC KEY-----", "")
                                   .Replace(Environment.NewLine, "");
    var rsa = RSA.Create();
    int bytesRead;
    rsa.ImportPkcs8PrivateKey((ReadOnlySpan<byte>)Convert.FromBase64String(certPrivateKey), out bytesRead);
    var signData = rsa.SignData(Encoding.UTF8.GetBytes(stringA), HashAlgorithmName.SHA1, RSASignaturePadding.Pkcs1);
    var sign = Convert.ToBase64String(signData);
```


Step 1 : Presume all data sent  is the set M. Sort non-empty values in M in ascending alphabetical order (i.e. lexicographical sequence), and join them into string A via the corresponding URL key-value format (e.g. key1=value1&key2=value2…).

    Notes:

    a. Sort parameter names in ascending alphabetical order based on their ASCII encoded names (e.g. lexicographical sequence);

    b. Empty parameter values are excluded in the signature;

    c. Parameter names are case-sensitive;

    d. When checking returned data or a Skyee push notification signature, the transferred sign parameter is excluded in this signature as it is compared with the created signature.

Step 2 : Perform SHA1 arithmetic on stringA,  thus get sign's value (signValue).

    Example:

    For the following transferred parameters:

    merchantId：1000000000 <br>
    appId：10000100 <br>
    orderId： 12345-12345767-12345677 <br>
    orderTime：15583942134 <br>
    c1：GBP <br>
    amt1：1023111 <br>
    c2: CNY <br>
    amt2: 9484213 <br>
    productId: 10000 <br>
    notificationUrl: https://merchant.com

    a. Sort ASCII code of parameter names by lexicographical sequence based on the format of "key=value"

    stringA="amt1=1023111&amt2=9484213&appId=10000100&c1=GBP&c2=CNY&merchantId=1000000000&notificationUrl=https://merchant.com&orderId=12345-12345767-12345677&orderTime=15583942134&productId=10000";

    b.The merchant sign the signature string using the merchant's RSA private key


## Verify a signature

```C#
// using RSAExtensions;

    var dic = new Dictionary<string, string>()
    {
        { "merchantId", "1000000000" },
        { "appId", "10000100"},
        { "orderId","12345-12345767-12345677"},
        { "payType" , "BankTransfer"},
        { "c1","GBP"},
        { "amt1","1023111"},
        { "c2","CNY"},
        { "amt2","9484213"},
        { "orderTime", "15583942134"},
        { "productId", "10000"},
        { "orderStatus" , "Paid"},
        { "errorCode" , ""},
    };

    string sign =
        "CzSlJ6X4nPsP5p36PQYf51a9OJRK6+V65kFwuD4wxeRdeoJZensPCD0YBB3uUlSphbR1RXACfSMXy9nfanbmh8n+dk8mUJiN2G4+Xwd0/HuYQqv9jUMMJT4UkfWO9CARlDrF4dUHDV4U7d4dcb4HjbRFeHyPbK2utWlHGQ1HsjJ6nupW2kxWIZ6MOVu0V5E+OUO1A5qMbjvIm8Etx5nEW/3v+qkH19S66FwjP0bh9HfYBGmA64YZHbc4W+3R5OF16EvGOm8sXNyF+9+IxUmbWrJ/a5GP6y8wvI6b7TZorjD1ss41Xuvuow0dtdwbXZn8ZPHcyxz2bdssh5ha9UEP7g==";
    byte[] signature = Convert.FromBase64String(sign);

    Dictionary<string, string> ascDic = dic.OrderBy(o => o.Key).ToDictionary(o => o.Key, p => p.Value);
    var sb = new StringBuilder();

    foreach (var item in ascDic)
    {
        if (!string.IsNullOrEmpty(item.Value))
        {
            sb.Append(item.Key).Append("=").Append(item.Value).Append("&");
        }
    }

    var stringA = sb.ToString().Substring(0, sb.ToString().Length - 1);

    var certPublicKey = File.ReadAllText("skyee-publick-key.pem");

    var rsa = RSA.Create();

    rsa.ImportPublicKey(RSAKeyType.Pkcs8, certPublicKey, isPem: true);
    bool isOK = rsa.VerifyData(Encoding.UTF8.GetBytes(stringA), signature, HashAlgorithmName.SHA1, RSASignaturePadding.Pkcs1); //verify sign
    Assert.True(isOK);
```


Step 1 : Presume all data received(excluded sign field) is the set M. Sort non-empty values in M in ascending alphabetical order (i.e. lexicographical sequence), and join them into string A via the corresponding URL key-value format (e.g. key1=value1&key2=value2…).

    Notes:

    a. Sort parameter names in ascending alphabetical order based on their ASCII encoded names (e.g. lexicographical sequence);

    b. Empty parameter values are excluded in the signature;

    c. Parameter names are case-sensitive;

    d. When Skyee push notification signature, the transferred sign parameter is excluded in this signature as it is compared with the created signature.

Step 2 : Perform SHA1 arithmetic on stringA,  thus get sign's value (signValue).

    Example:

    For the following transferred parameters:

    merchantId：1000000000 <br>
    appId：10000100 <br>
    orderId： 12345-12345767-12345677 <br>
    payType: BankTransfer <br>
    orderTime：15583942134 <br>
    c1：GBP <br>
    amt1：1023111 <br>
    c2: CNY <br>
    amt2: 9484213 <br>
    productId: 10000 <br>
    orderStatus: Paid <br>
    errorCode : <br>
    sign: CzSlJ6X4nPsP5p36PQYf51a9OJRK6+V65kFwuD4wxeRdeoJZensPCD0YBB3uUlSphbR1RXACfSMXy9nfanbmh8n+dk8mUJiN2G4+Xwd0/HuYQqv9jUMMJT4UkfWO9CARlDrF4dUHDV4U7d4dcb4HjbRFeHyPbK2utWlHGQ1HsjJ6nupW2kxWIZ6MOVu0V5E+OUO1A5qMbjvIm8Etx5nEW/3v+qkH19S66FwjP0bh9HfYBGmA64YZHbc4W+3R5OF16EvGOm8sXNyF+9+IxUmbWrJ/a5GP6y8wvI6b7TZorjD1ss41Xuvuow0dtdwbXZn8ZPHcyxz2bdssh5ha9UEP7g==

    a. Sort ASCII code of parameter names by lexicographical sequence based on the format of "key=value"

    stringA="amt1=1023111&amt2=9484213&appId=10000100&c1=GBP&c2=CNY&merchantId=1000000000&orderId=12345-12345767-12345677&orderStatus=Paid&orderTime=15583942134&payType=BankTransfer&productId=10000";

    b.The merchant verify the signature string using the Skyee's RSA public key
    
    Environment | Public key 
    --------- | ------- 
    Sandbox | -
    Production | [Download Skyee Public Key](https://pay.skyeepayment.com/files/publickey)


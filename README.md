# TVAPPS
DiGi TV APPS:: Telenor api instructions

in an webview open
https://connect.staging.telenordigital.com/oauth/authorize?client_id=hakjapan-banglatv-android&redirect_uri=http://stagingtv.universetv.net/digi/success.php&scope=phone+openid+id.user.right.read+payment.transactions.read+payment.transactions.write+payment.agreements.read+payment.agreements.write&response_type=code

after successfull login should receive a token in the url redirect, so collect the access_token from url and store it in your app (shared preference):
sample : http://stagingtv.universetv.net/digi/result.php?access_toen=5bE82z2SPOY7Zslnk2nDj3cJ5EQ

That's it you are now logged in!!





RETRIEVE USER INFO : 
======================================================================
Now you need phonenumber to show on user's profile. Where to get it?
You can use the access token to retrieve it.

To retrieve userinfo use following API
GET https://connect.staging.telenordigital.com/oauth/userinfo

HEADER
"Content-Type" : "application/json"
"Authorization":  "Bearer 5bE82z2SPOY7Zslnk2nDj3cJ5EQ" //"Bearer {access_token}"
"Accept" : "application/json"

It should return you the sub/id/connect_id , phone_number in return.
Example Response : 
{
sub: "5974078046150336512",
phone_number: "8801932986985",
phone_number_verified: true
}

Then parse the json and show it in the App.





Check if user has Rights : 
======================================================================
Rights can be also retrieved by using the accesstoken. To retrieve rights use the following API :

GET https://stagingapi.comoyo.com/id/users/$userId/rights?active=now

HEADER:
    "Content-Type" => "application/json",
    "Authorization" => "Bearer 5bE82z2SPOY7Zslnk2nDj3cJ5EQ",
    "Accept" => "application/json"

RESPONSE: see http://docs.telenordigital.com/connect/apis/rights.html




Showing Payment page: 
======================================================================
First you have to initiate a transaction to get the payment page. To Initiate a transaction send a POST request to the following api.

Request
------------------------------
POST https://staging-payment-payment2.comoyo.com/transactions
Request body RAW JSON : (Search google on how to add this to request body) :
{
    "orderId": "123433", //random
    "purchaseDescription": "Bangla TV Weekly",
    "amount": "MYR 2.00",
    "vatRate": "0.0",
    "successRedirect": "http://stagingtv.universetv.net/digi/payment.php?status=success",
    "cancelRedirect": "http://stagingtv.universetv.net/digi/payment.php?status=failed",
    "merchantName": "hakjapan-banglatv-android",
    "locale": "en-US",
    "allowedPaymentMethods": ["DOB"],
    "connectId": "5980630075270242304" //the id/sub you got from userinfo
}


Response
-------------------------------------------
{
    "amount": "MYR 2.00",
    "vatRate": 0,
    "orderId": "123433",
    "purchaseDescription": "Bangla TV Weekly",
    "successRedirect": "http://stagingtv.universetv.net/digi/payment.php?status=success",
    "cancelRedirect": "http://stagingtv.universetv.net/digi/payment.php?status=failed",
    "merchantName": "hakjapan-banglatv-android",
    "connectId": "5980630075270242304",
    "chargingAgreementId": null,
    "products": null,
    "sku": null,
    "timeSpec": null,
    "allowedPaymentMethods": [
        "DOB"
    ],
    "id": "hO0_YDAGSqyfCq3QlFM1fw",
    "vatAmount": "MYR 0.00",
    "startTime": "2015-03-24T21:00:10.966Z",
    "state": "STARTED",
    "returnCode": null,
    "chargedPaymentMethod": "NOT_SELECTED",
    "previousTransactionId": null,
    "links": [
        {
            "rel": "PAYMENT",
            "href": "https://staging-payment-payment2.comoyo.com/transactions/hO0_YDAGSqyfCq3QlFM1fw"
        }
    ],
    "completedTime": null
}


Open https://staging-payment-payment2.comoyo.com/transactions/hO0_YDAGSqyfCq3QlFM1fw in another webview to show Payment page.
The user will do the payment on the page.

Once the user successfully pays the payment, He will be redirected to http://stagingtv.universetv.net/digi/payment.php?status=success page.

Intercept the Redirect and Check for Rights again.

If the User has Right to see premium. Show him the premium contents.


Note : The Right response is pretty vast. We need to clarify the API response from Mehedi bhai.

cheers!

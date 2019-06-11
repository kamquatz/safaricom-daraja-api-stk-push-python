# Make an STK push with Daraja API
This is a simple tutorial on how to use the Lipa na M-Pesa Online Payment API to make an STK push and promopt users to make payments through M-pesa (C2B payments) using **Flask** and **Flask-RESTful**. It makes the payment process easy for customers by reducing the burden of having to remember the Paybill Number, account number and amount of the transaction in question. This is done by prompting users only to enter their M-pesa PIN.
Lipa na M-Pesa Online Payment API is used to initiate a M-Pesa transaction on behalf of a customer using STK Push

### Steps of involved
Thanks to [Peter Njeru](https://peternjeru.co.ke) for this explanation.

1. The Business sets the data in the request and sends it
2. The API receives the request and validates it internally first, then sends you an acknowledgement response.
3. The API then sends an STK Push request to the target customer's mobile phone. The customer's phone has to be online and unlocked to receive the request.
4. The customer confirms the payment amount via the message displayed on-screen, then either enters the PIN or cancels the request accordingly.
5. The API receives the customer's response. If the response is a negative, it cancels the transaction and sends a corresponding callback to the initiating 3rd party via the predefined callback URL in the initial request, with the info on why the transaction was cancelled. The possible negative responses could be due to the following scenarios:
    * An invalid PIN entered by the customer
    * Timeout due to customer not entering the PIN within a given time period (usually 1 min 30 secs)
    * The customer's SIM card not having the STK applet on it
    * A literal request cancellation by the user on their phone
    * Another STK transaction is already underway on the customer's phone (no more than one request can be processed at the same time on the same phone)

6. If the PIN is correct, it means the customer accepted the request. The API forwards the transaction to M-Pesa.
7. M-Pesa automatically processes the request, then sends the response back to the API system which then forwards it to you via the callback URL specified in your initial request. Here, the callback can also either be a success or failure, just like a normal C2B transaction.
8. There are no repeat calls for failed callbacks, thus if the API is unable to send the callback to you, you have the Transaction Status Query API to confirm the status of the request, or also confirm via the M-Pesa Org. portal.

## Getting a Daraja App
The fisrt step is to go to [Safaricom Developer's Website](https://developer.safaricom.co.ke) and get an account.

#### Creating a sandbox app
Got to [Creatind sandbox apps](https://developer.safaricom.co.ke/user/me/apps).

Creating an app involves setting an app name and choosing the API products for which you will want to use. API products are a business package of the available APIs and the rules for access built around them.
For this case make sure to use Lipa na Mpesa onlie.

##### Consumer key and consumer secret

Go your dashboard and select your new app.
The Keys tab shows the app keys (**CONSUMER_KEY** & **CONSUMER_SECRET**), the date they were issued and their expiry period. Sandbox keys do not expire.


## Making the request

#### Authentication
Before you make any request you have to get an *access_token* to authenticate your app. You do this by making an OAuth API request to generate an *access_token* for your app.

You will need a Basic Auth over HTTPS authorization token.
```

    consumer_key = "YOUR_APP_CONSUMER_KEY"
    consumer_secret = "YOUR_APP_CONSUMER_SECRET"
    api_URL = "https://sandbox.safaricom.co.ke/oauth/v1/generate?grant_type=client_credentials"

    # make a get request using python requests liblary
    r = requests.get(api_URL, auth=HTTPBasicAuth(consumer_key, consumer_secret))

```
Make the request and get your *access_token* from the response body
```
r.json()['access_token']

```




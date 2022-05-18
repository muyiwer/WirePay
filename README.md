
This is a JavaScript library for implementing WayaPay payment gateway

## Get Started

This Javascript library provides a wrapper to implement WayaPay Payment to your application via CDN
### Usage

This library can be implemented into your application via CDN using Javascript or JQuery

### Script Source
#### Production : 
#### Test: 

### Sample Function Request and Responses

#### Request for calling InitializePayment function.

To initialize the transaction, you'll need to pass information such as email, customer object, amount,merchantId, wayaPublicKey, etc. Email and amount,merchantId,description, wayaPublicKey are required. You can also pass any other additional information in the metadata object field. Here is the full list of parameters you can pass:
|Param       | Type                 | Default    | Required | Description                      
| :------------ | :------------------- | :--------- | :------- | :-------------------------------------------------
| amount	| `string`			   | undefined      | `true`  | Amount you want to debit customer e.g 1000.00, 10.00...
| description      | `string`             | undefined   | `true`  | description of the transaction
| email | `string`             | undefined       | `true`  | Email address of customer
| wayaPublicKey       | `string`        | undefined | `true`  | Your public key from wayaPay.
| currency      | `number`  |  `NGN`    | `false`   | Currency charge should be performed in. Allowed only `566`.
| merchantId      | `string`  |  undefined    | `true`   | merchant unique identification.
| mode      | `string`  |  `Debug`    | `true`   | Allowed values are `Debug` or `Live`.
| customer      | `object`  |  `undefined`    | `true`   | this includes `name`(requred) , `email`(required) and `phoneNumber`(optional) of the customer.

#### Response from calling InitializePayment function

|Param       | Type                 | Description                      
| :------------ | :------------------- | :-------------------------------------------------
| success	| `boolean`			 | Shows whether the intialise payment function call was successful or not
| message | `string`  | description of the response data
| data | `object`          | it includes `authorizeUrl` which is the url that you will use to redirect your customers to make payment, 
`transactionId`,  `customerName` and `customerAvoid`.


#### Request for calling VerifyPayment function.

To verify payment, you'll need to pass information such as publicKey, transactionId and mode are required. Here is the full list of parameters you can pass:
|Param       | Type                 | Default    | Required | Description                      
| :------------ | :------------------- | :--------- | :------- | :-------------------------------------------------
| transactionId      | `string`             | undefined   | `true`  | Unique case sensitive transaction identification

#### Response from calling VerifyPayment function
|Param       | Type                 | Description                      
| :------------ | :------------------- | :-------------------------------------------------
| success	| `boolean`			 | Shows whether the intialise payment function call was successful or not
| message | `string`  | description of the response data

#### data object
|Param       | Type                 | Description    
| :------------ | :------------------- | :-------------------------------------------------
| Amount | `string`  | amount debited from customer
| Description | `string`  | description of the transaction
| Currency | `string`  | amounnt currency
| Status | `string`  | PENDING, SUCCESSFUL, FAILED
| productName | `string`  | name of product rendered to customer
| businessName | `string`  | name of merchant business
| Fee | `string`  | conveniency fee
| customer | `object`  | customer details


```html
<!DOCTYPE html>
<html>
<style>
    body {
        margin-top: 60px;
        width: 30%;
        left: 0;
        right: 0;
        margin-left: 500px;
    }

    input[type=text],
    input[type=number],
    select {
        width: 100%;
        padding: 12px 20px;
        margin: 8px 0;
        display: inline-block;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
    }

    input[type=submit] {
        width: 100%;
        background-color: #4CAF50;
        color: white;
        padding: 14px 20px;
        margin: 8px 0;
        border: none;
        border-radius: 4px;
        cursor: pointer;
    }

    input[type=submit]:hover {
        background-color: #45a049;
    }

    div {
        border-radius: 5px;
        background-color: #f2f2f2;
        padding: 20px;
    }
</style>

<body>

    <h3>Merchant APP(WayaPayTest)</h3>

    <div>
        <p style="text-align: center;"><span id="error" style="color: red; font-weight: 600;"> </span>
        </p>
        <p style="text-align: center;"><span id="description"> </span> <span id="amount"
                style="font-weight: bold;"></span>
        </p>
        <form method="POST">
            <label for="fname">Full Name</label>
            <input required type="text" id="fullName" name="fullName" placeholder="Your full name..">
            <label for="email">Email</label>
            <input required type="text" id="email" name="firstname" placeholder="Your email..">
            <label for="Amount">Amount</label>
            <input required type="number" id="fee" name="amount" placeholder="Amount">
            <label for="phoneNum">Phone Number</label>
            <input required type="text" id="phoneNumber" name="phoneNumber" placeholder="Your phone number..">

            <input id="pay" type="submit" value="Pay">
        </form>
    </div>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"
        integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4=" crossorigin="anonymous"></script>

    <!-- <script type="module" src="./index.js"></script> --->
    <!--For Production Use the commented script -->
    <!-- <script data-main="scripts/app" src="https://myWayapay.com:6002/WayaPay.min.js"></script>  -->
    <script data-main="scripts/app" src="http://wayapayplugin-001-site1.itempurl.com/wayapay.min.js"></script>

    <script>
        $(document).ready(function () {
            const params = new URLSearchParams(window.location.search);
            const transactionId = params.get('transactionid'); //From the callback url/current url or any other way you can better implement it;
            WayaPay.VerifyPayment({
                transactionId: transactionId,
                mode: "Debug"
            }).then((response) => {
                let amount = response?.data?.Amount;
                if (response.success) {
                    //Transaction successful at payment gateway
                    $("#amount").text("You have payed NGN" + amount + " successfully");
                } else {
                    //Transaction not successful at payment gateway
                    if (transactionId)
                        $("#amount").text("Transaction not successful. Try again later")
                }
            });

            $("form").submit(function (e) {
                e.preventDefault();
                var email = $("#email").val();
                var amount = $("#fee").val();
                var fullName = $("#fullName").val();
                var phoneNumber = $("#phoneNumber").val();
                $("#pay").val("Paying..........");
                WayaPay.InitializePayment({
                    amount: amount + ".00",
                    mode: "Debug",
                    amount: amount + ".00",
                    description: "Order from" + fullName,
                    currency: 566,
                    fee: 1,
                    customer: {
                        name: fullName,
                        email: email,
                        phoneNumber: phoneNumber
                    },
                    merchantId: "MER_qZaVZ1645265780823HOaZW",
                    wayaPublicKey: "WAYAPUBK_TEST_0x3442f06c8fa6454e90c5b1a518758c70",

                    callbackUrl: `${window.location.href}?transactionid=${transactionId}`,
                }).then(response => {
                    if (response.success) {
                        window.location.href = response.data.authorizeUrl
                    } else {
                        $("#pay").val("");
                        $("#amount").text(response.message);
                    }

                });
            });

        });
    </script>
</body>

</html>

```



Please checkout [Wayapay Documentation](https://github.com) other ways you can integrate with our plugin
## Deployment

REMEMBER TO CHANGE THE MODE ON REQUEST OF THE FUNCTIONS WHEN DEPLOYING ON A LIVE/PRODUCTION SYSTEM


## How can I thank you?

Why not star the github repo? I'd love the attention! Why not share the link for this repository on Twitter or Any Social Media? Spread the word!

Don't forget to [follow me on twitter](https://twitter.com/muyiTechBadtGuy)!

Thanks!
Olumuyiwa Aro.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
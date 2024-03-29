# React Card Component with Bkash Checkout Integration

## Features

- Displays product image, name, price, and rating.
- Implements Bkash checkout integration for seamless payments.
- Provides a responsive and visually appealing design.

## Installation

1. Call backend bkash api:

   ```bash
    await axios
     .post("http://localhost:8000/bkash-checkout", {
       amount: 1200 || 0,
       orderID: "123456", // add your orderID
       reference: "ref01", // add your reference
       callbackURL: "http://localhost:8000/bkash-callback", //bkash backend callback url
    })
     .then((response) => {
      console.log(response?.data?.bkashURL)
    window.location.href = response?.data?.bkashURL
   })
    .catch((error) => {
     console.log("An error occurred:", error)
   })
   }
   ```

   # bKash Payment Gateway (Tokenized-Checkout)

   A Library to integrate bKash Payment Gateway on your backend application.
   Written in Javascript & covered all bKash Checkout (URL) API.

# NPM Package Link

```
https://www.npmjs.com/package/bkash-payment

```

# NPM Install

```
npm install bkash-payment
```

# Yarn Install

```
yarn add bkash-payment
```

# Sandbox Config

```
const bkashConfig = {
  base_url : 'https://tokenized.sandbox.bka.sh/v1.2.0-beta',
  username: 'sandboxTokenizedUser02',
  password: 'sandboxTokenizedUser02@12345',
  app_key: '4f6o0cjiki2rfm34kfdadl1eqq',
  app_secret: '2is7hdktrekvrbljjh44ll3d9l1dtjo4pasmjvs5vl5qr3fug4b'
 }
```

# Live Config

```
const bkashConfig = {
 base_url : 'https://tokenized.pay.bka.sh/v1.2.0-beta',
 username: 'your_bkash_username',
 password: 'your_bkash_password',
 app_key: 'your_bkash_app_key',
 app_secret: 'your_bkash_app_secret'
}
```

# Initializing the library

Node (bkashPaymentGateway.js)

```
const express = require('express')
const { createPayment, executePayment, queryPayment, searchTransaction, refundTransaction } = require('bkash-payment')
const cors = require('cors')
const bodyParser = require('body-parser')
const app = express()
const port = 3000

// Middleware setup
app.use(cors())
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({ extended: true }))

// bKash Credentials setup
const bkashConfig = {
  base_url : 'bKash_base_url',
  username: 'your_bkash_username',
  password: 'your_bkash_password',
  app_key: 'your_bkash_app_key',
  app_secret: 'your_bkash_app_secret'
 }

app.post("/bkash-checkout", async(req, res) => {
  try {
    const { amount, callbackURL, orderID, reference } = req.body
    const paymentDetails = {
      amount: amount || 10,                                                 // your product price
      callbackURL : callbackURL || 'http://127.0.0.1:3000/bkash-callback',  // your callback route
      orderID : orderID || 'Order_101',                                     // your orderID
      reference : reference || '1'                                          // your reference
    }
    const result =  await createPayment(bkashConfig, paymentDetails)
    res.redirect(result?.bkashURL)
  } catch (e) {
    console.log(e)
  }
})

app.get("/bkash-callback", async(req, res) => {
  try {
    const { status, paymentID } = req.query
    let result
    let response = {
      statusCode : '4000',
      statusMessage : 'Payment Failed'
    }
    if(status === 'success')  result =  await executePayment(bkashConfig, paymentID)

    if(result?.transactionStatus === 'Completed'){
      // payment success
      // insert result in your db
    }
    if(result) response = {
      statusCode : result?.statusCode,
      statusMessage : result?.statusMessage
    }
   res.redirect('your_frontend_route')  // Your frontend route
  } catch (e) {
    console.log(e)
  }
})

// Add this route under admin middleware
app.post("/bkash-refund", async (req, res) => {
  try {
    const { paymentID, trxID, amount } = req.body
    const refundDetails = {
      paymentID,
      trxID,
      amount,
    }
    const result = await refundTransaction(bkashConfig, refundDetails)
    res.send(result)
  } catch (e) {
    console.log(e)
  }
})

app.get("/bkash-search", async (req, res) => {
  try {
    const { trxID } = req.query
    const result = await searchTransaction(bkashConfig, trxID)
    res.send(result)
  } catch (e) {
    console.log(e)
  }
})

app.listen(port, () =>
  console.log(`Example app listening at http://127.0.0.1:${port}`)
)

```

# Create a Payment

```
  const paymentDetails = {
      amount: 10,                                      // your product price
      callbackURL : 'http://127.0.0.1:3000/callback',  // your callback route
      orderID : 'Order_101',                           // your orderID
      reference : '1'                                  // your reference
    }
const result =  await createPayment(bkashConfig, paymentDetails)
```

# Execute The Payment

```
const result =  await executePayment(bkashConfig, paymentID)
```

# Query a Payment

```
const result =  await queryPayment(bkashConfig, paymentID)
```

# Search a Transaction

```
const result =  await searchTransaction(bkashConfig, trxID)
```

# Refund a Transaction

```
const refundDetails = {
paymentID: "TR101001111",
trxID: "ASFFDDD8G",
amount: 10
}
const result =  await refundTransaction(bkashConfig, refundDetails)
```

# Sandbox Testing Number,OTP & PIN

```
For Successful Payment:  01619777283 , 01877722345
For Insufficient Payment: 01823074817
OTP: 123456
PIN: 12121
```

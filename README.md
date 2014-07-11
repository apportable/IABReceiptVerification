IABReceiptVerification
======================

Sample Google IAB receipt verification for an Apportable-powered app on Android using the StoreKit API.

In your app
-----------

Make sure you have a `SKPaymentTransactionObserver` registered with `[SKPaymentQueue defaultQueue]` and your observer
implements the `[SKPaymentTransactionObserver paymentQueue:updatedTransactions:]` method.

```objectivec
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transactions
{
    for (SKPaymentTransaction *txn in transactions)
    {
        if (txn.transactionState == SKPaymentTransactionStatePurchased)
        {
#ifdef ANDROID
            // Use Apportable-specific SKPaymentTransactionDecodeReceipt() helper method, and
            // SKPaymentTransactionReceiptSignedData and SKPaymentTransactionReceiptSignature keys
            NSDictionary *receiptDict = SKPaymentTransactionDecodeReceipt(txn.transactionReceipt);
            NSString *signedData = [receiptDict objectForKey:SKPaymentTransactionReceiptSignedData];
            NSString *signature = [receiptDict objectForKey:SKPaymentTransactionReceiptSignature];

            // Now send signature and signedData to your server for verification (should use HTTPS)
            ...;
#endif
        }
        ...;
    }
}
```

Sample Ruby IAB Verification
----------------------------

```ruby
require 'rubygems'
require 'openssl'
require 'base64'

base64EncodedPublicKey = 'BASE64-ENCODED RSA PUBLIC KEY FROM YOUR GOOGLE PLAY DEVELOPER CONSOLE "SERVICES & APIS TAB"'
signedData = 'SIGNEDDATA SENT FROM THE APP'
signature = 'SIGNATURE SENT FROM THE APP'

key = OpenSSL::PKey::RSA.new(Base64.decode64(base64EncodedPublicKey))
verified = key.verify(OpenSSL::Digest::SHA1.new, Base64.decode64(signature), signedData)

if verified
    print "verified!\n"
else
    print "NOT verified!\n"
end
```

Sample PHP IAB Verification
---------------------------

```php
$base64EncodedPublicKey = 'BASE64-ENCODED RSA PUBLIC KEY FROM YOUR GOOGLE PLAY DEVELOPER CONSOLE "SERVICES & APIS TAB"';
$signedData = 'SIGNEDDATA SENT FROM THE APP';
$signature = 'SIGNATURE SENT FROM THE APP';

$key = openssl_pkey_get_public($base64EncodedPublicKey);
$verified = (openssl_verify($signedData, base64_decode($signature), $key, OPENSSL_ALGO_SHA1) > 0);

if ($verified) {
    // verified ok
} else {
    // NOT verified
}
```


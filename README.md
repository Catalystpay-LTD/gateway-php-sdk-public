# Catalyst Pay SDK for PHP

#This is a PHP SDK for integrating with the Catalyst Pay API to handle payment processing.

## Important
To obtain the password that protects the `.zip` file, please contact **support@catalystpay.com**.

## Installation

`composer require catalystpay/gateway-php-sdk`

## Catalyst Pay Integration Guide
### 1) Go to root project and you can use this CatalystPaySDK class in your PHP code as follows

```php

require_once 'vendor/autoload.php';

use CatalystPay\CatalystPayResponseCode;
use CatalystPay\CatalystPaySDK;
try {
    // Configured  CatalystPaySDK
    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );

    // Form Values defined variable
    $data = [
        'amount' => 92.00,
        'currency' => 'EUR',
        'paymentType' => CatalystPaySDK::PAYMENT_TYPE_DEBIT,
        'billing.city' => 'surat',
        'billing.country' => 'DE',
        'billing.street1' => 'test',
        'billing.postcode' => 394210,
        'customer.email' => 'test@gmail.com',
        'customer.givenName' => 'John Smith',
        'customer.surname' => 'Don',
        'customer.ip' => '192.168.0.1'
    ];

    //Prepare Check out form 
    $responseData = $CatalystPaySDK->prepareCheckout($data);
    // Check if isPrepareCheckoutSuccess is true
    if ($responseData->isCheckoutSuccess()) {
        //Show checkout success
        $infoMessage = 'The checkout returned ' . $responseData->getResultCode() . ' instead of ' . CatalystPayResponseCode::CREATED_CHECKOUT;
            $wpwlOptions = "{
                iframeStyles: {
                    'card-number-placeholder': {
                        'color': '#ff0000',
                        'font-size': '16px',
                        'font-family': 'monospace'
                    },
                    'cvv-placeholder': {
                        'color': '#0000ff',
                        'font-size': '16px',
                        'font-family': 'Arial'
                    }
                }
            }";
            
       // Payment with card
        $formData = [
            'checkoutId' => $responseData->getId(),
            'shopperResultUrl' => 'http://localhost/CatalystPay-php-sdk/copy_and_pay_result.php',
            'dataBrands' => [
                CatalystPaySDK::PAYMENT_BRAND_VISA,
                CatalystPaySDK::PAYMENT_BRAND_MASTERCARD,
                CatalystPaySDK::PAYMENT_BRAND_AMEX
                ],
            'wpwlOptions' => $wpwlOptions
        ];
        echo $CatalystPaySDK->createPaymentForm($formData);

        // Payment with google pay
        $formData2 = [
            'checkoutId' => $responseData->getId(),
            'shopperResultUrl' => 'http://localhost/CatalystPay-php-sdk/copy_and_pay_result.php',
            'dataBrands' => [CatalystPaySDK::PAYMENT_BRAND_GOOGLE_PAY],
            'wpwlOptions' => $wpwlOptions
        ];
        echo $CatalystPaySDK->createPaymentForm($formData2);

        // Payment with rocket fuel
        $formData4 = [
            'checkoutId' => $responseData->getId(),
            'shopperResultUrl' => 'http://localhost/CatalystPay-php-sdk/copy_and_pay_result.php',
            'dataBrands' => [CatalystPaySDK::PAYMENT_BRAND_ROCKET_FUEL],
            'wpwlOptions' => $wpwlOptions
        ];
        echo $CatalystPaySDK->createPaymentForm($formData4);
    } else {
        echo "The Prepare Checkout was not successful";
    }
} catch (Exception $e) {
   echo $e->getMessage();
}
```

### 2) Go to root project and you can use this CatalystPaySDK class to get payment status in your PHP code as follows

```php

require_once 'vendor/autoload.php';
use CatalystPay\CatalystPaySDK;

try {

    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );
    $errorMsg = '';
    // Handle the payment status as needed
    if (isset(($_GET['id']))) {
        $checkoutId = $_GET['id'];
        $responseData = $CatalystPaySDK->getPaymentStatus($checkoutId);
        print_r($responseData->getApiResponse()); // Get payment status response 
        var_dump($responseData->isPaymentStatus()); // Check  payment status value True or False

        // Check IF payment  status is success 
        if ($responseData->isPaymentStatus()) {

            // Check IF payment transaction pending is true
            if ($responseData->isPaymentTransactionPending()) {
                echo 'The transaction should be pending, but is ' . $responseData->getResultCode();
            } elseif ($responseData->isPaymentRequestNotFound()) { // Check IF payment request not found is true
                echo'No payment session found for the requested id, but is ' . $responseData->getResultCode();
            }
        }
    }
} catch (Exception $e) {
    echo 'Error: ' . $e->getMessage();
}

```

## Gateway Pay Registration Tokens

### 1) Go to root project and you can use this CatalystPaySDK class for Prepare the checkout and create the registration form in your PHP code as follows

```php
require_once 'vendor/autoload.php';
use CatalystPay\CatalystPayResponseCode;
use CatalystPay\CatalystPaySDK;

try {
    // Configured  CatalystPaySDK
    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $isCreateRegistration = 'true';
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );

    // Form Values defined variable
    $data = [
        'testMode' => CatalystPaySDK::TEST_MODE_EXTERNAL,
        'createRegistration' => $isCreateRegistration
    ];
    //Prepare Check out form 
    $responseData = $CatalystPaySDK->prepareRegisterCheckout($data);

    //  print_r($responseData);
    // var_dump($responseData->isCheckoutSuccess());
    // Check if checkout success is true
    if ($responseData->isCheckoutSuccess()) {
        //Show checkout success
        $infoMessage = 'The checkout returned ' . $responseData->getResultCode() . ' instead of ' . CatalystPayResponseCode::CREATED_CHECKOUT;

        $wpwlOptions = "{
            iframeStyles: {
                'card-number-placeholder': {
                    'color': '#ff0000',
                    'font-size': '16px',
                    'font-family': 'monospace'
                },
                'cvv-placeholder': {
                    'color': '#0000ff',
                    'font-size': '16px',
                    'font-family': 'Arial'
                }
            }
        }";

        // Payment with card
        $formData = [
            'checkoutId' => $responseData->getId(),
            'shopperResultUrl' => 'http://localhost/CatalystPay-php-sdk/registration_token_payment.php',
            'dataBrands' => [
                CatalystPaySDK::PAYMENT_BRAND_VISA ,
                CatalystPaySDK::PAYMENT_BRAND_MASTERCARD,
                CatalystPaySDK::PAYMENT_BRAND_AMEX
                ],
            'wpwlOptions' => $wpwlOptions
        ];

        echo $CatalystPaySDK->getCreateRegistrationPaymentForm($formData);       
    } else {
        echo "The Prepare Checkout was not successful";
    }
} catch (Exception $e) {
   echo $e->getMessage();
} 
```


### 2) Go to root project and you can use this CatalystPaySDK class to get the registration status and Send payment using the token in your PHP code as follows

```php
// Example usage
try {
    $errorMsg = '';
    $isSuccessful = false;
    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );

    // Handle the payment Registration status as needed
    if (isset(($_GET['id']))) {
        $checkoutId = $_GET['id'];
        $responseData = $CatalystPaySDK->getRegistrationStatus($checkoutId);
        
        // Check IF payment registration status is success 
        if ($responseData->isRegistrationStatus()) {
            $paymentId = $responseData->getId(); // get the payment id

            // Form Values defined variable
            $data = [
                'paymentBrand' => CatalystPaySDK::PAYMENT_BRAND_VISA,
                'paymentType' =>  CatalystPaySDK::PAYMENT_TYPE_DEBIT,
                'amount' => 92.00,
                'currency' => 'EUR',
                'standingInstructionType' =>  CatalystPaySDK::STANDING_INSTRUCTION_TYPE_UNSCHEDULED,
                'standingInstructionMode' =>  CatalystPaySDK::STANDING_INSTRUCTION_MODE_INITIAL,
                'standingInstructionSource' => CatalystPaySDK::STANDING_INSTRUCTION_SOURCE_CIT,
                'testMode' => CatalystPaySDK::TEST_MODE_EXTERNAL
            ];

            // Send payment using the token
            $registerPayment = $CatalystPaySDK->sendRegistrationTokenPayment($paymentId, $data);

            //check if payment Successful true
            $isPaymentSuccessful =  $registerPayment->isSuccessful();

            print_r($registerPayment->getApiResponse()); // Get send payment Registration response
            // Check IF payment transaction pending is true
            if ($registerPayment->isPaymentTransactionPending()) {
               echo'The transaction should be pending, but is ' . $registerPayment->getResultCode();
            } elseif ($registerPayment->isPaymentRequestNotFound()) { // Check IF payment request not found is true
               echo 'No payment session found for the requested id, but is ' . $registerPayment->getResultCode();
            }
        }
    }
} catch (Exception $e) {
    echo 'Error: ' . $e->getMessage();
}
```

## Transaction Reports

### 1) Go to root project and you can use this CatalystPaySDK class for transaction reports allow to retrieve detailed transactional data from the platform in your PHP code as follows

```php
require_once 'vendor/autoload.php';

use CatalystPay\CatalystPaySDK;

// Example usage
try {

    // Configured  CatalystPaySDK
    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );

    // Get Transaction by id
    $responseData = $CatalystPaySDK->getTransactionById(['id' => '8ac7a4a1845f7e19018461a00b366a74', 'includeLinkedTransactions' => 'true', 'paymentTypes' => 'DB,3D','paymentMethods' => 'CC,DC']);
    print_r($responseData->getApiResponse()); // Get Transaction response 

       // Get Transaction by merchant ID 
    $transactionMerchant = $CatalystPaySDK->getMerchantTransactionById('test123');
    print_r($transactionMerchant->getApiResponse());

    // Get transactions for a specified time frame
    $transactionSpecifiedTimeFrame = $CatalystPaySDK->getTransactionByDateFilter(['dateFrom' => '2023-01-01 00:00:00', 'dateTo' => '2023-01-01 01:00:00','merchantTransactionId' => 'test123', 'paymentTypes' => 'DB,3D', 'paymentMethods' => 'CC,DC', 'limit' => 20]);
    print_r($transactionSpecifiedTimeFrame->getApiResponse());

    // Get transactions for a specified time frame with pagination
    $transactionPagination = $CatalystPaySDK->getTransactionByDateWithPagination(['dateFrom' => '2023-01-01 00:00:00', 'dateTo' => '2023-01-01 01:00:00','merchantTransactionId' => 'test123', 'paymentTypes' => 'DB,3D', 'paymentMethods' => 'CC,DC', 'pageNo' => 2]);
    print_r($transactionPagination);
} catch (Exception $e) {
    echo  $e->getMessage();
}
```

### 2) Go to root project and you can use this CatalystPaySDK class for settlement reports allow to retrieve detailed settlements data from the platform in your PHP code as follows
```php
require_once 'vendor/autoload.php';

use CatalystPay\CatalystPaySDK;

// Example usage
try {

    // Configured  CatalystPaySDK
    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );

    // Get summary level information for a certain date and/or settlement currency
    $settlementReportBySummary = $CatalystPaySDK->getSettlementReportBySummary(['dateFrom' => '2015-08-01', 'dateTo' => '2015-08-02', 'currency' => 'EUR', 'testMode' => CatalystPaySDK::TEST_MODE_INTERNAL]);
    print_r($settlementReportBySummary->getApiResponse());

    //Get further details for a particular aggregation id.
    $responseData = $CatalystPaySDK->getDetailLevelById(['id' => '8a82944a4cc25ebf014cc2c782423202','sortValue'=>'SettlementTxDate','sortOrder'=>'ASC', 'testMode' => CatalystPaySDK::TEST_MODE_INTERNAL]);
    print_r($responseData->getApiResponse());

    // Get detail level with pagination
    $settlementReportPagination = $CatalystPaySDK->getDetailLevelByIdWithPagination(['id' => '8a82944a4cc25ebf014cc2c782423202','reconciliationType'=>'SETTLED' ,'testMode' => CatalystPaySDK::TEST_MODE_INTERNAL, "pageNo" => 2]);
    print_r($settlementReportPagination);
} catch (Exception $e) {
    echo  $e->getMessage();
}

```

## Backoffice Operations

### 1) Go to root project and you can use this CatalystPaySDK class for  You can perform different types of backoffice operations for a rebill is performed against a previous payment, using our REST API from the platform in your PHP code as follows

```php
require_once 'vendor/autoload.php';

use CatalystPay\CatalystPaySDK;

// Example usage
try {

    // Configured  CatalystPaySDK
    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );
 
    // Capture an authorization
    $dataCapturePaymentsByOperations = [
        'paymentId' => '8a82944a4cc25ebf014cc2c782423202',
        'paymentType' => 'CP',
        'amount' => '10.00',
        'currency' => 'EUR',
    ];

    $paymentsByCaptureOperations = $CatalystPay->paymentsByOperations($dataCapturePaymentsByOperations);
    print_r($paymentsByCaptureOperations);

    // Refund a payment
    $dataRefundPaymentsByOperations = [
        'paymentId' => '8a82944a4cc25ebf014cc2c782423202',
        'paymentType' => 'RF',
        'amount' => '10.00',
        'currency' => 'EUR',
    ];

    $paymentsRefundByOperations = $CatalystPay->paymentsByOperations($dataRefundPaymentsByOperations);
    print_r($paymentsRefundByOperations);

    // Receipt for payment
    $dataReceiptPaymentsByOperations = [
        'paymentId' => '8a82944a4cc25ebf014cc2c782423202',
        'paymentType' => 'RC',
        'amount' => '10.00',
        'currency' => 'EUR',
    ];

    $paymentsReceiptByOperations = $CatalystPay->paymentsByOperations($dataReceiptPaymentsByOperations);
    print_r($paymentsReceiptByOperations);
       
    // Rebill for payment
    $dataRebillPaymentsByOperations = [
        'paymentId' => '8a82944a4cc25ebf014cc2c782423202',
        'paymentType' => 'RB',
        'amount' => '10.00',
        'currency' => 'EUR',
    ];

    $paymentsRebillByOperations = $CatalystPay->paymentsByOperations($dataRebillPaymentsByOperations);
    print_r($paymentsRebillByOperations);
     
    // Reverse a payment
    $dataReversePaymentsByOperations = [
        'paymentId' => $payCardRequest->getId(),
        'paymentType' => 'RV',
    ];

    $paymentsReverseByOperations = $CatalystPay->paymentsByOperations($dataReversePaymentsByOperations);
    print_r($paymentsReverseByOperations);
} catch (Exception $e) {
    echo  $e->getMessage();
}

```

### 2) Go to root project and you can use this CatalystPaySDK class for  You can perform different types of backoffice operations for Credit (stand-alone refund) using our REST API from the platform in your PHP code as follows

```php
require_once 'vendor/autoload.php';

use CatalystPay\CatalystPaySDK;

// Example usage
try {

    // Configured  CatalystPaySDK
    $token = 'OGE4Mjk0MTc0YjdlY2IyODAxNGI5Njk5MjIwMDE1Y2N8c3k2S0pzVDg=';
    $entityId = '8a8294174b7ecb28014b9699220015ca';
    $isProduction = false;
    $CatalystPaySDK = new CatalystPaySDK(
        $token,
        $entityId,
        $isProduction
    );

    $dataPaymentsByOperations =[
        'paymentType'=> 'CD',
        'amount'=> '10.00',
        'currency'=> 'EUR', 
        'paymentBrand' =>'VISA',
        'cardNumber' => '4200000000000000',
        'cardExpiryMonth' => '12',
        'cardExpiryYear' => '2025',
        'cardHolder' => 'Jane Jones',        
    ];
    
    $paymentsByOperations = $CatalystPaySDK->CreditStandAloneRefund($dataPaymentsByOperations);
    $resultPaymentsByOperations = $paymentsByOperations->getApiResponse(); 
    print_r($resultPaymentsByOperations);
} catch (Exception $e) {
    echo  $e->getMessage();
}

```

## Webhook Notifications

When using an Asynchronous workflow, you need to parse the incoming extension in order to ensure its authenticity and verify it against Catalyst Payc notification server.

Example:

```php
require_once "config.php";
require_once 'vendor/autoload.php';
use CatalystPay\Notification;

 // Check webhook configuration like auth tag , iv etc 
  if(!empty($_SERVER)){
    $keyFromConfiguration="4FBB6197CD8D21651452D444B1AB5B3C2204F1308E1E1B45E0B6BEF50E64ABC9";
    $authTag = $_SERVER['HTTP_X_AUTHENTICATION_TAG'];
    $iv = $_SERVER['HTTP_X_INITIALIZATION_VECTOR'];
  }

  // Check webhook body
  if(!empty(file_get_contents("php://input"))) {
    $httpBody = file_get_contents("php://input");
    $notification = new Notification($keyFromConfiguration,$httpBody,$iv,$authTag);
  
    //Get decrypted message from webhook  
   echo  $decrypted_data = $notification->getDecryptedMessage();
  }   
```

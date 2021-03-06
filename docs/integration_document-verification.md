![Document Verification](images/document_verification.png)

# Document Verification SDK for Android
Document Verification SDK is a powerful solution to enable scanning various types (Utility Bill, Bank statement and many others) of your customer's documents in your mobile application within seconds, also supporting data extraction from Utility Bills and Bank Statements from US, UK, France and Canada, as well as US Social Security Cards.

## Table of Content

- [Release notes](#release-notes)
- [Setup](#setup)
- [Integration](#integration)
- [Configuration](#configuration)
- [Customization](#customization)
- [SDK Workflow](#sdk-workflow)
- [Callback](#callback)

## Release notes
For technical changes, please read our [transition guide](transition-guide_document_verification.md)

SDK version: 2.10.1.

## Setup
The [basic setup](../README.md#basic-setup) is required before continuing with the following setup for DocumentVerification.

Using the SDK requires an activity declaration in your AndroidManifest.xml.

```
<activity
  android:name="com.jumio.dv.DocumentVerificationActivity"
	android:theme="@style/Theme.DocumentVerification"
	android:hardwareAccelerated="true"
	android:configChanges="orientation|screenSize|screenLayout|keyboardHidden"/>
```

You can specify your own theme (see [Customization](#customizing-look-and-feel) chapter). The orientation can be sensor based or locked with the attribute `android:screenOrientation`.


## Integration

### Dependencies

| Dependency        | Mandatory           | Description       | Size (Jumio libs only) |
| ----------------- |:-------------------:|:------------------|:-------------------:|
| com.jumio.android:core:2.10.1@aar                    | x | Jumio Core library            | 3.84 MB |
| com.jumio.android:dv:2.10.1@aar                      | x | Document Verification library | 105.28 KB |
| com.android.support:appcompat-v7:27.0.2             | x | Android native library        | - |
| com.android.support:support-v4:27.0.2               | x | Android native library        | - |
| com.jumio.android:javadoc:2.10.1                     |   | Jumio SDK Javadoc             | - |

If an optional module is not linked, the scan method is not available but the library size is reduced.

Applications implementing the SDK shall not run on rooted devices. Use either the below method or a self-devised check to prevent usage of SDK scanning functionality on rooted devices.
```
DocumentVerificationSDK.isRooted(Context context);
```

Call the method `isSupportedPlatform` to check if the device is supported.
```
DocumentVerificationSDK.isSupportedPlatform();
```

Check the Android Studio sample projects to learn the most common use.

## Initialization
To create an instance for the SDK, perform the following call as soon as your activity is initialized.

```
private static String YOURAPITOKEN = ""; 
private static String YOURAPISECRET = "";

DocumentVerificationSDK documentVerificationSDK = DocumentVerificationSDK.create(yourActivity, YOURAPITOKEN, YOURAPISECRET, JumioDataCenter.US);
```
Make sure that your customer API token and API secret are correct, specify an instance
of your activity and provide a reference to identify the scans in your reports (max. 100 characters or `null`). If your customer account is in the EU data center, use `JumioDataCenter.EU` instead.

__Note:__ Log into your Jumio customer portal, and you can find your customer API token and API secret on the "Settings" page under "API credentials". We strongly recommend you to store credentials outside your app.

## Configuration

### Document type
Use `setType` to pass the document type.
```
documentVerificationSDK.setType("DOCUMENTTYPE");
```

Possible types:

*  BS (Bank statement)
*  IC (Insurance card)
*  UB (Utility bill, front side)
*  CAAP (Cash advance application)
*  CRC (Corporate resolution certificate)
*  CCS (Credit card statement)
*  LAG (Lease agreement)
*  LOAP (Loan application)
*  MOAP (Mortgage application)
*  TR (Tax return)
*  VT (Vehicle title)
*  VC (Voided check)
*  STUC (Student card)
*  HCC (Health care card)
*  CB (Council bill)
*  SENC (Seniors card)
*  MEDC (Medicare card)
*  BC (Birth certificate)
*  WWCC (Working with children check)
*  SS (Superannuation statement)
*  TAC (Trade association card)
*  SEL (School enrolment letter)
*  PB (Phone bill)
*  USSS (US social security card)
*  SSC (Social security card)
*  CUSTOM (Custom document type)

#### Custom Document Type
Use the following method to pass your custom document code. Maintain your custom document code within your Jumio customer portal under "Settings" - "Multi Document" - "Custom".
```
documentVerificationSDK.setCustomDocumentCode("YOURCUSTOMDOCUMENTCODE");
```

### Country
The country needs to be in format [ISO-3166-1 alpha 3](http://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) or XKX for Kosovo.
```
documentVerificationSDK.setCountry("USA");
```

### Transaction identifiers

Use the following property to identify the scan in your reports (max. 100 characters).
```
documentVerificationSDK.setMerchantReportingCriteria("YOURREPORTINGCRITERIA");
```

A callback URL can be specified for individual transactions constraints see chapter [Callback URL](#callback-url)). This setting overrides your Jumio customer settings.
```
documentVerificationSDK.setCallbackUrl("YOURCALLBACKURL");
```

You can also set an additional information parameter (max. 255 characters).

__Note:__ The additional information must not contain sensitive data like PII (Personally Identifiable Information) or account login.
```
documentVerificationSDK.setAdditionalInformation("ADDITIONALINFORMATION");
```


### Miscellaneous
Use the following property to identify the scan in your reports (max. 100 characters).
```
documentVerificationSDK.setMerchantScanReference("YOURSCANREFERENCE");
```

You can also set a customer identifier (max. 100 characters).
```
documentVerificationSDK.setCustomerId("CUSTOMERID");
```

__Note:__ The customer ID and merchant scan reference must not contain sensitive data like PII (Personally Identifiable Information) or account login.

Use setCameraPosition to configure the default camera (front or back).
```
documentVerificationSDK.setCameraPosition(JumioCameraPosition.FRONT);
```

Use setDocumentName to override the document label on Help screen.
```
documentVerificationSDK.setDocumentName(“YOURDOCNAME”);
```

## Customization

### Customizing look and feel
The SDK can be customized to fit your application’s look and feel by specifying `Theme.DocumentVerification` as a parent style of your own theme. Click on the element `Theme.DocumentVerification` in the manifest while holding Ctrl and Android Studio will display the available items.

### Customizing theme at runtime

To customize the theme at runtime, overwrite the theme that is used for Netverify in the manifest by adding the line of code below. Use the resource id of a customized theme that uses `Theme.Netverify` as parent.
```
documentVerificationSDK.setCustomTheme(CUSTOMTHEME);
```

## SDK Workflow

### Starting the SDK

To show the SDK, call the respective method below within your activity or fragment.

Activity: `DocumentVerificationSDK.start();` <br/>
Fragment: `startActivityForResult(documentVerificationSDK.getIntent(),DocumentVerificationSDK.REQUEST_CODE);`

__Note:__ The default request code is 300. To use another code, override the public static variable `DocumentVerificationSDK.REQUEST_CODE` before displaying the SDK.

### Retrieving information

Implement the standard `onActivityResult` method in your activity or fragment for successful scans (`Activity.RESULT_OK`) and user cancellation notifications (`Activity.RESULT_CANCELED`). Call `documentVerificationSDK.destroy()` once you received the result.

```
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	if (requestCode == DocumentVerificationSDK.REQUEST_CODE) {
		if (resultCode == Activity.RESULT_OK) {
			// OBTAIN PARAMETERS HERE
			// YOURCODE
		} else if (resultCode == Activity.RESULT_CANCELED) {
			// String scanReference = data.getStringExtra(DocumentVerificationSDK.EXTRA_SCAN_REFERENCE);
			// int errorCode = data.getIntExtra(DocumentVerificationSDK.EXTRA_ERROR_CODE, 0);
			// String errorMessage = data.getStringExtra(DocumentVerificationSDK.EXTRA_ERROR_MESSAGE);
			// YOURCODE
		}
        // CLEANUP THE SDK AFTER RECEIVING THE RESULT
        // if (documentVerificationSDK != null) {
        // 	documentVerificationSDK.destroy();
        // 	documentVerificationSDK = null;
        // }
	}
}
```

Class **_Error codes_**:

|Code        			| Message   | Description    |
| :--------------:|:----------|:---------------|
|100<br/>110<br/>130<br/>140<br/>150<br/>160| We have encountered a network communication problem | Retry possible, user decided to cancel |
|210<br/>220| Authentication failed | API credentials invalid, retry impossible |
|230| No Internet connection available | Retry possible, user decided to cancel |
|250| Cancelled by end-user | No error occurred |
|260| The camera is currently not available | Camera cannot be initialized, retry impossible |
|280| Certificate not valid anymore. Please update your application | End-to-end encryption key not valid anymore, retry impossible |

## Callback
To get information about callbacks, Netverify Retrieval API, Netverify Delete API and Global Netverify settings and more, please read our [page with server related information](https://github.com/Jumio/implementation-guides/blob/master/netverify/callback.md).

## Copyright

&copy; Jumio Corp. 268 Lambert Avenue, Palo Alto, CA 94306

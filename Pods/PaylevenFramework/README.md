# PaylevenFramework-iOS-CocoaPod

[![Build Status](https://travis-ci.org/conichiGMBH/PaylevenFramework-iOS-CocoaPod.svg?branch=master)](https://travis-ci.org/conichiGMBH/PaylevenFramework-iOS-CocoaPod)
[![Pod Version](http://img.shields.io/cocoapods/v/PaylevenFramework.svg?style=flat)](http://cocoadocs.org/docsets/PaylevenFramework/)
[![Pod Platform](http://img.shields.io/cocoapods/p/PaylevenFramework.svg?style=flat)](http://cocoadocs.org/docsets/PaylevenFramework/)
[![CocoaPods](https://img.shields.io/badge/Licence-MIT-brightgreen.svg?style=flat)]()

This contains the [Payleven InApp-SDK-iOS Framework](https://github.com/payleven/InApp-SDK-iOS) to be linked as a CocoaPod.

## Reason
The reason for making this repo was that the Payleven InApp-SDK-iOS framework is not available to integrate it via `CocoaPods`. And the only option is to integrate it via `vendored_frameworks` as a dependency to other `Podspec`. But unfortunately this leads to another problem related to `CocoaPods` - and it mentioned in that [issue](https://github.com/CocoaPods/CocoaPods/issues/5819). Now the framework can be integrated as a `dependency` in the `Podspec`

## Install via CocoaPods
``` ruby
pod 'PaylevenFramework'
```

Open the *Info.plist* and add the following key related to App Transport Security:
```	
      <key>NSAppTransportSecurity</key>
	    <dict>
	        <key>NSExceptionDomains</key>
	        <dict>
	            <key>payleven.de</key>
	            <dict>
	                <key>NSExceptionRequiresForwardSecrecy</key>
	                <false/>
	                <key>NSIncludesSubdomains</key>
	                <true/>
	            </dict>
	        </dict>
	    </dict>
```

## Original README

### Prerequisites
* You or your client is operating in one of the countries supported by payleven.
* You have signed up [here](https://service.payleven.com/uk/developer?product=apppay) and received your API key.

#### Code    

##### Authenticate your app
Use the unique API key to authenticate your app

	 [[PLVInAppClient sharedInstance] registerWithAPIKey:@”anAPIKey”];
	
##### Add a payment instrument
Create an object of `PLVCreditCardPaymentInstrument` class. 
If it's the first time you are trying to add a payment instrument for your user, you need to create a user token, based on the user's email address.
	
	    PLVCreditCardPaymentInstrument * tempCC = [PLVCreditCardPaymentInstrument createCreditCardPaymentInstrumentWithPan:@"12345678901234"
	                                                                                                           expiryMonth:@"11"
	                                                                                                            expiryYear:@"2020"
	                                                                                                                   cvv:@"111"
	                                                                                                         andCardHolder:@"Payleven Cardholder"];
	
	//Create User Token	
	[[PLVInAppClient sharedInstance] createUserToken:@"anEmailAdress@payleven.de"
	                                   withPaymentInstrument:tempCC
	                                             useCase:@"Business"
	                                           andCompletion:^(NSString *userToken, NSError *error){
	            
	            if (userToken) {
	        		//Success, this token should now be forwarded to your Backend...
	            } else {
	                //Error handling
	            }
	}];
					
	
	//Or simply add Payment Instrument to existing User Token
	[[PLVInAppClient sharedInstance] addPaymentInstrument:tempCC 
											  forUserToken:<Pass User Token you received from createUserToken:> 
											   withUseCase:@"Business" 
											 andCompletion:^(NSError* error) {
											    if (error) {
											       //Error handling
											    } else {
												   //Success
												}
	 }];

##### Get the payment instruments for a user token
Use the user token to retrieve the payment instruments associated to it and to a specific use case.
The list of payment instruments is sorted based on the order in which the payment instruments will be selected when making a payment.
Note: Before offering your business services, call `getPaymentInstrumentsList` to make sure that the user has at least one valid (not expired) payment instrument.

	[[PLVInAppClient sharedInstance] getPaymentInstrumentsList:@"A User Token" 
					    withUseCase:@"A Use Case" andCompletion:^(NSArray *paymentInstrumentsArray, NSError *error){
										if(paymentInstrumentsArray){
											self.piArray = piListArray;
	    								} else {
											//Error handling
		    						    }
	}];

##### Set payment instruments order for a use case
To update the order in which the payment instruments will be used when making a payment, call `setPaymentInstrumentsOrder` with the ordered list of payment instruments, the user token and the use case to which they belong.
Check out PayInstTableViewController's tableView:moveRowAtIndexPath: in our sample app for a complete implementation.

	NSOrderedSet* ordedSet = [[NSOrderedSet alloc] initWithArray:self.piArray];
	    
	    [[PLVInAppClient sharedInstance] setPaymentInstrumentsOrder:ordedSet
	                                                   forUserToken:self.userToken
	                                                  andCompletion:^(NSError *error) {
      
	        if (error) {
				//Error handling
	        } else {
	            //Success
	        }
	}];

##### Remove payment instrument for a use case
Remove a payment instrument, belonging to a specific user token, from a use case. After this, the payment instrument cannot be used to make payments for that use case.

	[[PLVInAppClient sharedInstance] removePaymentInstrument:pi 
												  fromUseCase:@"A Use Case" 
												 forUserToken:@"A User Token" andCompletion:^(NSError* error){
	            
	        if (error) {
	            //Error handling
	        } else {
	            //Success
	        }            
	}];

##### Disable payment instrument
Disable a payment instrument, belonging to a specific user token. The payment instrument will be removed from all use cases. 

	[[PLVInAppClient sharedInstance] disablePaymentInstrument:pi forUserToken:@"A User Token" andCompletion:^(NSError* error){
	            
	        if (error) {
	            //Error occured, see error.localizedDescription
	        } else {
	            //Success
	        } 
	}];

#### Documentation
[API Reference](http://payleven.github.io/InApp-SDK-iOS/AppleDoc/)

## LICENSE

According to original repo it's `MIT`

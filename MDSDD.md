#Shopevolution SDD for TATA
6/28/2018 12:47:22 PM 

ver.01

    
    ##Scope and field of application
This document defines the functional specifications for customizing the Shopevolution™ system as required by Tata Uruguay. 
The supplied system is composed of the standard Shopevolution™ application together with the personalization for Tata Uruguay as specified in this document. 
The features of the standard version of Shopevolution™ are described in the Shopevolution™ manuals.


###General information on the system
The Shopevolution™ system is integrated into the store architecture by adding the following items:

•	**Shopping Server**: the Shopevolution™ Server, on which the main functions are located, in particular the basket server.

•	**Shopper Unit Welcome System** (negotiable prefer this term – unchanged elsewhere): the peripheral Shopevolution™ units that control the terminal management operations when the terminals are inserted into the cradles. They also control the customer validation phase.

•	**Access Points**: the bridges between the Wi-Fi radio network and the Shopevolution Ethernet LAN network.

•	**Joya Touch Wi-Fi Terminals**: the scanners that are given to the customers to do the shopping.

•	**Shopevolution™ Clients: Shopevolution’s administrative client**. This is a normal store PC connected through the WEB to the Shopping Server. 

![test alt](C:\aa\Pic1.PNG "CIAO")

###Self-shopping service name
Compra Express is the marketing name for the self-shopping service as promoted by Tata Uruguay to their customers.

![test alt](C:\aa\ServiceLogo.jpg "CIAO")

##Operations flow in the store

###Customer recognition 
As customers enter Ta-Ta, they can begin shopping with the JOYA by approaching the Head Unit (Shopper Unit) and entering the Cedula.
The Cedula digits entry is the only way for the customer to validate himself. No Loyalty card validation has been required.

![test alt](C:\aa\Customerrecognition.png "CIAO")

Chapter 3 describes the special characteristics of customer recognition. Recognition takes place by the customer's Cedula number, which unambiguously represents the customer into the system.

###Device pick up
If the customer validation phase is successful, one of the available devices is unlocked and its cradle slot is lit up.
Within a configurable timeout (default value 30 seconds), customers must pick up the terminal whose cradle slot is lit up.
 ![test alt](C:\aa\Customerpickup.png "CIAO")



###Shopping
In this phase, customers shop for goods, using the terminal to scan the product barcodes.
 ![test alt](C:\aa\Customershopping.png "CIAO")
  ![test alt](C:\aa\Customershopping2.png "CIAO")

Chapter 4 defines the types of barcodes in the store while chapters 7 describe the functions and screens appearing on the device.

###End of shopping
Once they have completed their shopping, customers go to a payment station enabled for the Self-scanning service.
The shopping can be completed both on Traditional POS and Self-Checkout.

  ![test alt](C:\aa\Customerendofshopping.png "CIAO")

Chapter 10 describes the checkout phase in detail while chapter 12 describes in detail the rules for checking out customers.

##Customer processing
The validation will be remote, this means that the customer enters his own cedula and then Shopevolution checks through an external system (Aurora) if the customer can use the service.

###Customer validation process

  ![test alt](C:\aa\CustomerValidationprocess.png "CIAO")

- Customer enters the Cedula at Head Unit
- Head Unit communicates the value to Shopevolution Server
- Shopevolution calls Aurora System to validate the Customer
- Aurora System answers to Shopevolution Server 
- Shopevolution set the proper Head Unit status 
- Customer picks up the device only if entitled to shop with the system

###Cedula range specifications
The regular expression for the Cédula de Ciudadanía is
 
| Start with | **1-9** |   |   |   |
|:----------:|:---:|---|---|---|
|   Lenght   |  **8**  |   |   |   |

Also foreign customers can use the system.
The maximum number of digits that can be entered at the Head Unit are **14**.


###Customer validation process
Shopevolution uses the cedula number as key to univocally identify the customer.
The validation algorithm is the following:

1.	Shopevolution checks if the cedula number is eligible to be a Shopevolution customer (regular expression)
•	If the barcode doesn’t match the cardcode regular expression then the customer cannot start the self-shopping trip

•	If the cedula  matched the cardcode regular expression then the info regarding the status of the customer will be requested to Aurora server. 

2.	Aurora server evaluates the card validity 
a.	If the cedula exists in Aurora then the info will be sent to Shopevolution server
b.	If the cedula doesn’t exists in Aurora then the customer won’t be able to use the system

**All the existing customers in Aurora can use the system**

###Customer registration
No registration is required. All the customers in Auroda system are ready to use Compra Express.

###End of a Self-shopping
When Shopevolution server receives the information that a customer has closed his self-shopping, Shopevolution server updates on local DB the customer’s info.

The customer’s info updated at the end of a self-shopping are:

1.	The customer status: it could be disabled in case of bad behaviour detected by shopping rescan.

2.	The new rescan level of the customer: it could be changed by effect of the shopping rescan.

3.	Other data related to customer’s history. Those data are used by the rescan algorithm (see section for details), in particular:
	- Last shopping date
	- Last rescanned shopping date
	- Remaining number of shopping to be consecutive scanned 

All these info will be sent to the Aurora server.


###Customer spot-check data update
Every time the customer spotcheck data are updated,  Shopevolution sends the update to the CCSV. 
Spotcheck data changes may happen because of:


- a shopping ending (with or without rescan) 

- a manual update performed through the Shopevolution administration interface (for example changing the customer spotcheck class).

The only exception to this rule is if a customer validation took place during an off-line condition of the connection. 

###Politic of retries during customer spot-check data update
If, at the end of a shopping or after a change done by means of the System Administration Web Site, the link with the Aurora system is not available, the requests of update of customer spot-check data are queued and periodically the Shopevolution system (re)tries to send them.

With this strategy, there could be the case that an update is received after the centralized data has already been modified by another shop .  **The centralized system, in order to keep an ordering between the updates, has to use a special field (last update date)  to decide if the data are newer (and update the central database) or not (and discard them).**

###Handling of customer card changes
When it is necessary to change the customer card (for example because the Customer has lost its card) the centralized system has to maintain the customer data (unique customer identifier, use of self-scan authorization, preferred language, spot-check data, last update date) associated to the new card. 
__This task is under the responsibility of the centralized retailer loyalty system.__


##In-store barcode types
###Price Item barcode
Barcodes starting with:

**20,21,22,24,27,28**
 
Length: 13 digits

Calculate QTY.

6 first digits (included radical)  are the product, next 6 are the price (2 decimals without point) and the last one the check digit.

*Example*: 2031601123459 – 

- 203160 (#product), 
- 112345 (price $1123.45) 
- 9 (check digit).

**23** 

Length: 13 digits

Calculate QTY.

5 digits (starting at third position, radical not included)  are the product, next 5 are the price (2 decimals without point) and the last one the check digit, POS calculate QTY.

*Example*: 2331601123453

- 31601 (#product), 
- 12345 (price $123.45) 
- 3 (check digit).

**26** 

Length: 13 digits

Calculate QTY.

7 first digits (included radical) are the product code, next 5 are the price (2 decimals without point) and the last one the check digit but POS doesn’t calculate quantity.

*Example*: 2631601123452 –

- 2031601 (#product), 
- 12345 (price $123.45) 
- 2 (check digit)

 
##Departments
This section is stil to be discussed

##Promotions and Discounts
For Phase1 of the project the device will show only the item gross price.

----------
#Joya Touch screens and functions
 
This chapter aims to define the look&feel of the Joya Touch from the end-customer perspective.

###Device personalization

- Form Factor : **Pistol** **Grip**
- Upper Enclosure: **Red**	
- Bottom Body : **Light Grey**
- Battery Cover : **Red**
- Retailer logo label : **Datalogic standard**
- PN : **911350079** JOYA TOUCH A6 A00AN04HL0GT0AN-TTS
 

  ![test alt](C:\aa\JTlookfeel.png "CIAO")

  ![test alt](C:\aa\JTlookfeel2.png "CIAO")

###JOYA Screens
Each shopping state has a relevant screen (or sequence) associated.
No customized font has been requested.

#####Shopping Screens
  ![test alt](C:\aa\screen\JTSS00.png "CIAO")
-----
 ![test alt](C:\aa\screen\JTSS01a.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS01b.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS01c.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS02.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS03a.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS03b.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS04a.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS04b.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS04bb.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS04c.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS04d.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS05a.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS05b.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS05c.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS06.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS06b.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS07.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS08.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS09.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS10.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS10b.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS10c.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS10d.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS10except.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS12.png "CIAO")
-----
![test alt](C:\aa\screen\JTSS13.png "CIAO")





##Checkout procedure  
A Self-shopping transaction can be close either in a SCOT  or a Traditional Till. 
The Rescan will be done ONLY with Joya device.
Please refer to the Shopevolution Integration Guidelines ver 4.0.20 for any info about the POS data exchange files (Scanin and Scanout)

###SCANIN settings
The scanin file name is:

canin

###SCANOUT settings
The scanout file will be generated always by the POS containing only the header infos since the info about a potential rescan are contained by the device.
Shopevolution has to receive only the communication by the POS that the customer paid.

The scanout file name is:


##Partial/Full Rescan 
For Phase1 all the rescan will be performed by an operator with the Joya Touch device.
All the rescan will be “partial”, according to the Spotcheck Class Table showed in chap. 12.1.

Please note that Partial Rescan isn’t available for customers in Class 1 and Class 2.
So the operator must scan all the customer basket  then close the rescan as Full Rescan scanning a barcode.
###Partial rescan procedure

At the end of Shopping, the system could ask for a rescan.

If a control is asked the operator starts the control scanning the Customer card (preferred way) or selecting the customer among the Rescan queue list on Joya Touch.

Customer card and shopping related information will appear. 

The operator start scanning a product collected randomly from the customer’s basket and if the item is correctly found, the items left counter decreases.

When operator reaches the total number of items to rescan without encountering errors  (depending by the customer spot-check class) then he can close the rescan phase by pressing the End of Partial Rescan virtual button.

Operator has to follow the indication on the screen, eventually closing the rescan using the button function.

Operator may require to close the rescan as FULL RESCAN. This option isn’t available via button, this means that the operator who wants close a FULL RESCAN needs to scan a particular barcode.  This barcode is not shown on the POS and will typically be a physical print held at the POS.

![test alt](C:\aa\screen\EndOfFullRescanbarcode.png "CIAO") 

It is possible to check the status of the rescan by pressing the review button. Operator will be trained to do so before closing the rescan, as it is not shown after rescan anymore.

The system will be configured as follow:

-	End rescan on touchscreen triggers an END RESCAN with DYNAMIC CORRECTION MODE 
-	1st mistake implies +3 items to be rescanned
-	2nd mistake implies +100 items to be rescanned (means a FULL rescan)




PFR Configurations	Available	Note
Open PFR by Customer device	X	
Open PFR by ShopOperator device	X	
Open PFR barcode with customer device	X	 
Allow PFR for forced Rescan	X	
End Partial Rescan by button	X	
End Full Rescan by button	X	
End Partial Rescan by barcode		
End Full Rescan by barcode		 
Correction Mode		
Min number of Items for Partial	X	5 items
		


##Re-scan configuration
The Rescan for phase1 and phase2 will be performed only by Shopevolution with PFR feature.
Here below the Rescan Algorithm configuration proposed for phase1

###Spotcheck classes
In table 1 here below the Spotcheck Classes configuration for phase 1.

# J2Pay

**version 1.0.0**

J2Pay is a payment processing library for Java.

**Goals of this library**

* Developer should be able to integrate any gateway without the need of reading documention of specific gateway.
* Provide generic methods for all gateways.

This library will be focusing only four major methods of gateways.

* Purchase
* Refund
* Void
* Rebill

# Understanding the library

This library has three packages.

* com.tranxactive.paymentprocessor.gateways (Contains all the available gateways).
* com.tranxactive.paymentprocessor.gateways.parameters (Contains classes for gateway parameters like Customers, cards).
* com.tranxactive.paymentprocessor.net (Contains classes for http requests and response.).

**List of some major classes**

Here is the list of some major classes you must know before starting working on this library.

* Gateway, is the top level abstract class all gateways must be inheriting this class.
* GatewayFactory, will be responsible for returning the required gateway.
* HTTPResponse, gateway reponse will be returning this class's object instead of plain text or json.
* Document, Represent the json data. also will be using for posting dynamic gateway data.
* AvailableGateways, enum contains the list of supported gateways. we will be passing this to GatewayFactory to get the desired gateway class object.

# Example

In this example we will be looking at purchase method.

Before moving forword we must understand that some paramters are always different for each gateways.
To handle this situation gateway class provides four sample parameters methods (will be looking at them shortly).

* gateway.getApiSampleParameters()
* gateway.getRefundSampleParameters()
* gateway.getVoidSampleParameters()
* gateway.getRebillSampleParameters()

**lets code.**

First we will get the desired gateway in this case Authorize.net.
We can get any supported gateway by the help of getGateway method of GatewayFactory class.

```java
Gateway gateway = GatewayFactory.getGateway(AvailableGateways.AUTHORIZE);
```
Now we can call purchase, refund, void and rebill methods.

Purchase method required 5 parameters.

* Document apiParamters, that is the gateway specif paramters always unique for each gateway. 
* Customer customer, this class represents customer personal information.
* CustomerCard customerCard, this class represents the Customer card details.
* Currency currency, that is enum contains the list of currecny in which amount will be charged.
* float amount, the amout that will be charged.

**1st Parameter**

lets get the apiSampleParameters by calling getApiSampleParameters() method.

```java
Document apiSampleParameters = gateway.getApiSampleParameters();
```
we will print apiSampleParameters to see what is the requirement.

```java
System.out.println(apiSampleParameters);
//output
{ "name" : "also called api user name / api login id", "transactionKey" : "the transaction key" }
```

As you can see for Authorize api parameters are name and transactionKey.
We will populate these values and pass to purchase method.

```java
apiSampleParameters.replace("name", "<your acount's use name here>");
apiSampleParameters.replace("transactionKey", "<your account's transaction key here>");
```

**2nd Parameter**

Customer class comes with chaining setter methods.

```java
Customer customer = new Customer();
        
customer
        .setFirstName("test lirst name")
        .setLastName("test last name")
        .setCountry(Country.US)
        .setState("TX")
        .setCity("test city")
        .setAddress("test address")
        .setZip("12345")
        .setPhoneNumber("1234567890");
```

**3rd Parameter**

CustomerCard class comes with chaining setter methods.

```java
CustomerCard customerCard = new CustomerCard();
customerCard
        .setName("test card name")
        .setNumber("5424000000000015")
        .setCvv(123)
        .setExpiryMonth("01")
        .setExpiryYear("2022");
```

4th and 5th parametres does not required any explanation.

now all parameters are ready we can pass them to purchase mehtod.
before purchase you can enable the test mode by calling setTestMode of gateway, By default test mode is disabled.

```java
HTTPResponse response = gateway.purchase(apiSampleParameters, customer, customerCard, Currency.USD, 45);
```

you can check the status of purchase request by calling isSuccessful method.
you can also get the response content by calling getContent method.

```java
response.isSuccessful();
response.getContent();
```

# Putting all code togather.

```java
Gateway gateway = GatewayFactory.getGateway(AvailableGateways.AUTHORIZE);
Document apiSampleParameters = gateway.getApiSampleParameters();

apiSampleParameters.replace("name", "<your acount's use name here>");
apiSampleParameters.replace("transactionKey", "<your account's transaction key here>");

Customer customer = new Customer();
        
customer
        .setFirstName("test lirst name")
        .setLastName("test last name")
        .setCountry(Country.US)
        .setState("TX")
        .setCity("test city")
        .setAddress("test address")
        .setZip("12345")
        .setPhoneNumber("1234567890");

CustomerCard customerCard = new CustomerCard();
customerCard
        .setName("test card name")
        .setNumber("5424000000000015")
        .setCvv(123)
        .setExpiryMonth("01")
        .setExpiryYear("2022");
        
HTTPResponse response = gateway.purchase(apiSampleParameters, customer, customerCard, Currency.USD, 45);

System.out.println(response.isSuccessful());
System.out.println(response.getContent());

```








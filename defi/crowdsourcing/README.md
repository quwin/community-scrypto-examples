# Crowdsourcing Campaign example
This example is a smart contract that runs a crowdsourcing campaign. The creator of the component becomes a fundraiser, while anyone else can pledge to the campaign, or recall their pledge under certain conditions.

The fundraiser can only withdraw the collected XRD if campaign has ended, and the goal has been met.

## Reset accounts
```
$ resim reset
```
Create two new accounts, and put the hashes in environment variables for easy access. Additionally, show the first account and copy the XRD token hash into environment variable.

```
$ resim new-account
$ export acct1=...
$ export privkey1=...
$ resim new-accounts
$ export acct2=...
$ export privkey2=...
$ export xrd=030000000000000000000000000000000000000000000000000004
```

## Publish the package
Publish the package, and put the package hash in environment variable for easy access.
```
$ resim publish .
$ export package=...
```

## Instantiate campaign as fundraiser
Create a component with the goal of collecting 10,000 XRD with a duration of 1 epoch. Save the component hash in environment variable for easy access.

Show the account, and save the fundraiser_badge hash to environment variable. This badge is used to withdraw the collected xrd after campaign has finished.

```
$ resim call-function $package CrowdsourcingCampaign new 10000 1
$ export component=...
$ resim show $acct1
$ export fundraiser_badge=...
```

## Get status of the campaign
Anyone can get status of the campaign without any authorization.
```
$ resim call-method $component status
```

## Pledge to the campaign
You can pledge to the campaign with any account, and recieve a `patron_badge` that you can use to recall the pledge with. Each patron badge is unique with it's
own resource definition. You cannot pledge after the campaign has finished.

You will get a patron_badge. You may use this badge to recall your pledge.

```
$ resim set-default-account $acct2 $privkey2
$ resim call-method $component pledge 5000,$xrd
$ resim show $acct2
$ export pledge_badge=...
```

## Recall pledge
If campaign has not finished, or the goal has not been met patrons are able to recall their pledge.

```
$ resim set-default-account $acct2 $privkey2
$ resim call-method $component recall_pledge 1,$pledge_badge
$ resim show $acct2
```

## Withdraw collected XRD from campaign
If the campaign has finished, and the goal has been met the fundraiser can collect XRD, with the fundraiser badge. It is important to note that the `withdraw` method uses the auth-zone for it's auth operations. This means that the fundraiser badge needs to be in the auth zone for this method call to succeed. Which means that we must use a transaction manifest to perform this operation. The following transaction manifest code can be used to perform this:

```sh
CALL_METHOD ComponentAddress("<Account Address>") "create_proof_by_amount" Decimal("1") ResourceAddress("<Fundraiser Badge Resource Address>");
CALL_METHOD ComponentAddress("<Component Address>") "withdraw";
CALL_METHOD_WITH_ALL_RESOURCES ComponentAddress("<Account Address>") "deposit_batch";
```
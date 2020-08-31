---
title: Creating a wallet and integrating Stripe With Django
cover-img: /assets/img/gsoc_ends.jpg
readtime: true
share-img: /assets/img/gsoc_ends.jpg
subtitle: Django Wallet Integration with Stripe
tags: [stripe, django, python]
---

Stripe gives developers an out of the box integration of Payment Gateway, which lets developer focus only on their WebApp. We use that feature to create a basic wallet for a WebApp in Django.

<img src="https://images.ctfassets.net/fzn2n1nzq965/3AGidihOJl4nH9D1vDjM84/9540155d584be52fc54c443b6efa4ae6/homepage.png?fm=jpg" width="100%" height="auto">

# Django Models

For creating a wallet in django I've created a model named as wallet which consists of the following params
```
class Wallet(models.Model):
	user = models.ForeignKey(User, on_delete=models.CASCADE)
	account_id = models.TextField(null=True, blank=True)
	current_balance = models.DecimalField(max_digits=6, decimal_places=2, default=0)
	created_at = models.DateTimeField(auto_now_add=True)
```
, along with two functions, i.e deposit and withdraw. The accound_id params is used to store the Stripe Account's id.

Next, create a model transaction which will contain all the information about the user's transaction details, 
```
class Transaction(models.Model):
	wallet = models.ForeignKey(Wallet, on_delete=models.CASCADE)
	value = models.DecimalField(max_digits=6, decimal_places=2)
	running_balance = models.DecimalField(max_digits=6, decimal_places=2)
	created_at = models.DateTimeField(auto_now_add=True)
```
The running_balance param is used to store the balance before the value is deposited or withdrawn from a wallet. 

Now, whenever a user is regitered you want to create a wallet for that user. So, for that we are using django's reciver signal, i.e, whenever a user's account is created this method gets triggered.

```
@receiver(post_save, sender=settings.AUTH_USER_MODEL)
def create_wallet(sender, instance=None, created=False, **kwargs):
	if created:
		Wallet.objects.create(user=instance)
```

# Stripe API

Stripe has two keys :

**Publishable API keys** are used to indentify your account and can be placed in your JS files and they only have the power to create tokens.
**Secret API keys** should be kept confidential and only stored in your own Server/Codebase.

To get yourself the Stripe API keys, register for a stripe account and generate it from dashboard. In dashboard you can see two kinds of keys,i.e test and live. Before taking the site live make sure the Live key's are used in the codebase.

Once you have the API keys you can store them in your environment variables and can store them in django settings.

# Stripe Installation
`
pip install stripe`

# Working with Stripe

* **To accept a card payment**
 * Create a card payment form using the [Stripe's Integration Builder](https://stripe.com/docs/payments/integration-builder)
 * After the card has been processed, add the required data in orderComplete function and send the details to the django server.
 * In Django,  Import stripe using `import stripe`
 * Collect the stripe's secret key from django settings
 * In Stripe object, append the stripe key
 *
```
  stripe.api_key = settings.STRIPE_SECRET_KEY
``` 
 *
and, Create a payment to your stripe account
```
charge = stripe.Charge.create(
			  amount=int(Decimal(request.POST['amount'])*100),
			  currency='usd',
			  description='Example charge',
			  source=request.POST['stripeToken'],
			)
```
Here, `stripeToken` is the object returend to us in the frontend in the orderComplete function.
After Payment Completion, check the payment status. If successful Add Money to the wallet, else send a response to the frontend with error message. 

* **To Send Money to a Client**
 *  To send money to a client's account one should also register for a Stripe Connect Account.
 * Next when you want to send money to a client's stripe account, you must connect Stripe to Django.
 * For that, append your stripe key to the stripe object.
 * Next create a stripe account locally, using `stripe.Account.create` command
 * This method will return a account id , so store it in your wallet's account id model.
 * Next, to we create a account link url, for the user to visit and login, for that we use Stripe's `stripe.AccountLink.create` method. This method will return a url, redirect the user to that url.
 ```
 account = stripe.Account.create(
		  type='express',
		)
	wallet.account_id = account.id
	wallet.save()
	account_links = stripe.AccountLink.create(
	  account=account,
	  return_url='http://127.0.0.1:8000/dashboard/user/stripe/connected/'+request.user.username,
	  refresh_url='http://127.0.0.1:8000/dashboard/user/profile/'+request.user.username,
	  type='account_onboarding',
	)
	return JsonResponse({'redirect': account_links.url, 'status': 'success'})
 ```
 * Once the user is logged in to the stripe account, the user is redirected to the return_url mentioned in the AccountLink.create method.
 * You can check the user's logged in status using the user's account_id saved in the user's wallet.
 * If the user is logged in, transfer the amount in his stripe account and deduct the money from your app's wallet.
 * ```
 stripe.Transfer.create(
		 amount=Decimal(request.POST['amount'])*100,
		 currency="usd",
		 destination=wallet.account_id,
		 transfer_group="ORDER_95",
	)
	wallet.withdraw(Decimal(request.POST['amount']))
wallet.save()
 ```
 
# Testing
To test the integration use stripe's provided card details, more details regarding that could be found [here](https://stripe.com/docs/testing)

# In Production
When moving towards production remember to replace the test keys with the live ones.

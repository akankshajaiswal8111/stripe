# Update Credit Card

Even before we had subscriptions we were technically storing the customer's credit card on the customer object inside of Stripe. Now, that's finally really important because this is the card that Stripe is going to try to use whenever they renew the subscription from the user. Actually, credit cards expire and so we're going to need to allow the user to change their credit card information right through their account area. Now on surface this is actually pretty easy. The card is just a property on the Stripe customer object so as long as we ... The process of changing, it looks a lot like checkout.

We'll run the same form that we do in checkout. Grab the Stripe token, assign that to the customer and that's it whereas normally in checkout we then actually charge them for something. Let's have a little button to this page to update the credit card information and make it so that it shows that same credit card form right here. In account.html.twig I'm going on to the section that talks about the credit card information. I have a credit card. I want a button here. Give it some styles and give it a special JS class called open credit card form. That isn't doing any special but in a second we're going to use that to actually trigger some JavaScript functionality.

We'll say update card. Next, down in this col xs x which is the right side of the screen what I did here another JS class update card wrapper. In a second we'll use to find this element. We'll make this style display none so that it doesn't show up till we actually click that change button. Inside, I had a header for update credit card. Then we want to reuse the entire checkout form. Unfortunately we already have that isolated into an _cardForm.html.twig template. Let's use the twig include function to include that html. Finally, we need to hook up the JavaScript. Whenever somebody clicks the JS open credit card form button we want to show these div down here.

On the top of my file I'm going to override a block called javascripts. This is just the way in my project to add JavaScript to just this page and call the parent function to include the normal JavaScript that's in our base layout. Inside, add a script tag. We'll just do a very simple document.ready function. Inside of that, select that JS open credit card form element then on click create a function where we prevent the default first and then we find our other element which was JS update card wrapper. We'll use the .slideToggle to open or close that. Fairly simple.

First, let's see if that works. Refresh and it doesn't. We actually get variable error does not exist from _cardForm.html.twig. Inside of their line 56 I'll remind you that we're actually referencing error variable. This is used when we submit the checkout form but if we have problems charging the user's card, we set an error variable and then we render it here. For now we don't have any errors that might be happening. In account.html.twig when we include that, we can set the error to note. Actually, even better than that in profile controller when we render that template let's actually pass error, null. Perfect. Click update card. There we go.

Now the problem probably being that it says checkout here and we're rather that say maybe update card. In card form, instead of saying checkout let's render a new variable called button text. Button text is not defined. Let's just say checkout. If there is no button text variable, it will just say checkout. If there is a button text variable it will use that. In account.html twig has a second argument to include which is an array and we'll set button text update card. Refresh, there's a nice new button. Cool. The second thing is that we know that on the checkout form there's actually quite a lot of JavaScript that we have added. A JavaScript that formats this nicely.

There's JavaScript that runs when we press checkout to send the information to Stripe. We aren't going to be able to reuse that stuff from here on the account form. You can see it's not happening right now. You can see my formatting looks awful. Instead of checkout, the html twig. For simplicity because this isn't a JavaScript course, we just in-lined all of our logic right here inside the template. To keep things simple, copy all of that script out and create a new template file called _creditcardformJavaScript.html.twig inside the order directory and then just paste that there.

Now, instead of checkout, we can just include that and copy that and include the same thing inside of account right at the top or a JavaScript section. Then refresh to see if that works. Here, we the same problem as before. Variable stripe public key does not exist because when we set up our credit card, JavaScript we referenced the Stripe public key variable. Then order controller. This is just something that we are reading from our configuration. That's what the biscuit parameter does and passing into our template. We'll just do the same thing from inside of our profile controller. I'm passing that variable in. We can set up our JavaScript correctly. Perfect.

Now, we do your formatting. With all the front end stuff set up we're finally ready to actually process this stuff correctly. Now, as a reminder, on the check out form when you checkout it sends all the credit card information to Stripe via Ajax. Strike sends us back a token and then we submit that token to our code which attaches into the customer and charges the customer. We're going to do the exact same thing except we want to submit that token to a different end point. This point they are going to attach that token which represents the credit card to the customer and do nothing else. We won't actually charge the user.

First, inside profile, our controller, let's create a new end point that we want the form to submit to. Create a public function update credit card action. We'll give the URL of /profile/card/update and the name of account update credit card. If we're going to measure we'll even add the add method post. With that there, we want to make sure that the form submits to this new end point. If you look at _cardForm.html.twig, the form action is actually empty. The reason for that is on the checkout form where she leave it empty so that it post right back to that same URL. In this case we want it to post not to this slash profile URL but to the new URL that we just created in profile controller.

To do that in card form, render a new variable called form action and then use the default filter to default that to nothing. If we don't pass form action, it will just be an empty stream like normal but if we do pass it we can override it with something custom. Now, in account.html.twig I'm going to include that template. We can pass it form action and then generate the URL to our new route. With that update, you can see that the form action for the credit card update is going to our new URL. Okay, finally, inside of this controller we just need to do the work. This is going to look very, very similar to what we did inside of order controller, specifically where we fetch the token and then use that.

In fact, let's copy this first line right here. Go back to profile controller and make sure you have this request object as an argument so that we can get access to that and that did just add the used statement for me up here. Step one, get the token from the request which request arrow request arrow get is equivalent to $_post stripe token. Second, let's fetch the current user object, the person who is logged in because we're going to need that. Third, we're going to need to make an API request to Stripe to send them the new token which represents the user's credit card so that it can attach it to our customer. That means we'll be using the Stripe client.

Say Stripe client equals this arrow get stripe_client. Here's the awesome part, if you open Stripe client, we already have a method in here called update customer card where we pass at the user object and the payment token or the Stripe token that was just submitted and it sets it on the user and saves it. In other words, the work for this is already done. The profile controller say Stripe client arrow update customer card. You pass it, user and token. That will update things on Stripe's side. Now, what about our database? Do we store any information about the credit card? Actually, we do now. In the user object we have a card last four and the card brand.

Since the user just updated their credit card information, that may have changed. Fortunately in subscription helper we already have this handled as well in update card details method. You pass it to user, we pass it to Stripe customer and it takes care of updating everything for us. Profile controller we just use that. This arrow get. Subscription_helper arrow update card details. We pass it to user. I pass it to Stripe customer which I actually don't have yet here but the update customer card method returns it so we can set that as the variable. We updated the card in Stripe. We updated our database and that's it. Add a success message and then redirect to our profile page.

Okay, let's give this a shot. Refresh the page. Put in a fake credit card information. This time I will give it a different expiration of 10/25. Hit update card. Looks like it worked. Go check out your customer in Stripe. I'll refresh the customer page. You can see the expiration was 10/20. Now it's 10/25. The credit card was updated perfectly. Now the last thing to handle is credit card failures. As we remember from checking out there's two ways a credit card can fail. Most credit card failures are caught immediately. When you hit update card, Stripe will tell you immediately that the card is not valid. Sometimes you don't find out until you actually try to attach the card to your customer.

Let me give you an example. If you go to Stripe's documentation and go to the testing section and go down to the bottom you'll find some tested credit cards that will fail. This one is the most important one right here. I'll copy that. Link that as my card and then hit update. This time you see a 500 error. Your card was declined. Specifically the \Stripe\Error\Card exception has been thrown the moment that we actually try to save the customer information. We already handled this in our checkout action. We just need to be just as careful when we update the credit card. What that means is the line is failing in this update customer card line. I'm going to wrap these try method and let's catch the \Stripe\Error\Card.

If it fails, set an error variable to, "There was a problem charging your card." We can catenate with e-getMessage which will have more information about what went wrong. Now here, what we need to do is actually really render the profile page but with that error. The easiest way to do that is actually just to say this addFlash error error. It's just like with the success flash I already have something set up to show you an error flash and look red and angry and things like that. Then we'll return this arrow redirect to route profile_account.

[Inaudible 00:17:35] little hands.

To try that out, go back, hit update card again. Paste that same number, hit update card. This time instead of a happy message we'll get a sad message. We got all of our bases covered.
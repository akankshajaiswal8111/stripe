# Subscription Renewal Webhook

There are a lot of different types of web hubs that you can listen to. You probably only need to listen to a few of them. The second one that we need to listen to is called invoice dot payment underscore succeeded. The reason we need to listen to this are two things.

First, our subscription has a billing period ends at. Every time we have a new renewal that will trigger an invoice and when that invoice is paid, we need to update the billing period's ends at to the next billing period, the next months from now.

The second reason you might want to have a web hub on this is because you might want to send your user an email to tell them that the payment was successful. Maybe send them an invoice. First, let's update our request bin web hook, to actually get all of the events instead of just the selected events. This is up to you but instead of me needing a click every time I need a new event and keep all of my web hooks with those same ones selected. I'm just going to choose send me all events.

When I do that, I'm going to go into my web hook controller. Down here I can't have this exception anymore since that went out via a normal situation. I'm going to say allow. We receive all web hook types. Okay. To see what a invoice payment succeeded looks like let's select our request bin and send that test web hook. Go over here, refresh and the top request is our new invoice payment succeeded.

Let's look at what we need to figure out here. One, we're going to need to figure out and find the subscription that this invoice is for. Now the tricky thing is invoices can happen for two reasons. They can happen just because a user orders some products and there's no subscription or this might be an invoice related to a subscription. Fortunately, the data here covers that. Specifically, there's a subscription field under the data object section. If this is filled in, it will hold the subscription ID and, if there isn't a subscription that this is related to, that will be empty. That will be our way of knowing whether this invoice is attached to a subscription or not.

Let's go into our web hook controller and we're going to have a second case statement here for invoice dot payment underscore succeeded. I'll add my break and then we'll start filling in the logic. First this is, we need that stretch subscription ID. Stretch subscription ID equals stretch event arrow data arrow object arrow subscription. Now, if there is a stretch subscription, in other words, if this invoice was for a subscription. Then we need to locate that subscription in our database. To do that, we can just reuse our this arrow find subscription help or method that we have at the bottom of this class.

Next, at this point we just have the stripe subscription ID. We need to query their API to get the full subscription object, because that object will have the end billing period date current period end. To do that, one of our stripe clients right now we don't have a method yet that can just return us to subscription. We set a new public function find subscription. It will take in a stripe subscription ID. It will return/stripe/subscription call upon and retrieve pass the stripe subscription ID, pretty simple.

Then web hook controller we can set that with stripe subscription equals this arrow, get stripe client arrow find subscription. Pass the stripe subscription ID. Now we have our subscription in the database. We have striped subscription which can tell us what they renew and the date is. We see the update our information in the database. To keep things organized, I am going to include this. As you may have guessed inside our subscription helper. It has got a new public function called handle subscription paid. This will take in our subscription objects and also take in the stripe/subscription object so that we can get the data off of that.

First thing we need to do is read off that current period and data. Actually we already did this once before, when we were adding the subscription of the user. I'll do the exact same thing here. I will copy that line and paste it down here. I will actually change that to be called new period end. Next we see on the update date on the subscription ends it this billing period ends. We will call subscription arrow set billing period ends. You notice I am not getting any auto complete because I do not actually have that method yet. We will fix that and pass to the new period end. Then enter at subscription at the bottom I will make sure to set that. I will use the code generate menu or command end go to settings and sure enough there is the billing period end at. I will fix my methods name here .

Finally we just need to persist subscription and flush just that subscription to the data base. Perfect, I won't set a test for this I will leave that to you guys. Now whenever that subscription is renewed we should automatically update the billing period ends at.

The second thing your controller we can call subscription helper which we already set earlier. Arrow up, arrow handles subscription paid pass on the subscription pas the stripe subscription and we should be good. The other thing you might want to do in your subscription helper when handling the subscription pay is send the user an email. It should seem pretty easy I will just write here I will send the user an email.

In fact I will let you add the logic for sending the email however you want. There is one catch related to stripe and that is that, this invoice dot payment succeeded web hook is going to trigger for renewals. It is also going to trigger at the moment you check out. If you send a user an email here that says, 'thanks for renewing your subscription'. All your new users are going to get it too, which is not going to be very cool. The way to fix that is to created renewal variable and set that to new period end created then subscription arrow get billing period ends.

This is a new subscription in about 2 seconds ago we just set up a subscription object and set the billing period ends at to be a true billing ends at. This is triggering for a new subscription. This new period end is going to match the one in database. If this is a renewal then the one we have in the database will be for last month and the new period end will be for next month and you can use that to figure out if this is a renewal. Now you can confidently send an email only on renewals.
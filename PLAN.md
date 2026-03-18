## Changes -
1. Turn Of Developer Mode in Web & App
2. Update By Default Set Backend URL In Web & App
3. Replace Ente Branding With Own
4. Pricing Plan Update
5. Payment getaway support for razorpay or phone-pay

## Pricing Plan
my price plan is simple, 1 Rupee Per GB. Minimum user need to take 10GB. for the first month this 10GB for free, from the next month they got billed. (If i signup in the app at 5st jan for one month i can freely use 10GB but from from next month 5st Feb i got the bill for 10GB)
if in 5 day we are not received the payment, the account got freezed. 
Plan Upgrade - During a month if user want to upgrade the plan from 10GB to 50GB at 15th Jan, in that case user will pay --->
(50GB * 1Rupee) -  (10GB * 1Rupee) = 40Rupee And the billing date will getting change to every month 15th.
Plan Downgrade - If user want to downgrade the plan from 50GB to 25GB at 28th Jan, in that case user will pay --->
(25GB * 1Rupee) = 25Rupee And the billing date will getting change to every month 28th.

## Payment Getaway
In the getaway we need to use razorpay or phone-pay in web & apps
For Now We Will Use Strip UPI For India -
1. Enable UPI in Stripe Dashboard — Go to Stripe Dashboard > Settings > Payment Methods > Enable "UPI" for the India account                               
2. Checkout Session — Stripe Checkout automatically shows UPI when enabled for Indian customers. The server already creates checkout sessions via stripe.go, and Stripe handles showing the right payment methods based on the customer's region.     

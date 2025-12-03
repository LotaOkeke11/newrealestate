# Stripe Payment Integration Setup Guide

## Overview
The subscription payment system has been fully integrated with Stripe. Users can now purchase Growth, Platinum, Diamond, and Elite subscription plans with secure card payments.

## What Has Been Implemented

### 1. Frontend Integration
- **SubscriptionPage**: Updated with Stripe checkout functionality
- **Payment Flow**: Clicking "Subscribe Now" redirects users to Stripe's secure checkout
- **Success Page**: Users are redirected to a success page after payment completion
- **Loading States**: Visual feedback during payment processing

### 2. Backend Edge Functions
Two Supabase Edge Functions have been deployed:

#### `create-subscription-checkout`
- Creates Stripe checkout sessions for subscriptions
- Handles user authentication
- Generates secure payment links

#### `stripe-webhook`
- Processes Stripe webhook events
- Activates subscriptions after successful payment
- Updates subscription status in database

### 3. Subscription Plans
The following plans are available:
- **Starter**: Free (₦0/month)
- **Growth**: ₦2,500/month
- **Platinum**: ₦7,500/month
- **Diamond**: ₦15,000/month
- **Elite**: ₦35,000/month

## Setup Instructions

### Step 1: Get Your Stripe Keys

1. Go to [Stripe Dashboard](https://dashboard.stripe.com/)
2. Create an account or sign in
3. Navigate to **Developers > API Keys**
4. Copy your **Publishable Key** (starts with `pk_test_` or `pk_live_`)
5. Copy your **Secret Key** (starts with `sk_test_` or `sk_live_`)

### Step 2: Configure Environment Variables

#### Local Environment (.env file)
Update your `.env` file with your Stripe publishable key:

```bash
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_your_publishable_key_here
```

#### Supabase Edge Function Secrets
Add your Stripe secret key to Supabase:

1. Go to your Supabase Project Dashboard
2. Navigate to **Edge Functions > Manage Secrets**
3. Add the following secrets:
   - Name: `STRIPE_SECRET_KEY`
   - Value: Your Stripe secret key (sk_test_... or sk_live_...)

### Step 3: Configure Stripe Webhook

1. In Stripe Dashboard, go to **Developers > Webhooks**
2. Click **Add Endpoint**
3. Enter your webhook URL:
   ```
   https://edjvphxgltxlsdddplyh.supabase.co/functions/v1/stripe-webhook
   ```
4. Select the following events to listen for:
   - `checkout.session.completed`
   - `customer.subscription.deleted`
5. Copy the **Signing Secret** (starts with `whsec_`)
6. Add it to Supabase secrets:
   - Name: `STRIPE_WEBHOOK_SECRET`
   - Value: Your webhook signing secret

### Step 4: Test the Integration

#### Testing in Development Mode

1. Use Stripe's test mode (test keys start with `pk_test_` and `sk_test_`)
2. Use test card numbers:
   - **Success**: 4242 4242 4242 4242
   - **Requires Authentication**: 4000 0025 0000 3155
   - **Declined**: 4000 0000 0000 9995
3. Use any future expiry date (e.g., 12/34)
4. Use any 3-digit CVV (e.g., 123)

#### Testing the Flow

1. Navigate to `/subscription` page
2. Click "Subscribe Now" on any paid plan
3. Complete payment with test card
4. Verify redirect to success page
5. Check that subscription is activated in database

## How It Works

### Payment Flow

1. **User Clicks Subscribe**
   - Frontend calls the `create-subscription-checkout` edge function
   - Function creates a Stripe checkout session
   - User is redirected to Stripe's hosted checkout page

2. **User Completes Payment**
   - Stripe processes the payment securely
   - User is redirected back to success page

3. **Webhook Processing**
   - Stripe sends webhook to `stripe-webhook` function
   - Function verifies webhook signature
   - Creates/updates subscription record in database
   - User gains access to subscribed plan features

### Access Control

- Free plan: Available to all users by default
- Paid plans: Access granted only after successful payment
- Subscriptions are stored in the `subscriptions` table
- User tier is checked throughout the application for feature access

## Database Schema

The subscription is stored in the `subscriptions` table with:
- `user_id`: The subscriber's user ID
- `tier_id`: The subscription tier ID
- `status`: active, canceled, or expired
- `start_date`: Subscription start date
- `end_date`: Subscription end date (monthly)
- `stripe_subscription_id`: Stripe's subscription ID
- `stripe_customer_id`: Stripe's customer ID

## Security Notes

- Never commit your secret keys to version control
- Always use test keys during development
- Switch to live keys only in production
- Webhook signature verification is mandatory for security
- Edge functions verify JWT tokens for authenticated endpoints

## Troubleshooting

### Payment Not Completing
- Check that webhook secret is configured correctly
- Verify webhook endpoint is receiving events in Stripe dashboard
- Check edge function logs in Supabase

### Redirect Issues
- Ensure success/cancel URLs are whitelisted in Stripe settings
- Verify origin header is being sent correctly

### Subscription Not Activating
- Check webhook logs for errors
- Verify subscription table has correct RLS policies
- Ensure user_id and tier_id are being passed correctly

## Support

If you encounter issues:
1. Check Stripe Dashboard logs
2. Check Supabase Edge Function logs
3. Verify all environment variables are set correctly
4. Ensure webhook is receiving events

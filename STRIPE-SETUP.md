# Stripe setup — Google review tap placards

## ✅ ALREADY DONE (2026-08-11)

The payment link is **built and live**:

**https://buy.stripe.com/bJefZhb4s7QL5mi1Cm67S01**
(`plink_1U3IBQFdpx7emFYwyOE4ghVo`, live mode, account `acct_1TwWbyFdpx7emFYw`)

It is already pasted into `google-reviews.html`, so the website takes cards now.
What's configured:

- **$40 Google review placard** — quantity locked to 1, always on the order
- **Two optional add-ons** the customer taps **+ Add** to include:
  **Additional placard $20** (0–10) and **Wallet tap card $20** (0–20)
- Collects **business name, full name, phone, billing + shipping address** (US)
- Three custom fields: their **Google Business Profile / review link** (required),
  best install days/times, and anything you should know (both optional)
- A custom thank-you message promising a same-day refund if you can't reach them
- **Post-payment invoice PDF is ON** (Stripe's 0.4%, capped at $2 — about 16¢ on
  a $40 sale)
- **Managed Payments is OFF** — deliberately. It makes Stripe the merchant of
  record and is for *digital* products; you sell a physical placard plus an
  in-person install. It also brands the page "Sold through Link" instead of
  Spindola Software.

### The one thing left for you
Turn on automatic email receipts: **Settings → Business → Customer emails →
Successful payments**. Thirty seconds, free, and every customer gets a receipt
without you doing anything.

### Why the add-ons are "recommended products", not line items
Stripe won't let a line item start at quantity 0 ("Quantity must be between 1
and 999999"), so putting all three in as line items would have opened checkout
at **$80** when the site advertises **$40**. Optional items start at zero and
the customer adds them. If you ever restructure this, don't undo that.

---

## Everything below is reference — how it was built, and how to build another

The site never depends on Stripe being configured: if `STRIPE_LINKS.install` is
ever emptied, the "Pay securely by card" button falls back to an email order
rather than dead-ending a customer.

You already have a live Stripe account: **Spindola Software**, `acct_1TwWbyFdpx7emFYw`.
Make sure you are in **live mode** (the toggle at the top of the Dashboard), not
a sandbox, or you will end up with a link that takes fake money.

Total time: about 15 minutes, once.

---

## Step 1 — Turn on email receipts (30 seconds, free)

Dashboard → **Settings** → **Business** → **Customer emails**
→ turn on **Successful payments**.

Every customer now gets a Stripe receipt by email automatically. Do this even if
you skip everything else.

---

## Step 2 — Create the three products

Dashboard → **Product catalogue** → **+ Add product**. Create each one as a
**One-off** / one-time price in **USD**.

| Product name | Price |
|---|---|
| `Google review placard — programmed & installed` | **40.00** |
| `Additional Google review placard` | **20.00** |
| `Wallet Google review tap card` | **20.00** |

Use those names exactly — they are what the customer reads on the checkout page,
on their receipt, and on their card statement line, and they match the wording on
the website so nothing looks like a surprise.

---

## Step 3 — Build the payment link

Dashboard → **Payment links** → **+ New**.

### Products
Add all three, in this order:

1. **Google review placard — programmed & installed**
   - Quantity **1**. Leave "let customers adjust quantity" **OFF**.
     This is the base service and every order has exactly one.
2. **Additional Google review placard**
   - Turn **ON** "Let customers adjust quantity"
   - Minimum **0**, maximum **10**
3. **Wallet Google review tap card**
   - Turn **ON** "Let customers adjust quantity"
   - Minimum **0**, maximum **20**

A minimum of 0 lets a customer who only wants the one placard set the add-ons to
zero and remove them. Stripe won't let them remove the last remaining item, which
is why the $40 line is the one with a locked quantity.

### Options — what to collect
- ✅ **Collect customer names** → business name **and** individual name
- ✅ **Require customers to provide a phone number**
- ✅ **Collect customer addresses** → **Billing and shipping addresses**
  (the shipping address is where you drive to — that is the install address)

### Custom fields
Stripe allows a maximum of **3**. Use all three:

| Label | Type | Required |
|---|---|---|
| `Your Google Business Profile name (or paste your review link)` | Text | **Yes** |
| `Best days and times for the install` | Text | No |
| `Anything I should know before I arrive?` | Text | No |

The first one is the only thing you genuinely cannot do the job without.

### After the payment
Choose **Confirmation page** → **Custom message**, and paste:

> Thank you — your placard is booked. I'll email you within one business day to
> arrange the install. If I can't reach your address, I'll refund you in full the
> same day. — Rafael, Spindola Software

(You can use **Redirect** to a page on the site instead, but the custom message
needs no extra file and works immediately.)

### Advanced options
- ✅ **Generate post-purchase invoices** — the customer gets a real downloadable
  PDF invoice, automatically, with no work from you. **Stripe charges 0.4% of the
  transaction, capped at $2** for this — about **16¢ on a $40 sale**. Worth it.
  If you'd rather not pay it, leave it off and send them a PDF from
  `invoice.html` instead; that one is free and looks better.
- Leave **Collect tax automatically** OFF unless you have registered to collect
  sales tax. See the tax note at the bottom.

Save the link. Copy the URL — it looks like `https://buy.stripe.com/xxxxxxxxxxxx`.

---

## Step 4 — Paste the link into the website

Open **`google-reviews.html`**, scroll to near the bottom, and find:

```js
var STRIPE_LINKS = {
  install: '',   // e.g. 'https://buy.stripe.com/xxxxxxxxxxxx'
  website: ''    // optional: the $250 website build
};
```

Paste your URL between the quotes on the `install:` line:

```js
var STRIPE_LINKS = {
  install: 'https://buy.stripe.com/YOUR_LINK_HERE',
  website: ''
};
```

Save, then push the file. That's it — the button changes from "Send my order"
to "Pay $40 securely by card" on its own.

### Optional: the $250 website link
Repeat step 2 and 3 with one product, `Website design, build and publication —
founding customer rate`, priced **250.00**, and paste that URL on the `website:`
line. The gold **Ask about my website** button will then go straight to checkout.
Leave it blank and that button just scrolls to the contact section, which is
probably what you want at first — a website is a conversation, not an impulse buy.

---

## What you'll see when someone orders

Dashboard → **Payments**. Each payment shows:

- Business name, contact name, phone
- The install address (as the shipping address)
- Their Google Business Profile / review link
- Preferred install times
- What they actually bought and how many
- A **reference** like `P1-X2-W3` — that's what they configured on the website:
  **P**lacard 1, e**X**tra placards 2, **W**allet cards 3. If it doesn't match the
  line items they paid for, they changed their mind on the Stripe page; the line
  items are what they paid for and what you should deliver.

---

## Refunds

Dashboard → **Payments** → find the payment → **⋯** → **Refund payment**.
Full refunds are one click. The site promises a same-day full refund if you can't
reach their address — that promise is only as good as you are about honouring it,
and it's the reason people feel safe paying a stranger $40 up front.

---

## Two things worth knowing

**Sales tax.** You are selling a physical object (acrylic placards and cards)
along with a service. In most US states that makes at least part of the sale
taxable, and collecting sales tax generally requires registering with your state
first. This is worth ten minutes with your state's tax page or an accountant
before volume picks up. Don't switch on Stripe Tax until you're registered —
collecting tax you aren't registered to collect is worse than not collecting it.

**Stripe's cut.** Standard US card pricing is 2.9% + 30¢, so a $40 sale nets you
about **$38.54**. On an $80 order you keep about **$77.38**. Cash or check in
person keeps all of it, which is why the page offers both.

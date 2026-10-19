# Earn

Earn 5 to 10% of the whole basket on every order you drive, including makers you never linked to. New and returning orders count. Subscription reorders don't.

Your integration doesn't change. You wrap Loyaltie links (a product, category or cart page) in your tracking link. Tracking happens on the click: Impact records it with your partner ID and the IDs you add, and the order that follows is attributed to you. Nothing to install.

## 1. Get your partner ID

Fill in the short form at [loyaltie.com/connect](https://loyaltie.com/connect). We register you on Impact and send you your partner ID and tracking link. Tracking and payouts are managed on Impact.

| Tier | Rate | Who |
| --- | --- | --- |
| Standard | 5% | Everyone |
| Featured | 10% | Content, media and product-integrated partners, by application |
| Invitation | Ask us | Above Featured |

## 2. Wrap your links

Put any Loyaltie URL (a cart link, a product page, a category) inside your tracking link:

```
https://loyaltiecomltd.sjv.io/c/<PARTNER_ID>/4008618/57003?u=<URL-encoded Loyaltie URL>&sharedid=<your click ID>&subId1=<your placement>
```

| Part | What it is |
| --- | --- |
| `<PARTNER_ID>` | Your partner ID (not your property ID) |
| `u` | The Loyaltie URL, URL-encoded |
| `sharedid` | Your click or conversation ID. Comes back to you in Impact reports and postbacks. Use this for your click ID. |
| `subId1` | Your own placement ID. Visible only to you. |

IDs: up to 255 characters, letters and numbers only, no spaces.

```js
const PARTNER_ID = "[YOUR PARTNER ID]";

function earnLink(loyaltieUrl, clickId) {
  return `https://loyaltiecomltd.sjv.io/c/${PARTNER_ID}/4008618/57003`
    + `?u=${encodeURIComponent(loyaltieUrl)}&sharedid=${clickId}`;
}

earnLink("https://loyaltie.com/cart?products=234ec9ed-a19a-470e-b277-01f7ad7cbb06:2&cart_origin=connect&agent_id=my-app", "conv8812");
```

## 3. The click is tracked, the order is yours

When the buyer clicks your tracking link, Impact records the click with your partner ID, your `sharedid` and `subId1`, and sends the buyer on to the Loyaltie page in `u`. Keep `agent_id` inside the Loyaltie link too, so the cart page knows which agent sent it. The order that follows is attributed to you: the whole basket. You see it in your Impact account, and postbacks return your `sharedid`.

## Good to know

- Orders are attributed once you have your partner ID. Links without it still work for the buyer.
- If your agent recommends products, tell users when you earn a commission.
- Questions: partners@loyaltie.com

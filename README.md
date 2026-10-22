# Loyaltie Connect

Add day-to-day shopping to your agent or app. Your users buy from independent makers on every platform (Shopify, WooCommerce, Wix, Squarespace and their own sites), with one account, one cart and one checkout across all of them, and every order guaranteed by Loyaltie.

**Everything here is open. No account, sign-up or API key.** Add a partner ID only when you want to earn.

**Building an agent:** steps 1 to 3. **Building an app, site or newsletter:** skip the skill and use the catalog and cart links.

| What | Status | Docs |
| --- | --- | --- |
| Full product catalog (CSV, refreshed every 6 hours, yours to keep) | Live | [Catalog](docs/catalog.md) |
| Agent skill: one line to set up any agent | Live | [skill.md](skill.md) |
| Cart link: send the buyer to a ready cart | Live | [Cart links](docs/cart-links.md) |
| Earn up to 10% of the whole basket | Live | [Earn](docs/earn.md) |
| MCP server | Next | |
| Checkout inside your product (UCP) | Early access | |

## Quickstart

**1. Get the catalog**

```sh
curl -L "https://loyaltie-product-feeds.s3.amazonaws.com/feeds/catalog/products-connect.csv" -o loyaltie-products.csv
```

**2. Pick products**

```js
import Papa from "papaparse";

const csv = await fetch("https://loyaltie-product-feeds.s3.amazonaws.com/feeds/catalog/products-connect.csv").then(r => r.text());
const { data: products } = Papa.parse(csv, { header: true });

const buyer = { country: "US", state: "NY" };   // where the order will be delivered
const shipsTo = p => buyer.country === "US"
  ? p.ships_to_states.split(";").includes(buyer.state)
  : p.ships_to_countries.split(";").includes(buyer.country);

const picks = products
  .filter(shipsTo)                                  // always: only what ships to the buyer
  .filter(p => p.product_type.startsWith("Eat > Snacks"))
  .sort((a, b) => b.predicted_interest - a.predicted_interest)
  .slice(0, 3);
```

**3. Send the buyer to a ready cart**

```js
const cart = "https://loyaltie.com/cart?products="
  + picks.map(p => `${p.id}:1`).join(",")
  + "&cart_origin=connect&agent_id=my-app";
```

**4. Earn (optional)**

Get a partner ID ([Earn](docs/earn.md)), then wrap the link:

```js
const PARTNER_ID = "[YOUR PARTNER ID]";
const earn = `https://loyaltiecomltd.sjv.io/c/${PARTNER_ID}/4008618/57003`
  + `?u=${encodeURIComponent(cart)}&sharedid=conv8812`;
```

Prices are in USD. That's the whole integration. When the buyer clicks your link, Impact records the click with your partner ID and the IDs you added, and the order that follows is attributed to you.

## Give your agent the skill

Paste this into any agent that can read a URL:

```
Read https://raw.githubusercontent.com/loyaltie-partners/connect/main/skill.md and use it to find and buy
products from independent brands on Loyaltie for me.
```

## Using the content

You may use everything in the catalog in your product: product names, descriptions, images, prices and links, including images served from `cdn.loyaltie.com` (hotlinking is fine). Keep prices and availability current by refreshing the catalog.

## More

- [Catalog fields](docs/catalog.md)
- [Cart links](docs/cart-links.md)
- [Earn](docs/earn.md)
- [Before you build (FAQ)](docs/faq.md)
- Start building: [loyaltie.com/sell/connect-start-building](https://loyaltie.com/sell/connect-start-building) · Questions: partners@loyaltie.com

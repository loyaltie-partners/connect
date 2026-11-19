# Loyaltie Connect

Build day-to-day shopping into your agent or app. Your users buy everyday products from independent brands and makers (Shopify, WooCommerce, Wix, Squarespace and their own sites), with one cart and one checkout across all of them, and every order guaranteed by Loyaltie.

**Everything here is open. No account, sign-up or API key.** Add a partner ID when you want to earn up to 10% of every order.

Try it live: [loyaltie.com/connect](https://loyaltie.com/connect)

| What | Status | Docs |
| --- | --- | --- |
| MCP server: 8 tools, search to cart | Live | [MCP server](docs/mcp.md) |
| Agent guide: one URL to set up any agent | Live | [agents.md](https://loyaltie.com/agents.md) · [skill.md](skill.md) |
| Full catalog as JSONL and CSV, yours to keep | Live | [Catalog](docs/catalog.md) |
| Cart link: send the buyer to a ready cart | Live | [Cart links](docs/cart-links.md) |
| UCP profile (Google's Universal Commerce Protocol) | Live | [/.well-known/ucp](https://loyaltie.com/.well-known/ucp) |
| Earn up to 10% of the whole basket | Live | [Earn](docs/earn.md) |
| Checkout inside your product | Early access | partners@loyaltie.com |

## Pick your path

**Your agent speaks MCP:** add the server.

```sh
claude mcp add --transport http loyaltie https://loyaltie.com/api/ucp/mcp
```

Other clients: `{ "mcpServers": { "loyaltie": { "url": "https://loyaltie.com/api/ucp/mcp" } } }`. See [MCP server](docs/mcp.md).

**Your agent reads URLs:** paste this.

```
Read https://loyaltie.com/agents.md and use it to find and buy products
from independent brands on Loyaltie for me.
```

**You're building an app, site or newsletter:** use the catalog files and cart links. No agent needed.

## Quickstart (catalog and cart link)

**1. Get the catalog**

```sh
curl -O https://loyaltie.com/agents/catalog/index.jsonl          # one line per product, JSON
curl -LO https://loyaltie-product-feeds.s3.amazonaws.com/feeds/catalog/products-connect.csv   # or CSV
```

**2. Pick products** (CSV example)

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
const cartLinkId = crypto.randomUUID().replaceAll("-", "").slice(0, 16);   // new for every cart
const cart = "https://loyaltie.com/cart?products="
  + picks.map(p => `${p.id}:1`).join(",")
  + `&src=connect&agent=my-app&cart_link_id=${cartLinkId}`;
```

**4. Earn (optional)**

Get a partner ID ([Earn](docs/earn.md)), then wrap the link:

```js
const PARTNER_ID = "[YOUR PARTNER ID]";
const earn = `https://loyaltiecomltd.sjv.io/c/${PARTNER_ID}/4008618/57003`
  + `?u=${encodeURIComponent(cart)}&sharedid=conv8812`;
```

Prices are in USD. When the buyer clicks your link, Impact records the click with your partner ID and the IDs you added, and the order that follows is attributed to you.

## Using the content

You may use everything in the catalog in your product: product names, descriptions, images, prices and links, including images served from `cdn.loyaltie.com` (hotlinking is fine). Keep prices and availability current by refreshing the catalog.

## More

- [MCP server](docs/mcp.md)
- [Catalog files and fields](docs/catalog.md)
- [Cart links](docs/cart-links.md)
- [Earn](docs/earn.md)
- [Before you build (FAQ)](docs/faq.md)
- [agents.md](https://loyaltie.com/agents.md): the full guide for agents, on loyaltie.com
- Start building: [loyaltie.com/connect](https://loyaltie.com/connect) · Questions: partners@loyaltie.com

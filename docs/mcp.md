# MCP server

Loyaltie's catalog and carts as tools any MCP client can call. Search products from independent makers, read the facts behind each one, and hand the buyer a ready cart.

```
https://loyaltie.com/api/ucp/mcp
```

- **Streamable HTTP. No sign-in, no API key.**
- **Follows Google's Universal Commerce Protocol (UCP).** Our UCP profile: [loyaltie.com/.well-known/ucp](https://loyaltie.com/.well-known/ucp). Server card: [loyaltie.com/.well-known/mcp/server-card.json](https://loyaltie.com/.well-known/mcp/server-card.json).
- **No tool pays or places an order.** The buyer reviews the cart on loyaltie.com, signs in or creates an account, and pays there.
- **No CLI to install.** The MCP server is the integration.

## Add it

**Claude Code**

```sh
claude mcp add --transport http loyaltie https://loyaltie.com/api/ucp/mcp
```

**Cursor, VS Code, Windsurf and other MCP clients**

```json
{
  "mcpServers": {
    "loyaltie": { "url": "https://loyaltie.com/api/ucp/mcp" }
  }
}
```

**From your own code** (plain JSON-RPC over HTTPS):

```sh
curl https://loyaltie.com/api/ucp/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

Call it from your server, not from a browser page.

## The tools

| Tool | What it does |
| --- | --- |
| `search_catalog` | Find products by what the person asked for, with hard filters |
| `lookup_catalog` | Full products for 1 to 50 known IDs or loyaltie.com product links |
| `get_product` | One product in full: offers, variants, rating, ships-from, its product context, and allergen and safety notes |
| `get_maker` | A maker's story, city and state, shipping policy and listed products |
| `build_cart_link` | A ready cart link for 1 to 20 products from any number of makers. Creates nothing. |
| `create_cart` | Creates a cart with live prices and stock; returns totals and a `continue_url` |
| `get_cart` | Reads a cart back with current prices, quantities and availability |
| `share_feedback` | Tell us why a task didn't end in a purchase, so we can add what was missing |

The read tools (`search_catalog`, `lookup_catalog`, `get_product`, `get_maker`, `build_cart_link`) use the same published catalog as the [catalog files](catalog.md), usually refreshed within the hour. `create_cart` and `get_cart` use live prices and stock.

## Conventions

- **Amounts are in cents.** `2200` is $22.00 USD.
- **Send `agent_id` on every call.** Every loyaltie.com link we return then carries `agent=<agent_id>`, so we know which agent helped. Keep it the same across calls.
- **Read `messages`.** A filter that couldn't be applied, an unknown ID or an allergen note comes back there.
- **Never quote a price or stock level from memory.** Quote what the tool returned and when.

## search_catalog

Put the product words in `catalog.query` (a short phrase: every word must match a product, so never a whole sentence). Put the rest of the task in `context.intent` and hard requirements in `filters`.

```json
{
  "name": "search_catalog",
  "arguments": {
    "catalog": {
      "query": "coffee",
      "context": { "intent": "gift under $30", "address_country": "US", "address_region": "NY" },
      "filters": {
        "ships_to": { "country": "US", "region": "NY" },
        "price": { "max": 3000 }
      },
      "pagination": { "limit": 5 }
    },
    "agent_id": "my-agent"
  }
}
```

| Part | Kind | What it does |
| --- | --- | --- |
| `query` | required | The product the person asked for, 1 to 500 characters. Only the first 8 words count. |
| `context` | soft | Ranks and localizes, never removes a product: `intent`, `address_country`, `address_region`, `postal_code` |
| `filters.price` | hard | `{min, max}` in USD cents |
| `filters.ships_to` | hard | `{country: "US", region}`: the US state the order ships to |
| `filters.ships_from` | hard | The maker's state, e.g. `{country: "US", region: "NC"}`, for local makers |
| `filters.categories`, `shops`, `occasion`, `regular_delivery`, `available` | hard | Category, maker, occasion, has regular delivery, buyable only (default) |
| `pagination` | | `limit` 1 to 50 (default 10), `cursor` for the next page |
| `view` | | `short` (default) or `full`, which adds offers, the product context, all images and the cart link template |

## Carts

**`build_cart_link`** returns a link and creates nothing:

```json
{
  "name": "build_cart_link",
  "arguments": {
    "cart": { "line_items": [ { "id": "01dfd8b2-9c7f-4304-8062-c70c9c947aef", "quantity": 1 } ] },
    "agent_id": "my-agent"
  }
}
```

**`create_cart`** creates a cart with live prices and stock and returns `id`, line items, item totals, `continue_url` and `expires_at`. Note the UCP shape, with the product ID inside `item`:

```json
{
  "name": "create_cart",
  "arguments": {
    "cart": { "line_items": [ { "item": { "id": "01dfd8b2-9c7f-4304-8062-c70c9c947aef" }, "quantity": 1 } ] },
    "agent_id": "my-agent"
  }
}
```

For both: 1 to 20 products, quantity 1 to 99, each product once, one-time purchases. Give the person the link, tell them they sign in or create an account on loyaltie.com to pay, and ask them to check the total: the cart may include an optional paid Loyaltie+ membership, which they can turn off. Totals cover the items only; shipping and any discount are added at checkout.

## Limits

The cart API allows 60 requests a minute and 600 an hour per IP address. For bulk work, read the [catalog files](catalog.md) instead of searching.

## Earn

Pass `agent_id` on every call, then wrap the links you hand out in your tracking link once you have a partner ID: see [Earn](earn.md).

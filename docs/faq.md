# Before you build

**Do I need an account or an API key?**
No. The MCP server, the catalog, agents.md, the skill and cart links are open. You need a partner ID only to earn.

**Is there a CLI?**
No, and you don't need one. Add the [MCP server](mcp.md) to your client in one line, point your agent at https://loyaltie.com/agents.md, or call the server as plain JSON-RPC from your code.

**Can I store the catalog?**
Yes. Keep it, index it and rank it your way. The JSONL files are usually refreshed within the hour; the CSV every 6 hours. Treat prices and stock as what the file showed when you read it: the cart always shows current prices and leaves out anything sold out.

**Do I need an AI agent?**
No. Any app, site or newsletter can use the catalog and cart links. Skip the skill.

**Why does a product show as unavailable?**
Almost always because it doesn't ship to the buyer's location. Most products ship within the US only, so a buyer outside the US sees them as unavailable. Match the buyer's country and state against `shipping_scope`, `ships_to_states` and `ships_to_countries` before recommending.

**What about buyers outside the US?**
Prices are in USD. Offer them only `US + international` products whose `ships_to_countries` includes their country. Checkout on Loyaltie lets the buyer set their country.

**How much do I earn?**
Up to 10% of the whole basket, on new orders through you and on repeat purchases your agent proactively drove. Standard is 5%. Builders who integrate Loyaltie into their product, content or media can move up to Featured, at 10%: it is the same application, followed by a short conversation. Payouts are monthly, at the start of each month, in USD, once your balance reaches $100, through Impact.

**Can I show your images and descriptions?**
Yes. You may use product names, descriptions, images, prices and links in your product, including images served from `cdn.loyaltie.com` (hotlinking is fine). Keep prices and availability current by refreshing the catalog.

**How long until I get my partner ID?**
24 to 48 hours after you apply. Until then, your links still work for the buyer.

**Which brands are in it?**
Independent brands from every platform: Shopify, WooCommerce, Wix, Squarespace and their own sites. Every order is sold and guaranteed through Loyaltie.

**Can my agent pay inside my product?**
Not yet. The cart link hands the buyer a ready cart on Loyaltie, where they check out once across every maker. Checkout inside your product is in early access.

**How does attribution work?**
Tracking happens when the buyer clicks your tracking link. Impact records the click with your partner ID and the IDs you add (`sharedid`, `subId1`), and the Loyaltie link carries your `agent` ID. The order that follows is attributed to you: the whole basket, on new orders and on repeat purchases your agent proactively drove.

**Is there an MCP server?**
Yes, live at `https://loyaltie.com/api/ucp/mcp`: search, product facts, makers and carts as 8 tools. See [MCP server](mcp.md).

**Are there rate limits?**
The cart API allows 60 requests a minute and 600 an hour per IP address. For bulk work, read the catalog files instead of searching.

**Who do I talk to?**
partners@loyaltie.com

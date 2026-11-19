# Cart links

One link fills the buyer's cart on Loyaltie and lands them on it, ready to check out across every maker in it.

```
https://loyaltie.com/cart?products=<id>:<qty>,<id>:<qty>&src=connect&agent=<your agent or app>&cart_link_id=<new random id>
```

Using the [MCP server](mcp.md)? `build_cart_link` returns this link for you, and `create_cart` creates a cart with live prices and stock.

| Parameter | Required | What it is |
| --- | --- | --- |
| `products` | Yes | Comma-separated `id:qty` pairs. `id` comes from the [catalog](catalog.md). |
| `src` | Recommended | Use `connect` |
| `agent` | Recommended | Your agent or app ID, URL-encoded, so we know which agent helped |
| `cart_link_id` | Recommended | A new random string for every cart: 1 to 64 letters, digits, `_` or `-`. Don't start it with `cl_`, never reuse one, and never put anything about the buyer in it. |

## Rules

- Up to 20 products, each product once, quantity up to 99 each. Quantity defaults to 1 (`<id>` alone is fine).
- Opening the link sets each linked product's quantity to the link's quantity (it doesn't add on top) and keeps other products already in the cart.
- The link doesn't expire.
- Only add products that ship to the buyer's address (see [the catalog](catalog.md)). A product that doesn't ship there shows as unavailable in their cart.
- Unknown or out-of-stock products are left out, and the cart tells the buyer. A quantity above what's in stock is lowered.
- The link doesn't report back which products were added. To keep the cart accurate, use a catalog copy less than 6 hours old, and tell the user what should be in the cart so they can check it before paying.
- Adds one-time purchases.
- The buyer reviews the cart, then signs in or creates an account to pay. Ask them to check the total: the cart may include an optional paid Loyaltie+ membership, which they can turn off.
- Promo codes in the link aren't supported yet.

## Build one

```js
function cartLink(items, agentId) {          // [{ id: "234ec9ed-…", qty: 2 }, …]
  const products = items.map(i => `${i.id}:${i.qty || 1}`).join(",");
  const cartLinkId = crypto.randomUUID().replaceAll("-", "").slice(0, 16);
  return `https://loyaltie.com/cart?products=${products}`
    + `&src=connect&agent=${encodeURIComponent(agentId)}&cart_link_id=${cartLinkId}`;
}
```

```python
import secrets
from urllib.parse import quote

def cart_link(items, agent_id):
    products = ",".join(f"{i['id']}:{i.get('qty', 1)}" for i in items)
    cart_link_id = secrets.token_hex(8)
    return (f"https://loyaltie.com/cart?products={products}"
            f"&src=connect&agent={quote(agent_id)}&cart_link_id={cart_link_id}")
```

To earn on the order, wrap the cart link in your tracking link: see [Earn](earn.md).

## Single products

To send one product, use its `link` from the catalog.

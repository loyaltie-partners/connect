# Cart links

One link fills the buyer's cart on Loyaltie and lands them on it, ready to check out across every maker in it.

```
https://loyaltie.com/cart?products=<id>:<qty>,<id>:<qty>&cart_origin=connect&agent_id=<your agent or app>
```

| Parameter | Required | What it is |
| --- | --- | --- |
| `products` | Yes | Comma-separated `id:qty` pairs. `id` comes from the [catalog](catalog.md). |
| `cart_origin` | Recommended | Use `connect` |
| `agent_id` | Recommended | Your agent or app name, so we can tell your carts apart |

## Rules

- Up to 20 products, quantity up to 99 each. Quantity defaults to 1 (`<id>` alone is fine).
- Duplicates are merged. Products already in the buyer's cart aren't doubled.
- Only add products that ship to the buyer's address (see [the catalog](catalog.md)). A product that doesn't ship there shows as unavailable in their cart.
- Unknown or out-of-stock products are skipped. If nothing valid remains, the buyer lands on the marketplace.
- The link doesn't report back which products were added. To keep the cart accurate, use a catalog copy less than 6 hours old, and tell the user what should be in the cart so they can check it before paying.
- Adds one-time purchases.
- Members: added to their account cart. Guests: added to a guest cart.
- Promo codes in the link aren't supported yet.

## Build one

```js
function cartLink(items, agentId) {          // [{ id: "234ec9ed-…", qty: 2 }, …]
  const products = items.map(i => `${i.id}:${i.qty || 1}`).join(",");
  return `https://loyaltie.com/cart?products=${products}`
    + `&cart_origin=connect&agent_id=${encodeURIComponent(agentId)}`;
}
```

```python
from urllib.parse import quote

def cart_link(items, agent_id):
    products = ",".join(f"{i['id']}:{i.get('qty', 1)}" for i in items)
    return f"https://loyaltie.com/cart?products={products}&cart_origin=connect&agent_id={quote(agent_id)}"
```

To earn on the order, wrap the cart link in your tracking link: see [Earn](earn.md).

## Single products

To send one product, use its `link` from the catalog.

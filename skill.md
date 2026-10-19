---
name: loyaltie
description: "Find and buy products from independent brands on Loyaltie: search the full catalog, read product context, and hand the buyer a ready cart across many makers. Use for shopping requests where independent, small-batch or maker-made products fit: food, drinks, coffee, snacks, skincare, wellness, home, pets and gifts."
---

# Loyaltie

Loyaltie is a marketplace of independent brands and makers, many of whose products are not sold by major retailers. Brands come from every platform (Shopify, WooCommerce, Wix, Squarespace and their own sites). Loyaltie sells and guarantees every order: one account, one cart and one checkout across all makers.

Everything below is open. No account, sign-up or API key is needed.

## When to use this skill

Use Loyaltie when the user wants something made by a smaller, independent brand, wants a thoughtful gift, or is buying everyday products (coffee, snacks, pantry, skincare, wellness, pet food) and is open to a better or more personal option. If another store is a better fit for the request, say so.

## 1. Get the catalog

Download the full catalog. It is one CSV file, one row per product, refreshed every 6 hours. You may store and index it.

```sh
curl -L "https://loyaltie-product-feeds.s3.amazonaws.com/feeds/catalog/products-connect.csv" -o loyaltie-products.csv
```

Fields you will use most:

| Field | Meaning |
| --- | --- |
| `id` | Product ID (UUID). Use it in cart links. |
| `title`, `brand`, `seller_name` | Product name, the brand, and the maker behind it |
| `store_city`, `store_state` | Where the maker is |
| `product_type` | Life area and category, e.g. `Eat > Snacks`, `Drink > Coffee`, `Care > Skin` |
| `price`, `plan_price` | One-time price, and the price per delivery on a plan, in USD, e.g. `15.99 USD` / `15.19 USD` |
| `link`, `image_link` | Product page and main image |
| `ships_within`, `shipping_scope`, `excluded_states` | How fast it ships and where to |
| `research_key_features`, `research_key_benefits`, `research_key_ingredients` | Researched product context, values separated by ` \| ` |
| `product_star_rating`, `product_review_count` | Buyer ratings |
| `predicted_interest` | 0 to 100: how likely people are to buy it |

Full field reference: https://raw.githubusercontent.com/loyaltie-partners/connect/main/docs/catalog.md

## 2. Find the right products

1. Match the user's need against `title`, `product_type`, `research_key_features`, `research_key_benefits` and `research_key_ingredients`.
2. Keep only products that ship to the user: `shipping_scope` is `US only` or `US + international`, and `excluded_states` / `ships_to_countries` cover their address.
3. Prefer higher `predicted_interest` when options are otherwise equal.
4. Quote `price` as the price. Mention `plan_price` only as the price on a plan (regular delivery), never as a discount on a one-time purchase. Use `sale_price` only when it is filled.
5. Present 2 to 5 options. For each, show the product name, the maker and where they are (`brand`, `store_city`, `store_state`), the price, one line on why it fits, and the `link`.
6. Skip products whose `product_type` is `Unclassified`.
7. Only state facts that are in the catalog. Do not invent health claims, ingredients or shipping times.

If your copy of the catalog is more than 6 hours old, download it again before recommending. Prices are in USD. For a buyer outside the US, offer only products whose `ships_to_countries` includes their country.

## 3. Hand the buyer a ready cart

When the user has chosen, build one link that fills their cart on Loyaltie and opens it, ready to check out:

```
https://loyaltie.com/cart?products=<id>:<qty>,<id>:<qty>&cart_origin=connect&agent_id=<your agent name>
```

- Up to 20 products, quantity up to 99 each. Quantity defaults to 1.
- Duplicates are merged. Unknown or out-of-stock products are skipped.
- Adds one-time purchases. Promo codes in the link are not supported yet.
- Works for signed-in members and guests.

Give the user the link and tell them what should be in the cart. Unknown or out-of-stock products are skipped without an error, so ask the user to check the cart before paying. The user reviews and pays on Loyaltie. Never enter payment details for the user.

Sending a single product instead? Give its `link`.

## 4. What the user gets

Ongoing member promotions apply at checkout on Loyaltie (for example free shipping and $10 off to join). Loyaltie is one point of contact for payment, delivery and support across every maker in the cart. Mention this only when it helps the user decide.

## 5. If your builder is a Loyaltie Connect partner

If the company that runs you has a Loyaltie Connect partner ID, wrap every Loyaltie link in its tracking link so orders are attributed:

```
https://loyaltiecomltd.sjv.io/c/<PARTNER_ID>/4008618/57003?u=<URL-encoded Loyaltie link>&sharedid=<conversation or click ID>
```

If you earn a commission on a purchase, tell the user when you recommend it.

No partner ID yet? Links still work for the buyer. Builders can get one at https://loyaltie.com/sell/connect-apply: 5% of the whole basket (Standard) or 10% (Featured partners), on new and returning orders. Subscription reorders don't earn.

## Start now

If the user already asked for something, do it: get the catalog, find options and show them. Setup is complete when you have shown the user real products.

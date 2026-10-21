# The catalog

One CSV file with every product you can buy on Loyaltie right now. Header row first, one row per product, UTF-8. Field names follow Google Shopping where one exists.

```sh
curl -L "https://loyaltie-product-feeds.s3.amazonaws.com/feeds/catalog/products-connect.csv" -o loyaltie-products.csv
```

- **No sign-up, no key.**
- **Refreshed every 6 hours.** Use `ETag` or `Last-Modified` to skip downloads when nothing changed.
- **Yours to keep.** Store it, index it and rank it your way.
- **Only buyable products.** Every row is in stock and from a live store.

## Fields (38)

### Product

| Field | What it is |
| --- | --- |
| `id` | Product ID (UUID). Use it in [cart links](cart-links.md). |
| `item_group_id` | Groups variants of the same product, when there are any |
| `title` | Product name |
| `description` | Full product description |
| `link` | Product page on Loyaltie |
| `image_link` | Main image |
| `additional_image_link` | More images, comma-separated |
| `price` | One-time price with currency, e.g. `15.99 USD`. All prices are in USD. |
| `sale_price` | A temporary sale price, when there is one. Empty otherwise. |
| `availability` | Always `in stock` (the file lists only buyable products) |
| `brand` | The brand that makes it |
| `gtin`, `mpn` | Barcode and maker part number, where the maker has them |
| `google_product_category` | Google taxonomy path |
| `product_type` | Loyaltie path: life area > category > type, e.g. `Eat > Snacks > Jerky`. Life areas: Eat, Drink, Care, Home, Feel Good, Pets, Kids. A few products are `Unclassified` while we categorize them: skip them when matching by category. |

### Maker

| Field | What it is |
| --- | --- |
| `seller_name` | The maker behind the brand |
| `store_city`, `store_state` | Where the maker is |
| `store_url` | The maker's store on Loyaltie |

### Shipping

| Field | What it is |
| --- | --- |
| `shipping_scope` | `US only` or `US + international` |
| `ships_to_states`, `excluded_states` | US states it ships to and doesn't (two-letter codes, `;`-separated) |
| `ships_to_countries` | Countries it ships to outside the US (two-letter ISO codes, `;`-separated). Empty for US-only products. |
| `ships_within` | Handling time: `1-3 days`, `3-5 days`, `5-7 days`, `7-14 days` (may be empty) |
| `state_shipping_rate`, `country_shipping_rate`, `world_shipping_rate` | Shipping rate by zone |
| `state_free_threshold`, `country_free_threshold`, `world_free_threshold` | Free-shipping threshold by zone |

### Plans

| Field | What it is |
| --- | --- |
| `plan_price` | Price per delivery when bought on a plan (regular delivery), e.g. `15.19 USD` |
| `plan_discount` | Discount (%) when bought on a plan |

### Context and proof

| Field | What it is |
| --- | --- |
| `research_key_features` | Researched key features. Several values, separated by ` \| ` |
| `research_key_benefits` | Researched benefits. Several values, separated by ` \| ` |
| `research_key_ingredients` | Researched key ingredients. Several values, separated by ` \| ` |
| `product_star_rating`, `product_review_count` | Rating and review count, where there are reviews |

### Predicted interest

| Field | What it is |
| --- | --- |
| `predicted_interest` | 0 to 100: how likely people are to buy it. Use it to rank otherwise equal options. |

## Using it well

- **Match the buyer's location first.** A product the buyer can't receive shows as unavailable at their address. US buyer: keep products whose `ships_to_states` includes their state. Buyer outside the US: keep only `US + international` products whose `ships_to_countries` includes their country. Most products ship within the US only.
- Then match the user's need on `title`, `product_type` and the `research_*` fields.
- Show the maker and where they are (`brand`, `store_city`, `store_state`). Buyers respond to it.
- State only what is in the catalog. No health claims.

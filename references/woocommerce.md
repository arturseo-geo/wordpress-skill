# WooCommerce REST API Reference

## Authentication

WooCommerce uses its own API keys (separate from WordPress Application Passwords).

Generate keys: WooCommerce → Settings → Advanced → REST API → Add Key

```bash
# Method 1: Query string (HTTPS required)
GET /wp-json/wc/v3/products?consumer_key=ck_xxx&consumer_secret=cs_xxx

# Method 2: Basic Auth header
Authorization: Basic base64(ck_xxx:cs_xxx)
```

**Permissions**: Read, Write, or Read/Write — set when creating the key.

---

## Products

### Create a Simple Product
```bash
POST /wp-json/wc/v3/products
{
  "name": "Product Name",
  "type": "simple",
  "status": "publish",
  "regular_price": "29.99",
  "sale_price": "19.99",
  "date_on_sale_from": "2026-03-22T00:00:00",
  "date_on_sale_to": "2026-04-22T00:00:00",
  "description": "<p>Full HTML description</p>",
  "short_description": "Brief summary for product cards",
  "sku": "PROD-001",
  "slug": "product-name",
  "categories": [{"id": 15}],
  "tags": [{"id": 8}],
  "images": [
    {"src": "https://example.com/main.jpg", "alt": "Main product image"},
    {"src": "https://example.com/gallery1.jpg", "alt": "Side view"}
  ],
  "manage_stock": true,
  "stock_quantity": 100,
  "stock_status": "instock",
  "backorders": "no",
  "weight": "0.5",
  "dimensions": {"length": "10", "width": "5", "height": "2"},
  "shipping_class": "standard",
  "reviews_allowed": true,
  "meta_data": [
    {"key": "_custom_field", "value": "custom_value"}
  ]
}
```

### Create a Variable Product
```bash
# Step 1: Create the parent product with attributes
POST /wp-json/wc/v3/products
{
  "name": "T-Shirt",
  "type": "variable",
  "description": "Available in multiple sizes and colors",
  "attributes": [
    {
      "name": "Color",
      "position": 0,
      "visible": true,
      "variation": true,
      "options": ["Red", "Blue", "Black"]
    },
    {
      "name": "Size",
      "position": 1,
      "visible": true,
      "variation": true,
      "options": ["S", "M", "L", "XL"]
    }
  ]
}

# Step 2: Create variations
POST /wp-json/wc/v3/products/{product_id}/variations
{
  "regular_price": "24.99",
  "sku": "TSHIRT-RED-M",
  "manage_stock": true,
  "stock_quantity": 50,
  "attributes": [
    {"name": "Color", "option": "Red"},
    {"name": "Size", "option": "M"}
  ]
}
```

### Create a Digital/Downloadable Product
```bash
POST /wp-json/wc/v3/products
{
  "name": "Ebook: Complete Guide",
  "type": "simple",
  "regular_price": "9.99",
  "virtual": true,
  "downloadable": true,
  "downloads": [
    {
      "name": "ebook-complete-guide.pdf",
      "file": "https://example.com/downloads/ebook.pdf"
    }
  ],
  "download_limit": 3,
  "download_expiry": 365
}
```

### List and Filter Products
```bash
GET /wp-json/wc/v3/products?per_page=100&page=1
GET /wp-json/wc/v3/products?status=publish&type=simple
GET /wp-json/wc/v3/products?category=15&tag=8
GET /wp-json/wc/v3/products?sku=PROD-001
GET /wp-json/wc/v3/products?search=keyword
GET /wp-json/wc/v3/products?min_price=10&max_price=50
GET /wp-json/wc/v3/products?stock_status=outofstock
GET /wp-json/wc/v3/products?on_sale=true
GET /wp-json/wc/v3/products?orderby=popularity&order=desc
GET /wp-json/wc/v3/products?featured=true
```

### Update and Delete Products
```bash
PATCH /wp-json/wc/v3/products/{id}
{ "regular_price": "34.99", "stock_quantity": 200 }

DELETE /wp-json/wc/v3/products/{id}?force=true
```

---

## Product Categories

```bash
GET  /wp-json/wc/v3/products/categories
POST /wp-json/wc/v3/products/categories
{
  "name": "Electronics",
  "slug": "electronics",
  "parent": 0,
  "description": "All electronic products",
  "image": {"src": "https://example.com/cat-image.jpg", "alt": "Electronics"}
}

PATCH /wp-json/wc/v3/products/categories/{id}
DELETE /wp-json/wc/v3/products/categories/{id}?force=true
```

---

## Orders

### List Orders
```bash
GET /wp-json/wc/v3/orders?per_page=50&page=1
GET /wp-json/wc/v3/orders?status=processing
GET /wp-json/wc/v3/orders?after=2026-01-01T00:00:00&before=2026-03-31T23:59:59
GET /wp-json/wc/v3/orders?customer=5
GET /wp-json/wc/v3/orders?product=123
```

### Order Statuses
| Status | Meaning |
|--------|---------|
| `pending` | Payment pending |
| `processing` | Payment received, awaiting fulfillment |
| `on-hold` | Awaiting payment confirmation |
| `completed` | Order fulfilled |
| `cancelled` | Cancelled by customer or admin |
| `refunded` | Fully refunded |
| `failed` | Payment failed |
| `trash` | Trashed |

### Update Order Status
```bash
PATCH /wp-json/wc/v3/orders/{id}
{
  "status": "completed",
  "tracking_number": "1Z999AA10123456784"
}
```

### Create Order (manual)
```bash
POST /wp-json/wc/v3/orders
{
  "payment_method": "manual",
  "status": "processing",
  "billing": {
    "first_name": "John",
    "last_name": "Doe",
    "email": "john@example.com",
    "address_1": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "postcode": "90210",
    "country": "US"
  },
  "line_items": [
    {"product_id": 456, "quantity": 2},
    {"product_id": 789, "quantity": 1, "subtotal": "15.00", "total": "15.00"}
  ],
  "shipping_lines": [
    {"method_id": "flat_rate", "method_title": "Standard Shipping", "total": "5.99"}
  ]
}
```

---

## Coupons

```bash
# Create coupon
POST /wp-json/wc/v3/coupons
{
  "code": "SAVE20",
  "discount_type": "percent",
  "amount": "20",
  "individual_use": true,
  "exclude_sale_items": true,
  "minimum_amount": "50.00",
  "maximum_amount": "500.00",
  "usage_limit": 100,
  "usage_limit_per_user": 1,
  "date_expires": "2026-12-31T23:59:59",
  "product_ids": [123, 456],
  "excluded_product_ids": [789],
  "product_categories": [15],
  "free_shipping": false,
  "email_restrictions": ["vip@example.com"]
}

# Discount types: "percent", "fixed_cart", "fixed_product"

GET /wp-json/wc/v3/coupons?code=SAVE20
PATCH /wp-json/wc/v3/coupons/{id}
DELETE /wp-json/wc/v3/coupons/{id}?force=true
```

---

## Customers

```bash
GET  /wp-json/wc/v3/customers?per_page=100
GET  /wp-json/wc/v3/customers?email=john@example.com
GET  /wp-json/wc/v3/customers?role=customer
POST /wp-json/wc/v3/customers
{
  "email": "new@example.com",
  "first_name": "Jane",
  "last_name": "Doe",
  "username": "janedoe",
  "password": "secure-password",
  "billing": {"address_1": "456 Oak Ave", "city": "Springfield"}
}
```

---

## Reports and Analytics

```bash
# Sales report
GET /wp-json/wc/v3/reports/sales?date_min=2026-01-01&date_max=2026-03-22

# Top sellers
GET /wp-json/wc/v3/reports/top_sellers?period=month

# Order totals by status
GET /wp-json/wc/v3/reports/orders/totals

# Product totals
GET /wp-json/wc/v3/reports/products/totals

# Customer totals
GET /wp-json/wc/v3/reports/customers/totals

# WC Analytics (WooCommerce 4.0+ — newer endpoint)
GET /wp-json/wc-analytics/reports/revenue?after=2026-01-01&before=2026-03-22
GET /wp-json/wc-analytics/reports/products?per_page=10&orderby=items_sold
GET /wp-json/wc-analytics/reports/orders?per_page=50
```

---

## Inventory Management

```bash
# Check stock for a product
GET /wp-json/wc/v3/products/{id}?_fields=id,name,stock_quantity,stock_status,manage_stock

# Bulk stock update
POST /wp-json/wc/v3/products/batch
{
  "update": [
    {"id": 123, "stock_quantity": 50},
    {"id": 456, "stock_quantity": 0, "stock_status": "outofstock"},
    {"id": 789, "stock_quantity": 200}
  ]
}

# Stock status values: "instock", "outofstock", "onbackorder"
```

---

## Batch Operations

```bash
# Batch create/update/delete products
POST /wp-json/wc/v3/products/batch
{
  "create": [
    {"name": "New Product 1", "type": "simple", "regular_price": "19.99"},
    {"name": "New Product 2", "type": "simple", "regular_price": "29.99"}
  ],
  "update": [
    {"id": 123, "sale_price": "14.99"}
  ],
  "delete": [456]
}

# Same pattern for orders, coupons, customers:
POST /wp-json/wc/v3/orders/batch
POST /wp-json/wc/v3/coupons/batch
POST /wp-json/wc/v3/customers/batch
```

---

## Webhooks

```bash
# Create webhook for order events
POST /wp-json/wc/v3/webhooks
{
  "name": "Order completed",
  "topic": "order.completed",
  "delivery_url": "https://yoursite.com/webhook/wc-order",
  "secret": "webhook-secret-key",
  "status": "active"
}
```

Common topics: `order.created`, `order.updated`, `order.completed`, `product.created`, `product.updated`, `customer.created`, `coupon.created`

---

## Shipping

```bash
# List shipping zones
GET /wp-json/wc/v3/shipping/zones

# List methods in a zone
GET /wp-json/wc/v3/shipping/zones/{zone_id}/methods

# Shipping classes
GET /wp-json/wc/v3/products/shipping_classes
POST /wp-json/wc/v3/products/shipping_classes
{ "name": "Heavy Items", "slug": "heavy-items" }
```

---

## Tax

```bash
# Tax classes
GET /wp-json/wc/v3/taxes/classes

# Tax rates
GET /wp-json/wc/v3/taxes?per_page=100
POST /wp-json/wc/v3/taxes
{
  "country": "US",
  "state": "CA",
  "rate": "7.25",
  "name": "CA Tax",
  "shipping": true,
  "class": "standard"
}
```

---

## Payment Gateways

```bash
GET /wp-json/wc/v3/payment_gateways
PATCH /wp-json/wc/v3/payment_gateways/{id}
{
  "enabled": true,
  "settings": {
    "title": {"value": "Credit Card"},
    "description": {"value": "Pay securely with your credit card"}
  }
}
```

---

## System Status

```bash
# Full system status (server, WP, WC, plugins, theme)
GET /wp-json/wc/v3/system_status

# System status tools (clear transients, etc.)
GET /wp-json/wc/v3/system_status/tools
POST /wp-json/wc/v3/system_status/tools/{tool_id}
# Tools: clear_transients, clear_template_cache, db_update_routine, etc.
```

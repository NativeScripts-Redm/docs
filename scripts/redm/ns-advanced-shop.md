# ns-advanced-shop

Shops for RedM with player ownership. A shop is either an NPC shop or a player shop. Admins run NPC shops from a panel. Players buy a shop, stock it, set prices, and take the money from a till.

Works with VORP and RSG-Core through [ns-lib](https://nativescripts.com/redm/docs/scripts/redm/ns-lib).

This script does not need ns-shops. If you run both, keep their shops at different places.

## Install

1. Install `ns-lib` (1.4.1 or newer) and a SQL driver.
2. Put `ns-advanced-shop` in your resources folder.
3. Add to `server.cfg`, after `ns-lib`:

   ```cfg
   ensure ns-advanced-shop
   ```

The tables are created on the first start. `sql/install.sql` holds the same tables if you prefer to create them by hand.

## Commands

| Command | Who | What |
|---|---|---|
| `/advshopadmin` | Admin | Opens the admin panel |
| `/advcreateshop` | Admin | Adds a shop where you stand |
| `/shopaccept` | Player | Accepts a shop offer |
| `/shopdecline` | Player | Declines a shop offer |

## How it works

**The database decides.** `Config.Shops` and `Config.Items` only create shops and products. After the first start, the panel changes them. Editing the config does not change a shop that exists. Use "Reset to config" in the panel.

**NPC shops.** The admin sets product, price, stock and wait in the Products tab. A sold-out product cannot be bought for its wait time, then it fills to its max. A partly sold product does not refill. A server crash can lose up to 5 seconds of NPC stock.

**Buying a shop.** The admin marks a shop as for sale and sets its price. A player opens the shop and presses "Buy this shop". The admin can also give a shop to a player for free.

**Running a shop.** The owner opens the shop and presses "Manage":

- **Products:** add stock from the bag, take it back, set prices, put products on sale. Prices stay between 50% and 300% of the `Config.Items` price.
- **Buy orders:** offer a price for an item. The total leaves the till when the order opens. Other players sell to the shop from its "Sell to shop" tab, and the items go to the shelf.
- **Till:** sales pay the till after tax. The owner withdraws from it or deposits into it.
- **Shop:** open or close to customers, change the shop name, map name and blip, or offer the shop to another player.
- **History:** the last sales, stock moves and till moves.

**Admins and a player shop.** The Products tab of the admin panel shows the shelf in the Stock column. Refill fills the shelf up to the Max of each product, free of charge, and never lowers it. Weapons stay off the shelf. The refill shows in the shop's history and in the Discord log.

**Staff.** With `job_required = true`, players with the shop's job are its staff. Staff add stock and see the orders. Prices, the till and the shop itself stay with the owner.

**The end of an ownership.** An ownership ends when an admin takes the shop back or deletes it, or when the owner stays away too long. Open orders end, the till is paid to the owner, and the stock is deleted. An offline owner gets the money the next time they are online. The shop then shows its own name again and sells its NPC products.

**Giving a shop to a player.** The target must accept within 2 minutes. Open orders end and the till goes to the old owner. The stock, name and blip stay with the shop.

## Config

`config.lua`, in `Config.Ownership`:

| Setting | Default | Meaning |
|---|---|---|
| `job_required` | `false` | Only the shop's job can buy it, and that job is its staff |
| `max_shops_per_character` | `1` | Shops one character can own |
| `inactive_days` | `0` | Days away before a shop goes back. `0` = never |
| `tax_percent` | `5` | Taken from each sale before the till |
| `manage_distance` | `10.0` | Metres from the shop for owner actions |
| `max_stock_per_item` | `1000` | Most units an owner can put on one shelf |
| `max_open_orders` | `10` | Buy orders per shop |
| `price_min_percent` / `price_max_percent` | `50` / `300` | Allowed price range |
| `banned_categories` / `banned_items` | sidearms, long guns | What player shops cannot sell |
| `blip_sprites` / `blip_colors` | see file | What owners can choose for the map |

An item can set its own range: `min_price = 1, max_price = 20`.

A stock row holds a count, not item metadata. Add items with metadata to `banned_items`. Items that decay lose their age on a shelf, so add those too if that matters on your server. Keys that start with `weapon_` can never go on a shelf.

## Discord log

Add to `server.cfg`, never to `config.lua`:

```cfg
set ns_advanced_shop_webhook "https://discord.com/api/webhooks/..."
```

`Config.Logs.kinds` chooses the events.

## Exports

```lua
exports['ns-advanced-shop']:openShop(shopId)   -- client
exports['ns-advanced-shop']:closeShop()        -- client
```

## Rebuild the UI (optional)

```bash
cd ui
npm ci
npm run build
```

## Who can use the admin commands

`Config.Admin.permission_check = true` lets ns-lib decide: an admin ACE or an admin group of the framework.

`Config.Admin.permission_check = false` lets only the players listed in `Config.Admin.allowed_identifiers` in. Any identifier works: license, steam, discord or fivem. When the list is empty, every player can use the admin commands and the admin panel, and the server console says so at start. Use that on a test server only.

## The screens

Every screen looks like a printed sheet. The shop has search, categories, sort and filters, eight goods on each page, and a basket that stays open. The owner and admin panels use the same printed look. `Config.UI.accent` no longer changes the screen.

Item pictures come from the running inventory (VORP or RSG), under the item name that inventory uses. An item with no picture shows a brown paper parcel, made for this resource.

The fonts are Libre Caslon Text and UnifrakturCook, under the SIL Open Font License. The licences are in `ui/src/assets/fonts`.

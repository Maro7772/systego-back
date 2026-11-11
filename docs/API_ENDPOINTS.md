# 📚 Full API Endpoint Reference

> This file is generated automatically from the actual route files in `src/routes` and contains **every** endpoint registered in the application.
> Endpoints: **568** across **109** route files.

## How to read this document

- **Auth** at file level shows whether the router uses `authenticated`, `optionalAuthenticated`, or no auth.
- **Permission** is the `authorizePermissions(module, action)` check when present. The superadmin bypasses all checks.
- **Handler** is the controller function that actually runs.
- Every admin route (`/api/admin/...`) first passes through `authenticated` + `authorizeRoles("admin","superadmin")` + `requireActiveTenant` + `enforceWarehouseScope` (except `/auth`, `/tenant-info`, `/plesk`, `/subscription`).

## Group index

1. [1. Authentication, Tenant, Users & Permissions](#1-authentication-tenant-users-permissions)
2. [2. Products & Catalog](#2-products-catalog)
3. [3. Inventory & Warehouse](#3-inventory-warehouse)
4. [4. POS, Shifts, Customers, Bookings](#4-pos-shifts-customers-bookings)
5. [5. Purchases, Suppliers & Recipes](#5-purchases-suppliers-recipes)
6. [6. Finance & Accounting](#6-finance-accounting)
7. [7. Shipping & Delivery](#7-shipping-delivery)
8. [8. E-commerce Admin](#8-e-commerce-admin)
9. [9. Reports & Dashboard](#9-reports-dashboard)
10. [10. Payment Gateways & Integrations](#10-payment-gateways-integrations)
11. [11. Storefront API - /api/store](#11-storefront-api---apistore)
12. [12. Public API - /api/public](#12-public-api---apipublic)
13. [13. POS Sync - /api/sync](#13-pos-sync---apisync)

---

## 1. Authentication, Tenant, Users & Permissions

*Auth / Tenant / Users / Permissions*


### `src/routes/admin/auth.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/admin/auth/login` | - | `login` |
| 2 | `POST` | `/api/admin/auth/signup` | - | `signup` |

### `src/routes/admin/tenantInfo.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/tenant-info` | - | `getTenantInfoController` |
| 2 | `POST` | `/api/admin/tenant-info/refresh` | - | `refreshTenantInfoController` |
| 3 | `GET` | `/api/store/tenant-info` | - | `getTenantInfoController` |
| 4 | `POST` | `/api/store/tenant-info/refresh` | - | `refreshTenantInfoController` |

### `src/routes/admin/subscription.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/subscription/payment-status` | - | `paymentStatusController` |
| 2 | `POST` | `/api/admin/subscription/submit-payment` | - | `submitPaymentController` |

### `src/routes/admin/ProfileRoutes.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `DELETE` | `/api/admin/profile` | - | `deleteMyProfile` |
| 2 | `GET` | `/api/admin/profile` | - | `getMyProfile` |
| 3 | `PUT` | `/api/admin/profile` | - | `updateMyProfile` |

### `src/routes/admin/Admin.ts`

**Auth:** Bearer — **Endpoints:** 9

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/admin` | - | `getAllUsers` |
| 2 | `POST` | `/api/admin/admin` | - | `createUser` |
| 3 | `DELETE` | `/api/admin/admin/:id` | - | `deleteUser` |
| 4 | `GET` | `/api/admin/admin/:id` | - | `getUserById` |
| 5 | `PUT` | `/api/admin/admin/:id` | - | `updateUser` |
| 6 | `PATCH` | `/api/admin/admin/:id/status` | - | `-` |
| 7 | `GET` | `/api/admin/admin/report/:id` | - | `getCashiermanShiftsReport` |
| 8 | `GET` | `/api/admin/admin/selection` | - | `getSelectionData` |
| 9 | `GET` | `/api/admin/admin/shifts/:shift_id/details` | - | `getShiftDetails` |

### `src/routes/admin/permission.ts`

**Auth:** Bearer — **Endpoints:** 12

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/permission` | permission:View | `getAllRoles` |
| 2 | `POST` | `/api/admin/permission` | permission:Add | `createRole` |
| 3 | `DELETE` | `/api/admin/permission/:id` | permission:Delete | `deleteRole` |
| 4 | `GET` | `/api/admin/permission/:id` | permission:View | `getRoleById` |
| 5 | `PUT` | `/api/admin/permission/:id` | permission:Edit | `updateRole` |
| 6 | `GET` | `/api/admin/permission/:id/permissions` | permission:View | `getRolePermissions` |
| 7 | `GET` | `/api/admin/permission/:id/permissions` | - | `getRolePermissions` |
| 8 | `PUT` | `/api/admin/permission/:id/permissions` | - | `-` |
| 9 | `POST` | `/api/admin/permission/:id/permissions/toggle` | - | `-` |
| 10 | `DELETE` | `/api/admin/permission/:roleId/permissions/:module/:actionId` | - | `-` |
| 11 | `GET` | `/api/admin/permission/modules-actions` | - | `getModulesAndActions` |
| 12 | `GET` | `/api/admin/permission/selection` | - | `getRolesForSelection` |

### `src/routes/admin/versionUpdater.ts`

**Auth:** Public — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/admin/version-updater` | - | `checkForChanges` |
| 2 | `POST` | `/api/admin/version-updater/status` | - | `getSyncStatus` |
| 3 | `GET` | `/api/admin/version-updater/status/:clientName` | - | `getSyncStatus` |
| 4 | `POST` | `/api/admin/version-updater/sync` | - | `syncAll` |
| 5 | `POST` | `/api/admin/version-updater/sync-backend` | - | `syncBackend` |
| 6 | `POST` | `/api/admin/version-updater/sync-frontend` | - | `syncFrontend` |

### `src/routes/admin/DecimalSetting.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/decimal-setting` | - | `getDecimalSetting` |
| 2 | `PUT` | `/api/admin/decimal-setting` | - | `updateDecimalSetting` |

### `src/routes/admin/notifications.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/notification` | notification:View | `getAllNotifications` |
| 2 | `GET` | `/api/admin/notification/:id` | notification:View | `getNotificationById` |
| 3 | `PUT` | `/api/admin/notification/:id/read` | notification:Edit | `markNotificationAsRead` |
| 4 | `PUT` | `/api/admin/notification/read-all` | notification:Edit | `markAllNotificationsAsRead` |

### `src/routes/admin/departments.ts`

**Auth:** Bearer — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/department` | - | `getAllDepartments` |
| 2 | `POST` | `/api/admin/department` | - | `createDepartment` |
| 3 | `DELETE` | `/api/admin/department/:id` | - | `deleteDepartment` |
| 4 | `GET` | `/api/admin/department/:id` | - | `getDepartmentById` |
| 5 | `PUT` | `/api/admin/department/:id` | - | `updateDepartment` |

### `src/routes/admin/pleskProxy.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/admin/plesk/proxy` | - | `pleskProxy` |

### `src/utils/dbGateway.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/admin/db/gateway` | - | `-` |

## 2. Products & Catalog

*Products / Categories / Brands / Variations / Units / Offers / Banners*


### `src/routes/admin/products.ts`

**Auth:** Bearer — **Endpoints:** 12

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `DELETE` | `/api/admin/product` | product:Delete | `deletemanyproducts` |
| 2 | `GET` | `/api/admin/product` | product:View | `getProduct` |
| 3 | `POST` | `/api/admin/product` | product:Add | `createProduct` |
| 4 | `DELETE` | `/api/admin/product/:id` | product:Delete | `deleteProduct` |
| 5 | `GET` | `/api/admin/product/:id` | product:View | `getOneProduct` |
| 6 | `PUT` | `/api/admin/product/:id` | product:Edit | `updateProduct` |
| 7 | `POST` | `/api/admin/product/code` | product:View | `getProductByCode` |
| 8 | `GET` | `/api/admin/product/generate-barcode/:product_price_id` | product:View | `generateBarcodeImageController` |
| 9 | `GET` | `/api/admin/product/generate-code` | product:View | `generateProductCode` |
| 10 | `POST` | `/api/admin/product/import` | product:Add | `importProductsFromExcel` |
| 11 | `GET` | `/api/admin/product/low-stock` | product:View | `getLowStockProducts` |
| 12 | `GET` | `/api/admin/product/select` | product:View | `modelsforselect` |

### `src/routes/admin/category.ts`

**Auth:** Public — **Endpoints:** 7

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `DELETE` | `/api/admin/category` | category:Delete | `deletemanycategories` |
| 2 | `GET` | `/api/admin/category` | category:View | `getCategories` |
| 3 | `POST` | `/api/admin/category` | category:Add | `createcategory` |
| 4 | `DELETE` | `/api/admin/category/:id` | category:Delete | `deleteCategory` |
| 5 | `GET` | `/api/admin/category/:id` | category:View | `getCategoryById` |
| 6 | `PUT` | `/api/admin/category/:id` | category:Edit | `updateCategory` |
| 7 | `POST` | `/api/admin/category/import` | category:Add | `importCategoriesFromExcel` |

### `src/routes/admin/brand.ts`

**Auth:** Bearer — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `DELETE` | `/api/admin/brand` | brand:Delete | `deletemanybrands` |
| 2 | `GET` | `/api/admin/brand` | brand:View | `getBrands` |
| 3 | `POST` | `/api/admin/brand` | brand:Add | `createBrand` |
| 4 | `DELETE` | `/api/admin/brand/:id` | brand:Delete | `deleteBrand` |
| 5 | `GET` | `/api/admin/brand/:id` | brand:View | `getBrandById` |
| 6 | `PUT` | `/api/admin/brand/:id` | brand:Edit | `updateBrand` |

### `src/routes/admin/Variation.ts`

**Auth:** Public — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/variation` | variation:View | `getAllVariations` |
| 2 | `POST` | `/api/admin/variation` | variation:Add | `createVariationWithOptions` |
| 3 | `DELETE` | `/api/admin/variation/:id` | variation:Delete | `deleteVariationWithOptions` |
| 4 | `GET` | `/api/admin/variation/:id` | variation:View | `getOneVariation` |
| 5 | `PUT` | `/api/admin/variation/:id` | variation:Edit | `updateVariationWithOptions` |
| 6 | `DELETE` | `/api/admin/variation/option/:id` | - | `deleteOption` |

### `src/routes/admin/units.ts`

**Auth:** Public — **Endpoints:** 9

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/units` | Units:View | `getUnits` |
| 2 | `POST` | `/api/admin/units` | Units:Add | `createUnit` |
| 3 | `DELETE` | `/api/admin/units/:id` | Units:Delete | `deleteUnit` |
| 4 | `GET` | `/api/admin/units/:id` | Units:View | `getUnitById` |
| 5 | `PUT` | `/api/admin/units/:id` | Units:Edit | `updateUnit` |
| 6 | `GET` | `/api/admin/units/active` | Units:View | `getActiveUnits` |
| 7 | `GET` | `/api/admin/units/base` | Units:View | `getBaseUnits` |
| 8 | `POST` | `/api/admin/units/convert` | Units:Edit | `convertUnit` |
| 9 | `POST` | `/api/admin/units/delete-many` | Units:Delete | `deleteManyUnits` |

### `src/routes/admin/Category_Material.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/category_material` | Category_Material:View | `getCategories` |
| 2 | `POST` | `/api/admin/category_material` | Category_Material:Add | `createcategory` |
| 3 | `DELETE` | `/api/admin/category_material/:id` | Category_Material:Delete | `deleteCategory` |
| 4 | `GET` | `/api/admin/category_material/:id` | Category_Material:View | `getCategoryById` |
| 5 | `PUT` | `/api/admin/category_material/:id` | Category_Material:Edit | `updateCategory` |

### `src/routes/admin/Offers.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/offer` | offer:View | `getOffers` |
| 2 | `POST` | `/api/admin/offer` | offer:Add | `createoffers` |
| 3 | `DELETE` | `/api/admin/offer/:id` | offer:Delete | `deleteoffer` |
| 4 | `GET` | `/api/admin/offer/:id` | offer:View | `getOffer` |
| 5 | `PUT` | `/api/admin/offer/:id` | offer:Edit | `updateoffer` |

### `src/routes/admin/Banner.ts`

**Auth:** Public — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/banner` | banner:View | `getBanners` |
| 2 | `POST` | `/api/admin/banner` | banner:Add | `createBanner` |
| 3 | `DELETE` | `/api/admin/banner/:id` | banner:Delete | `deleteBanner` |
| 4 | `GET` | `/api/admin/banner/:id` | banner:View | `getBannerById` |
| 5 | `PUT` | `/api/admin/banner/:id` | banner:Edit | `updateBanner` |
| 6 | `GET` | `/api/admin/banner/banner-modules` | - | `getBannerModules` |

### `src/routes/admin/Popup.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/popup` | popup:View | `-` |
| 2 | `POST` | `/api/admin/popup` | popup:Add | `-` |
| 3 | `DELETE` | `/api/admin/popup/:id` | popup:Delete | `-` |
| 4 | `GET` | `/api/admin/popup/:id` | popup:View | `-` |
| 5 | `PUT` | `/api/admin/popup/:id` | popup:Edit | `-` |

### `src/routes/admin/discount.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/discount` | discount:View | `getAllDiscounts` |
| 2 | `POST` | `/api/admin/discount` | discount:Add | `createDiscount` |
| 3 | `DELETE` | `/api/admin/discount/:id` | discount:Delete | `deleteDiscount` |
| 4 | `GET` | `/api/admin/discount/:id` | discount:View | `getDiscountById` |
| 5 | `PUT` | `/api/admin/discount/:id` | discount:Edit | `updateDiscount` |

### `src/routes/admin/coupons.ts`

**Auth:** Bearer — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/coupon` | coupon:View | `getcoupons` |
| 2 | `POST` | `/api/admin/coupon` | coupon:Add | `createcoupons` |
| 3 | `DELETE` | `/api/admin/coupon/:id` | coupon:Delete | `deletecoupon` |
| 4 | `GET` | `/api/admin/coupon/:id` | coupon:View | `getcouponById` |
| 5 | `PUT` | `/api/admin/coupon/:id` | coupon:Edit | `updatecoupon` |

### `src/routes/admin/points.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/point` | point:View | `-` |
| 2 | `POST` | `/api/admin/point` | point:Add | `-` |
| 3 | `DELETE` | `/api/admin/point/:id` | point:Delete | `-` |
| 4 | `GET` | `/api/admin/point/:id` | point:View | `-` |
| 5 | `PUT` | `/api/admin/point/:id` | point:Edit | `-` |

### `src/routes/admin/redeem_Points.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/redeem-points` | redeem_points:View | `-` |
| 2 | `POST` | `/api/admin/redeem-points` | redeem_points:Add | `-` |
| 3 | `DELETE` | `/api/admin/redeem-points/:id` | redeem_points:Delete | `-` |
| 4 | `GET` | `/api/admin/redeem-points/:id` | redeem_points:View | `-` |
| 5 | `PUT` | `/api/admin/redeem-points/:id` | redeem_points:Edit | `-` |

### `src/routes/admin/pandels.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pandel` | pandel:View | `-` |
| 2 | `POST` | `/api/admin/pandel` | pandel:Add | `-` |
| 3 | `DELETE` | `/api/admin/pandel/:id` | pandel:Delete | `-` |
| 4 | `GET` | `/api/admin/pandel/:id` | pandel:View | `-` |
| 5 | `PUT` | `/api/admin/pandel/:id` | pandel:Edit | `-` |

## 3. Inventory & Warehouse

*Stock / Warehouses / Transfers / Stock Take / Adjustments / Wasted / Labels*


### `src/routes/admin/Warehouse.ts`

**Auth:** Bearer — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/warehouse` | warehouse:View | `getWarehouses` |
| 2 | `POST` | `/api/admin/warehouse` | warehouse:Add | `createWarehouse` |
| 3 | `DELETE` | `/api/admin/warehouse/:id` | warehouse:Delete | `deleteWarehouse` |
| 4 | `GET` | `/api/admin/warehouse/:id` | warehouse:View | `getWarehouseById` |
| 5 | `PUT` | `/api/admin/warehouse/:id` | warehouse:Edit | `updateWarehouse` |

### `src/routes/admin/product_warehouse.ts`

**Auth:** Public — **Endpoints:** 7

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `DELETE` | `/api/admin/product_warehouse` | product_warehouse:Delete | `removeProductFromWarehouse` |
| 2 | `GET` | `/api/admin/product_warehouse` | product_warehouse:View | `getAllStocks` |
| 3 | `POST` | `/api/admin/product_warehouse` | product_warehouse:Add | `addProductToWarehouse` |
| 4 | `PUT` | `/api/admin/product_warehouse/:id` | product_warehouse:Edit | `updateProductStock` |
| 5 | `GET` | `/api/admin/product_warehouse/:warehouseId` | product_warehouse:View | `getWarehouseProducts` |
| 6 | `GET` | `/api/admin/product_warehouse/low-stock` | product_warehouse:View | `getLowStockProducts` |
| 7 | `GET` | `/api/admin/product_warehouse/stock/:id` | product_warehouse:View | `getStockById` |

### `src/routes/admin/stocks.ts`

**Auth:** Bearer — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/stock` | stock:View | `getStock` |
| 2 | `POST` | `/api/admin/stock` | stock:Add | `createStock` |
| 3 | `GET` | `/api/admin/stock/:id` | stock:View | `getStockById` |
| 4 | `POST` | `/api/admin/stock/finalFile` | stock:Add | `uploadFinalFile` |

### `src/routes/admin/Transfer.ts`

**Auth:** Public — **Endpoints:** 8

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/transfer` | transfer:View | `getalltransfers` |
| 2 | `POST` | `/api/admin/transfer` | transfer:Add | `createTransfer` |
| 3 | `DELETE` | `/api/admin/transfer/:id` | transfer:Delete | `deleteTransfer` |
| 4 | `GET` | `/api/admin/transfer/:id` | transfer:View | `getTransferById` |
| 5 | `PUT` | `/api/admin/transfer/:id` | transfer:Edit | `updateTransferStatus` |
| 6 | `GET` | `/api/admin/transfer/get/:warehouseId` | transfer:View | `getTransfersForWarehouse` |
| 7 | `GET` | `/api/admin/transfer/gettransferin/:warehouseId` | transfer:View | `gettransferin` |
| 8 | `GET` | `/api/admin/transfer/gettransferout/:warehouseId` | transfer:View | `gettransferout` |

### `src/routes/admin/stockTake.ts`

**Auth:** Public — **Endpoints:** 12

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/stocktake` | stocktake:View | `getStocktakes` |
| 2 | `POST` | `/api/admin/stocktake` | stocktake:Add | `createStocktake` |
| 3 | `DELETE` | `/api/admin/stocktake/:id` | stocktake:Delete | `deleteStocktake` |
| 4 | `GET` | `/api/admin/stocktake/:id` | stocktake:View | `getStocktakeById` |
| 5 | `PATCH` | `/api/admin/stocktake/:id/cancel` | stocktake:Edit | `cancelStocktake` |
| 6 | `GET` | `/api/admin/stocktake/:id/export` | stocktake:View | `exportStocktakeSheet` |
| 7 | `POST` | `/api/admin/stocktake/:id/import` | stocktake:Edit | `importStocktakeSheet` |
| 8 | `GET` | `/api/admin/stocktake/:id/items` | stocktake:View | `getStocktakeItems` |
| 9 | `PUT` | `/api/admin/stocktake/:id/items` | stocktake:Edit | `bulkUpdateStocktakeItems` |
| 10 | `PATCH` | `/api/admin/stocktake/:id/items/:itemId` | stocktake:Edit | `updateStocktakeItem` |
| 11 | `POST` | `/api/admin/stocktake/:id/resolve` | stocktake:Edit | `resolveStocktakeItems` |
| 12 | `POST` | `/api/admin/stocktake/:id/submit` | stocktake:Edit | `submitStocktake` |

### `src/routes/admin/adjustments.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/adjustment` | - | `getAdjustments` |
| 2 | `POST` | `/api/admin/adjustment` | - | `createAdjustment` |
| 3 | `GET` | `/api/admin/adjustment/:id` | - | `getAdjustmentById` |

### `src/routes/admin/adjustmentsreason.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/selectreason` | adjustment_reason:View | `getSelectReason` |
| 2 | `POST` | `/api/admin/selectreason` | adjustment_reason:Add | `createSelectReason` |
| 3 | `DELETE` | `/api/admin/selectreason/:id` | adjustment_reason:Delete | `deleteSelectReason` |
| 4 | `PUT` | `/api/admin/selectreason/:id` | adjustment_reason:Edit | `updateSelectReason` |

### `src/routes/admin/wasted.ts`

**Auth:** Public — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/wasted` | wasted:View | `getAllWasted` |
| 2 | `POST` | `/api/admin/wasted` | wasted:Add | `createWasted` |
| 3 | `DELETE` | `/api/admin/wasted/:id` | wasted:Delete | `deleteWasted` |
| 4 | `GET` | `/api/admin/wasted/:id` | wasted:View | `getWastedById` |
| 5 | `PATCH` | `/api/admin/wasted/:id/status` | wasted:Edit | `updateWastedStatus` |
| 6 | `GET` | `/api/admin/wasted/stats` | wasted:View | `getWastedStats` |

### `src/routes/admin/POS/wastedRoutes.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/admin/pos-wasted` | - | `createWasted` |

### `src/routes/admin/PickupPoint.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pickup-point` | pickup_point:View | `getPickupPoints` |
| 2 | `POST` | `/api/admin/pickup-point` | pickup_point:Add | `createPickupPoint` |
| 3 | `DELETE` | `/api/admin/pickup-point/:id` | pickup_point:Delete | `deletePickupPoint` |
| 4 | `GET` | `/api/admin/pickup-point/:id` | pickup_point:View | `getPickupPointById` |
| 5 | `PUT` | `/api/admin/pickup-point/:id` | pickup_point:Edit | `updatePickupPoint` |

### `src/routes/admin/generatelabel.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/admin/label/generate` | product:Add | `generateLabelsController` |
| 2 | `GET` | `/api/admin/label/sizes` | product:View | `getAvailableLabelSizes` |

### `src/routes/admin/Qztray.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/qztray/cert` | - | `getCert` |
| 2 | `GET` | `/api/admin/qztray/sign` | - | `signData` |
| 3 | `POST` | `/api/admin/qztray/sign` | - | `signData` |

## 4. POS, Shifts, Customers, Bookings

*POS / Sales / Returns / Shifts / Gift Cards / Customer Groups / Bookings*


### `src/routes/admin/POS/POSRoutes.ts`

**Auth:** Public — **Endpoints:** 11

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/admin/pos/apply-coupon` | POS:Add | `applyCoupon` |
| 2 | `GET` | `/api/admin/pos/sales` | POS:View | `getAllSales` |
| 3 | `POST` | `/api/admin/pos/sales` | POS:Add | `createSale` |
| 4 | `GET` | `/api/admin/pos/sales/:id` | POS:View | `getSales` |
| 5 | `POST` | `/api/admin/pos/sales/complete` | POS:View | `getShiftCompletedSales` |
| 6 | `GET` | `/api/admin/pos/sales/dues` | POS:View | `getDueSales` |
| 7 | `GET` | `/api/admin/pos/sales/next-invoice-number` | POS:View | `getNextInvoiceNumber` |
| 8 | `POST` | `/api/admin/pos/sales/pay-due` | POS:Add | `payDue` |
| 9 | `GET` | `/api/admin/pos/sales/pending` | POS:View | `getsalePending` |
| 10 | `DELETE` | `/api/admin/pos/sales/pending/:id` | POS:Delete | `deleteSalePending` |
| 11 | `GET` | `/api/admin/pos/sales/sales/pending/:sale_id` | POS:View | `getSalePendingById` |

### `src/routes/admin/POS/POSHomeRoutes.ts`

**Auth:** Public — **Endpoints:** 23

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pos-home/accounts` | POS:View | `getAccounts` |
| 2 | `GET` | `/api/admin/pos-home/brands` | POS:View | `getAllBrands` |
| 3 | `GET` | `/api/admin/pos-home/brands/:brandId/products` | POS:View | `getProductsByBrand` |
| 4 | `GET` | `/api/admin/pos-home/bundles` | POS:View | `getActiveBundles` |
| 5 | `GET` | `/api/admin/pos-home/cashiers` | POS:View | `getCashiers` |
| 6 | `POST` | `/api/admin/pos-home/cashiers/select` | POS:Add | `selectCashier` |
| 7 | `GET` | `/api/admin/pos-home/categories` | POS:View | `getAllCategorys` |
| 8 | `GET` | `/api/admin/pos-home/categories/:categoryId/products` | POS:View | `getProductsByCategory` |
| 9 | `GET` | `/api/admin/pos-home/countries` | POS:View | `getCountries` |
| 10 | `GET` | `/api/admin/pos-home/coupons` | POS:View | `getCoupons` |
| 11 | `GET` | `/api/admin/pos-home/currency` | POS:View | `getCurrency` |
| 12 | `GET` | `/api/admin/pos-home/customer-groups` | POS:View | `getCustomerGroups` |
| 13 | `GET` | `/api/admin/pos-home/customers` | POS:View | `getCustomers` |
| 14 | `GET` | `/api/admin/pos-home/discounts` | POS:View | `getDiscounts` |
| 15 | `GET` | `/api/admin/pos-home/due-customers` | POS:View | `getDueCustomers` |
| 16 | `GET` | `/api/admin/pos-home/featured` | POS:View | `getFeaturedProducts` |
| 17 | `GET` | `/api/admin/pos-home/gift-cards` | POS:View | `getGiftCards` |
| 18 | `GET` | `/api/admin/pos-home/payment-methods` | POS:View | `getPaymentMethods` |
| 19 | `GET` | `/api/admin/pos-home/products/:productId/warehouse-stock` | - | `getProductWarehouseStock` |
| 20 | `GET` | `/api/admin/pos-home/selections` | POS:View | `getAllSelections` |
| 21 | `GET` | `/api/admin/pos-home/service-fees` | POS:View | `getServiceFees` |
| 22 | `GET` | `/api/admin/pos-home/taxes` | POS:View | `getTaxes` |
| 23 | `GET` | `/api/admin/pos-home/warehouses` | POS:View | `getWarehouses` |

### `src/routes/admin/POS/CashierShiftRoutes.ts`

**Auth:** Public — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `PUT` | `/api/admin/cashier-shift/close/:id` | POS:Edit<br>cashier_shift_report:Edit | `endshiftcashier` |
| 2 | `GET` | `/api/admin/cashier-shift/current` | POS:View | `getCurrentShift` |
| 3 | `PUT` | `/api/admin/cashier-shift/end` | POS:Edit<br>cashier_shift:Edit | `endshiftcashier` |
| 4 | `PUT` | `/api/admin/cashier-shift/end/report` | POS:Edit<br>cashier_shift_report:Edit | `endShiftWithReport` |
| 5 | `POST` | `/api/admin/cashier-shift/logout` | POS:Add | `logout` |
| 6 | `POST` | `/api/admin/cashier-shift/start` | POS:Add<br>cashier_shift:Add | `startcashierShift` |

### `src/routes/admin/cashiershifts.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/cashiershift` | cashier_shift:View | `getAllCashierShifts` |
| 2 | `GET` | `/api/admin/cashiershift/:id` | cashier_shift:View | `getCashierShiftDetails` |
| 3 | `PUT` | `/api/admin/cashiershift/close/:id` | cashier_shift:Edit | `closeCashierShift` |

### `src/routes/admin/cashier.ts`

**Auth:** Public — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/cashier` | cashier:View | `getCashiers` |
| 2 | `POST` | `/api/admin/cashier` | cashier:Add | `createCashier` |
| 3 | `DELETE` | `/api/admin/cashier/:id` | cashier:Delete | `deleteCashier` |
| 4 | `GET` | `/api/admin/cashier/:id` | cashier:View | `getCashierById` |
| 5 | `PUT` | `/api/admin/cashier/:id` | cashier:Edit | `updateCashier` |
| 6 | `GET` | `/api/admin/cashier/select` | cashier:View | `getBankAccounts` |

### `src/routes/admin/POS/ReturnSaleRoutes.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/return-sale/all-returns` | POS:View | `getAllReturns` |
| 2 | `POST` | `/api/admin/return-sale/create-return` | POS:Add | `createReturn` |
| 3 | `GET` | `/api/admin/return-sale/return-by-id` | POS:View | `getReturnById` |
| 4 | `POST` | `/api/admin/return-sale/sale-for-return` | POS:View | `getSaleForReturn` |
| 5 | `GET` | `/api/admin/return-sale/sale-returns` | POS:View | `getSaleReturns` |

### `src/routes/admin/POS/BookingRoutes.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pos-booking` | POS:View | `getPOSBookings` |
| 2 | `POST` | `/api/admin/pos-booking` | POS:Add | `createBooking` |
| 3 | `PATCH` | `/api/admin/pos-booking/pay-remaining/:id` | POS:Add | `payRemaining` |

### `src/routes/admin/Booking.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/booking` | Booking:View | `getAllBookings` |
| 2 | `GET` | `/api/admin/booking/:id` | Booking:View | `getBookingById` |

### `src/routes/admin/POS/giftCardRoutes.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/gift-card` | POS:View<br>gift_card:View | `getAllGiftCards` |
| 2 | `POST` | `/api/admin/gift-card` | POS:Add<br>gift_card:Add | `createGiftCard` |
| 3 | `GET` | `/api/admin/gift-card/:id` | POS:View<br>gift_card:View | `getGiftCard` |
| 4 | `PUT` | `/api/admin/gift-card/:id` | POS:Edit<br>gift_card:Edit | `updateGiftCard` |
| 5 | `POST` | `/api/admin/gift-card/:id/redeem` | POS:Edit<br>gift_card:Edit | `redeemGiftCard` |

### `src/routes/admin/POS/customerGroupRoutes.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pos_customer` | POS:View<br>customer_group:View | `getallgroups` |
| 2 | `POST` | `/api/admin/pos_customer` | POS:Add<br>customer_group:Add | `createCustomerGroup` |
| 3 | `POST` | `/api/admin/pos_customer/customer` | POS:Add<br>customer:Add | `createCustomer` |
| 4 | `GET` | `/api/admin/pos_customer/customers` | POS:View<br>customer_group:View | `getCustomers` |

### `src/routes/admin/POS/POSWarehouseRoutes.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pos/warehouse/:warehouseId` | POS:View | `getPOSWarehouseProducts` |
| 2 | `GET` | `/api/admin/pos/warehouse/destinations` | POS:View | `getPOSWarehouseDestinations` |
| 3 | `GET` | `/api/admin/pos/warehouse/products` | POS:View | `getPOSWarehouseProducts` |

### `src/routes/admin/POS/POSTransferRoutes.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pos/transfer` | transfer:View | `listPOSTransfers` |
| 2 | `POST` | `/api/admin/pos/transfer` | transfer:Add | `createPOSTransfer` |
| 3 | `GET` | `/api/admin/pos/transfer/:id` | transfer:View | `getPOSTransferById` |
| 4 | `PUT` | `/api/admin/pos/transfer/:id` | transfer:Edit | `updatePOSTransferStatus` |

### `src/routes/admin/POS/expenses.ts`

**Auth:** Bearer — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/expense` | POS:View | `getExpenses` |
| 2 | `POST` | `/api/admin/expense` | POS:Add | `createExpense` |
| 3 | `DELETE` | `/api/admin/expense/:id` | POS:Delete | `deleteExpense` |
| 4 | `GET` | `/api/admin/expense/:id` | POS:View | `getExpenseById` |
| 5 | `PUT` | `/api/admin/expense/:id` | POS:Edit | `updateExpense` |
| 6 | `GET` | `/api/admin/expense/selection` | POS:View | `selectionExpense` |

### `src/routes/admin/POS/onlineOrders.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pos-online-orders` | POS:View<br>orders:View | `getAllOnlineOrders` |
| 2 | `GET` | `/api/admin/pos-online-orders/:id` | POS:View<br>orders:View | `getOnlineOrderById` |
| 3 | `PUT` | `/api/admin/pos-online-orders/:id/status` | POS:Edit<br>orders:Edit | `updateOnlineOrderStatus` |

### `src/routes/admin/POS/InstallmentRoutes.ts`

**Auth:** Public — **Endpoints:** 7

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/pos/installments/customer/:customerId` | POS:View | `getPosCustomerInstallmentPlans` |
| 2 | `GET` | `/api/admin/pos/installments/due` | POS:View | `listPosDueInstallments` |
| 3 | `GET` | `/api/admin/pos/installments/plans` | POS:View | `listPosInstallmentPlans` |
| 4 | `GET` | `/api/admin/pos/installments/plans/:id` | POS:View | `getPosInstallmentPlan` |
| 5 | `PUT` | `/api/admin/pos/installments/plans/:planId/items/:itemId/pay` | POS:Edit | `payPosInstallment` |
| 6 | `POST` | `/api/admin/pos/installments/preview` | POS:View | `previewPosInstallment` |
| 7 | `GET` | `/api/admin/pos/installments/settings` | POS:View | `getPosInstallmentSettings` |

### `src/routes/admin/customerRoutes.ts`

**Auth:** Public — **Endpoints:** 15

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/customer` | customer:View | `getCustomers` |
| 2 | `POST` | `/api/admin/customer` | POS:Add<br>customer:Add | `createCustomer` |
| 3 | `DELETE` | `/api/admin/customer/:id` | customer:Delete | `deleteCustomer` |
| 4 | `GET` | `/api/admin/customer/:id` | customer:View | `getCustomerById` |
| 5 | `PUT` | `/api/admin/customer/:id` | customer:Edit | `updateCustomer` |
| 6 | `GET` | `/api/admin/customer/:id/orders` | customer:View | `getCustomerOrders` |
| 7 | `GET` | `/api/admin/customer/countries` | customer:View | `getCountriesWithCities` |
| 8 | `GET` | `/api/admin/customer/due` | customer:View | `getDueCustomers` |
| 9 | `GET` | `/api/admin/customer/groups` | customer_group:View | `getallgroups` |
| 10 | `POST` | `/api/admin/customer/groups` | customer_group:Add | `creategroup` |
| 11 | `DELETE` | `/api/admin/customer/groups/:id` | customer_group:Delete | `deletegroup` |
| 12 | `GET` | `/api/admin/customer/groups/:id` | customer_group:View | `getgroupbyid` |
| 13 | `PUT` | `/api/admin/customer/groups/:id` | customer_group:Edit | `updategroup` |
| 14 | `GET` | `/api/admin/customer/single-page/:id` | customer:View | `getCustomerSinglePageData` |
| 15 | `GET` | `/api/admin/customer/with-orders` | customer:View | `getCustomersWithOrders` |

## 5. Purchases, Suppliers & Recipes

*Purchases / Returns / Suppliers / BOM*


### `src/routes/admin/Purchase.ts`

**Auth:** Bearer — **Endpoints:** 9

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/purchase` | purchase:View | `getAllPurchases` |
| 2 | `POST` | `/api/admin/purchase` | purchase:Add | `createPurchase` |
| 3 | `GET` | `/api/admin/purchase/:id` | purchase:View | `getPurchaseById` |
| 4 | `PUT` | `/api/admin/purchase/:id` | purchase:Edit | `updatePurchase` |
| 5 | `GET` | `/api/admin/purchase/critical-expiry` | purchase:View | `getCriticalExpiryProducts` |
| 6 | `GET` | `/api/admin/purchase/expired` | purchase:View | `getExpiredProducts` |
| 7 | `GET` | `/api/admin/purchase/expiring` | purchase:View | `getExpiringProducts` |
| 8 | `GET` | `/api/admin/purchase/low-stock` | purchase:View | `getLowStockProducts` |
| 9 | `GET` | `/api/admin/purchase/selection` | purchase:View | `selection` |

### `src/routes/admin/returnPurchase.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/return-purchase/all-returns` | purchase_return:View | `getAllReturns` |
| 2 | `POST` | `/api/admin/return-purchase/create-return` | purchase_return:Add | `createReturn` |
| 3 | `POST` | `/api/admin/return-purchase/purchase-for-return` | purchase_return:View | `getPurchaseForReturn` |
| 4 | `GET` | `/api/admin/return-purchase/purchase-returns/:id` | purchase_return:View | `getPurchaseReturns` |
| 5 | `GET` | `/api/admin/return-purchase/return-by-id/:id` | purchase_return:View | `getReturnById` |

### `src/routes/admin/suppliers.ts`

**Auth:** Bearer — **Endpoints:** 7

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/supplier` | Supplier:View | `getSuppliers` |
| 2 | `POST` | `/api/admin/supplier` | Supplier:Add | `createSupplier` |
| 3 | `DELETE` | `/api/admin/supplier/:id` | Supplier:Delete | `deleteSupplier` |
| 4 | `GET` | `/api/admin/supplier/:id` | Supplier:View | `getSupplierById` |
| 5 | `PUT` | `/api/admin/supplier/:id` | Supplier:Edit | `updateSupplier` |
| 6 | `GET` | `/api/admin/supplier/:id/details` | Supplier:View | `getSupplierSinglePage` |
| 7 | `GET` | `/api/admin/supplier/countries` | Supplier:View | `getCountriesWithCities` |

### `src/routes/admin/Recipe.ts`

**Auth:** Public — **Endpoints:** 9

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/recipe` | recipe:View | `getAllRecipes` |
| 2 | `POST` | `/api/admin/recipe` | recipe:Add | `createRecipe` |
| 3 | `DELETE` | `/api/admin/recipe/:id` | recipe:Delete | `deleteRecipe` |
| 4 | `PUT` | `/api/admin/recipe/:id` | recipe:Edit | `updateRecipe` |
| 5 | `GET` | `/api/admin/recipe/:productId` | recipe:View | `getRecipesByProductId` |
| 6 | `GET` | `/api/admin/recipe/check/:productId` | recipe:View | `checkProductRecipe` |
| 7 | `POST` | `/api/admin/recipe/produce` | recipe:Add | `produceProductFromRecipe` |
| 8 | `GET` | `/api/admin/recipe/productions` | recipe:View | `getAllProductions` |
| 9 | `GET` | `/api/admin/recipe/selection` | recipe:View | `selecttion` |

## 6. Finance & Accounting

*Accounts / Ledger / Revenue / Expenses / Payable / Receivable / Installments / Taxes*


### `src/routes/admin/Financial_Account.ts`

**Auth:** Public — **Endpoints:** 7

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/bank_account` | financial_account:View | `getBankAccounts` |
| 2 | `POST` | `/api/admin/bank_account` | financial_account:Add | `createBankAccount` |
| 3 | `DELETE` | `/api/admin/bank_account/:id` | financial_account:Delete | `deleteBankAccount` |
| 4 | `GET` | `/api/admin/bank_account/:id` | financial_account:View | `getBankAccountById` |
| 5 | `PUT` | `/api/admin/bank_account/:id` | financial_account:Edit | `updateBankAccount` |
| 6 | `GET` | `/api/admin/bank_account/select-warehouses` | financial_account:View | `selectwarehousesforbankaccount` |
| 7 | `POST` | `/api/admin/bank_account/transfer` | financial_account:Add | `transferMoney` |

### `src/routes/admin/AccountingLedger.ts`

**Auth:** Bearer — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/ledger` | - | `getAccountingLedgers` |
| 2 | `GET` | `/api/admin/ledger/:id` | - | `getAccountingLedgerById` |

### `src/routes/admin/Revenue.ts`

**Auth:** Bearer — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/revenue` | Revenue:View | `getRevenues` |
| 2 | `POST` | `/api/admin/revenue` | Revenue:Add | `createRevenue` |
| 3 | `DELETE` | `/api/admin/revenue/:id` | Revenue:Delete | `deleteRevenue` |
| 4 | `GET` | `/api/admin/revenue/:id` | Revenue:View | `getRevenueById` |
| 5 | `PUT` | `/api/admin/revenue/:id` | Revenue:Edit | `updateRevenue` |
| 6 | `GET` | `/api/admin/revenue/selection` | Revenue:View | `selectionRevenue` |

### `src/routes/admin/expensesAdmin.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/expenseAdmin` | expenseAdmin:View | `getExpensesAdmin` |
| 2 | `POST` | `/api/admin/expenseAdmin` | expenseAdmin:Add | `createExpenseAdmin` |
| 3 | `GET` | `/api/admin/expenseAdmin/:id` | expenseAdmin:View | `getExpenseByIdAdmin` |
| 4 | `PUT` | `/api/admin/expenseAdmin/:id` | expenseAdmin:Edit | `updateExpenseAdmin` |
| 5 | `GET` | `/api/admin/expenseAdmin/selection` | expenseAdmin:View | `getselectionExpenseAdmin` |

### `src/routes/admin/expensecategory.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/expensecategory` | expense_category:View | `getExpenseCategories` |
| 2 | `POST` | `/api/admin/expensecategory` | expense_category:Add | `createExpenseCategory` |
| 3 | `DELETE` | `/api/admin/expensecategory/:id` | expense_category:Delete | `deleteExpenseCategory` |
| 4 | `GET` | `/api/admin/expensecategory/:id` | expense_category:View | `getExpenseCategoryById` |
| 5 | `PUT` | `/api/admin/expensecategory/:id` | expense_category:Edit | `updateExpenseCategory` |

### `src/routes/admin/Payable.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/payable` | purchase:View | `getPayables` |
| 2 | `POST` | `/api/admin/payable/installment/:id/transaction` | purchase:Edit | `addPayableTransaction` |

### `src/routes/admin/Receivable.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/receivable` | POS:View | `getReceivables` |
| 2 | `POST` | `/api/admin/receivable/customer/:id/transaction` | POS:Add | `addReceivableTransaction` |

### `src/routes/admin/finicialaccountReport.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/finicial-report` | financial_report:View | `getFinancialReport` |

### `src/routes/admin/payments.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/payment` | payment:View | `getpayments` |
| 2 | `GET` | `/api/admin/payment/:id` | payment:View | `getpaymentById` |

### `src/routes/admin/ServiceFee.ts`

**Auth:** Public — **Endpoints:** 7

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/service-fees` | service_fees:View | `getAllServiceFees` |
| 2 | `POST` | `/api/admin/service-fees` | service_fees:Add | `createServiceFee` |
| 3 | `DELETE` | `/api/admin/service-fees/:id` | service_fees:Delete | `deleteServiceFee` |
| 4 | `GET` | `/api/admin/service-fees/:id` | service_fees:View | `getServiceFeeById` |
| 5 | `PUT` | `/api/admin/service-fees/:id` | service_fees:Edit | `updateServiceFee` |
| 6 | `PATCH` | `/api/admin/service-fees/:id/status` | service_fees:Status | `toggleServiceFeeStatus` |
| 7 | `GET` | `/api/admin/service-fees/select` | service_fees:View | `getallwarehouses` |

### `src/routes/admin/Currency.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/currency` | currency:View | `getCurrencies` |
| 2 | `POST` | `/api/admin/currency` | currency:Add | `createCurrency` |
| 3 | `DELETE` | `/api/admin/currency/:id` | currency:Delete | `deleteCurrency` |
| 4 | `GET` | `/api/admin/currency/:id` | currency:View | `getCurrencyById` |
| 5 | `PUT` | `/api/admin/currency/:id` | currency:Edit | `updateCurrency` |

### `src/routes/admin/Taxes.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/taxes` | Taxes:View | `getTaxes` |
| 2 | `POST` | `/api/admin/taxes` | Taxes:Add | `createTaxes` |
| 3 | `DELETE` | `/api/admin/taxes/:id` | Taxes:Delete | `deleteTaxes` |
| 4 | `GET` | `/api/admin/taxes/:id` | Taxes:View | `getTaxesById` |
| 5 | `PUT` | `/api/admin/taxes/:id` | Taxes:Edit | `updateTaxes` |

### `src/routes/admin/installments.ts`

**Auth:** Public — **Endpoints:** 10

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/installments/customer/:customerId` | - | `getCustomerInstallmentPlans` |
| 2 | `GET` | `/api/admin/installments/due` | - | `listDueInstallments` |
| 3 | `GET` | `/api/admin/installments/plans` | - | `listInstallmentPlans` |
| 4 | `GET` | `/api/admin/installments/plans/:id` | - | `getInstallmentPlan` |
| 5 | `PUT` | `/api/admin/installments/plans/:id/cancel` | - | `cancelInstallmentPlan` |
| 6 | `PUT` | `/api/admin/installments/plans/:planId/items/:itemId` | - | `updateInstallmentItem` |
| 7 | `PUT` | `/api/admin/installments/plans/:planId/items/:itemId/pay` | - | `payInstallment` |
| 8 | `POST` | `/api/admin/installments/preview` | - | `previewInstallment` |
| 9 | `GET` | `/api/admin/installments/settings` | - | `getInstallmentSettingsController` |
| 10 | `PUT` | `/api/admin/installments/settings` | - | `updateInstallmentSettings` |

## 7. Shipping & Delivery

*Bosta / Couriers / Delivery Men & Assignments / Zones / Cities / Countries*


### `src/routes/admin/Shipping.ts`

**Auth:** Public — **Endpoints:** 28

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/shipping/bosta/cities` | zone:View | `getBostaCities` |
| 2 | `POST` | `/api/admin/shipping/bosta/deliveries/bulk` | zone:Edit | `bulkCreateBostaDeliveries` |
| 3 | `POST` | `/api/admin/shipping/bosta/deliveries/from-order` | zone:Edit | `createBostaDeliveryFromOrder` |
| 4 | `POST` | `/api/admin/shipping/bosta/deliveries/return` | zone:Edit | `createBostaReturnDelivery` |
| 5 | `GET` | `/api/admin/shipping/bosta/districts/:cityId` | zone:View | `getBostaDistricts` |
| 6 | `GET` | `/api/admin/shipping/bosta/orders/:orderId/shipment` | zone:View | `getBostaShipmentByOrder` |
| 7 | `GET` | `/api/admin/shipping/bosta/pickup-locations` | zone:View | `listPickupLocations` |
| 8 | `PUT` | `/api/admin/shipping/bosta/pickup-locations/:locationId/default` | zone:Edit | `setDefaultPickupLocation` |
| 9 | `POST` | `/api/admin/shipping/bosta/pickup-locations/sync` | zone:Edit | `syncPickupLocation` |
| 10 | `POST` | `/api/admin/shipping/bosta/pickups` | zone:Edit | `createBostaPickup` |
| 11 | `POST` | `/api/admin/shipping/bosta/pickups/daily` | zone:Edit | `createDailyPickup` |
| 12 | `GET` | `/api/admin/shipping/bosta/pickups/pending` | zone:View | `getPendingPickups` |
| 13 | `GET` | `/api/admin/shipping/bosta/pricing/insurance` | zone:View | `getBostaInsuranceEstimate` |
| 14 | `GET` | `/api/admin/shipping/bosta/pricing/sector` | zone:View | `getBostaSectorPricing` |
| 15 | `GET` | `/api/admin/shipping/bosta/pricing/shipment` | zone:View | `getBostaShipmentPricing` |
| 16 | `GET` | `/api/admin/shipping/bosta/shipments` | zone:View | `listBostaShipments` |
| 17 | `DELETE` | `/api/admin/shipping/bosta/shipments/:shipmentId` | zone:Edit | `cancelBostaShipment` |
| 18 | `GET` | `/api/admin/shipping/bosta/shipments/:shipmentId/history` | zone:View | `getBostaTrackingHistory` |
| 19 | `GET` | `/api/admin/shipping/bosta/shipments/:shipmentId/label` | zone:View | `getBostaLabel` |
| 20 | `POST` | `/api/admin/shipping/bosta/shipments/:shipmentId/refresh` | zone:Edit | `refreshBostaTracking` |
| 21 | `POST` | `/api/admin/shipping/bosta/shipments/:shipmentId/sync` | zone:Edit | `syncBostaShipment` |
| 22 | `GET` | `/api/admin/shipping/bosta/shipments/stats` | zone:View | `getBostaShipmentsStats` |
| 23 | `POST` | `/api/admin/shipping/bosta/test` | zone:Edit | `testBostaConnection` |
| 24 | `GET` | `/api/admin/shipping/bosta/track/:trackingNumber` | zone:View | `trackBostaShipment` |
| 25 | `GET` | `/api/admin/shipping/free-products` | product:View | `getFreeShippingProducts` |
| 26 | `PUT` | `/api/admin/shipping/free-products` | product:Edit | `updateFreeShippingProducts` |
| 27 | `GET` | `/api/admin/shipping/settings` | zone:View | `getShippingSettings` |
| 28 | `PUT` | `/api/admin/shipping/settings` | zone:Edit | `updateShippingSettings` |

### `src/routes/admin/DeliveryMan.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/delivery-man` | - | `getDeliveryMen` |
| 2 | `POST` | `/api/admin/delivery-man` | - | `createDeliveryMan` |
| 3 | `DELETE` | `/api/admin/delivery-man/:id` | - | `deleteDeliveryMan` |
| 4 | `GET` | `/api/admin/delivery-man/:id` | - | `getDeliveryManById` |
| 5 | `PUT` | `/api/admin/delivery-man/:id` | - | `updateDeliveryMan` |

### `src/routes/admin/DeliveryAssignment.ts`

**Auth:** Public — **Endpoints:** 7

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/delivery-assignment/delivery-men/:id/orders` | zone:View | `getDeliveryManOrders` |
| 2 | `GET` | `/api/admin/delivery-assignment/delivery-men/available` | zone:View | `getAvailableDeliveryMen` |
| 3 | `POST` | `/api/admin/delivery-assignment/orders/:orderId/assign` | zone:Edit | `manualAssign` |
| 4 | `POST` | `/api/admin/delivery-assignment/orders/:orderId/auto-assign` | zone:Edit | `autoAssign` |
| 5 | `GET` | `/api/admin/delivery-assignment/orders/:orderId/delivery-info` | zone:View | `getOrderDeliveryInfo` |
| 6 | `POST` | `/api/admin/delivery-assignment/orders/:orderId/delivery-status` | zone:Edit | `updateStatus` |
| 7 | `POST` | `/api/admin/delivery-assignment/orders/:orderId/unassign` | zone:Edit | `unassign` |

### `src/routes/admin/Couriers.ts`

**Auth:** Bearer — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/courier` | courier:View | `getCouriers` |
| 2 | `POST` | `/api/admin/courier` | courier:Add | `createCourier` |
| 3 | `DELETE` | `/api/admin/courier/:id` | courier:Delete | `deleteCourier` |
| 4 | `GET` | `/api/admin/courier/:id` | courier:View | `getCourierById` |
| 5 | `PUT` | `/api/admin/courier/:id` | courier:Edit | `updateCourier` |

### `src/routes/admin/Zone.ts`

**Auth:** Bearer — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/zone` | zone:View | `getZones` |
| 2 | `POST` | `/api/admin/zone` | zone:Add | `createzone` |
| 3 | `DELETE` | `/api/admin/zone/:id` | zone:Delete | `deleteZone` |
| 4 | `GET` | `/api/admin/zone/:id` | zone:View | `getZoneById` |
| 5 | `PUT` | `/api/admin/zone/:id` | zone:Edit | `updateZone` |
| 6 | `GET` | `/api/admin/zone/countries` | zone:View | `getCountriesWithCities` |

### `src/routes/admin/City.ts`

**Auth:** Bearer — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/city` | city:View | `getCities` |
| 2 | `POST` | `/api/admin/city` | city:Add | `createCity` |
| 3 | `DELETE` | `/api/admin/city/:id` | city:Delete | `deleteCity` |
| 4 | `GET` | `/api/admin/city/:id` | city:View | `getCityById` |
| 5 | `PUT` | `/api/admin/city/:id` | city:Edit | `updateCity` |

### `src/routes/admin/Country.ts`

**Auth:** Bearer — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/country` | country:View | `getCountries` |
| 2 | `POST` | `/api/admin/country` | country:Add | `createCountry` |
| 3 | `DELETE` | `/api/admin/country/:id` | country:Delete | `deleteCountry` |
| 4 | `GET` | `/api/admin/country/:id` | country:View | `getCountryById` |
| 5 | `PUT` | `/api/admin/country/:id` | country:Edit | `updateCountry` |

## 8. E-commerce Admin

*Online Orders / Store Orders / Store Settings / Shipping Content*


### `src/routes/admin/onlineOrders.ts`

**Auth:** Public — **Endpoints:** 6

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/online-orders` | orders:View | `getAllOnlineOrders` |
| 2 | `DELETE` | `/api/admin/online-orders/:id` | - | `deleteOnlineOrder` |
| 3 | `GET` | `/api/admin/online-orders/:id` | orders:View | `getOnlineOrderById` |
| 4 | `PATCH` | `/api/admin/online-orders/:id/bosta-address` | orders:Edit | `updateOrderBostaAddress` |
| 5 | `PATCH` | `/api/admin/online-orders/:id/status` | orders:Edit | `updateOnlineOrderStatus` |
| 6 | `POST` | `/api/admin/online-orders/bulk-create-shipments` | orders:Edit | `bulkCreateShipments` |

### `src/routes/admin/orders.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/orders` | orders:View | `getAllOrders` |
| 2 | `GET` | `/api/admin/orders/:id` | orders:View | `getOrderById` |
| 3 | `GET` | `/api/admin/orders/report` | orders:View | `getOrdersReport` |

### `src/routes/admin/ecommerceData.ts`

**Auth:** Public — **Endpoints:** 10

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/ecommerce-data` | - | `getEcommerceData` |
| 2 | `POST` | `/api/admin/ecommerce-data` | - | `createEcommerceData` |
| 3 | `DELETE` | `/api/admin/ecommerce-data/:id` | - | `deleteEcommerceData` |
| 4 | `GET` | `/api/admin/ecommerce-data/:id` | - | `getEcommerceDataById` |
| 5 | `PUT` | `/api/admin/ecommerce-data/:id` | - | `updateEcommerceData` |
| 6 | `GET` | `/api/store/ecommerce-data` | - | `getEcommerceData` |
| 7 | `POST` | `/api/store/ecommerce-data` | - | `createEcommerceData` |
| 8 | `DELETE` | `/api/store/ecommerce-data/:id` | - | `deleteEcommerceData` |
| 9 | `GET` | `/api/store/ecommerce-data/:id` | - | `getEcommerceDataById` |
| 10 | `PUT` | `/api/store/ecommerce-data/:id` | - | `updateEcommerceData` |

### `src/routes/admin/storeSettings.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/store-settings` | - | `getStoreSettings` |
| 2 | `PUT` | `/api/admin/store-settings` | - | `updateStoreSettings` |
| 3 | `GET` | `/api/admin/store-settings/themes/:slug` | - | `browseThemesBySlug` |
| 4 | `GET` | `/api/admin/store-settings/themes/categories` | - | `browseThemesCategories` |
| 5 | `GET` | `/api/admin/store-settings/themes/categories/:categoryId` | - | `getCategoryThemes` |

### `src/routes/admin/ordertype.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/order-type` | orderType:View | `getOrderTypes` |
| 2 | `PUT` | `/api/admin/order-type/:id` | orderType:Edit | `updateOrderTypeStatus` |

## 9. Reports & Dashboard

*Dashboard / Product / Movement / Filters / Financial*


### `src/routes/admin/dsashboard.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/dashboard` | - | `getDashboard` |
| 2 | `GET` | `/api/admin/dashboard/quick-stats` | - | `getQuickStats` |

### `src/routes/admin/productReport.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/product-report` | product_report:View | `getProductSalesReport` |
| 2 | `GET` | `/api/admin/product-report/selection` | product_report:View | `selection` |

### `src/routes/admin/productMovement.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/product-movement` | product_movement:View | `getProductMovement` |
| 2 | `GET` | `/api/admin/product-movement/selection` | product_movement:View | `selection` |

### `src/routes/admin/reportFilters.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/report-filters` | - | `getReportFilterOptions` |

## 10. Payment Gateways & Integrations

*Paymob / Geidea / Fawry / Payment Methods*


### `src/routes/admin/Paymob.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/paymob` | paymob:View | `getPaymob` |
| 2 | `POST` | `/api/admin/paymob` | paymob:Add | `createPaymob` |
| 3 | `GET` | `/api/admin/paymob/:id` | paymob:View | `getPaymobId` |
| 4 | `PUT` | `/api/admin/paymob/:id` | paymob:Edit | `updatePaymob` |

### `src/routes/admin/Geidea.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/geidea` | - | `getGeidea` |
| 2 | `POST` | `/api/admin/geidea` | - | `createGeidea` |
| 3 | `DELETE` | `/api/admin/geidea/:id` | - | `deleteGeideaConfig` |
| 4 | `GET` | `/api/admin/geidea/:id` | - | `getGeideaById` |
| 5 | `PUT` | `/api/admin/geidea/:id` | - | `updateGeidea` |

### `src/routes/admin/Fawry.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/fawry` | - | `getFawry` |
| 2 | `POST` | `/api/admin/fawry` | - | `createFawry` |
| 3 | `GET` | `/api/admin/fawry/:id` | - | `getFawryId` |
| 4 | `PUT` | `/api/admin/fawry/:id` | - | `updateFawry` |

### `src/routes/admin/payment_method.ts`

**Auth:** Public — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/admin/payment_method` | payment_method:View | `getAllPaymentMethods` |
| 2 | `POST` | `/api/admin/payment_method` | payment_method:Add | `createPaymentMethod` |
| 3 | `DELETE` | `/api/admin/payment_method/:id` | payment_method:Delete | `deletePaymentMethod` |
| 4 | `GET` | `/api/admin/payment_method/:id` | payment_method:View | `getPaymentMethodById` |
| 5 | `PUT` | `/api/admin/payment_method/:id` | payment_method:Edit | `updatePaymentMethod` |

## 11. Storefront API - /api/store

*Auth / Catalog / Cart / Address / Order / Wishlist / Installments / Shipping*


### `src/routes/users/auth.ts`

**Auth:** Bearer — **Endpoints:** 9

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `POST` | `/api/store/auth/apple-login` | - | `appleAuth` |
| 2 | `POST` | `/api/store/auth/complete-profile` | - | `completeProfile` |
| 3 | `PUT` | `/api/store/auth/edit-profile/:id` | - | `editProfile` |
| 4 | `GET` | `/api/store/auth/get-profile` | - | `getProfile` |
| 5 | `POST` | `/api/store/auth/google-login` | - | `googleAuth` |
| 6 | `POST` | `/api/store/auth/login` | - | `login` |
| 7 | `POST` | `/api/store/auth/resend-otp` | - | `resendOtp` |
| 8 | `POST` | `/api/store/auth/signup` | - | `signup` |
| 9 | `POST` | `/api/store/auth/verify-otp` | - | `verifyOtpAndLogin` |

### `src/routes/users/products.ts`

**Auth:** Bearer (optional) — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/product` | - | `getAllProducts` |
| 2 | `GET` | `/api/store/product/:id` | - | `getProductById` |
| 3 | `GET` | `/api/store/product/best-sellers` | - | `getBestSellingProducts` |
| 4 | `GET` | `/api/store/product/category/:id` | - | `getProductsByCategory` |

### `src/routes/users/Categoey.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/category` | - | `getAllCategorys` |
| 2 | `GET` | `/api/store/category/:id` | - | `getCategoryById` |

### `src/routes/users/brand.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/brand` | - | `getAllBrands` |
| 2 | `GET` | `/api/store/brand/:id` | - | `getBrandById` |

### `src/routes/users/banner.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/banner` | - | `getBanners` |

### `src/routes/users/bundle.ts`

**Auth:** Public — **Endpoints:** 2

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/offer` | - | `getAllBundles` |
| 2 | `GET` | `/api/store/offer/:id` | - | `getBundleById` |

### `src/routes/users/wishlist.ts`

**Auth:** Bearer — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/wishlist` | - | `getUserWishlist` |
| 2 | `DELETE` | `/api/store/wishlist/clear` | - | `clearWishlist` |
| 3 | `POST` | `/api/store/wishlist/toggle` | - | `toggleWishlist` |

### `src/routes/users/Address.ts`

**Auth:** Bearer — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/address` | - | `getMyAddresses` |
| 2 | `POST` | `/api/store/address` | - | `addAddress` |
| 3 | `DELETE` | `/api/store/address/:id` | - | `deleteAddress` |
| 4 | `PUT` | `/api/store/address/:id` | - | `updateAddress` |
| 5 | `GET` | `/api/store/address/lists` | - | `getAllLists` |

### `src/routes/users/Cart.ts`

**Auth:** Bearer (optional) — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/cart` | - | `getCart` |
| 2 | `POST` | `/api/store/cart/apply-coupon` | - | `applyCoupon` |
| 3 | `DELETE` | `/api/store/cart/clear` | - | `clearCart` |
| 4 | `POST` | `/api/store/cart/sync-cart` | - | `syncCart` |

### `src/routes/users/Order.ts`

**Auth:** Bearer (optional) — **Endpoints:** 11

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/order/:id` | - | `getOrderDetails` |
| 2 | `POST` | `/api/store/order/checkout` | - | `createOrder` |
| 3 | `GET` | `/api/store/order/my-orders` | - | `getMyOrders` |
| 4 | `GET` | `/api/store/order/status/:ref` | - | `getOrderStatusByRef` |
| 5 | `GET` | `/api/store/order/verify-payment/paymob/:orderId` | - | `verifyPaymobPaymentStatus` |
| 6 | `GET` | `/api/store/order/webhook/fawry` | - | `-` |
| 7 | `POST` | `/api/store/order/webhook/fawry` | - | `-` |
| 8 | `GET` | `/api/store/order/webhook/geidea` | - | `-` |
| 9 | `POST` | `/api/store/order/webhook/geidea` | - | `-` |
| 10 | `GET` | `/api/store/order/webhook/paymob` | - | `-` |
| 11 | `POST` | `/api/store/order/webhook/paymob` | - | `-` |

### `src/routes/users/installments.ts`

**Auth:** Bearer (optional) — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/installments` | - | `getMyInstallmentPlans` |
| 2 | `GET` | `/api/store/installments/:id` | - | `getMyInstallmentPlan` |
| 3 | `GET` | `/api/store/installments/order/:orderId` | - | `getMyOrderInstallmentPlan` |
| 4 | `POST` | `/api/store/installments/preview` | - | `previewStoreInstallment` |
| 5 | `GET` | `/api/store/installments/settings` | - | `getStoreInstallmentSettings` |

### `src/routes/users/paymentmethod.ts`

**Auth:** Bearer — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/payment-methods` | - | `getPaymentMethods` |

### `src/routes/users/ordertype.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/order-type` | - | `getOrderTypes` |

### `src/routes/users/Warehouse.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/warehouse` | - | `getWarehouses` |

### `src/routes/users/storeSettings.ts`

**Auth:** Public — **Endpoints:** 1

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/store-settings` | - | `getStoreSettings` |

### `src/routes/users/shipping.ts`

**Auth:** Public — **Endpoints:** 4

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/store/shipping/active-method` | - | `getActiveShippingMethod` |
| 2 | `GET` | `/api/store/shipping/bosta/cities` | - | `getBostaCitiesForUser` |
| 3 | `GET` | `/api/store/shipping/bosta/districts/:cityId` | - | `getBostaDistrictsForUser` |
| 4 | `GET` | `/api/store/shipping/bosta/pricing` | - | `getBostaPricingForUser` |

## 12. Public API - /api/public

*Public products / Custom order accept & reject webhooks*


### `src/routes/public.ts`

**Auth:** x-api-key — **Endpoints:** 5

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/public/orders/accept` | - | `acceptCustomOrder` |
| 2 | `POST` | `/api/public/orders/accept` | - | `acceptCustomOrder` |
| 3 | `GET` | `/api/public/orders/reject` | - | `rejectCustomOrder` |
| 4 | `POST` | `/api/public/orders/reject` | - | `rejectCustomOrder` |
| 5 | `GET` | `/api/public/products` | - | `getPublicProducts` |

## 13. POS Sync - /api/sync

*Bootstrap / Push / Pull*


### `src/routes/admin/POS/sync.ts`

**Auth:** Public — **Endpoints:** 3

| # | Method | Path | Permission | Handler |
|---|---|---|---|---|
| 1 | `GET` | `/api/sync/bootstrap/:table` | - | `bootstrapTable` |
| 2 | `GET` | `/api/sync/pull` | - | `pullChangeLog` |
| 3 | `POST` | `/api/sync/push` | - | `pushChanges` |

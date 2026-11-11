# 🗄️ Full Database Schema Reference

> This document is generated automatically from the Mongoose schema files under `src/models/schema`.
> Every table/model entry shows the source file, the Mongoose model name, and all fields with their types, references (ref), and enums.


---

## `src/models/schema/admin/Action.ts`

### Model: `Action`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `roleId` | Schema.Types.ObjectId | ref: Role<br>required: true |
| `name` | String | required: true |


---

## `src/models/schema/admin/adjustments.ts`

### Model: `Adjustment`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `date` | Date | default: Date.now  |
| `reference` | String | default: function () { |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse<br>required: true |
| `note` | String | - |
| `productId` | Schema.Types.ObjectId | ref: Product |
| `productId.type` | - | - |
| `productId.ref` | - | - |
| `quantity` | Number | required: true |
| `select_reasonId` | Schema.Types.ObjectId | ref: SelectReason |


---

## `src/models/schema/admin/Banner.ts`

### Model: `Banner`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | - |
| `name.type` | - | - |
| `name.enum` | - | - |
| `ar_name` | String | required: true |
| `title` | String | required: false |
| `description` | String | required: false |
| `images` | String | required: true |
| `images.type` | - | - |
| `images.required` | - | - |
| `link` | String | default: ""  |
| `isActive` | Boolean | default: true  |


---

## `src/models/schema/admin/Booking.ts`

### Model: `Booking`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `number_of_days` | Number | required: true |
| `deposit` | Number | required: true |
| `CustmerId` | String | ref: Customer |
| `WarehouseId` | ObjectId | ref: Warehouse |
| `ProductId` | ObjectId | ref: Product |
| `CategoryId` | ObjectId | ref: Category |
| `option_id` | ObjectId | ref: ProductPriceOption |
| `status` | String | enum: pending,pay,failer<br>default: "pending" |
| `_id` | String | default: () => randomUUID()  |


---

## `src/models/schema/admin/BostaShipment.ts`

### Model: `BostaShipment`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `superadminId` | Schema.Types.ObjectId | ref: User<br>required: true |
| `relatedModel` | String | enum: Order, Sale<br>required: true |
| `relatedId` | String | required: true |
| `type` | Number | default: 10 |
| `deliveryId` | String | default: null |
| `trackingNumber` | String | default: null |
| `awb` | String | default: null  |
| `status` | String | default: null |
| `statusCode` | Number | default: null  |
| `labelUrl` | String | default: null  |
| `cod` | Number | default: 0  |
| `shippingCost` | Number | default: 0  |
| `receiver` | String | default: ""  |
| `receiver.firstName` | String | default: ""  |
| `receiver.lastName` | String | default: ""  |
| `receiver.phone` | String | default: ""  |
| `receiver.email` | String | default: ""  |
| `dropOffAddress` | String | default: ""  |
| `dropOffAddress.city` | String | default: ""  |
| `dropOffAddress.zoneId` | String | default: ""  |
| `dropOffAddress.districtId` | String | default: ""  |
| `dropOffAddress.firstLine` | String | default: ""  |
| `dropOffAddress.secondLine` | String | default: ""  |
| `dropOffAddress.buildingNumber` | String | default: ""  |
| `dropOffAddress.floor` | String | default: ""  |
| `dropOffAddress.apartment` | String | default: ""  |
| `weight` | Number | default: 1  |
| `itemsCount` | Number | default: 1  |
| `description` | String | default: ""  |
| `notes` | String | default: ""  |
| `pickup` | String | default: null |
| `pickup.pickupId` | String | default: null |
| `pickup.scheduledDate` | String | default: null  |
| `pickup.scheduledTimeSlot` | Schema.Types.Mixed | default: null  |
| `pickup.status` | String | default: null  |
| `pickup.contactPerson` | String | default: ""  |
| `pickup.contactPerson.firstName` | String | default: ""  |
| `pickup.contactPerson.lastName` | String | default: ""  |
| `pickup.contactPerson.phone` | String | default: ""  |
| `pickup.contactPerson.email` | String | default: ""  |
| `pickup.notes` | String | default: ""  |
| `pickup.createdAt` | Date | default: null  |
| `pickup.rawResponse` | Schema.Types.Mixed | default: null  |
| `trackingHistory` | String | default: null  |
| `trackingHistory.status` | String | default: null  |
| `trackingHistory.statusCode` | Number | default: null  |
| `trackingHistory.snapshotAt` | Date | default: Date.now  |
| `trackingHistory.data` | Schema.Types.Mixed | default: null  |
| `lastSyncAt` | Date | default: null  |
| `lastTrackAt` | Date | default: null  |
| `rawResponse` | Schema.Types.Mixed | default: null  |


---

## `src/models/schema/admin/brand.ts`

### Model: `Brand`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | - |
| `logo` | String | - |
| `is_featured` | Boolean | default: false  |


---

## `src/models/schema/admin/cashier.ts`

### Schema: `CashierSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | required: true |
| `warehouse_id` | ObjectId | ref: Warehouse<br>required: true |
| `status` | Boolean | default: true  |
| `cashier_active` | Boolean | default: false  |
| `bankAccounts` | ObjectId | ref: BankAccount |
| `bankAccounts.type` | - | - |
| `bankAccounts.ref` | - | - |
| `printer_type` | String | enum: USB, NETWORK |
| `printer_IP` | String | - |
| `printer_port` | Number | - |
| `Printer_name` | String | - |


---

## `src/models/schema/admin/category.ts`

### Model: `Category`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | required: true |
| `image` | String | - |
| `banner` | String | - |
| `parentId` | Schema.Types.ObjectId | ref: Category |
| `product_quantity` | Number | default: 0  |
| `Is_Online` | Boolean | default: true  |
| `is_featured` | Boolean | default: false  |
| `order` | Number | default: 999 |


---

## `src/models/schema/admin/Category_Material.ts`

### Model: `CategoryMaterial`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | required: true |
| `is_active` | Boolean | default: true  |
| `parent_category_id` | Schema.Types.ObjectId | ref: CategoryMaterial<br>default: null  |
| `image` | String | - |


---

## `src/models/schema/admin/City.ts`

### Model: `City`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `country` | Schema.Types.ObjectId | ref: Country<br>required: true |
| `shipingCost` | Number | default: 0 |


---

## `src/models/schema/admin/Counter.ts`

### Model: `Counter`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `_id` | String | required: true |
| `seq` | Number | default: 0  |


---

## `src/models/schema/admin/Country.ts`

### Model: `Country`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `isDefault` | Boolean | default: false |


---

## `src/models/schema/admin/coupons.ts`

### Model: `Coupon`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `coupon_code` | String | required: true<br>unique |
| `type` | String | enum: percentage, flat<br>required: true |
| `amount` | Number | required: true |
| `minimum_amount_for_use` | Number | default: 0  |
| `quantity` | Number | required: true |
| `available` | Number | required: true |
| `expired_date` | Date | required: true |
| `image` | String | default: ""  |


---

## `src/models/schema/admin/Couriers.ts`

### Model: `Courier`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `phone_number` | String | required: true<br>unique |
| `address` | String | required: true |


---

## `src/models/schema/admin/Currency.ts`

### Model: `Currency`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `amount` | Number | - |
| `isdefault` | Boolean | default: false |


---

## `src/models/schema/admin/DecimalSetting.ts`

### Model: `DecimalSetting`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `decimal_places` | Number | enum: 0, 1, 2, 3<br>default: 2<br>required: true |


---

## `src/models/schema/admin/delivery.ts`

### Model: `Delivery`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `phone_number` | String | required: true |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse<br>required: true |
| `status` | Boolean | default: true |
| `photo` | String | - |
| `identity_type` | String | enum: ID,Passport |
| `identity_number` | String | - |
| `identity_photo` | String | - |
| `email` | String | - |
| `password` | String | - |


---

## `src/models/schema/admin/deliveryMan.ts`

### Schema: `DeliveryMan`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `email` | String | required: true |
| `password` | String | required: true |
| `phone_number` | String | required: true |
| `status` | String | enum: active, inactive<br>default: "active"  |
| `photo` | String | - |
| `completedOrders` | Number | default: 0  |
| `failedOrders` | Number | default: 0  |
| `totalEarnings` | Number | default: 0  |
| `maxConcurrentOrders` | Number | default: 10 |


---

## `src/models/schema/admin/departments.ts`

### Model: `Department`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `description` | String | required: true |


---

## `src/models/schema/admin/Discount.ts`

### Model: `Discount`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `amount` | Number | required: true |
| `type` | String | enum: percentage, fixed<br>required: true |
| `status` | Boolean | default: true  |
| `applyIn` | String | enum: POS, E-commerce<br>default: "E-commerce" |
| `all_warehouses` | Boolean | default: true  |
| `warehouse_ids` | ObjectId | ref: Warehouse |
| `warehouse_ids.type` | - | - |
| `warehouse_ids.ref` | - | - |


---

## `src/models/schema/admin/EcommerceData.ts`

### Schema: `EcommerceDataSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | default: "Main Store"<br>required: true |
| `phone` | String | default: ""  |
| `email` | String | default: ""  |
| `address` | String | default: ""  |
| `status` | String | enum: active, inactive<br>default: "active" |
| `social_links` | String | default: ""  |
| `social_links.facebook` | String | default: ""  |
| `social_links.instagram` | String | default: ""  |
| `social_links.whatsapp` | String | default: ""  |
| `social_links.twitter` | String | default: ""  |
| `social_links.tiktok` | String | default: ""  |
| `social_links.youtube` | String | default: ""  |
| `social_links.linkedin` | String | default: ""  |
| `header` | String | default: ""  |
| `header.logo` | String | default: ""  |
| `header.title` | String | default: ""  |
| `header.announcement` | String | default: ""  |
| `header.links` | String | - |
| `header.links.type` | - | - |
| `header.links.trim` | - | - |
| `footer` | String | default: ""  |
| `footer.logo` | String | default: ""  |
| `footer.bio` | String | default: ""  |
| `footer.copyright` | String | default: ""  |


---

## `src/models/schema/admin/expensecategory.ts`

### Model: `ExpenseCategory`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true<br>unique |
| `status` | Boolean | default: true |


---

## `src/models/schema/admin/Factials.ts`

### Model: `Factials`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `product_id` | Schema.Types.ObjectId | ref: Product<br>required: true |
| `materials` | Schema.Types.ObjectId | ref: Material<br>required: true |
| `materials.material_id` | Schema.Types.ObjectId | ref: Material<br>required: true |
| `materials.quantity` | Number | required: true |
| `amount_of_product` | Number | required: true |
| `created_by` | Schema.Types.ObjectId | ref: User<br>required: true |
| `date` | Date | default: Date.now  |


---

## `src/models/schema/admin/Fawry.ts`

### Schema: `fawrySchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `payment_method_id` | Schema.Types.ObjectId | ref: PaymentMethod<br>required: true |
| `merchantCode` | String | required: true |
| `secureKey` | String | required: true |
| `isActive` | Boolean | default: false  |
| `sandboxMode` | Boolean | default: true  |


---

## `src/models/schema/admin/Financial_Account.ts`

### Model: `BankAccount`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | required: true |
| `warehouseId` | ObjectId | ref: Warehouse<br>required: true |
| `warehouseId.type` | - | - |
| `warehouseId.ref` | - | - |
| `warehouseId.required` | - | - |
| `image` | String | - |
| `balance` | Number | default: 0  |
| `description` | String | - |
| `status` | Boolean | default: true  |
| `in_POS` | Boolean | default: false  |


---

## `src/models/schema/admin/Geidea.ts`

### Schema: `geideaSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `payment_method_id` | Schema.Types.ObjectId | ref: PaymentMethod<br>required: true |
| `publicKey` | String | required: true |
| `apiPassword` | String | required: true |
| `merchantId` | String | required: true |
| `webhookSecret` | String | required: true |
| `isActive` | Boolean | default: true  |


---

## `src/models/schema/admin/income_categories.ts`

### Model: `incomecategories`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |


---

## `src/models/schema/admin/InstallmentPlan.ts`

### Model: `InstallmentPlan`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `plan_number` | String | unique |
| `source` | String | enum: pos, ecommerce<br>required: true |
| `customer_id` | Schema.Types.String | ref: Customer<br>required: true |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse<br>required: true |
| `order_reference` | String | default: ""  |
| `down_payment` | Number | default: 0 |
| `financed_amount` | Number | required: true |
| `interest_type` | String | enum: percentage, fixed<br>default: "percentage" |
| `interest_value` | Number | default: 0 |
| `interest_amount` | Number | default: 0 |
| `total_payable` | Number | required: true |
| `installments_count` | Number | required: true |
| `remaining_amount` | Number | required: true |
| `start_date` | Date | default: Date.now  |
| `status` | String | enum: active, completed, cancelled<br>default: "active" |
| `notes` | String | default: ""  |
| `created_by` | Schema.Types.ObjectId | ref: User |

### Model: `InstallmentItem`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `plan_id` | Schema.Types.ObjectId | ref: InstallmentPlan<br>required: true |
| `sale_id` | Schema.Types.String | ref: Sale |
| `order_id` | Schema.Types.ObjectId | ref: Order |
| `customer_id` | Schema.Types.String | ref: Customer |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse |
| `sequence` | Number | required: true |
| `amount` | Number | required: true |
| `due_date` | Date | required: true |
| `paid_amount` | Number | default: 0 |
| `status` | String | enum: pending, paid, overdue, cancelled<br>default: "pending" |
| `paid_at` | Date | default: null  |
| `paid_by` | Schema.Types.ObjectId | ref: User<br>default: null  |


---

## `src/models/schema/admin/InstallmentSetting.ts`

### Model: `InstallmentSetting`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `key` | String | default: "default" <br>unique |
| `enabled` | Boolean | default: true  |
| `interest_value` | Number | default: 0 |
| `max_installments` | Number | default: 12 |
| `min_down_payment_percent` | Number | default: 0 |
| `updated_by` | Schema.Types.ObjectId | ref: User |


---

## `src/models/schema/admin/Materials.ts`

### Model: `Material`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | required: true |
| `photo` | String | - |
| `description` | String | - |
| `ar_description` | String | - |
| `category_id` | Schema.Types.ObjectId | ref: CategoryMaterial<br>required: true |
| `quantity` | Number | default: 0  |
| `expired_ability` | Boolean | default: false  |
| `date_of_expiry` | Date | - |
| `low_stock` | Number | default: 0  |
| `unit` | String | enum: kg, g, piece, liter, meter<br>required: true |


---

## `src/models/schema/admin/Notfication.ts`

### Model: `Notification`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `type` | String | enum: expiry, expired, low_stock, new_order, new_sale<br>required: true |
| `productId` | ObjectId | ref: Product |
| `purchaseItemId` | ObjectId | ref: PurchaseItem |
| `message` | String | required: true |
| `isRead` | Boolean | default: false |
| `_id` | String | default: () => randomUUID() |


---

## `src/models/schema/admin/Offers.ts`

### Model: `Offers`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `categoryId` | ObjectId | ref: Category |
| `categoryId.type` | - | - |
| `categoryId.ref` | - | - |
| `productId` | ObjectId | ref: Product |
| `productId.type` | - | - |
| `productId.ref` | - | - |
| `discountId` | ObjectId | ref: Discount |


---

## `src/models/schema/admin/ordertype.ts`

### Model: `OrderType`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `type` | String | - |
| `isActive` | Boolean | default: true  |


---

## `src/models/schema/admin/pandels.ts`

### Schema: `pandelProductSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `productId` | ObjectId | ref: Product<br>required: true |
| `productPriceId` | ObjectId | ref: ProductPrice<br>default: null |
| `quantity` | Number | default: 1 |

### Model: `Pandel`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `startdate` | Date | required: true |
| `enddate` | Date | required: true |
| `status` | Boolean | default: true  |
| `all_warehouses` | Boolean | default: true  |
| `warehouse_ids` | ObjectId | ref: Warehouse |
| `warehouse_ids.type` | - | - |
| `warehouse_ids.ref` | - | - |
| `images` | String | - |
| `images.type` | - | - |
| `products` | - | - |


---

## `src/models/schema/admin/payment_methods.ts`

### Model: `PaymentMethod`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true<br>unique |
| `isActive` | Boolean | default: true  |
| `discription` | String | required: true |
| `icon` | String | required: true |
| `type` | String | enum: manual, automatic<br>required: true |
| `requiresProof` | Boolean | default: false  |


---

## `src/models/schema/admin/Paymob.ts`

### Model: `Paymob`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `isActive` | Boolean | default: false  |
| `iframe_id` | String | required: true |
| `integration_id` | String | required: true |
| `hmac_key` | String | required: true |
| `payment_method_id` | ObjectId | ref: PaymentMethod<br>required: true |


---

## `src/models/schema/admin/PickupPoint.ts`

### Model: `PickupPoint`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | default: ""  |
| `address` | String | default: ""  |
| `ar_address` | String | default: ""  |
| `phone` | String | default: ""  |
| `lat` | Number | - |
| `lng` | Number | - |
| `is_active` | Boolean | default: true  |


---

## `src/models/schema/admin/points.ts`

### Model: `Point`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `amount` | Number | required: true |
| `points` | Number | required: true |


---

## `src/models/schema/admin/Popup.ts`

### Model: `Popup`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `title_ar` | String | required: true |
| `title_En` | String | required: true |
| `description_ar` | String | required: true |
| `description_En` | String | required: true |
| `image` | String | - |
| `link` | String | required: true |


---

## `src/models/schema/admin/POS/CashierShift.ts`

### Model: `CashierShift`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `start_time` | Date | - |
| `end_time` | Date | - |
| `status` | String | enum: open, closed<br>default: "open"  |
| `total_expenses` | Number | default: 0  |
| `net_cash_in_drawer` | Number | default: 0  |
| `_id` | String | default: () => randomUUID()  |


---

## `src/models/schema/admin/POS/ChangeLog.ts`

### Model: `ChangeLog`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `table_name` | String | required: true |
| `record_id` | String | required: true |
| `op` | String | enum: insert, update, delete<br>required: true |
| `payload` | Schema.Types.Mixed | default: null  |
| `warehouseId` | Schema.Types.Mixed | default: null  |
| `originClientId` | String | default: null |
| `sourceChangeId` | String | - |


---

## `src/models/schema/admin/POS/customer.ts`

### Model: `Customer`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `email` | String | unique |
| `phone_number` | String | - |
| `address` | String | - |
| `country` | Schema.Types.ObjectId | ref: Country |
| `city` | Schema.Types.ObjectId | ref: City |
| `customer_group_id` | Schema.Types.String | ref: CustomerGroup |
| `total_points_earned` | Number | default: 0 |
| `is_Due` | Boolean | default: false  |
| `amount_Due` | Number | default: 0  |
| `password` | String | - |
| `is_profile_complete` | Boolean | default: false |
| `otp_code` | String | default: null |
| `otp_expires_at` | Date | default: null |
| `imagePath` | String | default: null |
| `wishlist` | Schema.Types.ObjectId | ref: Product |
| `wishlist.type` | - | - |
| `wishlist.ref` | - | - |
| `addresses` | Schema.Types.ObjectId | ref: Address |
| `addresses.type` | - | - |
| `addresses.ref` | - | - |
| `google_id` | String | unique |
| `apple_id` | String | unique |
| `auth_provider` | String | enum: local, google, apple<br>default: "local" |
| `_id` | String | default: () => randomUUID()  |

### Model: `CustomerGroup`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true<br>unique |
| `status` | Boolean | default: true  |
| `_id` | String | default: () => randomUUID()  |


---

## `src/models/schema/admin/POS/expenses.ts`

### Model: `Expense`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `amount` | Number | required: true |
| `Category_id` | ObjectId | ref: ExpenseCategory<br>required: true |
| `shift_id` | String | ref: CashierShift<br>required: false |
| `cashier_id` | ObjectId | ref: User<br>required: false |
| `admin_id` | ObjectId | ref: User<br>required: false |
| `note` | String | - |
| `financial_accountId` | ObjectId | ref: BankAccount<br>required: true |
| `_id` | String | default: () => randomUUID()  |


---

## `src/models/schema/admin/POS/giftCard.ts`

### Model: `GiftCard`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `code` | String | required: true<br>unique |
| `amount` | Number | required: true |
| `customer_id` | Schema.Types.String | ref: Customer |
| `expiration_date` | Date | - |
| `isActive` | Boolean | default: true  |
| `_id` | String | default: () => randomUUID()  |


---

## `src/models/schema/admin/POS/payment.ts`

### Model: `Payment`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `sale_id` | Schema.Types.String | ref: Sale<br>required: true |
| `status` | String | enum: pending, completed<br>default: "completed" |
| `payment_proof` | String | - |
| `_id` | String | default: () => randomUUID()  |


---

## `src/models/schema/admin/POS/ReturnSale.ts`

### Schema: `ReturnItemSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `product_id` | Schema.Types.ObjectId | ref: Product |
| `product_price_id` | Schema.Types.ObjectId | ref: ProductPrice |
| `bundle_id` | Schema.Types.ObjectId | ref: Pandel |
| `original_quantity` | Number | required: true |
| `returned_quantity` | Number | required: true |
| `price` | Number | required: true |
| `subtotal` | Number | required: true |
| `_id` | String | default: () => randomUUID()  |

### Model: `Return`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `reference` | String | default: function () {<br>unique |
| `sale_id` | Schema.Types.String | ref: Sale<br>required: true |
| `sale_reference` | String | required: true |
| `customer_id` | Schema.Types.String | ref: Customer |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse<br>required: true |
| `cashier_id` | Schema.Types.ObjectId | ref: User<br>required: true |
| `shift_id` | Schema.Types.String | ref: CashierShift<br>required: true |
| `items` | - | - |
| `total_amount` | Number | required: true |
| `financials` | Schema.Types.ObjectId | ref: BankAccount<br>required: true |
| `financials.account_id` | Schema.Types.ObjectId | ref: BankAccount<br>required: true |
| `financials.amount` | Number | required: true |
| `refund_method` | String | enum: cash, card, store_credit, original_method<br>default: "original_method" |
| `refund_account_id` | Schema.Types.ObjectId | ref: BankAccount |
| `image` | String | default: "" |
| `note` | String | default: "" |
| `date` | Date | default: Date.now |
| `_id` | String | default: () => randomUUID()  |
| `reason` | String | default: "" |


---

## `src/models/schema/admin/POS/Sale.ts`

### Model: `Sale`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `reference` | String | default: function () {<br>unique |
| `daily_order_number` | Number | default: 1 |
| `customer_id` | Schema.Types.String | ref: Customer<br>required: false |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse<br>required: true |
| `account_id` | Schema.Types.ObjectId | ref: BankAccount |
| `account_id.type` | - | - |
| `account_id.ref` | - | - |
| `order_pending` | Number | enum: 0, 1<br>default: 1 |
| `order_tax` | Schema.Types.ObjectId | ref: Taxes |
| `order_discount` | Schema.Types.ObjectId | ref: Discount |
| `service_fees` | Schema.Types.ObjectId | ref: ServiceFee<br>enum: fixed, percentage<br>required: false |
| `service_fees.service_fee_id` | Schema.Types.ObjectId | ref: ServiceFee<br>required: false |
| `service_fees.title` | String | required: true |
| `service_fees.type` | String | enum: fixed, percentage<br>required: true |
| `service_fees.rate` | Number | required: true |
| `service_fees.amount` | Number | required: true |
| `service_fees.module` | String | enum: online, pos<br>required: true |
| `service_fees.warehouseId` | Schema.Types.ObjectId | ref: Warehouse<br>required: false |
| `service_fee_total` | Number | default: 0  |
| `grand_total` | Number | required: true |
| `gift_card_id` | Schema.Types.String | ref: GiftCard |
| `coupon_id` | String | default: ""  |
| `applied_coupon` | Boolean | default: false  |
| `cashier_id` | Schema.Types.ObjectId | ref: User<br>required: true |
| `shift_id` | Schema.Types.String | ref: CashierShift<br>required: true |
| `shipping` | Number | default: 0  |
| `tax_rate` | Number | default: 0  |
| `tax_amount` | Number | default: 0  |
| `total` | Number | default: 0  |
| `paid_amount` | Number | default: 0  |
| `note` | String | default: ""  |
| `date` | Date | default: Date.now  |
| `_id` | String | default: () => randomUUID()  |
| `return_status` | String | enum: none, partial, full<br>default: "none" |
| `returned_amount` | Number | default: 0 |
| `returned_quantity` | Number | default: 0 |

### Model: `ProductSale`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `sale_id` | Schema.Types.String | ref: Sale<br>required: true |
| `product_id` | Schema.Types.ObjectId | ref: Product |
| `bundle_id` | Schema.Types.ObjectId | ref: Pandel |
| `quantity` | Number | required: true |
| `price` | Number | required: true |
| `subtotal` | Number | required: true |
| `discount` | Number | default: 0  |
| `discount_type` | String | enum: fixed, percentage<br>default: "fixed" |
| `original_price` | Number | default: 0  |
| `product_price_id` | Schema.Types.ObjectId | ref: ProductPrice |
| `isGift` | Boolean | default: false  |
| `isBundle` | Boolean | default: false  |
| `options_id` | Schema.Types.ObjectId | ref: Option |
| `options_id.type` | - | - |
| `options_id.ref` | - | - |
| `_id` | String | default: () => randomUUID()  |


---

## `src/models/schema/admin/position.ts`

### Model: `Position`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |


---

## `src/models/schema/admin/products.ts`

### Model: `Product`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `ar_description` | String | required: true |
| `image` | String | - |
| `categoryId` | ObjectId | ref: Category |
| `categoryId.type` | - | - |
| `categoryId.ref` | - | - |
| `brandId` | ObjectId | ref: Brand |
| `discountId` | ObjectId | ref: Discount |
| `product_unit` | ObjectId | ref: Unit |
| `sale_unit` | ObjectId | ref: Unit |
| `purchase_unit` | ObjectId | ref: Unit |
| `code` | String | unique |
| `price` | Number | - |
| `free_shipping` | Boolean | default: false  |
| `quantity` | Number | - |
| `description` | String | - |
| `exp_ability` | Boolean | default: false  |
| `minimum_quantity_sale` | Number | default: 1  |
| `low_stock` | Number | - |
| `whole_price` | Number | - |
| `start_quantaty` | Number | - |
| `cost` | Number | - |
| `taxesId` | ObjectId | ref: Taxes |
| `product_has_imei` | Boolean | default: false  |
| `different_price` | Boolean | default: false  |
| `show_quantity` | Boolean | default: true  |
| `maximum_to_show` | Number | - |
| `gallery_product` | String | - |
| `gallery_product.type` | - | - |
| `is_featured` | Boolean | default: false  |
| `Is_Online` | Boolean | default: true  |


---

## `src/models/schema/admin/product_price.ts`

### Model: `ProductPrice`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `productId` | ObjectId | ref: Product<br>required: true |
| `price` | Number | required: true |
| `code` | String | unique |
| `gallery` | String | - |
| `gallery.type` | - | - |
| `quantity` | Number | default: 0  |
| `strat_quantaty` | Number | default: 0  |
| `cost` | Number | default: 0  |

### Model: `ProductPriceOption`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `product_price_id` | ObjectId | ref: ProductPrice<br>required: true |
| `option_id` | ObjectId | ref: Option<br>required: true |


---

## `src/models/schema/admin/Product_Warehouse.ts`

### Model: `Product_Warehouse`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `productId` | ObjectId | ref: Product<br>required: true |
| `warehouseId` | ObjectId | ref: Warehouse<br>required: true |
| `quantity` | Number | default: 0 <br>required: true |
| `low_stock` | Number | default: 0  |


---

## `src/models/schema/admin/Purchase.ts`

### Schema: `PurchaseSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `reference` | String | default: function () {<br>unique |
| `date` | Date | default: Date.now <br>required: true |
| `warehouse_id` | ObjectId | ref: Warehouse |
| `supplier_id` | ObjectId | ref: Supplier<br>default: null  |
| `tax_id` | ObjectId | ref: Taxes |
| `receipt_img` | String | - |
| `payment_status` | String | enum: partial, full, later |
| `exchange_rate` | Number | default: 1 <br>required: true |
| `total` | Number | required: true |
| `discount` | Number | default: 0  |
| `shipping_cost` | Number | default: 0  |
| `grand_total` | Number | required: true |
| `note` | String | - |


---

## `src/models/schema/admin/PurchaseInstallment.ts`

### Schema: `PurchaseInstallmentSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `purchase_id` | ObjectId | ref: Purchase<br>required: true |
| `amount` | Number | required: true |
| `paid_amount` | Number | default: 0 |
| `date` | Date | required: true |
| `status` | String | enum: pending, paid<br>default: "pending"  |


---

## `src/models/schema/admin/PurchaseInvoice.ts`

### Model: `PurchaseInvoice`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `purchase_id` | ObjectId | ref: Purchase |
| `installment_id` | ObjectId | ref: PurchaseInstallment |
| `financial_id` | ObjectId | ref: BankAccount |
| `amount` | Number | required: true |
| `date` | Date | default: Date.now <br>required: true |


---

## `src/models/schema/admin/purchase_due_payment.ts`

### Model: `PurchaseDuePayment`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `purchase_id` | ObjectId | ref: Purchase |
| `amount` | Number | required: true |
| `date` | Date | default: Date.now <br>required: true |


---

## `src/models/schema/admin/purchase_item.ts`

### Model: `PurchaseItem`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `date` | Date | default: Date.now <br>required: true |
| `product_id` | ObjectId | ref: Product |
| `product_price_id` | Schema.Types.ObjectId | ref: ProductPrice |
| `material_id` | ObjectId | ref: Material |
| `category_id` | ObjectId | ref: Category |
| `date_of_expiery` | Date | - |
| `purchase_id` | ObjectId | ref: Purchase |
| `patch_number` | String | - |
| `warehouse_id` | ObjectId | ref: Warehouse |
| `quantity` | Number | required: true |
| `unit_cost` | Number | required: true |
| `subtotal` | Number | required: true |
| `discount_share` | Number | default: 0  |
| `unit_cost_after_discount` | Number | default: 0  |
| `tax` | Number | default: 0  |
| `item_type` | String | enum: product, material<br>default: "product" |


---

## `src/models/schema/admin/purchase_item_option.ts`

### Model: `PurchaseItemOption`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `date` | Date | default: Date.now <br>required: true |
| `purchase_item_id` | ObjectId | ref: PurchaseItem |
| `product_price_id` | ObjectId | ref: ProductPrice |
| `option_id` | ObjectId | ref: Option |
| `quantity` | Number | default: 0  |


---

## `src/models/schema/admin/Recipe.ts`

### Model: `Recipe`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `product_id` | Schema.Types.ObjectId | ref: Product<br>required: true |
| `material_id` | Schema.Types.ObjectId | ref: Material<br>required: true |
| `material_quantity` | Number | required: true |
| `unit` | String | enum: kg, g, piece, liter, meter<br>required: true |

### Model: `Production`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `product_id` | Schema.Types.ObjectId | ref: Product<br>required: true |
| `product_quantity` | Number | required: true |


---

## `src/models/schema/admin/redeem_Points.ts`

### Model: `redeem_Points`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `amount` | Number | required: true |
| `points` | Number | required: true |


---

## `src/models/schema/admin/ReturnPurchase.ts`

### Schema: `ReturnItemSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `product_id` | Schema.Types.ObjectId | ref: Product |
| `product_price_id` | Schema.Types.ObjectId | ref: ProductPrice |
| `bundle_id` | Schema.Types.ObjectId | ref: Pandel |
| `original_quantity` | Number | required: true |
| `returned_quantity` | Number | required: true |
| `price` | Number | required: true |
| `subtotal` | Number | required: true |

### Model: `ReturnPurchase`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `reference` | String | default: function () {<br>unique |
| `purchase_id` | Schema.Types.ObjectId | ref: Purchase |
| `purchase_reference` | String | required: true |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse<br>required: true |
| `supplier_id` | Schema.Types.ObjectId | ref: Supplier |
| `total_amount` | Number | required: true |
| `refund_account_id` | Schema.Types.ObjectId | ref: BankAccount |
| `refund_method` | String | enum: cash, card, store_credit, original_method<br>default: "original_method" |
| `image` | String | default: "" |
| `note` | String | default: "" |
| `date` | Date | default: Date.now |
| `reason` | String | default: "" |


---

## `src/models/schema/admin/Revenue.ts`

### Model: `Revenue`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | required: true |
| `amount` | Number | required: true |
| `Category_id` | ObjectId | ref: ExpenseCategory<br>required: true |
| `admin_id` | ObjectId | ref: User<br>required: true |
| `note` | String | - |
| `financial_accountId` | ObjectId | ref: BankAccount<br>required: true |


---

## `src/models/schema/admin/RevenueCategory.ts`

### Model: `RevenueCategory`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true<br>unique |
| `status` | Boolean | default: true |


---

## `src/models/schema/admin/roles.ts`

### Schema: `PermissionActionSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `action` | String | required: true |

### Schema: `RolePermissionSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `module` | String | required: true |
| `actions` | [PermissionActionSchema] | default: [] |

### Schema: `RoleSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `status` | String | enum: active, inactive<br>default: "active" |
| `permissions` | [RolePermissionSchema] | default: [] |


---

## `src/models/schema/admin/selectReason.ts`

### Model: `SelectReason`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `reason` | String | required: true |
| `createdAt` | Date | default: Date.now |


---

## `src/models/schema/admin/ServiceFee.ts`

### Schema: `ServiceFeeSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `title` | String | required: true |
| `amount` | Number | required: true |
| `type` | String | enum: fixed, percentage<br>required: true |
| `module` | String | enum: online, pos<br>required: true |
| `warehouseId` | Schema.Types.ObjectId | ref: Warehouse<br>default: null  |
| `status` | Boolean | default: true  |


---

## `src/models/schema/admin/ShippingSettings.ts`

### Schema: `bostaAddressSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `firstName` | String | default: ""  |
| `lastName` | String | default: ""  |
| `phone` | String | default: ""  |
| `email` | String | default: ""  |
| `city` | String | default: ""  |
| `cityId` | String | default: ""  |
| `zoneId` | String | default: ""  |
| `districtId` | String | default: ""  |
| `firstLine` | String | default: ""  |
| `secondLine` | String | default: ""  |
| `buildingNumber` | String | default: ""  |
| `floor` | String | default: ""  |
| `apartment` | String | default: ""  |
| `businessLocationId` | String | default: ""  |
| `pickupContactName` | String | default: ""  |
| `pickupContactPhone` | String | default: ""  |
| `defaultPickupTimeSlot` | String | default: "10:00"  |
| `defaultPickupTimeSlot.from` | String | default: "10:00"  |
| `defaultPickupTimeSlot.to` | String | default: "14:00"  |

### Schema: `bostaConfigSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `enabled` | Boolean | default: false  |
| `apiKey` | String | default: "" |
| `baseUrl` | String | default: "https://app.bosta.co/api/v2"  |
| `environment` | String | enum: staging, production<br>default: "production" |
| `pickup` | bostaAddressSchema | default: () => ({ |
| `defaults` | String | default: "Parcel"  |
| `defaults.packageType` | String | default: "Parcel"  |
| `defaults.size` | String | default: "MEDIUM"  |
| `defaults.weight` | Number | default: 1 |
| `defaults.itemsCount` | Number | default: 1 |
| `defaults.description` | String | default: "Order"  |
| `codEnabled` | Boolean | default: true  |
| `webhookUrl` | String | default: ""  |
| `webhookSecret` | String | default: ""  |
| `shippingMarkupType` | String | enum: fixed, percentage<br>default: "fixed" |
| `autoCreatePickup` | Boolean | default: true  |
| `pickupLeadDays` | Number | default: 0 |
| `lastTestedAt` | Date | default: null  |
| `lastTestStatus` | String | enum: success, failed, untested<br>default: "untested" |

### Schema: `selfConfigSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `enabled` | Boolean | default: true  |
| `method` | String | enum: zone, flat_rate<br>default: "zone" |
| `flatRate` | Number | default: 0 |

### Model: `ShippingSettings`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `superadminId` | Schema.Types.ObjectId | ref: User<br>required: true<br>unique |
| `activeMethod` | String | enum: self, bosta<br>default: "self" |
| `self` | selfConfigSchema | default: () => ({ |
| `bosta` | bostaConfigSchema | default: () => ({ |
| `freeShippingEnabled` | Boolean | default: false  |
| `singletonKey` | String | default: undefined |


---

## `src/models/schema/admin/Stock.ts`

### Model: `Stock`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `warehouseId` | Schema.Types.ObjectId | ref: Warehouse<br>required: true |
| `reference` | String | unique |
| `type` | String | enum: full, partial<br>required: true |
| `category_id` | Schema.Types.ObjectId | ref: Category |
| `category_id.type` | - | - |
| `category_id.ref` | - | - |
| `brand_id` | Schema.Types.ObjectId | ref: Brand |
| `brand_id.type` | - | - |
| `brand_id.ref` | - | - |
| `initial_file` | String | required: false |
| `final_file` | String | required: false |


---

## `src/models/schema/admin/stockTake.ts`

### Model: `Stocktake`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `code` | String | required: true<br>unique |
| `warehouseId` | ObjectId | ref: Warehouse<br>required: true |
| `type` | String | enum: full, partial<br>required: true |
| `mode` | String | enum: manual, excel<br>required: true |
| `status` | String | enum: processing, completed, cancelled<br>default: "processing" |
| `createdBy` | ObjectId | ref: User<br>required: true |
| `completedAt` | Date | default: null  |


---

## `src/models/schema/admin/stockTakeItem.ts`

### Model: `StocktakeItem`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `stocktakeId` | ObjectId | ref: Stocktake<br>required: true |
| `productId` | ObjectId | ref: Product<br>required: true |
| `productPriceId` | ObjectId | ref: ProductPrice<br>default: null |
| `warehouseId` | ObjectId | ref: Warehouse<br>required: true |
| `productNameSnapshot` | String | default: ""  |
| `skuSnapshot` | String | default: ""  |
| `systemQty` | Number | required: true |
| `actualQty` | Number | default: null  |
| `difference` | Number | default: null  |
| `resolutionType` | String | enum: shortage, surplus, match, null<br>default: null |
| `resolutionStatus` | String | enum: pending, resolved, skipped<br>default: "pending" |
| `resolutionAction` | String | default: null  |
| `resolvedAt` | Date | default: null  |
| `resolvedBy` | ObjectId | ref: User<br>default: null |
| `resolutionReferenceId` | ObjectId | default: null |


---

## `src/models/schema/admin/storeSettings.ts`

### Model: `AppSetting`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `key` | String | default: 'default'<br>unique |
| `templateSlug` | String | required: true |
| `templateSectionsSnapshot` | String | - |
| `templateSectionsSnapshot.type` | - | - |
| `storeName` | String | required: true |
| `logoUrl` | String | - |
| `fontStyle` | String | default: 'default'  |
| `colors` | Map | default: { |
| `colors.type` | - | - |
| `colors.of` | - | - |
| `colors.default` | object | - |
| `sections` | String | default: true <br>required: true |
| `sections.key` | String | required: true |
| `sections.enabled` | Boolean | default: true  |
| `sections.templateSlug` | String | default: "default"  |
| `sections.fontStyle` | String | default: 'default'  |
| `sections._id` | - | - |


---

## `src/models/schema/admin/suppliers.ts`

### Model: `Supplier`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `image` | String | - |
| `username` | String | required: true |
| `email` | String | required: true<br>unique |
| `phone_number` | String | required: true<br>unique |
| `address` | String | - |
| `company_name` | String | - |
| `ar_company_name` | String | - |
| `cityId` | ObjectId | ref: City |
| `countryId` | ObjectId | ref: Country |
| `contact_person` | String | - |
| `registration_date` | Date | default: Date.now  |
| `status` | String | enum: active, inactive<br>default: "active"  |
| `notes` | String | - |
| `notes.type` | - | - |


---

## `src/models/schema/admin/Taxes.ts`

### Model: `Taxes`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true<br>unique |
| `status` | Boolean | default: true  |
| `amount` | Number | required: true |
| `type` | String | enum: percentage, fixed<br>required: true |


---

## `src/models/schema/admin/Transfer.ts`

### Model: `Transfer`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `reference` | String | default: function () {<br>unique |
| `date` | Date | default: Date.now  |
| `fromWarehouseId` | ObjectId | ref: Warehouse<br>required: true |
| `toWarehouseId` | ObjectId | ref: Warehouse<br>required: true |
| `products` | ObjectId | ref: Product<br>default: null<br>required: true |
| `products.productId` | ObjectId | ref: Product<br>required: true |
| `products.productPriceId` | ObjectId | ref: ProductPrice<br>default: null |
| `products.quantity` | Number | required: true |
| `approved_products` | ObjectId | ref: Product<br>default: null<br>required: true |
| `approved_products.productId` | ObjectId | ref: Product<br>required: true |
| `approved_products.productPriceId` | ObjectId | ref: ProductPrice<br>default: null |
| `approved_products.quantity` | Number | required: true |
| `status` | String | enum: pending, received, rejected<br>default: "pending" |
| `reason` | String | default: "" // changed from required: true to default<br>required: true |


---

## `src/models/schema/admin/units.ts`

### Model: `Unit`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `code` | String | required: true<br>unique |
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `base_unit` | ObjectId | ref: Unit<br>default: null // null means this is the same base unit |
| `operator` | String | enum: *, /<br>default: "*" |
| `operator_value` | Number | default: 1 // value used for multiplication or division |
| `is_base_unit` | Boolean | default: false // whether this is a base unit |
| `status` | Boolean | default: true  |


---

## `src/models/schema/admin/User.ts`

### Schema: `PermissionActionSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `action` | String | required: true |

### Schema: `PermissionSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `module` | String | required: true |
| `actions` | [PermissionActionSchema] | default: [] |

### Schema: `UserSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `username` | String | required: true<br>unique |
| `email` | String | required: true<br>unique |
| `password_hash` | String | required: true |
| `company_name` | String | - |
| `phone` | String | - |
| `role` | String | enum: superadmin, admin<br>default: "admin" |
| `status` | String | enum: active, inactive<br>default: "active" |
| `image_url` | String | - |
| `address` | String | - |
| `vat_number` | String | - |
| `state` | String | - |
| `postal_code` | String | - |
| `warehouse_id` | Schema.Types.ObjectId | ref: Warehouse |


---

## `src/models/schema/admin/Variation.ts`

### Schema: `VariationSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |

### Schema: `OptionSchema`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `variationId` | Schema.Types.ObjectId | ref: Variation<br>required: true |
| `name` | String | required: true |
| `status` | Boolean | default: true  |


---

## `src/models/schema/admin/Warehouse.ts`

### Model: `Warehouse`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true |
| `ar_name` | String | required: true |
| `address` | String | required: true |
| `ar_address` | String | required: true |
| `phone` | String | - |
| `email` | String | - |
| `number_of_products` | Number | default: 0  |
| `stock_Quantity` | Number | default: 0  |
| `Is_Online` | Boolean | default: false  |


---

## `src/models/schema/admin/wasted.ts`

### Model: `Wasted`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `_id` | String | default: () => randomUUID()  |
| `productId` | ObjectId | ref: Product<br>required: true |
| `productPriceId` | ObjectId | ref: ProductPrice<br>default: null |
| `warehouseId` | ObjectId | ref: Warehouse<br>required: true |
| `quantity` | Number | required: true |
| `reason` | String | enum: theft, lost, stocktake_not_found, damaged, expired, other<br>required: true |
| `note` | String | - |
| `userId` | ObjectId | ref: User<br>required: true |
| `isApproved` | Boolean | default: false |


---

## `src/models/schema/admin/Zone.ts`

### Model: `Zone`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `name` | String | required: true<br>unique |
| `ar_name` | String | required: true |
| `countryId` | ObjectId | ref: Country<br>required: true |
| `cityId` | ObjectId | ref: City<br>required: true |
| `cost` | Number | - |
| `shipingCost` | Number | default: 0  |


---

## `src/models/schema/users/Address.ts`

### Model: `Address`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `user` | Schema.Types.String | ref: Customer<br>required: true |
| `zone` | Schema.Types.ObjectId | ref: Zone<br>default: null  |
| `country` | Schema.Types.ObjectId | ref: Country<br>default: null  |
| `bostaCityName` | String | default: ""  |
| `bostaZoneId` | String | default: ""  |
| `bostaZoneName` | String | default: ""  |
| `bostaDistrictId` | String | default: ""  |
| `bostaDistrictName` | String | default: ""  |
| `buildingNumber` | String | default: ""  |
| `floorNumber` | String | default: ""  |
| `apartmentNumber` | String | default: ""  |
| `uniqueIdentifier` | String | default: ""  |
| `notes` | String | default: ""  |
| `isDefault` | Boolean | default: false  |


---

## `src/models/schema/users/Cart.ts`

### Model: `Cart`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `user` | Schema.Types.String | ref: Customer<br>required: false<br>unique |
| `sessionId` | String | required: false<br>unique |
| `cartItems` | Schema.Types.ObjectId | ref: Product<br>default: 1<br>required: true |
| `cartItems.product` | Schema.Types.ObjectId | ref: Product<br>required: true |
| `cartItems.quantity` | Number | default: 1<br>required: true |
| `cartItems.price` | Number | required: true |
| `cartItems.variant` | Schema.Types.ObjectId | ref: ProductPrice<br>required: false |
| `totalCartPrice` | Number | default: 0<br>required: true |
| `coupon` | Schema.Types.ObjectId | ref: Coupon<br>required: false |
| `couponDiscount` | Number | default: 0 |
| `serviceFee` | Number | default: 0 |
| `taxAmount` | Number | default: 0 |
| `totalPriceAfterDiscount` | Number | default: 0 |


---

## `src/models/schema/users/Order.ts`

### Model: `Orders`

| Field | Type | ref / enum / default / required |
|---|---|---|
| `reference` | String | default: function () {<br>unique |
| `customerPhone` | String | default: ""  |
| `user` | Schema.Types.String | ref: Customer<br>required: false |
| `orderType` | String | default: "delivery"<br>required: true |
| `warehouse` | Schema.Types.ObjectId | ref: Warehouse<br>required: false |
| `cartItems` | Schema.Types.ObjectId | ref: Product<br>required: false |
| `cartItems.product` | Schema.Types.ObjectId | ref: Product |
| `cartItems.quantity` | - | - |
| `cartItems.price` | - | - |
| `cartItems.variant` | Schema.Types.ObjectId | ref: ProductPrice<br>required: false |
| `shippingAddress` | String | default: ""  |
| `shippingAddress.details` | String | - |
| `shippingAddress.city` | String | - |
| `shippingAddress.zone` | String | - |
| `shippingAddress.street` | String | - |
| `shippingAddress.apartmentNumber` | String | - |
| `shippingAddress.floorNumber` | String | - |
| `shippingAddress.buildingNumber` | String | - |
| `shippingAddress.uniqueIdentifier` | String | - |
| `shippingAddress.bostaCityName` | String | default: ""  |
| `shippingAddress.bostaZoneId` | String | default: ""  |
| `shippingAddress.bostaZoneName` | String | default: ""  |
| `shippingAddress.bostaDistrictId` | String | default: ""  |
| `shippingAddress.bostaDistrictName` | String | default: ""  |
| `totalOrderPrice` | Number | required: true |
| `coupon` | Schema.Types.ObjectId | ref: Coupon<br>required: false |
| `couponDiscount` | Number | default: 0  |
| `serviceFee` | Number | default: 0  |
| `taxAmount` | Number | default: 0  |
| `totalPriceAfterDiscount` | Number | default: 0  |
| `paymentMethod` | Schema.Types.ObjectId | ref: PaymentMethod<br>required: true |
| `paymentGateway` | String | enum: manual, paymob, geidea, fawry<br>default: "manual" |
| `paymentStatus` | String | enum: unpaid, pending, paid, failed<br>default: "unpaid" |
| `paymobOrderId` | String | - |
| `paymobTransactionId` | String | - |
| `paymobIframeUrl` | String | - |
| `paymobCallbackPayload` | Schema.Types.Mixed | - |
| `geideaSessionId` | String | - |
| `geideaTransactionId` | String | - |
| `geideaCallbackPayload` | Schema.Types.Mixed | - |
| `proofImage` | String | - |
| `status` | String | default: "pending" |
| `statusDescription` | String | default: "Your Order is Placed Successfully" |
| `statusHistory` | String | ref: User<br>enum: system, admin, delivery_man, bosta, customer<br>default: "" <br>required: true |
| `statusHistory.status` | String | required: true |
| `statusHistory.description` | String | default: ""  |
| `statusHistory.source` | String | enum: system, admin, delivery_man, bosta, customer<br>default: "system" |
| `statusHistory.updatedBy` | Schema.Types.ObjectId | ref: User<br>default: null |
| `statusHistory.updatedAt` | Date | default: Date.now  |
| `shippingMethod` | String | enum: self, bosta, null<br>default: null |
| `shipmentType` | String | enum: self, bosta, null<br>default: null |
| `selfShipment` | Schema.Types.ObjectId | ref: DeliveryMan<br>enum:  unassigned, assigned, picked_up, out_for_delivery, delivered, failed, returned, <br>default: null |
| `selfShipment.deliveryManId` | Schema.Types.ObjectId | ref: DeliveryMan<br>default: null |
| `selfShipment.warehouseId` | Schema.Types.ObjectId | ref: Warehouse<br>default: null |
| `selfShipment.pickupPointId` | Schema.Types.ObjectId | ref: PickupPoint<br>default: null |
| `selfShipment.pickupPointName` | String | default: "" |
| `selfShipment.status` | String | enum:  unassigned, assigned, picked_up, out_for_delivery, delivered, failed, returned, <br>default: "unassigned" |
| `selfShipment.assignedAt` | Date | default: null  |
| `selfShipment.assignedBy` | Schema.Types.ObjectId | ref: User<br>default: null |
| `selfShipment.assignmentType` | String | enum: auto, manual, null<br>default: null |
| `selfShipment.pickedUpAt` | Date | default: null  |
| `selfShipment.outForDeliveryAt` | Date | default: null  |
| `selfShipment.deliveredAt` | Date | default: null  |
| `selfShipment.failedAt` | Date | default: null  |
| `selfShipment.returnedAt` | Date | default: null  |
| `selfShipment.failureReason` | String | default: ""  |
| `selfShipment.deliveryNotes` | String | default: ""  |
| `bostaShipment` | Schema.Types.ObjectId | ref: BostaShipment<br>default: null |


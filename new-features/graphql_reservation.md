# GraphQL Reservation Detail

Get a reservation by id and choose the fields you want. Only what you select is fetched.

<div class="doc-grid cols-2">
  <div class="doc-card">
    <div class="doc-card-label">⚡ Select only what you need</div>
    <div class="doc-card-value">Ask for <code>code</code>, run 1 query</div>
  </div>
  <div class="doc-card">
    <div class="doc-card-label">🧩 One request</div>
    <div class="doc-card-value">Reservation + payments + files together</div>
  </div>
</div>

## Endpoint

<div class="api-card">
  <div class="endpoint-bar">
    <span class="method post">POST</span>
    <code>/graphql</code>
  </div>
  <p class="api-desc">Runs a query. Use the same <code>Authorization: Bearer</code> token as REST. Open <code>GET /graphql</code> in a browser for the playground.</p>
</div>

!> Field names are `snake_case`, same as REST.

## Quick start

<div class="doc-steps">
  <div class="doc-step">
    <span class="doc-step-num">1</span>
    <div class="doc-step-body">
      <div class="doc-step-title">Pick the fields</div>
      <div class="doc-step-desc">Write a query with <code>reservation(id)</code> and the fields you need.</div>
    </div>
  </div>
  <div class="doc-step">
    <span class="doc-step-num">2</span>
    <div class="doc-step-body">
      <div class="doc-step-title">Send it</div>
      <div class="doc-step-desc">POST <code>{"query": "..."}</code> to <code>/graphql</code>.</div>
    </div>
  </div>
  <div class="doc-step">
    <span class="doc-step-num">3</span>
    <div class="doc-step-body">
      <div class="doc-step-title">Read <code>data</code></div>
      <div class="doc-step-desc">The response has exactly the shape of your query.</div>
    </div>
  </div>
</div>

## Examples

<details class="scalar-detail" open>
<summary>Basic fields</summary>

```graphql
query {
  reservation(id: "01K58GMS0W9MB0PQ4Y19EAEBMZ") {
    code
    status
    unit_code
  }
}
```

```json
{ "data": { "reservation": { "code": "string", "status": "string", "unit_code": "string" } } }
```

</details>

<details class="scalar-detail">
<summary>With payments and extension histories</summary>

```graphql
query {
  reservation(id: "01K58GMS0W9MB0PQ4Y19EAEBMZ") {
    code
    payments
    extension_histories
  }
}
```

</details>

<details class="scalar-detail">
<summary>Full detail screen</summary>

```graphql
query {
  reservation(id: "01K58GMS0W9MB0PQ4Y19EAEBMZ") {
    id code status unit_code customer_name sales_amount paid_amount
    reservation_customers
    reservation_contacts
    active_unit_prices
    reservation_ioms
    reservation_barters
    attachments
    virtual_accounts
    is_outstanding_fine
    payments
    extension_histories
    file_histories
    confirmation_progress
    existing_customers
  }
}
```

</details>

<details class="scalar-detail">
<summary>Call from JavaScript</summary>

```js
const res = await fetch(`${API_PREFIX}/graphql`, {
  method: "POST",
  headers: { "Content-Type": "application/json", Authorization: `Bearer ${token}` },
  body: JSON.stringify({
    query: `query ($id: String!) { reservation(id: $id) { code payments } }`,
    variables: { id: reservationId },
  }),
});
const { data, errors } = await res.json();
```

</details>

## Fields

### Basic fields

Always cheap. Selecting many costs the same as one.

`id` `code` `status` `status_alih_hak` `payment_method` `note` `internal_note` `unit_id` `unit_code` `unit_description` `company_code` `company_name` `project_code` `project_name` `sub_project_code` `sub_project_name` `group_description` `sales_person_name` `sales_person_type` `sales_person_manager_name` `customer_code` `customer_name` `ownership_name` `lead_names` `va_suffix` `sales_amount` `dpp_amount` `ppn_amount` `commitment_fee` `transaction_amount` `paid_amount` `promo_gift_amount` `confirm_1_date` `confirm_2_date` `cancel_date` `reopen_date` `bast_date` `created_at` `created_by` `updated_at` `updated_by`

<details class="scalar-detail">
<summary>All basic fields</summary>

`opportunity_id` `payment_method_secondary` `is_HOP` `is_VVIP` `is_LPTD` `source_product_info_id` `source_product_info_description` `other_source_product_info` `sales_person_id` `barter_type` `is_sales_group` `is_developer_load` `is_cancel_input` `cancel_input_note` `is_dividend_sales` `is_ppn_incentive` `in_review_date` `in_review_expiry_date` `waiting_for_paid_date` `waiting_for_paid_expiry_date` `confirm_1_expiry_date` `is_deleted` `is_anjak` `commitment_fee_amount` `sales_marketing_amount` `sales_marketing_number_of_payments` `customer_id` `company_id` `profit_center` `is_in_review_extension` `ppjb_generate_date` `project_id` `sub_project_id` `is_initial_confirm_2` `group_id` `bast_datetime` `is_kpr_lunas` `is_kpr_secondary_lunas`

</details>

### Related data

Each one runs its own query, **only when selected**.

| Field | Returns | REST equivalent |
| :--- | :--- | :--- |
| `payments` | Payments | `?include=payments` |
| `extension_histories` | Extension histories | `?include=extension_histories` |
| `file_histories` | File histories | `?include=file_histories` |
| `confirmation_progress` | Latest confirmation progress | `?include=confirmation_progress` |
| `existing_customers` | Customers sharing a lead's `business_id` | `?include=existing_customers` |
| `reservation_customers` | Customers with lead data | in `GET /reservation/{id}` |
| `reservation_contacts` | Contact persons | in `GET /reservation/{id}` |
| `active_unit_prices` | Prices, details, gifts, programs | in `GET /reservation/{id}` |
| `reservation_ioms` | IOMs | in `GET /reservation/{id}` |
| `reservation_barters` | Barters | in `GET /reservation/{id}` |
| `attachments` | Document attachments | in `GET /reservation/{id}` |
| `virtual_accounts` | Virtual accounts | in `GET /reservation/{id}` |
| `is_outstanding_fine` | `true` / `false` | in `GET /reservation/{id}` |

!> These return raw JSON. Select the name only, like `payments`. Do not write `payments { amount }`.

## Errors

GraphQL answers HTTP `200` even on query errors. Check `errors` in the body.

```json
{
  "data": { "reservation": null },
  "errors": [{ "message": "Reservation with id ... not found", "path": ["reservation"] }]
}
```

| Case | Result |
| :--- | :--- |
| <span class="badge yellow">Unknown id</span> | `errors` has the message, `reservation` is `null` |
| <span class="badge yellow">Unknown field</span> | `errors` has a validation message |
| <span class="badge red">Invalid token</span> | Rejected before the query runs |

## REST or GraphQL

| Need | Use |
| :--- | :--- |
| A few fields or a few relations | <span class="badge blue">GraphQL</span> |
| Everything the detail returns | <span class="badge gray">Either</span> speed is about equal |
| Typed contract for nested data | <span class="badge gray">REST</span> |

?> Selected relations run one after another, so the cost is the sum of what you select. Read only, no mutation.

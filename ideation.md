## Understanding of the Product: Calyb AI
---
From the Calyb website, we can see that the requirement to make use of this software is the SDK of the client software, and a product access (assuming this is the same as the sandbox environment that is provided with Vendure). This application adds an AI layer which is added on top of available Saas products to automate the working.

**Observations from the website**
1. The context is to be derived from the SDK and the product access.
2. We first ask the user his role, tool to use, purpose, etc. 
3. There is like a chat window where we type the "User Intent".
4. That is split up into different sub steps, like Deepseek "Thinking".
5. AI Agents, Decision assistance, tours, walkthroughs, or nudges - are methods to guide the user

## Current Methodology
---
1. Using the available GraphQL Schema, we can infer all the API operations that can be done from the backend.
2. When doing network analysis, we can find out what button is being clicked and what API endpoint it targets.

## **Track:** The Promotion Challenge
**Request:** Create a Black Friday coupon code 'BF2024'. It should apply a 15% discount, but only if the order total is above $100 AND the customer is in the 'VIP' group.

### Manual Workflow with API Calls:

**STEP 1:** Click the “Promotions” Tab:

- From the Network Tab analysis, we can see that clicking the "Promotions" button in the UI makes three api calls namely, userSavedViews, globalSavedViews and PromotionList.

- From the API Documentation, for the request GraphQL query, we have the input of "PromotionListOptions" that is taken in. As this is a read operation, there is no output other than viewing the UI section for the list of promotions. 

**Request Payloads:**
```q
{"query":"query GetSettingsStoreValue($key: String!) 
{\n getSettingsStoreValue(key: $key)\n}","variables":{"key":"vendure.dashboard.userSavedViews"}}
```
```q
{"query":"query GetSettingsStoreValue($key: String!) 
{\n getSettingsStoreValue(key: $key)\n}","variables":{"key":"vendure.dashboard.globalSavedViews"}}
```
```q
{"query":"query PromotionList($options: PromotionListOptions) 
{\n promotions(options: $options) {\n items {\n id\n name\n enabled\n couponCode\n startsAt\n endsAt\n }\n totalItems\n }\n}",
"variables":{"options":{"take":10,"skip":0,"sort":{},"filter":{"name":{"contains":""},"couponCode":{"contains":""}},"filterOperator":"OR"}}}
```
**Response Payload:**
```q
{
    "data": {
        "promotions": {
            "items": [
                {
                    "id": "1",
                    "name": "New Year Discount 70%",
                    "enabled": false,
                    "couponCode": "",
                    "startsAt": "2025-12-31T17:00:00.000Z",
                    "endsAt": "2026-02-27T17:00:00.000Z"
                },
                {
                    "id": "2",
                    "name": "Black Friday Coupon Code (15%)",
                    "enabled": false,
                    "couponCode": "BF2024",
                    "startsAt": null,
                    "endsAt": null
                }
            ],
            "totalItems": 2
        }
    }
}
```

**STEP 2:** Click the “+ New Promotion” Button:

- From the Network Tab analysis, we can see that clicking the “+ New Promotion” button in the UI makes two api calls namely, GetPromotionConditions, and GetPromotionActions.

- From the API Documentation, for the request GraphQL query, we have the input of "PromotionListOptions" that is taken in. As this is a read operation, there is no output other than viewing the UI section for the list of promotions.

**Request Payloads:**
```q
{"query":"query GetPromotionConditions 
{\n  promotionConditions {\n    ...ConfigurableOperationDef\n  }\n}\n\n
fragment ConfigurableOperationDef on ConfigurableOperationDefinition {\n  args {\n    ...ConfigArgDefinition\n  }\n  code\n  description\n}\n\n
fragment ConfigArgDefinition on ConfigArgDefinition {\n  name\n  type\n  required\n  defaultValue\n  list\n  ui\n  label\n  description\n}"}
```
```q
{"query":"query GetPromotionActions 
{\n  promotionActions {\n    ...ConfigurableOperationDef\n  }\n}\n\n
fragment ConfigurableOperationDef on ConfigurableOperationDefinition {\n  args {\n    ...ConfigArgDefinition\n  }\n  code\n  description\n}\n\n
fragment ConfigArgDefinition on ConfigArgDefinition {\n  name\n  type\n  required\n  defaultValue\n  list\n  ui\n  label\n  description\n}"}
```

**STEP 3:** Click the “Create” Button:

- From the Network Tab analysis, we can see that clicking the “Create” button in the UI makes two api calls namely, CreatePromotion Mutation, and PromotionDetail.

- From the API Documentation, for the request GraphQL query, we have the input of "PromotionListOptions" that is taken in. As this is a read operation, there is no output other than viewing the UI section for the list of promotions.

**Request Payloads:**
```q
{"query":"mutation CreatePromotion($input: CreatePromotionInput!) {\n createPromotion(input: $input) 
{\n __typename\n ... on Promotion {\n id\n }\n ... on ErrorResult {\n errorCode\n message\n }\n }\n}",
"variables":{"input":{"enabled":false,"startsAt":"2026-01-13T18:30:00.000Z","endsAt":"2026-01-28T18:30:00.000Z",
"couponCode":"BF2024","perCustomerUsageLimit":1,"usageLimit":1,
"conditions":[{"code":"minimum_order_amount","arguments":[{"name":"amount","value":"10000"},{"name":"taxInclusive","value":"true"}]}],
"actions":[{"code":"order_percentage_discount","arguments":[{"name":"discount","value":"15"}]}],"translations":[{"languageCode":"en","name":"Black Friday Coupon Code (15%)","description":"","customFields":{}}],"customFields":{}}}}
```
```q
{"query":"query PromotionDetail($id: ID!) {\n promotion(id: $id) 
{\n id\n createdAt\n updatedAt\n name\n description\n enabled\n couponCode\n perCustomerUsageLimit\n usageLimit\n startsAt\n endsAt\n 
conditions {\n ...ConfigurableOperation\n }\n actions {\n ...ConfigurableOperation\n }\n
translations {\n id\n languageCode\n name\n description\n }\n customFields\n }\n}\n\n
fragment ConfigurableOperation on ConfigurableOperation {\n args {\n name\n value\n }\n code\n}","variables":{"id":"2"}}
```
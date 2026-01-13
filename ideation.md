# Understanding of the Product: Calyb AI
---
From the Calyb website, we can see that the requirement to make use of this software is the SDK of the client software, and a product access (assuming this is the same as the sandbox environment that is provided with Vendure). This application adds an AI layer which is added on top of available Saas products to automate the working.

**Observations from the website**
1. The context is to be derived from the SDK and the product access.
2. We first ask the user his role, tool to use, purpose, etc. 
3. There is like a chat window where we type the "User Intent".
4. That is split up into different sub steps, like Deepseek "Thinking".
5. AI Agents, Decision assistance, tours, walkthroughs, or nudges - are methods to guide the user

**Current Methodology**

0. For the input question passed by the user, the "User Intent" we can use a **Query Translation** method to split up the main query into doable sub-queries (more like how we have Thinking in DeepSeek enabling Chain of Thought prompting), and execute one by one.
1. Using the available GraphQL Schema, we can infer all the API operations that can be done from the backend.
2. When doing network analysis, we can find out what button is being clicked and what API endpoint it targets.
3. With the combination of the API Docs (Which can be considered as a Data Store, like how we use in a RAG application), the list of operations we can do with GraphQL Queries, and Network Tab Analysis we can form the "Context" that can be passed to our Agent/LLM .

# **Track:** The Promotion Challenge
---
**Request:** Create a Black Friday coupon code 'BF2024'. It should apply a 15% discount, but only if the order total is above $100 AND the customer is in the 'VIP' group.

### **Manual Workflow with API Calls:**

**STEP 1:** Click the **“Promotions”** Tab:

- From the Network Tab analysis, we can see that clicking the **"Promotions"** button in the UI makes three api calls namely, **userSavedViews, globalSavedViews and PromotionList**. 

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

**STEP 2:** Click the **“+ New Promotion”** Button:

- From the Network Tab analysis, we can see that clicking the **“+ New Promotion”** button in the UI makes two api calls namely, **GetPromotionConditions, and GetPromotionActions**.

**Request Payloads:**
```q
{"query":"query GetPromotionConditions {\n  promotionConditions {\n    ...ConfigurableOperationDef\n  }\n}\n\n
fragment ConfigurableOperationDef on ConfigurableOperationDefinition {\n  args {\n    ...ConfigArgDefinition\n  }\n  code\n  description\n}\n\n
fragment ConfigArgDefinition on ConfigArgDefinition {\n  name\n  type\n  required\n  defaultValue\n  list\n  ui\n  label\n  description\n}"}
```
```q
{"query":"query GetPromotionActions {\n  promotionActions {\n    ...ConfigurableOperationDef\n  }\n}\n\n
fragment ConfigurableOperationDef on ConfigurableOperationDefinition {\n  args {\n    ...ConfigArgDefinition\n  }\n  code\n  description\n}\n\n
fragment ConfigArgDefinition on ConfigArgDefinition {\n  name\n  type\n  required\n  defaultValue\n  list\n  ui\n  label\n  description\n}"}
```

**STEP 3:** Click the **“Create”** Button:

- From the Network Tab analysis, we can see that clicking the **“Create”** button in the UI makes two api calls namely, **CreatePromotion Mutation, and PromotionDetail**.

**Request Payloads:**
```q
{"query":"mutation CreatePromotion($input: CreatePromotionInput!) {\n  createPromotion(input: $input) 
{\n    __typename\n    ... on Promotion {\n      id\n    }\n    ... on ErrorResult {\n      errorCode\n      message\n    }\n  }\n}",
"variables":{"input":{"enabled":true,"couponCode":"BF2024","perCustomerUsageLimit":1,"usageLimit":1,"conditions":[{"code":"minimum_order_amount","arguments":[{"name":"amount","value":"10000"},{"name":"taxInclusive","value":"false"}]},{"code":"customer_group","arguments":[{"name":"customerGroupId","value":"1"}]}],"actions":[{"code":"order_percentage_discount","arguments":[{"name":"discount","value":"15"}]}],"translations":[{"languageCode":"en","name":"Black Friday Coupon Code (15%)","description":"","customFields":{}}],"customFields":{}}}}
```
```q
{"query":"query PromotionDetail($id: ID!) {\n promotion(id: $id) 
{\n id\n createdAt\n updatedAt\n name\n description\n enabled\n couponCode\n perCustomerUsageLimit\n usageLimit\n startsAt\n endsAt\n 
conditions {\n ...ConfigurableOperation\n }\n actions {\n ...ConfigurableOperation\n }\n
translations {\n id\n languageCode\n name\n description\n }\n customFields\n }\n}\n\n
fragment ConfigurableOperation on ConfigurableOperation {\n args {\n name\n value\n }\n code\n}","variables":{"id":"2"}}
```

# **Track:** The Global Challenge
---
**Request:** Set up a new shipping zone for 'Oceania'. Add 'Australia' and 'New Zealand' to this zone. Then, configure a 'Flat Rate' shipping method of $15 that only applies to this zone.

### **Manual Workflow with API Calls**:

**STEP 1:** Click **"Zones"** in the Admin Settings Tab

- From the Network Tab analysis, we can see that clicking the **Zones** button in the UI makes an api calls named, **ZoneList Query with takes the input as ZoneListOptions**.

**Request Payloads:**
```q
{"query":"query ZoneList($options: ZoneListOptions) {\n  zones(options: $options) {\n    items {\n      ...ZoneItem\n    }\n    totalItems\n  }\n}\n\n
fragment ZoneItem on Zone {\n  id\n  name\n}","variables":{"options":{"take":10,"skip":0,"sort":{},"filter":{}}}}
```

**STEP 2:** Click **"Create"** in the Zones Tab

- From the Network Tab analysis, we can see that clicking the Create button in the UI makes an api calls named, **CreateZone Mutation with takes the input as CreateZoneInput**.

**Request Payloads:**
```q
{"query":"mutation CreateZone($input: CreateZoneInput!) {\n  createZone(input: $input) 
{\n    id\n  }\n}","variables":{"input":{"name":"Oceania Newest","memberIds":[],"customFields":{}}}}
```

**STEP 3:** Automatic Loading after Creating a New Zone

- From the Network Tab analysis, we can see that automatically once we create a new zone it makes two api calls named, **CountryList Query, and Zone Members Query**.

**STEP 4:** Click **"AU and AZ"** in the Countries List

- From the Network Tab analysis, we can see that clicking **AU and NZ** Seperately in the UI makes an api call named, **CreateZone Mutation with takes the input as CreateZoneInput**.

**Request Payloads:**
```q
{"query":"mutation AddMembersToZone($zoneId: ID!, $memberIds: [ID!]!) 
{\n  addMembersToZone(zoneId: $zoneId, memberIds: $memberIds) {\n    id\n  }\n}","variables":{"zoneId":"8","memberIds":["13"]}}
```
```q
{"query":"mutation AddMembersToZone($zoneId: ID!, $memberIds: [ID!]!) 
{\n  addMembersToZone(zoneId: $zoneId, memberIds: $memberIds) {\n    id\n  }\n}","variables":{"zoneId":"8","memberIds":["159"]}}
```

**STEP 5:** Click **"Shipping Methods"** in the Admin Settings Tab

- From the Network Tab analysis, we can see that clicking **"Shipping Methods"** in the Admin Settings Ta an api call named, **ShippingMethodList Query**.

**Request Payloads:**
```q
{"query":"query ShippingMethodList($options: ShippingMethodListOptions) 
{\n  shippingMethods(options: $options) {\n    items {\n      ...ShippingMethodItem\n    }\n    totalItems\n  }\n}\n\nfragment ShippingMethodItem on ShippingMethod {\n  id\n  name\n  code\n  fulfillmentHandlerCode\n}",
"variables":{"options":{"take":10,"skip":0,"sort":{},"filter":{"name":{"contains":""}}}}}
```

**STEP 6:** Click **"+ New Shipping Method and Create"** in the Shipping Methods Tab

- From the Network Tab analysis, we can see that clicking **"+ New Shipping Method and Create"** in the Shipping Methods Tab makes an api call named, **CreateShippingMethod Mutation**.

**Request Payloads:**
```q
{"query":"mutation CreateShippingMethod($input: CreateShippingMethodInput!) 
{\n  createShippingMethod(input: $input) {\n    id\n  }\n}",
"variables":{"input":{"code":"FR150","fulfillmentHandler":"manual-fulfillment","checker":{"code":"default-shipping-eligibility-checker","arguments":[{"name":"orderMinimum","value":"1000"}]},
"calculator":{"code":"default-shipping-calculator","arguments":[{"name":"rate","value":"1500"},{"name":"includesTax","value":"auto"},{"name":"taxRate","value":"0"}]},
"translations":[{"languageCode":"en","name":"Flat Rate","description":"","customFields":{}}],"customFields":{}}}}
```
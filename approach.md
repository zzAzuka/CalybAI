# Core Approach

- The method I used to map the UI labels to the API fields is Network Tab Analysis, where I manually run through the steps and found out each of the GraphQL query that was connected with it. 

- The inputs, types and shapes of all the data structures and naming conventions were checked and understood
against the Vendure GraphQL API Docs.

## Approach for Track: Promotion Challenge

**Ambuguity Faced:** The task asks us to add the 15% Black Friday to all customer that belong to the "VIP" customer group, but as it was not present by default, my workflow also contains
the UI Events + API Calls made in order to create the Customer group and then create the Promotions.

1. **UI Action: Clicking "Customer Group" In the dashboard**
    - Hidden API Event: CustomerGroupList Query, userSavedViews Query and globalSavedViews Query
    - Dependency: Nothing, UI Loading
2. **UI Action: Clicking "+ Create New Customer Group" In the UI**
    - Hidden API Event: CreateCustomerGroup Mutation
    - Dependency: returns createCustomerGroup.id
3. **UI Action: Automatically open the Created Customer Group to add Customers** 
    - Hidden API Event: CustomerGroup Query
    - Dependency: uses createCustomerGroup.id from Step 2 and opens
4. **UI Action: Automatically shows the list of customers in that Customer Group** 
    - Hidden API Event: CustomerGroupMemberList Query
    - Dependency: uses createCustomerGroup.id from Step 2
5. **UI Action: Choosing and Adding Customers to this Customer Group**
    - Hidden API Event: AddCustomerToGroup Mutation
    - Dependency: uses customerId (which is from when the customer was added) and groupID (which is internally counted for the number of groups)
6. **UI Action: Clicking "Promotions" In the dashboard**
    - Hidden API Event: PromotionList Query
    - Dependency: Nothing, UI Loading
7. **UI Action: Clicking "Promotions" In the dashboard**
    - Hidden API Event: PromotionList Query
    - Dependency: Returns Typename and ID in case of success or Typename, Error Code or Message in case of failure
8. **UI Action: Clicking "+ New Promotion" In the dashboard**
    - Hidden API Event: GetPromotionActions Query, Get PromotionConditions Query
    - Dependency: returns Metadata in the type of ConfigurableOperationDef
9. **UI Action: Clicking "Create" In the New Promotions Tab**
    - Hidden API Event: CreatePromotion Mutation
    - Dependency: returns typename and createPromotion.id
10. **UI Action: Shows the created Promotion "BF2024"**
    - Hidden API Event: PromotionDetail Query
    - Dependency: needs the createPromotion.id from Step 9

---

## Approach for Track: Global Challenge

**Ambuguity Faced:** The task asks us to add a condition in the Flat Rate Shipping Method where we had to 
only give that charge for a specific zone. As the UI elements, did not have the option to add a new
Shiiping Eligibility Criteria, I found from the documentation where they had made a "defaultShippingEligibilityCriteria" and I made some edits so that it followed the same format and it will
show up in the UI if we edit that in the back-end.

**Code to add an Oceanic Zone Eligibility Checker**
```q
import { LanguageCode } from '@vendure/common/lib/generated-types';

import { ShippingEligibilityChecker } from './shipping-eligibility-checker';

export const oceaniaShippingEligibilityChecker = new ShippingEligibilityChecker({
    code: 'oceania-shipping-eligibility-checker',
    description: [{ languageCode: LanguageCode.en, value: 'Australia and New Zealand Shipping Eligibility Checker' }],
    args: {
        orderZone: {
            type: 'ID',
            defaultValue: '',
            ui: { component: 'zone-list-input' },
            label: [{ languageCode: LanguageCode.en, value: 'Eligible Zone' }],
            description: [
                {
                    languageCode: LanguageCode.en,
                    value: 'Order is eligible only if it is part of the Oceania Zone',
                },
            ],
        },
    },
    check: (ctx, order, args) => {
        //The Logic to check and compare the Zone
    },
});
```

1. **UI Action: Clicking "Zones" In the Admin Settings Tab**
    - Hidden API Event: ZoneList Query
    - Dependency: Nothing, UI Loading
2. **UI Action: Clicking "Create New Zone" In the Zones Section**
    - Hidden API Event: CreateZone Mutation
    - Dependency: returns createZone.id
3. **UI Action: Automatically Loading after Zone Creation** 
    - Hidden API Event: CountryList Query, Zone Members Query
    - Dependency: 
4. **UI Action: Clicking "AU and NZ" in the Countries List** 
    - Hidden API Event: CreateZone Mutation
    - Dependency: returns ID nad uses the zoneID created in Step 2 and membersID for the country
5. **UI Action: Click "Shipping Methods" in the Admin Settings Tab**
    - Hidden API Event: ShippingMethodList Query
    - Dependency: Nothing, UI Shows the list of Shipping Methods
6. **UI Action: Click "+ New Shipping Method and Create" in the Shipping Methods Tab**
    - Hidden API Event: CreateShippingMethod Mutation
    - Dependency: returns an ID, Takes variable data from the User Data in the UI
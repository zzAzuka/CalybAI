## Track: Promotion Challenge

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
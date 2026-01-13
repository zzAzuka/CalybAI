## Findings in Track: The Promotion Challenge

The input for createPromotionInput is of the form,
```q
input CreatePromotionInput {
enabled: Boolean!
startsAt: DateTime
endsAt: DateTime
couponCode: String
perCustomerUsageLimit: Int
usageLimit: Int
conditions: [ConfigurableOperationInput!]!
actions: [ConfigurableOperationInput!]!
translations: [PromotionTranslationInput!]!
customFields: JSON
}
```

Contrasting this with the UI we can see that,
    - Conditions and Actions are in the form of ConfigurableOperationInput with the arguments of {code: String, arguments: [name: String, value: String]}
    - A created promotion is of the Type "Promotion" which has id, createdAt, updatedAt, startsAt, endsAt: DateTime, couponCode, perCustomerUsageLimit, usageLimit, name, description, enabled, conditions, actions, translations, customFields.
    - When we gave the condition to be as $100, the conditions key-value pair looked like **"conditions":[{"code":"minimum_order_amount","arguments":[{"name":"amount","value":"10000"}]}]**, where we can see that the value is 10000 which shows it takes the amount in cents.
    - For all the customer group created (VIP in this case), the payload "arguments":[{"name":"customerGrouptId}] is showing that all the customer groups are being named as "CustomerGroupdID".
    - When adiing a user into the VIP Customer Group, the name is not being used as reference rather, the groupID which is assigned when we created the group is used to assign it to an user.

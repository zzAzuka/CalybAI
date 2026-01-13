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
}```

Contrasting this with the UI we can see that,
    - Conditions and Actions are in the form of ConfigurableOperationInput with the arguments of {code: String, arguments: [name: String, value: String]}
    - A created promotion is of the Type "Promotion" which has id, createdAt, updatedAt, startsAt, endsAt: DateTime, couponCode, perCustomerUsageLimit, usageLimit, name, description, enabled, conditions, actions, translations, customFields.
    - When we gave the condition to be as $100, the conditions key-value pair looked lik **"conditions":[{"code":"minimum_order_amount","arguments":[{"name":"amount","value":"10000"}]}]**, where we can see that the value is 10000 which shows it takes the amount in cents.

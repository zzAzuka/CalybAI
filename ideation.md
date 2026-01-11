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
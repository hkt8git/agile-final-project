---
name: User Story
about: This template is for creating user stories
title: 'Need the ability to retrieve a product from the catalog'
labels: 'enhancement'
assignees: 'anniesama'

---

**As a** [Product owner]  
 **I need** [function]  
 **So that** [benefit]  
 
Example 
As a Costumer
I need to retrieve a product from the catalog by its identifier
So that I can view accurate product details before deciding to buy

   
 ### Details and Assumptions
 **Retrieval supports lookup by productId (and optionally by SKU)

If the product does not exist (or is deleted), return “not found”
   
 ### Acceptance Criteria  
   
 ```gherkin
Given That a product exists in the catalog with example productId "P123" 
When I request the product by productId "P123"
Then I receive the product details for "P123"
 ```

# Apollo Overview

Apollo Structure:

```bash
/data-access/ #<------- Data Access Project
- /graphql/ #<--------- Azure Function root directory
  - /data-sources/ #<-- Data API code here
    - /cosmosdb/ #<---- Cosmos Specific API code 
  - /init/ #<---------- Function Initialization code  (db startup etc)
  - /resolvers/ #<----- Apollo GraphQL code
    - /core/ #<-------- Base GraphQL Schema files: Scalars etc
    - /mutations/ #<--- Mutation graphql definitions and related code
      - /inputs/ #<---- Input graphql definitions for mutations
     
    - /queries/
      - /inputs/
     
    - /types/
- /shared/
  ... other shared stuff
  - /data-sources/
    - /cosmosdb/
      - /models/
-... other functions


/ui/
  /src/
    /components/ 
      /atom
      /molecule
      /component
         comonent-name.tsx
         component-name.graphql
      /page
        
```

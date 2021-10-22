# Directory Structure





The data-access project will have a number of new directories :

```bash
/fullstack.code-workspace

..
/data-access/
- /graphql/ #graph ql code 
  - /resolvers
    - /functions  #common functionality used with queries and mutations 
    - /mutations  #graphQL mutiation implementations
    - /queries    #graphQL query     implementations 
  - /typedefs     #graphQl type definitions  
  - /queries.ts   #imports all queries   into a single file
  - /mutations.ts #imports all mutations into a single file
  - /typedefs.ts  #importa all typefefs  into a single file 
- /shared
  - /data-sources
   - /cosmos-db
     - /functions  #mongoDb complex queries
     - /models     #mongoDb models / typescript types  
- /... #other necessary files and folders

```


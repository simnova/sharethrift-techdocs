# Configure Azure BLOB Storage

## Allow CORS to your site - Necessary for Feature Flags & PDF Files

Setting up the CORS permissions allows your web application running from one domain access another domain. Without allowing CORS, your website will not be able to get the documents stored in BLOB.

To provide permissions:

1. Navigate to [http://portal.azure.com/](http://portal.azure.com/)
2. Go to your BLOB Storage Container
3. In the left panel: Go to Settings -> CORS&#x20;
4. Provide the following data for Blob Service:
   1. Allowed Origins: The website which needs access to BLOB Storage
   2. Allowed Methods: The request types allowed to your Allowed Origin
   3. Allowed Headers: Headers allowed to your Allowed Origin
5. Repeat step 4 for all websites that need access

| Allowed Origins | Allowed Methods             | Allowed Headers | Exposed Headers | Max age |
| --------------- | --------------------------- | --------------- | --------------- | ------- |
| localhost:3000  | POST, GET, DELETE, PUT,HEAD | x-ms-blob-type  |                 |         |


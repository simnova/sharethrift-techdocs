# Website Integration

Use the following code in the website to access feature flags:

```typescript
import axios from "axios";

const api = axios.create({
    adapter: cache.adapter,
});

const options = {
    url: //Blob URL goes here
};

await api(options).then((response) => {
    return response.data;
}).catch((error) => {
    //Handle error
});
```

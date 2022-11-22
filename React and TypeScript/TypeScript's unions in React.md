---
repository: https://github.com/stevekinney/inspirational-quotes
branch: unions
---

Check this out:

```ts
const [requestStatus, setrequestStatus] = useState<
  'loading' | 'error' | 'success'
>('loading');

const getPosts = () => {
  setrequestStatus('loading');
  fetchPosts(count)
    .then(setQuotes)
    .then(() => {
      setrequestStatus('success');
    })
    .catch(() => setrequestStatus('error'));
};
```

![](_attachments/Pasted%20image%2020221115063052.png)
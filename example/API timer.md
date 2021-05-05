You can await anything
Last but not least, await can be used for both synchronous and asynchronous expressions. For example, you can write await 5, which is equivalent to Promise.resolve(5). This might not seem very useful at first, but it's actually a great advantage when writing a library or a utility function where you don't know whether the input will be sync or async.

Imagine you want to record the time taken to execute some API calls in your application, and you decide to create a generic function for this purpose. Here's how it would look with promises
```javascript
const recordTime = async (makeRequest) => {
  const timeStart = Date.now();
  await makeRequest(); // works for any sync or async function
  const timeEnd = Date.now();
  console.log('time take:', timeEnd - timeStart);
}
```
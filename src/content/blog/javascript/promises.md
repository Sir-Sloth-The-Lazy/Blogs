---
title: "Promises & Async/Await"
description: "Master asynchronous JavaScript — from Promises to async/await, error handling, and concurrency patterns."
date: 2025-02-10
subject: "JavaScript"
order: 2
---

## The Asynchronous Problem

JavaScript is single-threaded. It runs one operation at a time on a single call stack. Yet modern applications need to perform network requests, read files, and respond to user interactions without freezing the UI.

The solution lies in **asynchronous programming** — a paradigm where operations are initiated and their results are handled later, without blocking the main thread.

## Understanding Promises

A `Promise` is an object that represents a value which may not be available yet. It exists in one of three states:

- **Pending** — the operation is still in progress
- **Fulfilled** — the operation completed successfully
- **Rejected** — the operation failed

```js
const fetchUser = new Promise((resolve, reject) => {
  setTimeout(() => {
    const user = { id: 1, name: 'Ada Lovelace' };
    resolve(user);
  }, 1000);
});

fetchUser
  .then(user => console.log(user.name))
  .catch(err => console.error(err));
```

### Chaining Promises

The real power of Promises lies in chaining. Each `.then()` returns a new Promise, enabling sequential async operations:

```js
fetch('/api/user/1')
  .then(res => res.json())
  .then(user => fetch(`/api/posts?author=${user.id}`))
  .then(res => res.json())
  .then(posts => console.log(posts))
  .catch(err => console.error('Failed:', err));
```

## Async/Await

Introduced in ES2017, `async/await` provides syntactic sugar over Promises. It makes asynchronous code read like synchronous code:

```js
async function getUserPosts(userId) {
  const userRes = await fetch(`/api/user/${userId}`);
  const user = await userRes.json();

  const postsRes = await fetch(`/api/posts?author=${user.id}`);
  const posts = await postsRes.json();

  return posts;
}
```

### Error Handling

Use `try/catch` blocks for error handling with async/await:

```js
async function loadData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  } catch (error) {
    console.error('Failed to load data:', error);
    return null;
  }
}
```

## Concurrency Patterns

### Promise.all

Execute multiple promises concurrently and wait for all to complete:

```js
const [users, posts, comments] = await Promise.all([
  fetch('/api/users').then(r => r.json()),
  fetch('/api/posts').then(r => r.json()),
  fetch('/api/comments').then(r => r.json()),
]);
```

### Promise.allSettled

Wait for all promises to settle, regardless of success or failure:

```js
const results = await Promise.allSettled([
  fetch('/api/critical'),
  fetch('/api/optional'),
]);

results.forEach(result => {
  if (result.status === 'fulfilled') {
    console.log('Success:', result.value);
  } else {
    console.log('Failed:', result.reason);
  }
});
```

### Promise.race

Returns the first promise to settle:

```js
const result = await Promise.race([
  fetch('/api/data'),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Timeout')), 5000)
  ),
]);
```

## Summary

Asynchronous programming is at the heart of JavaScript. Promises provide a robust foundation, while async/await makes the code clean and readable. Understanding concurrency patterns like `Promise.all` and `Promise.allSettled` is essential for writing performant applications.

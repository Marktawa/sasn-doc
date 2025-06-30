## Manual install of Vue

## Data structures to iterate on

### Array

- 1D Array
```js
const names = ["Gary", "Solomon"]
```

### Objects

- 1D Object
```js
const names = { "Gary", "Solomon" }
```

- 1D Object with key value pairs
```js
const names = 
{ 
    "first": "Gary", 
    "second": "Solomon" 
}
```

- 2D Object with key value pairs
```js
const names =
{
    "first": {
        "id": 1,
        "name": "Gary"
    },
    "second": {
        "id": 2,
        "name": "Solomon"
    }
}
```

- Object containing array of objects
```js
{
    "data": [
        {
            "id": 1,
            "name": "Gary"
        },
        {
            "id": 2,
            "name": "Solomon"
        }
    ]
}
```

## For loop in Vue

### Array

- Add the following to `app.vue`
```vue
<script setup>
const names = ["Gary", "Solomon"]
</script>

<template>
    <h1>Names from API</h1>
    <ul>
        <li v-for="name in names">
            {{ name }}
        </li>
    </ul>
</template>
```
- Run server
- Screenshot of list of names

### 1D Object

- Add the following to `app.vue`
```vue
<script setup>
const names = 
{ 
    "first": "Gary", 
    "second": "Solomon" 
}
</script>

<template>
    <h1>Names from API</h1>
    <ul>
        <li v-for="name in names">
            {{ name }}
        </li>
    </ul>
</template>
```
- Run server
- Screenshot of list of names

### Nested Object

- Add the following to `app.vue`
```vue
<script setup>
const persons =
{
    "first": {
        "id": 1,
        "name": "Gary"
    },
    "second": {
        "id": 2,
        "name": "Solomon"
    }
}
</script>

<template>
    <h1>Names from API</h1>
    <ul>
        <li v-for="person in persons">
            {{ person.name }}
        </li>
    </ul>
</template>
```
- Run server
- Screenshot of list of names

### Object containing array of objects

- Add the following to `app.vue`
```vue
<script setup>
const persons =
{
    "data": [
        {
            "id": 1,
            "name": "Gary"
        },
        {
            "id": 2,
            "name": "Solomon"
        }
    ]
}
</script>

<template>
    <h1>Names from API</h1>
    <ul>
        <li v-for="person in persons.data">
            {{ person.name }}
        </li>
    </ul>
</template>
```
- Run server
- Screenshot of list of names

## Forms in Vue

- Update `app.vue`
```vue
<template>
    <h1>Add Name</h1>
    <form>
        <input type="text" name="name" />
        <button type="submit">Submit</button>
    </form>
</template>
```

### Log to console form input
- Update `app.vue`
```vue
<script setup>
const names = ["Gary", "Solomon"]
</script>

<template>
    <h1>Add Name</h1>
    <form>
        <input type="text" name="name" />
        <button type="submit">Submit</button>
    </form>
</template>
```
- Run server
- Check browser console

### Update array using form input

- Update `app.vue`
```vue
<script setup>
const names = ["Gary", "Solomon"]
</script>

<template>
    <h1>Add Name</h1>
    <form>
        <input type="text" name="name" />
        <button type="submit">Submit</button>
    </form>
</template>
```
- Run server
- Screenshot of list of names



# task-list

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).
=======
# Test

## Simple task management app using Vuejs

1. Create a new git repository and copy this file as Readme.MD and commit

2. This is resourse you can use:

- Mockup: https://www.figma.com/proto/teZLon9a7Tp7ot89MgPEK8/Tasking?node-id=2%3A2&scaling=min-zoom
- Graphql endpoint: https://tasking-ltl.herokuapp.com/v1/graphql

(You can use this webapp to test the graphql endpoint: https://www.graphqlbin.com/v2/new)

3. Complete your code and push to the repository


### Sample request

#### Query all tasks
```
{
  task {
    id
    title
    is_completed
  }
}
```

#### Update task
```
mutation {
  update_task(
    where: {
      id: {
        _eq: 1
      }
    },
  	_set: {
      is_completed: true
    }
  ){
    affected_rows
  }
}
```

#### Add task
```
mutation {
  insert_task_one(object: {
    title: "test6"
  }
  ){
    id
  }
}
```

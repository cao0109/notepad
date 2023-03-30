# mongodb 在 nodejs 中的应用

## 1. 安装

```bash
npm install mongodb --save
```

## 2. 连接数据库

```js
const MongoClient = require('mongodb').MongoClient;
const url = 'mongodb://localhost:27017';
const dbName = 'myproject';

MongoClient.connect(url, function(err, client) {
  console.log("Connected successfully to server");

  const db = client.db(dbName);

  client.close();
});
```

```ts
import { MongoClient } from 'mongodb';

const url = 'mongodb://localhost:27017';
const dbName = 'myproject';

MongoClient.connect(url, function(err, client) {
  console.log("Connected successfully to server");

  const db = client.db(dbName);

  client.close();
});
```

## 定义entity

```ts
import {Schema, Document} from 'mongoose';

export interface User extends Document {
    
    // String
    name: string;
    // Number
    age: number;
    // Date
    birthday: Date;
    // Boolean
    isMale: boolean;
    // Array
    hobbies: string[];
    // Object
    address: {
        province: string;
        city: string;
    };
    // Mixed (混合类型)
    other: any;
    // ObjectId
    // ref: 'User'
    job: Schema.Types.ObjectId;
    // Array of ObjectId
    // ref: 'User'
    friends: Schema.Types.ObjectId[];
    // Array of Mixed
    // ref: 'User'
    otherFriends: any[];
}

```

## 批量更新插入

```ts
const User = require('./user.model');

const users = [
    { uid: '123', username: 'Alice' },
    { uid: '456', username: 'Bob' },
    { uid: '789', username: 'Charlie' }
];

const updates = users.map(user => ({
    updateOne: {
        filter: { uid: user.uid },
        update: { username: user.username },
        upsert: true
    }
}));

User.bulkWrite(updates)
    .then(result => {
        console.log(result);
    })
    .catch(error => {
        console.error(error);
    });

```

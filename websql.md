# websql

通过前端创建 sqlite 数据库实现前端数据的本地化存储

- 原生 websql 基础操作代码

  ```javascript
  function executeSql(sql, params, onResolved, onRejected) {
    db.transaction((tx) => {
      tx.executeSql(sql, params, onResolved, onRejected);
    });
  }
  ```

  websql api 是异步函数, 想要在获取数据后做处理需要使用大量回调来控制函数的顺序执行, 如下

  ```javascript
  function foo(param, dataHandler, errorHandler) {
    db.executeSql(
      "sql_01",
      param,
      (res1) =>
        db.executeSql(
          "sql_02",
          res1,
          (res2) => db.executeSql("sql_03", res2, dataHandler, errorHandler),
          errorHandler
        ),
      errorHandler
    );
  }
  ```

- websql 基础操作封装代码

  ```javascript
  function executeSql(sql, params) {
    return new Promise((resolve, reject) => {
      db.transaction((tx) => {
        tx.executeSql(
          sql,
          params,
          (tx, value) => resolve(value),
          (tx, reason) => reject(reason)
        );
      });
    });
  }
  ```

  通过 Promise 封装基本的 sql 执行, 返回一个 Promise , 使得数据库操作类似以下代码

  ```javascript
  function foo(param, dataHandler, errorHandler) {
    db.executeSql("sql_01", param)
      .then((res1) => db.executeSql("sql_02", res1))
      .then((res2) => db.executeSql("sql_03", res2))
      .then(dataHandler)
      .catch(errorHandler);
  }
  ```

- 使用 Promise 封装后同样可以使用 `async` , `await` 优化链式操作

  ```javascript
  async function foo(param, dataHandler, errorHandler) {
    try {
      const res1 = await db.executeSql("sql_01", param);
      const res2 = await db.executeSql("sql_02", res2);
      const res3 = await db.executeSql("sql_03", res3);
      dataHandler(res3);
    } catch (error) {
      errorHandler(error);
    }
  }
  ```

# A Node.js simple implementation of xxl job executor

> fork from https://github.com/Aouchinx/xxl-job-executor-nodejs.git

### add dependency


```json
{
  "dependencies": {
    "@dalongrong/xxl-job-executor": "^0.0.1"
  }
}
```

### usage

1. job handler

```javascript
/**
 * job handler demo
 * @param {any} logger
 * @param {Object} params
 * @param {Object} context
 * @return {Promise<any>}
 */
const demoJobHandler = async (logger, jobParams, context) => {
    logger.debug('params: %o, context: %o', params, context)
    const sleep = async (millis) => new Promise((resolve) => setTimeout(resolve, millis))
    for (let i = 1; i < 10; i++) {
      await sleep(1000)
      logger.debug(`${i}s passed`)
    }
  }
```

2. envionment variables

> 可以使用dotenv管理

```dotenv
XXL_JOB_EXECUTOR_KEY=executor-example-express
XXL_JOB_SCHEDULE_CENTER_URL=http://127.0.0.1:8080/xxl-job-admin
XXL_JOB_ACCESS_TOKEN=<accessToken>
# 任务执行日志存储路径
XXL_JOB_JOB_LOG_PATH=logs/job
# 执行器运行日志开关(非任务执行日志)，未设置的话默认关闭
XXL_JOB_DEBUG_LOG=true
```

3. apply executor

```javascript
const XxlJobExecutor = require('@dalongrong/xxl-job-executor')
const jobHandlers = new Map([ [ 'demoJobHandler', demoJobHandler ] ])
const context = { /*anything*/ }
const xxlJobExecutor = new XxlJobExecutor(jobHandlers, context)
// 注意注册的appDomain中使用的ip地址,可选的使用address包中的ip 获取工具
await xxlJobExecutor.applyMiddleware({ app, appType, appDomain, path })
```

具体用法参考 examples 提供的`express` 和 `koa` 的集成示例

4. remove executor

> 应该监听进程状态信息 

```javascript

process.on('SIGINT', async () => {
    console.log('************ The process SIGINT completely. ************')
    await xxlJobExecutor.close()
    process.exit()

})

process.on('SIGHUP', async () => {
    console.log('************ The process SIGHUP completely. ************')
    await xxlJobExecutor.close()
    process.exit()

})

process.on('SIGTERM', async () => {
    console.log('************ The process SIGTERM completely. ************')
    await xxlJobExecutor.close()
    process.exit()
})
```
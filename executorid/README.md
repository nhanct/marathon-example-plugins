# ExecutorID example plugin

Currently marathon generates a unique ExecutorID for all tasks thus starting a new executor process for every task (1-1 relationship). This is completely sufficient for the 99% of all use cases but sometimes a user might want to reuse the same executor instance for multiple tasks (1-n relationship). This is achieved by setting the same `TaskInfo.ExecutorInfo.ExecutorID` for all the tasks that should share an executor. This plugin when given an application with a custom executor and a label with a key `MARATHON_EXECUTOR_ID` and a value of the executor Id will use it to override the `TaskInfo.ExecutorInfo.ExecutorID`.

**Note:** all application sharing the same executor Id will share the same executor instance allowing to save resources. The downfall is that all the tasks started with the same executor id must have identical `TaskInfo.ExecutorInfo`. Among other things that means environment variables must be identical. Since marathon would automatically generate per-task environment variables like `MARATHON_APP_VERSION`, `MESOS_TASK_ID` or `PORTx` this will not work. For this reason this plugin removes all the environment variables. It is possible to be more selective and remove only those environment variables that change from task to task but that's too much hustle for this simple plugin.

## Usage
A simple app definition setting the executor Id `custom-executor` looks like:
```
{
      "id": "sleep",
      "cmd": "sleep 232323",
      "cpus": 0.01,
      "mem": 32,
      "disk": 0,
      "instances": 1,
      "executor": "/path/to/custom/executor-binary",
      "labels": {
        "MARATHON_EXECUTOR_ID": "custom-executor"
      }
    }
```
See the [plugin configuration file](https://github.com/mesosphere/marathon-example-plugins/blob/master/executorid/src/main/resources/mesosphere/marathon/example/plugin/executorid/plugin-conf.json)

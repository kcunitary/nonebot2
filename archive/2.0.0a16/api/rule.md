---
contentSidebar: true
sidebarDepth: 0
---

# NoneBot.rule 模块

## 规则

每个事件响应器 `Matcher` 拥有一个匹配规则 `Rule` ，其中是 **异步** `RuleChecker` 的集合，只有当所有 `RuleChecker` 检查结果为 `True` 时继续运行。

:::tip 提示
`RuleChecker` 既可以是 async function 也可以是 sync function，但在最终会被 `nonebot.utils.run_sync` 转换为 async function
:::


## _class_ `Rule`

基类：`object`


* **说明**

    `Matcher` 规则类，当事件传递时，在 `Matcher` 运行前进行检查。



* **示例**


```python
Rule(async_function) & sync_function
# 等价于
from nonebot.utils import run_sync
Rule(async_function, run_sync(sync_function))
```


### `__init__(*checkers)`


* **参数**

    
    * `*checkers: Callable[[Bot, Event, T_State], Awaitable[bool]]`: **异步** RuleChecker



### `checkers`


* **说明**

    存储 `RuleChecker`



* **类型**

    
    * `Set[Callable[[Bot, Event, T_State], Awaitable[bool]]]`



### _async_ `__call__(bot, event, state)`


* **说明**

    检查是否符合所有规则



* **参数**

    
    * `bot: Bot`: Bot 对象


    * `event: Event`: Event 对象


    * `state: T_State`: 当前 State



* **返回**

    
    * `bool`



## `startswith(msg, ignorecase=False)`


* **说明**

    匹配消息开头



* **参数**

    
    * `msg: str`: 消息开头字符串



## `endswith(msg, ignorecase=False)`


* **说明**

    匹配消息结尾



* **参数**

    
    * `msg: str`: 消息结尾字符串



## `keyword(*keywords)`


* **说明**

    匹配消息关键词



* **参数**

    
    * `*keywords: str`: 关键词



## `command(*cmds)`


* **说明**

    命令形式匹配，根据配置里提供的 `command_start`, `command_sep` 判断消息是否为命令。

    可以通过 `state["_prefix"]["command"]` 获取匹配成功的命令（例：`("test",)`），通过 `state["_prefix"]["raw_command"]` 获取匹配成功的原始命令文本（例：`"/test"`）。



* **参数**

    
    * `*cmds: Union[str, Tuple[str, ...]]`: 命令内容



* **示例**

    使用默认 `command_start`, `command_sep` 配置

    命令 `("test",)` 可以匹配：`/test` 开头的消息
    命令 `("test", "sub")` 可以匹配”`/test.sub` 开头的消息


:::tip 提示
命令内容与后续消息间无需空格！
:::


## _class_ `ArgumentParser`

基类：`argparse.ArgumentParser`


* **说明**

    `shell_like` 命令参数解析器，解析出错时不会退出程序。



## `shell_command(*cmds, parser=None)`


* **说明**

    支持 `shell_like` 解析参数的命令形式匹配，根据配置里提供的 `command_start`, `command_sep` 判断消息是否为命令。

    可以通过 `state["_prefix"]["command"]` 获取匹配成功的命令（例：`("test",)`），通过 `state["_prefix"]["raw_command"]` 获取匹配成功的原始命令文本（例：`"/test"`）。

    可以通过 `state["argv"]` 获取用户输入的原始参数列表

    添加 `parser` 参数后, 可以自动处理消息并将结果保存在 `state["args"]` 中。



* **参数**

    
    * `*cmds: Union[str, Tuple[str, ...]]`: 命令内容


    * `parser: Optional[ArgumentParser]`: `nonebot.rule.ArgumentParser` 对象



* **示例**

    使用默认 `command_start`, `command_sep` 配置，更多示例参考 `argparse` 标准库文档。


```python
from nonebot.rule import ArgumentParser

parser = ArgumentParser()
parser.add_argument("-a", action="store_true")

rule = shell_command("ls", parser=parser)
```

:::tip 提示
命令内容与后续消息间无需空格！
:::


## `regex(regex, flags=0)`


* **说明**

    根据正则表达式进行匹配。

    可以通过 `state["_matched"]` `state["_matched_groups"]` `state["_matched_dict"]`
    获取正则表达式匹配成功的文本。



* **参数**

    
    * `regex: str`: 正则表达式


    * `flags: Union[int, re.RegexFlag]`: 正则标志


:::tip 提示
正则表达式匹配使用 search 而非 match，如需从头匹配请使用 `r"^xxx"` 来确保匹配开头
:::


## `to_me()`


* **说明**

    通过 `event.is_tome()` 判断事件是否与机器人有关



* **参数**

    
    * 无

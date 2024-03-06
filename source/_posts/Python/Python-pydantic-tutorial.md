---
title: Pydantic & Typing —— Python 类型注解深入 
date: 2023-09-24 00:41:23
updated: 2023-09-24 00:41:23
categories: 
tags: 
description: 
thumbnail: 
banner_img:
---

# Pydantic & Typing —— Python 类型注解库深入

众所周知，Python 是一门弱类型的语言，在定义变量时不用标注其类型，类型间也可以隐形的转换。这导致了开发时候 IDE 提示不智能，容易出现 BUG 等问题。

JavaScript 也是一门弱类型语言，但衍生出了他的超集 Typescript 拥有更加完善的注释系统。

而 Python3 开始可以显性的添加 function hint 类似：`def cat(age: int, name: str) -> str` 等简单的类型，但稍微复杂的类型就需要用到 `typing` 等模块。

而 `Pydantic` 就解决了这个痛点，可以定义一个复杂的类型，集成了 **数据验证，IDE 智能提示、Json 支持** ，等功能，提高开发体验，减少 BUG。

**目前 `Pydantic` 已从 V1 升级成 V2 相关代码请修改并适配**

## Reference

Pydantic Official Document：[Welcome to Pydantic - Pydantic](https://docs.pydantic.dev/latest/) [Models - Pydantic](https://docs.pydantic.dev/latest/usage/models/)

Python `Typing` Official Document：[typing — Support for type hints — Python 3.11.5 documentation](https://docs.python.org/3/library/typing.html)

## Vscode Support Pydantic

使用 VS Code 的 **Pylance** 扩展。它是推荐的下一代 Python 官方 VS Code 插件。

- Open the "User Settings"  
    打开“用户设置”
- Search for `Type Checking Mode`  
	搜索 `Type Checking Mode`
- You will find an option under `Python › Analysis: Type Checking Mode`  
    您将在 `Python › Analysis: Type Checking Mode` 下找到一个选项
- Set it to `basic` or `strict` (by default it's `off`)  
    将其设置为 `basic` 或 `strict` （默认情况下为 `off` ）

## Callable 回调函数签名注释

`Callable[[int], str]` 表示采用 `int` 类型的单个参数并返回 `str` 的函数，例如可以注释以下函数。

```python
def callback(num: int) -> str: ...
```

示例：

```python
# Awaitable: 可等待对象
from collections.abc import Callable, Awaitable

def feeder(get_next_item: Callable[[], str]) -> None:
    …  # Body

def async_query(on_success: Callable[[int], None],
                on_error: Callable[[int, Exception], None]) -> None:
    …  # Body

async def on_update(value: str) -> None:
    …  # Body

callback: Callable[[str], Awaitable[None]] = on_update
```

## Generics `/dʒəˈner.ɪk/` (adj.一般的) 泛型支持

> 泛型是一种编程语言特性，允许在编写代码时使用 **一些通用的类型**，而不是特定的类型。它可以让代码更加通用、可重用和安全。  
> 比如 `def func(parameter:T) -> T` T 就是一个泛型，根据传入的参数类型来决定传出的参数类型，而不指定传入是某个类型。

Python 是一种动态类型语言，它没有像 Java 或 C# 那样的严格类型限制。因此，Python 中的泛型并不像 Java 或 C# 中那样显式地定义类型参数。但是，Python 中可以使用 **泛型编程技术** 来实现通用、可重用的代码。

`TypeVar()` 参考：[typing — Support for type hints — Python 3.11.5 documentation](https://docs.python.org/3/library/typing.html#typing.TypeVar)

```python
from typing import TypeVar, List


T = TypeVar('T')  # 定义泛型类型变量 T T 可以是 Any 任何类型
S = TypeVar('S', bound=str)  # 可以是 str 的任何子类型
A = TypeVar('A', str, bytes)  # 必须是 str 或者 bytes 相当于 Union[str, bytes]

def repeat(x: T, n: int) -> List[T]:
    return [x] * n

s = repeat('hello', 3)  # s 的类型为 List[str]
n = repeat(42, 4)  # n 的类型为 List[int]
```

### User-defined Generics types 用户定义的泛型

`TypeVar()` 只支持 bound 绑定一个标准类型，而 `Generic` 支持绑定用户自定义的类型。

```python
from typing import TypeVar, Generic
from logging import Logger

T = TypeVar('T')

# 用户定义的类可以定义为泛型类。
class LoggedVar(Generic[T]):
    def __init__(self, value: T, name: str, logger: Logger) -> None:
        self.name = name
        self.logger = logger
        self.value = value

    def set(self, new: T) -> None:
        self.log('Set ' + repr(self.value))
        self.value = new

    def get(self) -> T:
        self.log('Get ' + repr(self.value))
        return self.value

    def log(self, message: str) -> None:
        self.logger.info('%s: %s', self.name, message)
```

## Annotating `/ˈæn.ə.teɪt/` (v. 给..注解) tuples 注释元组

在 Python 中，类型系统都假定容器的数据类型一样，如 `List[str]` ，但 `Mapping` 可以支持多种类型的容器。

`Mapping` 只接受两个类型参数：**第一个表示键的类型**，**第二个表示值的类型**。

`Mapping` 类似于 `Dict` 字典

```python
from collections.abc import Mapping

x: list[int] = []

# Type checker error: ``list`` only accepts a single type argument:
y: list[int, str] = [1, 'foo']

# Type checker will infer that all keys in ``z`` are meant to be strings,
# and that all values in ``z`` are meant to be either strings or ints
z: Mapping[str, str | int] = {
	"name":"cherry",
	"age":int
}
```

`tuple` 类型天生支持多个参数：

```python
# OK: ``x`` is assigned to a tuple of length 1 where the sole element is an int
x: tuple[int] = (5,)

# OK: ``y`` is assigned to a tuple of length 2;
# element 1 is an int, element 2 is a str
y: tuple[int, str] = (5, "foo")

# OK: 省略号表示接收N个 int 参数
z: tuple[int, ...] = (1, 2, 3)
```

## Order Typing Type 其他注释类型

### Union 联合

`Union[X, Y]` 相当于 `X | Y` ，表示 X 或 Y。

版本 Python 3.10 中更改：联合现在可以写为 `X | Y`

```python
from typing import Union

num: Union[str, int] = "1212" # OK!
num: Union[str, int] = 1212 # OK!

# in >= Python3.10
num: str | int = "1212" # 使用 | 表示或运算

```

### Optional 可选

`Optional[X]` 相当于 `X | None` （或 `Union[X, None]` ）。

```python
def foo(arg: Optional[int] = None) -> None:
```

### Annotated `/'ænə,teitid/` 注释

这个在 FastAPI 中添加 router 注释用的多。用于将 metadata 元数据添加到一个类型中的 `__metadata__` 属性。

```python
from typing import Annotated
# 接收可变参数，支持定义多个元数据
>>> X = Annotated[int, "very", "important", "metadata"]
>>> X
typing.Annotated[int, 'very', 'important', 'metadata']
>>> X.__metadata__
('very', 'important', 'metadata')


# @dataclass 指明这个类是一个储存数据的类
@dataclass
class ctype:
    kind: str


@dataclass
class ValueRange:
    lo: int
    hi: int

Annotated[int, ValueRange(3, 10), ctype("char")]
```

## Sequence[英 /ˈsiːkwəns/ 序列], Iterable (可迭代) & Iterator (迭代器)

```python
# Sequence
from typing import Sequence
from pydantic import BaseModel
class Model(BaseModel):
    sequence_of_ints: Sequence[int] = None
print(Model(sequence_of_ints=[1, 2, 3, 4]).sequence_of_ints)
#> [1, 2, 3, 4]
print(Model(sequence_of_ints=(1, 2, 3, 4)).sequence_of_ints)
#> (1, 2, 3, 4)
```

### Infinite[英 /ˈɪnfɪnət/ 无限] Generators 无限生成器

```python
from typing import Iterable # 可迭代对象
from pydantic import BaseModel

class Model(BaseModel):
    infinite: Iterable[int]


def infinite_ints():
	# 无限生成器 Infinite Generators
    i = 0
    while True:
        yield i
        i += 1


# 在初始验证期间， `Iterable` 字段仅执行简单的检查以确保所提供的参数是可迭代的。为了防止它被消耗，不会急切地对生成的值进行验证。
m = Model(infinite=infinite_ints())
print(m)
"""
infinite=ValidatorIterator(index=0, schema=Some(Int(IntValidator { strict: false })))
"""

for i in m.infinite:
    print(i)
    #> 0
    #> 1
    #> 2
    #> 3
    #> 4
    #> 5
    #> 6
    #> 7
    #> 8
    #> 9
    #> 10
    if i == 10:
        break

```

## Pydantic BaseModel

```python
from pydantic import BaseModel
class UserModel(BaseModel): 
	id: int 
	name: str
```

### Validator 验证器

Field validators 字段验证器：

如果您想将验证器附加到模型的特定字段，可以使用 `@field_validator` 装饰器。

- 第一个参数值是 `UserModel` 类，而不是 `UserModel` 的实例。所以在 `@field_validator` 装饰器下方使用 `@classmethod` 装饰器来进行正确的类型检查。
- 第二个参数是要验证的字段值；可以随意命名。
- 第三个参数（如果存在）是 `pydantic.FieldValidationInfo` 的实例，用于记录验证的信息。
- 验证器应该返回解析的值或引发 `ValueError` 或 `AssertionError`，也可以无脑 `assert`

```python
from pydantic import (
    BaseModel,
    FieldValidationInfo, # 字段验证的信息
    ValidationError,
    field_validator,
)


class UserModel(BaseModel):
    id: int
    name: str

	# 验证名字
    @field_validator('name')
    @classmethod
    def name_must_contain_space(cls, v: str) -> str:
        if ' ' not in v:
            raise ValueError('must contain a space')
        return v.title()

    # you can select multiple fields, or use '*' to select all fields
    @field_validator('id', 'name')
    @classmethod
    def check_alphanumeric(cls, v: str, info: FieldValidationInfo) -> str:
        if isinstance(v, str):
            # info.field_name is the name of the field being validated
            is_alphanumeric = v.replace(' ', '').isalnum()
            assert is_alphanumeric, f'{info.field_name} must be alphanumeric'
        return v

```

model_validator 模型验证器，针对整个模型进行验证，并提供几个 Hook 钩子，可用来预处理部分数据。务必 return 出处理完的整个数据。

```python
from typing import Any

from pydantic import BaseModel, ValidationError, model_validator


class UserModel(BaseModel):
    username: str
    password1: str
    password2: str

	# 在进入模型之前处理
    @model_validator(mode='before')
    @classmethod
    def check_card_number_omitted(cls, data: Any) -> Any:
        if isinstance(data, dict):
            assert (
                'card_number' not in data
            ), 'card_number should not be included'
        return data

	# 在进入模型处理之后运行，只在模型成功验证后处理。
    @model_validator(mode='after')
    def check_passwords_match(self) -> 'UserModel':
        pw1 = self.password1
        pw2 = self.password2
        if pw1 is not None and pw2 is not None and pw1 != pw2:
            raise ValueError('passwords do not match')
        return self
```

### Field [英 /fi:ld/ 字段]

`Field` 函数用于自定义元数据并将其添加到模型的字段中。用于改变 Pydantic 的行为。

```python
from pydantic import BaseModel, Field


class User(BaseModel):
	# 提供默认值
    name: str = Field(default='John Doe')
	# 提供 factory function 生成默认数据
	id: int = Field(default_factory=lambda: uuid4().hex)
	# alias 别名
	...
	# constrain 约束 gt/lt/ge 限制大小
	...
	# 
```

### 序列化 Serialization [英 /ˈsɪəri:əˌlaɪz/]

```python
m.model_dump() # 导出 Python 类型
m.model_dump_json() # 导出 Json 类型
```

## Read Environment value IN BaseSettings 使用 Pydantic 管理环境变量

配置一个 Python 程序的最佳实践不是编写复杂的解析逻辑来解析 ini yaml 等配置文件，而是直接在环境变量读取。使用 `pydantic-settings` 可以很方便的从环境变量里读取配置信息。

- 创建一个定义明确、类型提示的应用程序配置类
- 自动从环境变量中读取对配置的修改

install：安装

```python
pip install pydantic-settings
```

示例：

```python
from typing import Any, Callable, Set

from pydantic import (
    AliasChoices,
    AmqpDsn,
    BaseModel,
    Field,
    ImportString,
    PostgresDsn,
    RedisDsn,
)

from pydantic_settings import BaseSettings, SettingsConfigDict


class SubModel(BaseModel):
    foo: str = 'bar'
    apple: int = 1


class Settings(BaseSettings):
	# 配置 alias 实际读取 `my_auth_key` 环境变量
    auth_key: str = Field(validation_alias='my_auth_key')  

    api_key: str = Field(alias='my_api_key')  

    redis_dsn: RedisDsn = Field(
        'redis://user:pass@localhost:6379/1',
        validation_alias=AliasChoices('service_redis_dsn', 'redis_url'),  (3)
    )
    pg_dsn: PostgresDsn = 'postgres://user:pass@localhost:5432/foobar'
    amqp_dsn: AmqpDsn = 'amqp://user:pass@localhost:5672/'

    special_function: ImportString[Callable[[Any], Any]] = 'math.cos'  (4)

    # to override domains:
    # export my_prefix_domains='["foo.com", "bar.com"]'
    domains: Set[str] = set()

    # to override more_settings:
    # export my_prefix_more_settings='{"foo": "x", "apple": 1}'
    more_settings: SubModel = SubModel()

	# model 配置，为所有环境变量设置 prefix [英 /'priːfɪks/]
    model_config = SettingsConfigDict(env_prefix='my_prefix_')  (5)


print(Settings().model_dump())
"""
{
    'auth_key': 'xxx',
    'api_key': 'xxx',
    'redis_dsn': Url('redis://user:pass@localhost:6379/1'),
    'pg_dsn': MultiHostUrl('postgres://user:pass@localhost:5432/foobar'),
    'amqp_dsn': Url('amqp://user:pass@localhost:5672/'),
    'special_function': math.cos,
    'domains': set(),
    'more_settings': {'foo': 'bar', 'apple': 1},
}
"""
```

### 通过 .env 文件加载

环境变量的优先级＞.env 文件

.env file:

```text
# ignore comment
ENVIRONMENT="production"
REDIS_ADDRESS=localhost:6379
MEANING_OF_LIFE=42
MY_VAR='Hello world'

```

在 model_config 处加载：

```python
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file='.env', env_file_encoding='utf-8')

```

实例化对象加载：

```python
settings = Settings(_env_file='prod.env', _env_file_encoding='utf-8')
```

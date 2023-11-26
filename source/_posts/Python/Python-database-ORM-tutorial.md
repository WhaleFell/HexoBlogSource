---
title: Python database 数据库 ORM 框架
date: 2023-09-09 08:12:64
updated: 2023-09-09 08:12:64
categories: Python
tags:
  - databases
  - python
  - ORM
  - mysql
description: 
thumbnail: 
banner_img:
---

# Python database 数据库 ORM 框架

**ORM** (**O**object **R**elational **M**apper) 对象关系映射，用于在 Code 中通过 Object 来操作**关系型数据库**，将用户定义的 Python 类映射到数据库表和其他构造 ，而不用直接手撕 SQL 语句。增加安全性和可维护性。

SQLAlchemy （SQL 炼金术 `/ˈæl.kə.mi/` ）是 Python 中用于操作各种 **关系型数据库** 的 **ORM**

[SQLAlchemy 2.0 教程 - Yifei's Notes](https://yifei.me/note/2652)

[SQLAlchemy Offical Tutorial](https://docs.sqlalchemy.org/en/20/intro.html)

![](http://pan.whaleluo.top/api/raw/?path=/picstorage/blog/img/Python-database-ORM-tutorial-components.png)

SQLAlchemy 包含 对象关系映射器 **ORM** 和 核心 **Core** 两部分 API，ORM 构建于 Core 之上，如上图。**DBAPI** 代表底层数据库的驱动，例如：`pymysql` `sqlite3` 与特定数据库交互的第三方驱动程序。

## Install

```shell
pip install SQLAlchemy
```

## Establish Connectivity 建立链接

`Engine` 对象通过 `create_engine()` 方法来构造，是只用创建一次的全局对象。  
`create_engine()` 首次返回不会链接到数据库，仅在调用 Engine 对象执行数据库操作时才链接到数据库 （被称为 **Lazy loading** 的设计模式），[lazy initialization](https://docs.sqlalchemy.org/en/20/glossary.html#term-lazy-initialization)

```python
from sqlalchemy import create_engine
engine = create_engine("sqlite+pysqlite:///:memory:", echo=True)
```

### Connect mysql server

reference: [MySQL and MariaDB — SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/dialects/mysql.html)

## Transactions 事务操作

### Core Engine Connect

```python
from sqlalchemy import text

# 获取数据库游标
with engine.connect() as conn:
	
	# 返回 Result 对象	
    result = conn.execute(text("select 'hello world'"))
    print(result.all())
    
    # 提交变更
    conn.commit()
	result = conn.execute(text("SELECT x, y FROM some_table"))
	for row in result:
		print(f"x: {row.x}  y: {row.y}")
	
	# 带参数的 sql 语句拼接
    result = conn.execute(text("SELECT x, y FROM some_table WHERE y > :y"), {"y": 2})
    for row in result:
        print(f"x: {row.x}  y: {row.y}")

	# 多参数 executemany
	conn.execute(
         text("INSERT INTO some_table (x, y) VALUES (:x, :y)"),
         [{"x": 11, "y": 12}, {"x": 13, "y": 14}],
     )

```

### ORM Engine Session

使用 ORM 时的基本事务/数据库交互对象称为 `Session`。  
`Session` 在结束事务后实际上并不保留 `Connection` 对象。下次需要对数据库执行 SQL 时，它会从 `Engine` 获取新的 `Connection` 。

```python
from sqlalchemy.orm import Session

stmt = text("SELECT x, y FROM some_table WHERE y > :y ORDER BY x, y")
with Session(engine) as session:
    result = session.execute(stmt, {"y": 6})
    for row in result:
        print(f"x: {row.x}  y: {row.y}")
    
    result = session.execute(
         text("UPDATE some_table SET y=:y WHERE x=:x"),
        [{"x": 9, "y": 11}, {"x": 13, "y": 15}],
   )
    session.commit()
```

通过 with 语句构建 begin 开始、rollback 回滚、commit 提交 语句。

```python
# verbose version of what a context manager will do
with Session(engine) as session:
    # session.begin() auto commit database
    # create session and add objects
	with session.begin():
		session.add(some_object)
        session.add(some_other_object)
	    # inner context calls session.commit(), if there were no exceptions
		# outer context calls session.close()
    try:
        session.add(some_object)
        session.add(some_other_object)
    except:
        session.rollback()
        raise
    else:
        session.commit()
```

更简洁的实现：

```python
# create session and add objects
with Session(engine) as session, session.begin():
    session.add(some_object)
    session.add(some_other_object)
	# inner context calls session.commit(), if there were no exceptions
	# outer context calls session.close()
```

或者使用 session macker(会话生成器)：

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

# an Engine, which the Session will use for connection
# resources
engine = create_engine("postgresql+psycopg2://scott:tiger@localhost/")

# a sessionmaker(), also in the same scope as the engine
Session = sessionmaker(engine)

# we can now construct a Session() and include begin()/commit()/rollback()
# at once
with Session.begin() as session:
    session.add(some_object)
    session.add(some_other_object)
	# commits the transaction, closes the session
```

## Database Metadata 数据库元数据 ORM

SQLAlchemy 中数据库元数据最常见的基础对象称为 `MetaData` 、 `Table` 和 `Column`。  
一种创建表的方式是 **显性使用 MetaData** 表集合来创建：

```python
from sqlalchemy import Table, Column, Integer, String
# `MetaData` 对象是表的集合
from sqlalchemy import MetaData
metadata_obj = MetaData()

# 用户表
user_table = Table(
    "user_account",
    metadata_obj,
    Column("id", Integer, primary_key=True),
    Column("name", String(30)),
    Column("fullname", String),
)

# 新建表
metadata_obj.create_all(engine)
```

### Declarative base 声明式基础

一种是使用 **对象** 的方式来创建表。

```python
from sqlalchemy.orm import DeclarativeBase
# 创建一个 DeclarativeBase 的子类 Base
# 可以基于这个子类 Base 来映射 mapper 数据
# 构造声明性类定义的基类 相当于已经弃用的 Base = declarative_base()
class Base(DeclarativeBase):
    pass

# 建立 `Base` 类后，可以基于 Base 类 定义 ORM 映射类
from typing import List
from typing import Optional
from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy.orm import relationship

# ORM 映射类
class User(Base):
    __tablename__ = "user_account"
    # 为了指示 `Table` 中的列，使用 `mapped_column()` 构造，并结合基于 `Mapped` 类型的键入注释。该对象将生成应用于 `Table` 构造的 `Column` 对象。
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(30))
    
	# 对于具有简单数据类型且没有其他选项的列，我们可以单独指示 `Mapped` 类型注释，使用简单的 Python 类型
    fullname: Mapped[Optional[str]]
    addresses: Mapped[List["Address"]] = relationship(back_populates="user")
	
	# 获得可读的字符串输出
    def __repr__(self) -> str:
        return f"User(id={self.id!r}, name={self.name!r}, fullname={self.fullname!r})"

class Address(Base):
    __tablename__ = "address"
    id: Mapped[int] = mapped_column(primary_key=True)
    email_address: Mapped[str]
    user_id = mapped_column(ForeignKey("user_account.id"))
    user: Mapped[User] = relationship(back_populates="addresses")
    def __repr__(self) -> str:
        return f"Address(id={self.id!r}, email_address={self.email_address!r})"

# 向数据库构建表
# 运行 PRAGMA 语句，但不会生成新表，因为发现它们已经存在
Base.metadata.create_all(engine)

```

### Table Reflection 表反射

用于识别已有数据库的映射。

```python
# `MetaData` 对象是表的集合,元数据
from sqlalchemy import MetaData
metadata_obj = MetaData()
# 将创建一个新的 `Table` 对象 自动识别
some_table = Table("some_table", metadata_obj, autoload_with=engine)
```

## Working with Data 处理数据

### Working with sql core 拼接构造 sql 语句

reference: [Using SELECT Statements — SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/tutorial/data_select.html)

#### Insert 插入

```python
with engine.connect() as conn:
	# insert(user_table).values(name="spongebob", fullname="Spongebob Squarepants")
    result = conn.execute(
        insert(user_table),
        [
            {"name": "sandy", "fullname": "Sandy Cheeks"},
            {"name": "patrick", "fullname": "Patrick Star"},
        ],
    )
    conn.commit()
```

#### Select 选择查询

```python
from sqlalchemy import select
stmt = select(user_table).where(user_table.c.name == "spongebob")
print(stmt) # 生成一段 mysql 语句
# SELECT user_account.id, user_account.name, user_account.fullname FROM user_account WHERE user_account.name = :name_1

# first() 第一个数据
row = session.execute(select(User)).first()

# all() 全部数据
session.execute(
				select(User.name, Address).where(User.id == Address.user_id).order_by(Address.id)
			).all()

# where 语句 c: column
user_table.c.name == "squidward"
select(user_table).where(user_table.c.name == "squidward")

# 多查
print(
    select(address_table.c.email_address)
    .where(user_table.c.name == "squidward")
    .where(address_table.c.user_id == user_table.c.id)
)
print(
    select(address_table.c.email_address).where(
        user_table.c.name == "squidward",
        address_table.c.user_id == user_table.c.id,
    )
)

# AND OR 语句
from sqlalchemy import and_, or_
print(
    select(Address.email_address).where(
        and_(
            or_(User.name == "squidward", User.name == "sandy"),
            Address.user_id == User.id,
        )
    )
)

# ORDER BY 排序依据
print(select(user_table).order_by(user_table.c.name))
```

#### Update 更新

```python
from sqlalchemy import update
stmt = (
    update(user_table)
    .where(user_table.c.name == "patrick")
    .values(fullname="Patrick the Star")
)
print(stmt)


with engine.begin() as conn:
    result = conn.execute(
        update(user_table)
        .values(fullname="Patrick McStar")
        .where(user_table.c.name == "patrick")
    )
    print(result.rowcount)

update_stmt = (
    update(user_table)
    .where(user_table.c.name == "patrick")
    .values(fullname="Patrick the Star")
    .returning(user_table.c.id, user_table.c.name) # 定义返回的数据
)


print(update_stmt)
delete_stmt = (
    delete(user_table)
    .where(user_table.c.name == "patrick")
    .returning(user_table.c.id, user_table.c.name)
)
print(delete_stmt)
```

#### Delete 删除

```python
delete_stmt = (
    delete(user_table)
    .where(user_table.id == address_table.user_id)
    .where(address_table.email_address == "patrick@aol.com")
)
```

#### Count 计数

[python - How to count rows with SELECT COUNT(\*) with SQLAlchemy? - Stack Overflow](https://stackoverflow.com/questions/12941416/how-to-count-rows-with-select-count-with-sqlalchemy/76155009#76155009?newreg=931f85ad102d493db5aaa753479640f9)

```python
from sqlalchemy import func, select

class MyModel(Base):
	...

statement = select(func.count()).select_from(MyModel)
count: int = session.execute(statement).scalar()
```

### Woking with ORM

Instances of Classes represent Rows 表类的实例代表行。

```python
squidward = User(name="squidward", fullname="Squidward Tentacles")
krabs = User(name="ehkrabs", fullname="Eugene H. Krabs")
session = Session(engine)
session.add(squidward)
session.commit()
```

从主键获取对象：

```python
some_squidward = session.get(User, 4) # 直接返回数据库对象类型
some_squidward = User(id=4, name='squidward', fullname='Squidward Tentacles')
```

从 execute 获取对象：`scalar()` scalar /ˈskeɪ.lər/

```python
sandy = session.execute(select(User).filter_by(name="sandy")).scalar_one()

session.execute(
  select(User)
).scalars().all()

# or

session.scalars(
  select(User)
).all()
```

## AsyncIO Support

对异步 IO 的支持：[Overview — SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/intro.html#asyncio-support)

reference: [Asynchronous I/O (asyncio) — SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/orm/extensions/asyncio.html)

install:

```shell
pip install sqlalchemy[asyncio]
```

install Asynchronous DBAPI:

```python

# SQLTIE3 sqlite+aiosqlite:///database.db  # 数据库文件名为 database.db 不存在的新建一个
# 异步 mysql+aiomysql://user:password@host:port/dbname
DB_URL = "mysql+aiomysql://root:123456@localhost:3306/tgconfigs"
```

uses:

```python
import asyncio

from sqlalchemy import Column
from sqlalchemy import MetaData
from sqlalchemy import select
from sqlalchemy import String
from sqlalchemy import Table
from sqlalchemy.ext.asyncio import create_async_engine

meta = MetaData()
t1 = Table("t1", meta, Column("name", String(50), primary_key=True))

class Base(AsyncAttrs, DeclarativeBase):
    pass

async def async_main() -> None:
	# 创建异步数据库 engine
    engine = create_async_engine(
        "postgresql+asyncpg://scott:tiger@localhost/test",
        echo=True,
    )

    async with engine.begin() as conn:
	    # `run_sync()` 方法可用于调用特殊的 DDL 函数，例如不包含可等待挂钩的 `MetaData.create_all()`
        await conn.run_sync(meta.create_all)

        await conn.execute(
            t1.insert(), [{"name": "some name 1"}, {"name": "some name 2"}]
        )

    async with engine.connect() as conn:
        # select a Result, which will be delivered with buffered
        # results
        result = await conn.execute(select(t1).where(t1.c.name == "some name 1"))

        print(result.fetchall())

    # for AsyncEngine created in function scope, close and
    # clean-up pooled connections
    # 当在超出上下文并被垃圾回收的作用域中使用 `AsyncEngine` 对象时，建议使用 `await` 调用 `AsyncEngine.dispose()` 方法，如上例中的 `async_main` 函数。这确保了连接池保持打开的任何连接都将在可等待上下文中得到正确处理。
    await engine.dispose()


asyncio.run(async_main())
```

### Complete example 完整示例

```python
from __future__ import annotations

import asyncio
import datetime
from typing import List

from sqlalchemy import ForeignKey
from sqlalchemy import func
from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncAttrs
from sqlalchemy.ext.asyncio import async_sessionmaker
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy.ext.asyncio import create_async_engine
from sqlalchemy.orm import DeclarativeBase
from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy.orm import relationship
from sqlalchemy.orm import selectinload


class Base(AsyncAttrs, DeclarativeBase):
    pass


class A(Base):
    __tablename__ = "a"

    id: Mapped[int] = mapped_column(primary_key=True)
    data: Mapped[str]
    create_date: Mapped[datetime.datetime] = mapped_column(server_default=func.now())
    bs: Mapped[List[B]] = relationship()


class B(Base):
    __tablename__ = "b"
    id: Mapped[int] = mapped_column(primary_key=True)
    a_id: Mapped[int] = mapped_column(ForeignKey("a.id"))
    data: Mapped[str]

# 类型注释一个函数
async def insert_objects(async_session: async_sessionmaker[AsyncSession]) -> None:
	# 使用 async session maker 构造 session
    async with async_session() as session:
        async with session.begin():
            session.add_all(
                [
                    A(bs=[B(), B()], data="a1"),
                    A(bs=[], data="a2"),
                    A(bs=[B(), B()], data="a3"),
                ]
            )


async def select_and_update_objects(
    async_session: async_sessionmaker[AsyncSession],
) -> None:
    async with async_session() as session:
        stmt = select(A).options(selectinload(A.bs))

        result = await session.execute(stmt)

        for a1 in result.scalars():
            print(a1)
            print(f"created at: {a1.create_date}")
            for b1 in a1.bs:
                print(b1)

        result = await session.execute(select(A).order_by(A.id).limit(1))

        a1 = result.scalars().one()

        a1.data = "new data"

        await session.commit()

        # access attribute subsequent to commit; this is what
        # expire_on_commit=False allows
        print(a1.data)

        # alternatively, AsyncAttrs may be used to access any attribute
        # as an awaitable (new in 2.0.13)
        for b1 in await a1.awaitable_attrs.bs:
            print(b1)


async def async_main() -> None:
    engine = create_async_engine(
        "postgresql+asyncpg://scott:tiger@localhost/test",
        echo=True,
    )

    # async_sessionmaker: a factory for new AsyncSession objects.
    # expire_on_commit - don't expire objects after transaction commit
    async_session = async_sessionmaker(engine, expire_on_commit=False)

	# 异步使用同步方法
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

    await insert_objects(async_session)
    await select_and_update_objects(async_session)

    # for AsyncEngine created in function scope, close and
    # clean-up pooled connections
    await engine.dispose()


asyncio.run(async_main())
```

## Legacy query API 旧版查询 API

[Legacy Query API — SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/orm/queryguide/query.html#legacy-query-api)

多年来，它是使用 SQLAlchemy ORM 时唯一的 SQL 接口。从版本 2.0 开始，一种全新的工作方式现已成为标准方法，其中适用于 Core 的相同 `select()` 构造也适用于 ORM，为构建查询提供一致的接口。

从旧版本迁移到 2.0 [SQLAlchemy 2.0 - Major Migration Guide — SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/changelog/migration_20.html#migration-20-query-usage)

SQLAlchemy 2.0 中最大的明显变化是使用 `Session.execute()` 与 `select()` 结合使用来运行 ORM 查询，而不是使用 `Session.query()` 。正如其他地方提到的，没有计划实际删除 `Session.query()` API 本身，因为它现在是通过在内部使用新 API 来实现的，它将保留为旧版 API，并且这两个 API 都可以自由使用。

## SQLAlchemy Extansion 扩展

### Sqlalchemy-utils

看样子比较老了。

[GitHub - kvesteri/sqlalchemy-utils: Various utility functions and datatypes for SQLAlchemy.](https://github.com/kvesteri/sqlalchemy-utils)

> Various utility **functions** and **datatypes** for SQLAlchemy. SQLAlchemy 的各种实用函数和数据类型。

## Q&A

### Is the Session thread-safe (线程安全)? Is AsyncSession safe(异步并发安全) to share in concurrent tasks?[¶](https://docs.sqlalchemy.org/en/20/orm/session_basics.html#is-the-session-thread-safe-is-asyncsession-safe-to-share-in-concurrent-tasks "Permalink to this heading")

`Session` 是一个可变的、有状态的对象，`session` 的实例就无法在并发线程或异步任务之间共享。 `Session` 旨在以非并发方式使用。

在设计并发数据库应用程序时，适当的模型是 **每个并发任务/线程都处理自己的数据库事务 `Session`**。

确保这种使用的最佳方法是在线程或任务内部的顶级 Python 函数中本地使用 **标准上下文管理器** 模式。

### sqlalchemy.exc.CompileError: VARCHAR(varchar 变长字符串) Requires a length on dialect(方言) mysql

[python - InvalidRequestError: VARCHAR requires a length on dialect mysql - Stack Overflow](https://stackoverflow.com/questions/16472725/invalidrequesterror-varchar-requires-a-length-on-dialect-mysql)

official docs: [ORM Mapped Class Overview — SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/orm/mapping_styles.html#imperative-mapping)

在 mysql 中使用 **变长字符串** 时需要使用 `String(100)` 来定义字符串长度：

```python
class TGForwardConfig(Base):
    __tablename__ = 'forward_config'

    id: Mapped[int] = mapped_column(primary_key=True, doc="主键")
    source: Mapped[int] = mapped_column(doc="源群聊ID")
    dest: Mapped[int] = mapped_column(doc="目标群聊ID")
    forward_history_count: Mapped[int] = mapped_column(doc="转发历史信息的数量")
    interval_second: Mapped[int] = mapped_column(doc="间隔时间单位 s", default=20)

    remove_word: Mapped[Optional[str]] = mapped_column(
        String(100), doc="删除的文字,用,分隔", nullable=True)
    cut_word: Mapped[Optional[str]] = mapped_column(
        String(100), doc="截断词,用 , 分隔", nullable=True)
    skip_word: Mapped[Optional[str]] = mapped_column(
        String(100), doc="跳过词,用 , 分隔", nullable=True)
    add_text: Mapped[Optional[str]] = mapped_column(
        String(100), doc="跳过语,用 , 分隔", nullable=True)
```

### Default timestamp

[python - SQLAlchemy default DateTime - Stack Overflow](https://stackoverflow.com/questions/13370317/sqlalchemy-default-datetime)

使用 `server_default` 而不是 `default` ，因此值将由数据库本身处理。

```python
create_at: Mapped[datetime] = mapped_column(
	server_default=func.now(), default=None, nullable=False
)
```

### Optimize

数据库连接方面，可以避免出现 `sqlalchemy.exc.OperationalError: (pymysql.err.OperationalError) (2013, 'Lost connection to MySQL server during query')` 的错误，reference: [sqlalchemy 报错 Lost connection to MySQL server during query 解决 - 金色旭光 - 博客园](https://www.cnblogs.com/goldsunshine/p/17304427.html)

```python
engine = create_async_engine(DB_URL, pool_pre_ping=True, pool_recycle=600)
```

### `relationship` 延迟加载

```python
class User(Base):
    __tablename__ = 'user'
    __table_args__ = {'comment': '转载用户表'}

    id: Mapped[str] = mapped_column(
        String(20), primary_key=True, comment="用户 ID")
        
	# lazy='subquery'
    configs: Mapped[List["TGForwardConfig"]] = relationship(
        'TGForwardConfig', backref='user', lazy='subquery')
    
    reg_at: Mapped[datetime] = mapped_column(
        nullable=False, server_default=func.now(), comment='注册时间'
    )

    auth_time: Mapped[datetime] = mapped_column(
        default=lambda: datetime.now() + timedelta(days=7), nullable=True, comment='授权时间')

    def __repr__(self):
        return f'<User(user_id={self.id}, reg_at={self.reg_at}, auth_time={self.auth_time})>'
```

### DatabaseURL 数据库 URL

```python
# SQLTIE3 sqlite+aiosqlite:///database.db  # 数据库文件名为 database.db 不存在的新建一个
# 异步 mysql+aiomysql://user:password@host:port/dbname
DB_URL = os.environ.get("DB_URL") or "mysql+aiomysql://root:123456@localhost/tgforward?charset=utf8mb4"
```

## Alembic [英 /ə'lembɪk/ 蒸馏器] sqlalchemy 数据库迁移

reference: 

1. [FastAPI with Async SQLAlchemy, SQLModel, and Alembic | TestDriven.io](https://testdriven.io/blog/fastapi-sqlmodel/)
2. using asyncio with alembic（异步支持）： [Cookbook — Alembic 1.12.0 documentation](https://alembic.sqlalchemy.org/en/latest/cookbook.html#using-asyncio-with-alembic)
3. 官方文档: [https://alembic.sqlalchemy.org/en/latest/index.html](https://alembic.sqlalchemy.org/en/latest/index.html)
4. 相关项目: [https://github.com/python-gino/gino](https://github.com/python-gino/gino) - [文档](https://www.bookstack.cn/read/gino-1.0-zh/3d56d5fe80ab5932.md)

alembic [英 /ə'lembɪk/] 是 sqlalchemy 的作者开发的。用来做 OMR 模型与数据库的迁移与映射。`alembic` 使用方式跟 `git` 有点了类似，表现在两个方面，第一个，`alembic` 的所有命令都是以 `alembic` 开头；第二，`alembic` 的迁移文件也是通过版本进行控制的。首先，通过 `pip install alembic` 进行安装。以下将解释 `alembic` 的用法：

### 初始化 alembic 仓库

在终端中，`cd` 到你的项目目录中，然后执行命令 `alembic init alembic`，创建一个名叫 `alembic` 的仓库。

### 创建模型类

创建一个 `models.py` 模块，然后在里面定义你的模型类，示例代码如下：

```python
import asyncio
from typing import List, Dict, Optional, Mapping, Type, TypeVar, Tuple
import typing
from typing_extensions import Annotated
from datetime import datetime, timedelta

# sqlalchemy type
import sqlalchemy.orm
from sqlalchemy import (
    ForeignKey,
    func,
    select,
    update,
    String,
    DateTime,
    Integer,
    Float,
    Boolean,
)

# sqlalchemy asynchronous support
from sqlalchemy.ext.asyncio import (
    AsyncAttrs,
    async_sessionmaker,
    AsyncSession,
    create_async_engine,
)

# sqlalchemy ORM
from sqlalchemy.orm import (
    DeclarativeBase,
    Mapped,
    mapped_column,
    relationship,
)

import inspect

from .string_template import StringTemplate, CustomParam, getBeijingTime

IDPK = Annotated[
    int,
    mapped_column(primary_key=True, autoincrement=True, comment="ID主键"),
]


class Base(AsyncAttrs, DeclarativeBase):
    """ORM 基类 | 详见[1]、[3]"""

    __table_args__ = {
        "mysql_engine": "InnoDB",  # MySQL引擎
        "mysql_charset": "utf8mb4",  # 设置表的字符集
        "mysql_collate": "utf8mb4_general_ci",  # 设置表的校对集
    }

```

### 设置数据库连接

在 [alembic.ini](https://alembic.sqlalchemy.org/en/latest/tutorial.html#editing-the-ini-file "alembic.ini") 中设置数据库的连接，`sqlalchemy.url = driver://user:pass@localhost/dbname`，比如以 mysql 数据库为例，则配置后的代码为：

`sqlalchemy.url = mysql+mysqldb://root:root@localhost/alembic_demo?charset=utf8`

### 设置 target_metadata

为了使用模型类更新数据库，需要在 `env.py` 文件中设置 [target_metadata](https://alembic.sqlalchemy.org/en/latest/ops.html?highlight=target_metadata#alembic.operations.Operations.f "target_metadata")，默认为 `target_metadata=None`。使用 `sys` 模块把当前项目的路径导入到 `path` 中：

```python
# add your model's MetaData object here
# for 'autogenerate' support
# from myapp import mymodel
# target_metadata = mymodel.Base.metadata
from app.database.model import Base
target_metadata = Base.metadata
```

### 自动生成迁移文件

使用 `alembic revision --autogenerate -m "message"` 将当前模型中的状态生成迁移文件。

异步 Base 支持：

```shell
alembic init -t async alembic
```

### 更新数据库

使用 `alembic upgrade head` 将刚刚生成的迁移文件，真正映射到数据库中。同理，如果要降级，那么使用 `alembic downgrade head`。

### 命令和参数解释

1. `init`：创建一个 alembic 仓库。
2. `revision`：创建一个新的版本文件。
3. `–autogenerate`：自动将当前模型的修改，生成迁移脚本。
4. `-m`：本次迁移做了哪些修改，用户可以指定这个参数，方便回顾。
5. `upgrade`：将指定版本的迁移文件映射到数据库中，会执行版本文件中的 upgrade 函数。如果有多个迁移脚本没有被映射到数据库中，那么会执行多个迁移脚本。
6. `[head]`：代表最新的迁移脚本的版本号。
7. `downgrade`：会执行指定版本的迁移文件中的 downgrade 函数。
8. `heads`：展示 head 指向的脚本文件版本号。
9. `history`：列出所有的迁移版本及其信息。
10. `current`：展示当前数据库中的版本号。

另外，在你第一次执行 `upgrade` 的时候，就会在数据库中创建一个名叫 `alembic_version` 表，这个表只会有一条数据，记录当前数据库映射的是哪个版本的迁移文件。

### 经典错误

|错误描述|原因|解决办法|
|---|---|---|
|`FAILED: Target database is not up to date.`|主要是 `heads` 和 `current` 不相同。`current` 落后于 heads 的版本。|将 `current` 移动到 `head` 上。`alembic upgrade head`|
|`FAILED: Can't locate revision identified by '77525ee61b5b'`|数据库中存的版本号不在迁移脚本文件中|删除数据库的 `alembic_version` 表中的数据，重新执行 `alembic upgrade head`|

### Is it possible to store the alembic connect string outside of `alembic.ini`

ref: [python - Is it possible to store the alembic connect string outside of alembic.ini? - Stack Overflow](https://stackoverflow.com/questions/22178339/is-it-possible-to-store-the-alembic-connect-string-outside-of-alembic-ini)

```python
# this is the Alembic Config object, which provides
# access to the values within the .ini file in use.
config = context.config

# this will overwrite the ini-file sqlalchemy.url path
# with the path given in the config of the main code
import config as ems_config
config.set_main_option('sqlalchemy.url', ems_config.config.get('sql', 'database'))
```

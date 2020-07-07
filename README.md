# 数据库变更测试用例

功能点

## SQLServer的测试点

### 前置条件

1. 建立好SQLServer客户端（Windows/Linux最好都能提供)
2. 有可连接的SQLServer服务器
3. 建立SQLServer的数据库服务
4. 建立好SQLServer的数据库实例，并且配置好SQLServer客户端，并且调试通过。
5. 建立好对应的SQL包，并将SQL包与上述SQLServer服务关联

#### 一、 测试获取变更对象列表

| GIVEN | WHEN | THEN |
| ---- | ---- | ---- |
| 以以下数据建立SQL包版本后 | 发起SQL包变更、第四步获取检查对象 | 返回 |
| 1. [SQL包不符合目录规范](testdata/test_invalid) |  |  报错，提示『未选择正确的路径』 |
| 2. [SQL包版本符合目录规范](testdata/test_ok) | | 获取检查对象get_author_threads, author_view, threads, posts（根据文件的排序顺序来定) |


#### 二、测试SQL执行过程

| GIVEN | WHEN | THEN |
| ---- | ---- | ---- |
| 以以下数据建立SQL包版本后 | 执行，查看任务返回 | 返回 |
| 1. [SQL包不符合规范](testdata/test_invalid)，选择不备份 |  |  报错，提示『未选择正确的路径』 |
| 2. SQLServer对应的数据库不存在，[使用SQL包](testdata/test_ok)，选择不备份 | | 备份步骤时，报错，提示数据库不存在 |
| 3. 数据库存在，[使用SQL包](testdata/test_ok)，存在语法错误，选择不备份 | | 报错，对应的文件的语法错误 |
| 4. 数据库存在，[SQL包版本符合规范](testdata/test_ok)，选择备份库 | | 正常（*注：服务器如果是Express Edition，可能不支持备份库*） |
| 5. 数据库存在，[使用SQL包](testdata/test_ok)，选择备份对象 |  | 表备份时提示表不存在 |
| 6. 数据库存在，[使用SQL包](testdata/test_ok)，选择备份对象，出错后跳过 |  | 存在语法错误，now不是t-sql的语法关键字 |
| 7. 数据库存在，[使用SQL包](testdata/test_mssql_ok)，不选择备份 |  | 正常 


##### 2.1 测试备份对象的SQL语句

| GIVEN | WHEN | THEN | 
| ---- | ---- | ----|
| 根据SQL包里定义的数据库对象 | 选择了备份选项 | 生成的SQL |
| posts, table对象 | 未选择备份 | 无备份语句 |
| posts, table对象 | 选择库备份 | 无对象备份语句 |
| posts, table对象 | 选择对象备份 | `select * into posts_20200604 from posts`<br>以及`sp_columns posts` |
| author_view, view| 选择对象备份 | `sp_helptext author_view` |
| get_author_view, procedure| 选择对象备份 | `sp_helptext get_author_view` |


##### 2.2 测试检查对象的SQL语句

| GIVEN | WHEN | THEN | 
| ---- | ---- | ----|
| 根据SQL包里定义的数据库对象 | 检查步骤 |  生成的SQL |
| posts, threads |  |  `select name,CONVERT(varchar(100),create_date,120),CONVERT(varchar(100),modify_date,120) from sys.objects where name in ("posts", "threads")`



## MySQL的测试点


### 前置条件

1. 建立好MySQL客户端（Windows/Linux最好都能提供)
2. 有可连接的MySQL服务器
3. 建立MySQL的数据库服务
4. 建立好MySQL的数据库实例，并且配置好MySQL客户端，并且调试通过。
5. 建立好对应的SQL包，并将SQL包与上述MySQL服务关联


#### 一、 测试获取变更对象列表

| GIVEN | WHEN | THEN |
| ---- | ---- | ---- |
| 以以下数据建立SQL包版本后 | 发起SQL包变更、第四步获取检查对象 | 返回 |
| 1. [SQL包不符合目录规范](testdata/test_invalid) |  |  报错，提示『未选择正确的路径』 |
| 2. [SQL包版本符合目录规范](testdata/test_ok) | | 获取检查对象get_author_threads, author_view, threads, posts（根据文件的排序顺序来定) |

*注：注意mysql与sqlserver的一些语法特性不一样，比如mysql可以使用`create table if not exists posts`这种。*


#### 二、测试SQL执行过程

| GIVEN | WHEN | THEN |
| ---- | ---- | ---- |
| 以以下数据建立SQL包版本后 | 执行，查看任务返回 | 返回 |
| 1. [SQL包不符合规范](testdata/test_invalid)，选择不备份 |  |  报错，提示『未选择正确的路径』 |
| 2. 数据库不存在，[使用SQL包](testdata/test_ok)，选择不备份 | | 备份步骤时，报错，提示数据库不存在 |
| 3. 数据库存在，[使用SQL包](testdata/test_ok)，存在语法错误，选择不备份 | | 报错，对应的文件的语法错误 |
| 4. 数据库存在，[SQL包版本符合规范](testdata/test_ok)，选择备份库 | | 会提示**不支持备份库** |
| 5. 数据库存在，[使用SQL包](testdata/test_ok)，选择备份对象 |  | 表备份时提示表不存在 |
| 6. 数据库存在，[使用SQL包](testdata/test_ok)，选择备份对象，出错后跳过 |  | 正常执行 |


##### 2.1 测试备份对象的SQL语句

| GIVEN | WHEN | THEN | 
| ---- | ---- | ----|
| 根据SQL包里定义的数据库对象 | 选择了备份选项 | 生成的SQL |
| posts, table对象 | 未选择备份 | 无备份语句 |
| posts, table对象 | 选择库备份 | 无对象备份语句 |
| posts, table对象 | 选择对象备份 | `create table posts_20200604 select * from posts`<br>以及`show create table posts` |
| author_view, view| 选择对象备份 | `show create procedure author_view` |
| get_author_view, procedure| 选择对象备份 | `show create procedure get_author_view` |


##### 2.2 测试检查对象的SQL语句

| GIVEN | WHEN | THEN | 
| ---- | ---- | ----|
| 根据SQL包里定义的数据库对象 | 检查步骤 |  生成的SQL |
| posts, threads |  |  `show procedure status where name in ("posts", "threads")`



## Oracle的测试点


### 前置条件

1. 建立好Oracle客户端（Windows/Linux最好都能提供)
2. 有可连接的Oracle服务器
3. 建立Oracle的数据库服务
4. 建立好Oracle的数据库实例，并且配置好Oracle客户端，并且调试通过。
5. 建立好对应的SQL包，并将SQL包与上述Oracle服务关联


#### 一、 测试获取变更对象列表

| GIVEN | WHEN | THEN |
| ---- | ---- | ---- |
| 以以下数据建立SQL包版本后 | 发起SQL包变更、第四步获取检查对象 | 返回 |
| 1. [SQL包不符合目录规范](testdata/test_invalid) |  |  报错，提示『未选择正确的路径』 |
| 2. [SQL包版本符合目录规范](testdata/test_ok) | | 获取检查对象get_author_threads, author_view, threads, posts（根据文件的排序顺序来定) |

*注：注意Oracle与sqlserver的一些语法特性不一样，比如Oracle可以使用`create table if not exists posts`这种。*


#### 二、测试SQL执行过程

| GIVEN | WHEN | THEN |
| ---- | ---- | ---- |
| 以以下数据建立SQL包版本后 | 执行，查看任务返回 | 返回 |
| 1. [SQL包不符合规范](testdata/test_invalid)，选择不备份 |  |  报错，提示『未选择正确的路径』 |
| 2. 数据库不存在，[使用SQL包](testdata/test_ok)，选择不备份 | | 备份步骤时，报错，提示数据库不存在 |
| 3. 数据库存在，[使用SQL包](testdata/test_ok)，存在语法错误，选择不备份 | | 报错，对应的文件的语法错误 |
| 4. 数据库存在，[SQL包版本符合规范](testdata/test_ok)，选择备份库 | | 会提示**不支持备份库** |
| 5. 数据库存在，[使用SQL包](testdata/test_ok)，选择备份对象 |  | 表备份时提示表不存在 |
| 6. 数据库存在，[使用SQL包](testdata/test_ok)，选择备份对象，出错后跳过 |  | 正常执行 |


##### 2.1 测试备份对象的SQL语句

| GIVEN | WHEN | THEN | 
| ---- | ---- | ----|
| 根据SQL包里定义的数据库对象 | 选择了备份选项 | 生成的SQL |
| posts, table对象 | 未选择备份 | 无备份语句 |
| posts, table对象 | 选择库备份 | 无对象备份语句 |
| posts, table对象 | 选择对象备份 | `create table posts_20200604 as select * from posts`<br>以及`SELECT dbms_metadata.get_ddl('TABLE', 'posts') FROM dual` |
| author_view, view| 选择对象备份 | `SELECT TEXT FROM USER_SOURCE WHERE UPPER(NAME)='author_view'` |
| get_author_view, procedure| 选择对象备份 | `SELECT TEXT FROM USER_SOURCE WHERE UPPER(NAME)='get_author_view'` |


##### 2.2 测试检查对象的SQL语句

| GIVEN | WHEN | THEN | 
| ---- | ---- | ----|
| 根据SQL包里定义的数据库对象 | 检查步骤 |  生成的SQL |
| posts, threads |  |  `SELECT OBJECT_NAME,CREATED,LAST_DDL_TIME from USER_OBJECTS where OBJECT_NAME in ("posts", "threads") and rownum=1`


# Java工程结构

## 对象定义

1. AO：应用对象，在 Web 层与 Service 层之间抽象的复用对象模型
2. BO：业务对象
3. DO：领域对象/与数据库表结构对应
4. DAO：数据访问对象
5. DTO：数据传输对象
6. PO：持久化对象
7. QO：查询对象
8. TO：数据传输对象
9. VO：视图对象/值对象

## 常用包名

- bean：存放实体类文件
- entity：存放实体类文件
- pojo：存放实体类文件
- model：存放模型类文件
- dataobject：存放数据源对象类文件
- domain：存放领域对象文件
- dto：存放数据传输对象文件
- conn:建立了一个数据库连接对象，其他所有涉及到数据库操作的文件都需要包含这个文件并引用该对象。
- dao：存放数据层文件
- repository：和dao层类似，在使用Spring Data JPA时的常用包名
- mapper：和dao层类似,在使用Mybatis时的常用包名
- service：存放服务层文件
- action：存放服务层文件（在ssh中常用包名）
- manager：存放服务层文件（针对第三方的）
- spi：存放服务层文件
- controller：存放控制层文件
- web：存放控制层文件
- api：存放控制层文件（web api）
- config：存放配置类文件
- enums：存放枚举类文件
- constant：存放定义常量的文件
- exception：存放异常类文件
- job：存放任务类文件
- utils：存放工具类文件
- tools：存放工具类文件
- aspect：存放切面类文件
- converter：存放转换器文件
- interceptor：存放拦截器文件
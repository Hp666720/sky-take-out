
# 苍穹外卖（Sky Take Out）
苍穹外卖是一个外卖平台后端服务项目，基于 Spring Boot 构建，采用 Maven 多模块架构，为商家管理端和用户端提供完整的 RESTful API 接口服务。

---

## 目录

- [项目简介](#项目简介)
- [技术栈](#技术栈)
- [项目结构](#项目结构)
- [环境要求](#环境要求)
- [快速开始](#快速开始)
- [配置说明](#配置说明)
- [功能模块](#功能模块)
- [接口文档](#接口文档)
- [项目亮点](#项目亮点)

---

## 项目简介

本项目为外卖平台的后端服务系统，分为**管理端（Admin）**和**用户端（User）**两套接口体系。管理端供商家后台使用，涵盖员工管理、菜品管理、套餐管理、订单管理等核心功能；用户端供微信小程序用户使用，支持浏览菜品、购物车、下单支付、地址管理等功能。

---

## 技术栈

| 技术 | 说明 | 版本 |
|------|------|------|
| Spring Boot | 核心框架 | 2.7.3 |
| MyBatis | 持久层框架 | 2.2.0 |
| MySQL | 关系型数据库 | - |
| Druid | 数据库连接池 | 1.2.1 |
| Redis | 缓存中间件 | - |
| JWT | 令牌认证 | 0.9.1 |
| Knife4j | API 接口文档 | 3.0.2 |
| WebSocket | 实时通信 | - |
| 阿里云 OSS | 对象存储 | 3.10.2 |
| 微信支付 | 在线支付 | 0.4.8 |
| Apache POI | Excel 处理 | 3.16 |
| Lombok | 简化代码 | 1.18.20 |
| FastJSON | JSON 处理 | 1.2.76 |
| PageHelper | MyBatis 分页插件 | 1.3.0 |
| AspectJ | AOP 切面编程 | 1.9.4 |

---

## 项目结构

```

sky-take-out/
├── sky-common/                 # 公共模块 - 存放通用工具类和配置
│   └── src/main/java/com/sky/
│       ├── constant/           # 常量类（JWT、消息、密码、状态等）
│       ├── context/            # 线程上下文（存储当前用户ID）
│       ├── enumeration/        # 枚举类（操作类型等）
│       ├── exception/          # 自定义业务异常
│       ├── json/               # JSON 序列化配置（JacksonObjectMapper）
│       ├── properties/         # 配置属性类（OSS、JWT、微信）
│       ├── result/             # 统一响应封装（Result、PageResult）
│       └── utils/              # 工具类（OSS、HTTP、JWT、微信支付）
│
├── sky-pojo/                   # 实体模块 - 存放数据模型
│   └── src/main/java/com/sky/
│       ├── dto/                # 数据传输对象（前端 -> 后端）
│       ├── entity/             # 数据库实体类
│       └── vo/                 # 视图对象（后端 -> 前端）
│
├── sky-server/                 # 服务模块 - 核心业务逻辑
│   └── src/main/java/com/sky/
│       ├── annotation/         # 自定义注解
│       ├── aspect/             # AOP 切面（自动填充公共字段）
│       ├── config/             # 配置类（WebMvc、Redis、OSS、WebSocket）
│       ├── controller/
│       │   ├── admin/          # 管理端接口（9个Controller）
│       │   └── user/           # 用户端接口（8个Controller）
│       ├── handler/            # 全局异常处理器
│       ├── interceptor/        # 拦截器（JWT令牌校验）
│       ├── mapper/             # MyBatis Mapper 接口（11个）
│       ├── service/            # 业务层接口及实现
│       ├── task/               # 定时任务
│       └── websocket/          # WebSocket 实时通信
│
└── pom.xml                     # 父工程 POM（依赖版本统一管理）
```
---

## 环境要求

- **JDK** 8+
- **Maven** 3.6+
- **MySQL** 5.7+
- **Redis** 5.0+

---

## 快速开始

### 1. 克隆项目

```
bash
git clone <repository-url>
cd sky-take-out
```
### 2. 初始化数据库

创建 MySQL 数据库，导入项目提供的 SQL 脚本。

### 3. 修改配置

编辑 `sky-server/src/main/resources/application-dev.yml`，配置数据库和 Redis 连接信息：

```
yaml
sky:
  datasource:
    host: localhost
    port: 3306
    database: sky_take_out
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: your_password
  redis:
    host: localhost
    port: 6379
    password:
    database: 0
  alioss:
    access-key-id: your-access-key-id
    access-key-secret: your-access-key-secret
    bucket-name: your-bucket-name
    endpoint: your-endpoint
  wechat:
    app-id: your-app-id
    app-secret: your-app-secret
```
### 4. 启动项目

```
bash
cd sky-server
mvn spring-boot:run
```
服务启动后访问：http://localhost:8080

---

## 配置说明

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `server.port` | 服务端口 | 8080 |
| `sky.jwt.admin-secret-key` | 管理端 JWT 签名密钥 | itcast |
| `sky.jwt.admin-ttl` | 管理端令牌有效期 | 7200000ms（2小时） |
| `sky.jwt.user-secret-key` | 用户端 JWT 签名密钥 | itheima |
| `sky.jwt.user-ttl` | 用户端令牌有效期 | 7200000ms（2小时） |
| `mybatis.mapper-locations` | Mapper XML 文件路径 | classpath:mapper/*.xml |
| `mybatis.configuration.map-underscore-to-camel-case` | 驼峰命名映射 | true |

---

## 功能模块

### 管理端（Admin）

| 模块 | 说明 |
|------|------|
| 员工管理 | 员工新增、编辑、启停用、分页查询、密码修改 |
| 分类管理 | 菜品/套餐分类的增删改查、分页查询 |
| 菜品管理 | 菜品新增（含口味）、编辑、启停用、分页查询、批量删除 |
| 套餐管理 | 套餐新增（含菜品）、编辑、启停用、分页查询、批量删除 |
| 订单管理 | 订单查询、确认、取消、派送、完成 |
| 数据统计 | 营业额、用户数、订单数等运营数据统计与报表导出 |
| 工作台 | 今日数据概览 |

### 用户端（User）

| 模块 | 说明 |
|------|------|
| 微信登录 | 小程序授权登录，获取 JWT 令牌 |
| 菜品浏览 | 按分类浏览菜品及口味详情 |
| 套餐浏览 | 查看套餐详情及包含的菜品 |
| 购物车 | 添加/删除/清空购物车 |
| 地址管理 | 收货地址的增删改查 |
| 订单提交 | 下单、支付、取消、催单 |
| 历史记录 | 历史订单查询 |

---

## 接口文档

项目集成了 **Knife4j** 接口文档工具，启动后可通过以下地址访问：

- 接口文档地址：http://localhost:8080/doc.html

---

## 项目亮点

- **多模块架构**：采用 Maven 多模块设计，职责清晰，便于维护和扩展
- **统一响应封装**：所有接口统一返回 `Result` 对象，包含状态码、消息和数据
- **全局异常处理**：通过 `@RestControllerAdvice` 统一捕获并处理业务异常
- **JWT 双令牌机制**：管理端和用户端使用独立的 JWT 密钥和令牌，安全性更高
- **AOP 自动填充**：通过自定义注解 + AOP 切面自动填充创建时间、更新时间、创建人、修改人等公共字段
- **Redis 缓存**：对高频查询数据进行缓存，提升接口响应速度
- **Spring Cache**：使用 `@Cacheable`、`@CacheEvict` 注解简化缓存逻辑
- **WebSocket 实时推送**：来单提醒、客户催单通过 WebSocket 实时通知商家
- **阿里云 OSS**：图片上传至阿里云对象存储，支持公共读访问
- **微信支付集成**：对接微信支付 API，完成下单支付闭环流程
- **Apache POI 导出**：支持运营数据 Excel 报表导出
- **定时任务**：自动处理超时未支付订单和派送中订单

---

## 许可证

本项目仅供学习参考使用。

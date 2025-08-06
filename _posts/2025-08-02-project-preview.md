---
layout: post
title: "Llama-WebServer项目解析"
date: 2025-08-02
tags: [C++, Llama, WebServer, 源码解析]
comments: true
author: Fengmengguang
---

本文对 LlamaWebServer 项目的架构和核心模块进行解析。

## 项目概述

LlamaWebServer 是一个基于 C++ 的模块化 Web 服务项目，主要用作大型语言模型（LLaMA）的前端服务。

核心功能：
- HTTP 服务器：处理客户端请求和静态文件服务
- LLaMA TCP 服务：与 LLaMA 模型交互
- 模块化设计：日志、内存管理、HTTP 解析、数据库等功能独立

## 项目模块

根据项目结构，分为以下四个核心模块：

### 1. HTTP 服务器模块

### 1. HTTP 服务器模块

职责：处理所有面向用户的 HTTP 业务，包括路由、中间件和静态文件服务
核心类：LlamaHttpApplication
涉及文件：
```
src/main_http_modular.cc
src/http/HttpServer.cc, HttpContext.cc, HttpRequest.cc, HttpResponse.cc
src/http/Middleware.cc
```

### 2. LLaMA TCP 服务模块

职责：接收来自 HTTP 服务器的 TCP 请求，与 LLaMA 模型进行交互
涉及文件：
```
src/llama_service_tcp.cc
src/services/LlamaTcpServer.cc
```

### 3. 数据库模块

职责：通过 DatabaseManager 与 MySQL 数据库交互，提供用户管理、会话管理等功能
涉及文件：
```
src/db/DatabaseManager.cpp, src/db/DBConnectionPool.cc
src/db/DBQueryHelper.cc
```

### 4. 核心库模块

职责：提供项目依赖的基础功能，如日志、内存管理、线程池等
涉及文件：
```
log/
memory/
src/ (通用工具类)
include/ (通用头文件)
```

## 各模块实现

### 1. HTTP 服务器模块

- main_http_modular.cc: 服务器入口，初始化并启动 LlamaHttpApplication
- HttpServer: 基于 TcpServer 构建，封装 HTTP 协议逻辑
- HttpContext: 解析 TCP 数据流，生成结构化的 HttpRequest 对象
- LlamaHttpApplication: 核心业务逻辑，处理 LLaMA 相关请求

### 2. LLaMA TCP 服务模块

- llama_service_tcp.cc: TCP 服务入口，初始化并启动 LlamaTcpServer
- LlamaTcpServer: 通过 popen 执行 LLaMA 的可执行文件，并传递用户输入

### 3. 数据库模块

- DatabaseManager: 单例类，管理数据库连接，提供所有数据库操作的统一接口
- DBConnectionPool: 数据库连接池，高效复用数据库连接
- DBQueryHelper: 提供 SqlConditionBuilder 等辅助工具

### 4. 核心库模块

- 日志 (log/): 高性能异步日志系统 (AsyncLogging)
- 内存 (memory/): memoryPool，用于高效内存管理
- 线程 (Thread.h): 封装 std::thread，提供更易用的线程类
- 事件循环 (EventLoop.h): 基于 Reactor 模式的网络库核心
- 网络 (TcpServer.h, TcpConnection.h): 完整的 TCP 网络库
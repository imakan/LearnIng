# 千万访问量的项目，前端需要注意些什么

## 前端

> * 交互优化：请求时间过长，相应间隔过长；
> * 日志埋点：生产环境操作埋点，报错处理；
> * 异地容灾：cdn切换、后端环境、uat灰度；
> * CDN：容灾、备份、切换、迁移、渐进恢复；
> * 减少事故、快速处理
> * 请求方面：客户端请求合并、顺序依赖、权限控制、大数据查询逻辑拆分、请求性能优化、减少服务器宽带和占用时间、迅速响应；
> * 前端解耦、类似后端SOA；
> * 版本控制：前端接口及资源的版本控制；

## 后端

> * 后端相关：负载均衡
> * 自动扩容、监控、报警、降级
> * 页面监控、错误上报、容灾
> * 数据一致性
> * 高并发；
> * 资源共享
> * 版本控制：项目版本、接口版本

## Roast

这个题是一个开放性问题，可以侧重于安全与性能（请求）相关的内容围绕回答。需要再强调项目的类型是2B还是2C，各有侧重，基本上可以拆分为用户环境、前端部署、后端服务三个大点

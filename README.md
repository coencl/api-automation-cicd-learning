# API 自动化测试 + CI/CD 学习项目

## 项目简介

本项目是一个完整的 API 自动化测试 + CI/CD 学习实践，记录从零搭建自动化测试体系的完整过程。  
包含两条 Jenkins 流水线：**dev-pipeline**（开发部署）和 **api-autotest pipeline**（自动化测试），实现代码提交到测试报告的全链路自动化。

## 技术栈

| 类别 | 工具 |
|------|------|
| 测试框架 | pytest + requests |
| 测试报告 | Allure |
| CI/CD | Jenkins |
| 代码仓库 | GitLab.com |
| 被测应用 | Flask |
| 容器化 | Docker |
| 镜像仓库 | 阿里云容器镜像服务 |
| 代码检查 | flake8 |

## 整体架构

```
开发者 push 代码到 dev-project
         │
         ▼
  GitLab Webhook 触发
         │
         ▼
  ┌─── dev-pipeline ───────────────────────────┐
  │  1. 拉取代码（dev-project）                  │
  │  2. 打包 Docker 镜像                        │
  │  3. 推送镜像到阿里云镜像仓库                  │
  │  4. 部署 Flask 容器（flask-app）             │
  │  5. 触发 api-autotest pipeline ──────────┐ │
  └──────────────────────────────────────────│─┘
                                             │
                                             ▼
                          ┌─── api-autotest pipeline ────────────┐
                          │  1. 拉取代码（api-autotest）           │
                          │  2. 安装依赖（pip）                    │
                          │  3. 代码检查（flake8）                 │
                          │  4. 执行测试（pytest × 4 用例）        │
                          │  5. 生成 Allure 报告                  │
                          │  6. 回写状态到 GitLab MR              │
                          │  7. 发送邮件通知                       │
                          └──────────────────────────────────────┘
```

## 两个 GitLab 仓库

| 仓库 | 说明 | 地址 |
|------|------|------|
| dev-project | 被测 Flask 应用 | https://gitlab.com/qc4693945/dev-project |
| api-autotest | 自动化测试脚本 | https://gitlab.com/qc4693945/api-autotest |

两个仓库各有自己的 Jenkinsfile，对应 Jenkins 中的两条独立流水线。

## 测试用例

| 用例 | 方法 | 接口 |
|------|------|------|
| test_get_users | GET | /users |
| test_get_user_by_id | GET | /users/\<id\> |
| test_create_user | POST | /users |
| test_delete_user | DELETE | /users/\<id\> |

4 个用例，全部通过（4 passed in 4.48s）。

## 已实现功能

- ✅ pytest + requests 接口自动化测试框架
- ✅ Allure 测试报告生成与发布
- ✅ Jenkins dev-pipeline（代码构建 + Docker 打包 + 部署）
- ✅ Jenkins api-autotest pipeline（代码检查 + 测试执行 + 报告）
- ✅ GitLab Webhook 自动触发构建
- ✅ dev-pipeline 部署完成后自动触发测试（下游触发）
- ✅ 构建结果回写 GitLab MR（红绿状态）
- ✅ 邮件通知（测试通过/失败）
- ✅ 质量门禁：flake8 代码规范检查（max-line-length=120）
- ✅ Docker 打包镜像 + 推送阿里云镜像仓库
- ✅ Docker 容器化部署 Flask 应用

## 环境信息

| 环境 | 配置 |
|------|------|
| 本地开发 | Mac Mini，Python 3.13，VSCode |
| 云服务器 | Ubuntu 22.04，3.4GB RAM |
| Jenkins | Docker 容器，端口 8080 |
| Flask 应用 | Docker 容器（flask-app），端口 5000 |
| GitLab | gitlab.com，cloud-hosted |
| 邮件 | SMTP smtp.qq.com:465 |

## 流水线触发方式

| 触发方式 | 触发哪条流水线 |
|---------|--------------|
| push 到 dev-project main 分支 | dev-pipeline |
| push 到 api-autotest main 分支 | api-autotest pipeline |
| dev-pipeline 部署成功后 | api-autotest pipeline（下游触发）|
| Jenkins 手动点击"立即构建" | 任意一条（手动触发）|

## 质量门禁

| 门禁项 | 工具 | 规则 |
|--------|------|------|
| 代码规范 | flake8 | max-line-length=120，检查 tests/ 目录 |
| 测试通过率 | pytest | 100% 通过才算成功 |

## 学习过程中遇到的问题

**1. flake8 代码规范问题**
- 问题：测试文件不符合 PEP 8 规范导致 CI 失败
- 解决：函数前加 2 个空行，文件末尾加换行符，删除未使用的 import

**2. GitLab Connection 配置**
- 问题：Jenkins 需要回写构建状态到 GitLab MR
- 解决：安装 GitLab Plugin，使用 API Token 建立连接，Jenkinsfile 加入 `updateGitlabCommitStatus`

**3. Jenkins 容器访问宿主机 Docker**
- 问题：Jenkins 运行在容器内，需要执行 docker 命令打包镜像
- 解决：启动 Jenkins 容器时挂载 `-v /var/run/docker.sock:/var/run/docker.sock`

**4. 服务器重启后 Docker 权限丢失**
- 问题：重启后 Jenkins 容器无法执行 docker 命令
- 解决：每次重启后执行 `chmod 666 /var/run/docker.sock`

## 待完善

- ⬜ 代码覆盖率门禁（pytest-cov，目标 ≥ 80%）
- ⬜ 性能测试（Locust）

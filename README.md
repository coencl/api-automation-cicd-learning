# api-automation-cicd-learning
API自动化测试 + CI/CD 完整学习项目

# API 自动化测试 + CI/CD 学习项目

## 项目简介

本项目是一个完整的 API 自动化测试 + CI/CD 学习实践，记录从零搭建自动化测试体系的完整过程。

## 技术栈

| 类别 | 工具 |
|------|------|
| 测试框架 | pytest + requests |
| 测试报告 | Allure |
| CI/CD | Jenkins |
| 代码仓库 | GitLab |
| 被测应用 | Flask |
| 代码检查 | flake8 |

## 架构图
本地开发
↓ git push
GitLab（代码仓库）
↓ Webhook 触发
Jenkins（云服务器）
↓
├── 代码检查（flake8）
├── 执行测试（pytest）
├── 生成报告（Allure）
└── 发送邮件通知
↓
GitLab MR 显示红绿状态

## 已实现功能

- ✅ pytest + requests 接口自动化测试框架
- ✅ Allure 测试报告生成
- ✅ Jenkins CI/CD 流水线
- ✅ GitLab Webhook 自动触发构建
- ✅ 构建结果回写 GitLab MR（红绿状态）
- ✅ 邮件通知（测试通过/失败）
- ✅ 质量门禁：flake8 代码规范检查
- ✅ 分支策略 + MR 流程实践

## 待完成

- ⬜ 代码覆盖率门禁（pytest-cov）
- ⬜ Docker 打包部署
- ⬜ 性能测试

## 相关仓库

- 测试项目：https://gitlab.com/qc4693945/api-autotest
- 被测应用：https://gitlab.com/qc4693945/dev-project

## 学习记录

### 遇到的问题和解决方法

**1. flake8 代码规范问题**
- 问题：测试文件不符合 PEP 8 规范导致 CI 失败
- 解决：函数前加 2 个空行，文件末尾加换行符，删除未使用的 import

**2. GitLab Connection 配置**
- Jenkins 需要安装 GitLab Plugin
- 使用 GitLab API Token 建立连接
- Jenkinsfile 加入 `updateGitlabCommitStatus` 回写构建状态

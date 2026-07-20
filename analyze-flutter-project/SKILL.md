---
name: analyze-flutter-project
description: 对 Flutter 项目进行系统性代码分析，生成结构化的 Project_Info.md 文档。当用户说「帮我分析这个 Flutter 项目」、「分析这个项目」、「生成项目信息文档」、「写 Project_Info.md」、「看看这个 Flutter 项目的架构」或任何涉及 Flutter 项目代码分析、文档生成的请求时，必须使用本 skill。即使只是说「看看这个项目结构」或「这个项目用了什么技术」也应当触发。本 skill 只读不写，不会修改任何项目代码。
---

# Flutter 项目代码分析 → Project_Info.md

只读分析，不修改代码。

## 铁律

1. **只写亲眼看到的** — 每个结论必须有代码依据。不存在「大概率用了 XXX」。
2. **不确定就问** — 停下来问用户，不猜不推测。
3. **没有就是没有** — 模块未使用就写「未使用」或跳过，不编造。

## 分析流程

### 1. 项目结构概览

浏览整体目录，识别：
- `lib/` 顶层结构、各子目录职责
- `pubspec.yaml`（名称、描述、版本、`environment` 段）
- `android/app/build.gradle(.kts)`（minSdk / targetSdk）
- `ios/Podfile`（iOS 最低版本）
- `main.dart`

#### 1.1 大型项目（≥50文件或≥15子目录）

最多读 **4个文件** 覆盖全部信息，读完即停：

1. `pubspec.yaml` → 技术栈、依赖、资源、环境配置
2. `main.dart` imports → 架构分层
3. 路由文件 → 所有页面和模块
4. 任选 **3个** 不同模块的实现文件验证细节

此外固定读取 `build.gradle(.kts)` 和 `Podfile` 的环境配置行（不计入4文件限额）。

读完就够写文档了。不写中间草稿，不确定的跳过统一问。未覆盖模块告知用户。

### 2. 逐项分析

**约束：** 每条结论标注 `(文件路径:行号)`

#### 2.1 状态管理
- 方案（Provider / Riverpod / Bloc / GetX / GetIt 等）
- 数据→UI 更新机制
- 组织结构（Provider 树、Bloc 分布等）

#### 2.2 网络请求
- 库（Dio / http 等）
- 拦截器（日志注入、token 注入、错误拦截）
- 错误处理（全局处理、重试、超时）
- API 基地址配置

#### 2.3 路由
- 方案（Navigator 2.0 / GoRouter / GetX 等）
- 注册方式（路由表、注解生成）
- 跳转模式（命名路由、声明式路由）

#### 2.4 主题
- 定义方式（ThemeData、自定义 Theme 扩展）
- 色值定义位置
- 深色/浅色模式支持

#### 2.5 多语言
- 方案（flutter_localizations / intl / 自定义）
- 文案文件位置和格式
- 切换机制

#### 2.6 工具类
- 工具函数（时间/数据格式化等）
- Mixin / Extension
- 通用 Widget 封装
- build_runner 代码生成器配置

#### 2.7 第三方库
- 从 `pubspec.yaml` 提取依赖
- 重点关注功能库，说明用途
- 依赖存在但已读文件范围内未引用 → 记录并问用户

#### 2.8 代码规范
- `analysis_options.yaml` 中的 lint 规则配置
- 使用的 lint 包（flutter_lints / very_good_analysis / custom_lint 等）
- exclude 规则

#### 2.9 Assets 资源
- `pubspec.yaml` 中 `flutter/assets` 注册的目录和文件
- 资源目录结构（images/ fonts/ lottie/ audio/ 等）
- 引用方式（`Image.asset` 字符串路径 vs 常量类/枚举）
- 启动图/图标管理工具（flutter_native_splash、flutter_launcher_icons 等）
- 字体配置（family、weight、style）
- 资源生成工具（flutter_gen、assets_generator 等）

### 3. 内容审查与确认

逐项向用户确认理解是否正确（已分析清楚，只是确认）：
- 把握大的简要带过
- 不确定的明确告知「不确定什么」
- 用户纠正后更新
- 全部确认后生成文档

## 输出

- 文件：`Project_Info.md`（项目根目录）
- 中文 Markdown，仅文件内容本身，不附带分析过程

## 文档模板

```markdown
# {项目名称} 项目信息

## 基本信息
- 项目名称： | 描述： | 版本号：

## 环境信息
- Dart SDK：{约束}（`pubspec.yaml:environment`）
- Android minSdk：{值}（`android/app/build.gradle:xx`）
- Android targetSdk：{值}
- iOS 最低版本：{值}（`ios/Podfile:xx`）

## 项目结构
{目录概览，标注关键目录来源位置}

## Assets 资源
- 注册：{文件/目录列表}（`pubspec.yaml:xx`）
- 目录结构：{说明}
- 引用方式：{方式}（示例 `lib/...dart:xx`）
- 字体：{配置}（`pubspec.yaml:xx`）

## 主题
- 定义：{方式}（`lib/theme/...dart:xx`）
- 色值：{位置}（`lib/theme/...dart:xx`）
- 深色/浅色：{是否支持}

## 多语言
- 方案：{方案}（`lib/l10n/...arb:xx`）
- 切换：{机制}（`lib/services/...dart:xx`）

## 路由
- 方案：{方案}（`lib/router/...dart:xx`）
- 注册：{方式}（`lib/router/...dart:xx`）

## 状态管理
- 方案：{方案}（`lib/providers/...dart:xx`）
- 更新机制：{机制}
- 组织：{文件分布}

## 网络请求
- 库：{库}（`lib/services/...dart:xx`）
- 拦截器：{配置}（`lib/services/...dart:xx`）
- 错误处理：{方式}（`lib/services/...dart:xx`）
- API 基地址：{地址}（`lib/config/...dart:xx`）

## 常用工具类
- 工具函数：{说明}（`lib/utils/...dart:xx`）
- Mixin/Extension：{说明}（`lib/extensions/...dart:xx`）
- 通用 Widget：{说明}（`lib/widgets/...dart:xx`）

## 第三方库
{库名} — {用途}（`pubspec.yaml:xx`，使用示例 `lib/...dart:xx`）

## 代码规范
- lint 包：{包}（`analysis_options.yaml:xx`）
- 自定义规则：{列表}
- exclude：{路径}
```

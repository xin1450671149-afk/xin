# ai-cms-豆包大模型集成-集成one-api标准
基于轻量级PHP语言开发的CMS-AI内容管理系统后端，集成了one-api配置模型接口，豆包模型接口，适用于喜欢开发前端集成调用的用户，开源后端，前端UI用户自行设计开发，前端模板进行集成到后端接口。
后台仪表盘首页
<img width="3364" height="1822" alt="image" src="https://github.com/user-attachments/assets/d937c2e8-059b-441e-8497-97542f9226a4" />
模型配置页面
<img width="3420" height="1844" alt="image" src="https://github.com/user-attachments/assets/0bd0ba2a-13fe-4461-865e-61c52210345e" />
<img width="3260" height="1650" alt="image" src="https://github.com/user-attachments/assets/9a8a9963-f87b-4d92-a5dd-73c0dc6ccd1f" />

宝塔面板部署指南（BT Panel）

本文档指导在宝塔面板（BT Panel）将本 CMS 项目部署到生产环境。

## 环境要求
- Linux 服务器（CentOS/Ubuntu 等）已安装宝塔面板
- 软件版本建议：
  - Nginx 1.20+ 或 Apache 2.4+
  - PHP 8.1+（启用 `pdo_mysql`、`curl`、`mbstring`、`openssl`，可选 `gd`/`imagick`）
  - MySQL 8.0+

## 一、拉取代码与站点路径
1. 在宝塔「网站」中新建站点，域名可用测试域或公网域；选择 `SSL` 是否启用。
2. 将本项目上传至服务器，例如 `/www/wwwroot/cms`。
3. 站点运行目录设置为项目的 `public` 目录：`/www/wwwroot/cms/public`。
4. 默认文档顺序建议：`dashboard.php`、`index.php`、`login.html`。

## 二、数据库配置
1. 在宝塔「数据库」创建数据库（例如：`cms`），记录用户名与密码。
2. 编辑项目 `server/config.php`，填入数据库连接参数：
   ```php
   return [
     'host' => '127.0.0.1',
     'port' => 3306,
     'dbname' => 'cms',
     'user' => 'root',
     'pass' => '你的密码',
     'charset' => 'utf8mb4',
   ];
   ```
3. 首次初始化数据库结构与默认管理员账号（`admin/123456`）：
   - 在宝塔「终端」或 SSH 登录到项目根目录，执行：
     ```bash
     php server/seed.php
     ```
   - 脚本会自动创建/迁移数据表，并写入默认权限与角色。

## 三、权限与目录
- 确保 Web 用户对以下目录具备写入权限：
  - `server/storage/logs/`
  - `server/storage/backups/`
  - 如需上传功能，保证 `public/uploads/` 可写（若不存在可自行创建）。
- 在宝塔中可通过「文件」面板将上述目录权限设为 `755` 或 `775`，并确保属主/属组与 PHP 运行用户一致。

## 四、Nginx/Apache 配置要点
### Nginx
- 站点根目录：`/www/wwwroot/cms/public`
- 默认文档：`dashboard.php index.php login.html`
- 常规 PHP 解析按宝塔默认配置即可；若需要伪静态，可添加：
  ```nginx
  location / {
    try_files $uri $uri/ /index.php?$query_string;
  }
  ```
  注：本项目以页面脚本为主，非必须；保留默认也可正常访问。

### Apache
- 站点根目录指向 `public`，启用 PHP；无需额外 Rewrite。

## 五、上线后验证
1. 访问 `https://你的域名/dashboard.php`，确认首页数据正常。
2. 访问 `https://你的域名/admin/users.php`，验证用户列表、编辑弹窗、保存功能。
3. 若报错提示缺少列（如 `points`/`membership_level`），请再次执行：
   ```bash
   php server/seed.php
   ```
   若无法使用命令行，可在配置文件 `server/config.php` 设置 `init_token`，然后访问：
   `https://你的域名/admin/init.php?token=你的令牌` 触发初始化（完成后请将 `init_token` 置空或删除）。

## 六、安全与优化建议
- 立即修改默认管理员密码，并创建新的管理员账号用于日常维护。
- 在宝塔中开启 SSL，并强制跳转 HTTPS。
- 备份策略：
  - 使用宝塔计划任务每日执行：`php server/seed.php`（可用于轻量迁移检查），并定期备份数据库与 `server/storage/backups/`。
- 若启用 AI 相关模块，请在「系统配置」页面中正确填写模型接口配置与密钥。

## 七、常见问题
- 数据库连接失败：检查 `server/config.php` 与宝塔数据库信息是否一致，MySQL 端口是否对内开放。
- 页面 403：登录账号权限不足，确保绑定了包含 `manage_users` 等权限的角色（默认 `super_admin`）。
备注：
- 业余开发者一枚，本系统为开源免费，如需定制商业版，可联系开发者微信497117223或者qq：1450671149
- 

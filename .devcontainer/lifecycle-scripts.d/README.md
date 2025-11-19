# DevContainer 生命周期脚本目录

这个目录包含了所有 DevContainer 生命周期事件的自定义脚本。

## 目录结构

```
lifecycle-scripts.d/
├── 01-onCreateCommand.d/          # 容器首次创建时执行
├── 02-updateContentCommand.d/     # 容器内容更新时执行（重建容器时）
├── 03-postCreateCommand.d/        # 容器创建完成后执行
├── 04-postStartCommand.d/         # 容器每次启动时执行
└── 05-postAttachCommand.d/        # VS Code 每次附加到容器时执行
```

目录名前缀数字表示 DevContainer 生命周期的执行顺序。

## 使用方法

### 1. 添加自定义脚本

在对应的生命周期目录下创建脚本文件，例如：

```bash
# 在容器启动时执行自定义命令
echo '#!/bin/bash' > 04-postStartCommand.d/10-my-custom-script
echo 'echo "执行我的自定义脚本"' >> 04-postStartCommand.d/10-my-custom-script
chmod +x 04-postStartCommand.d/10-my-custom-script
```

### 2. 脚本命名规则

- 脚本文件名应以数字开头（如 `00-`, `10-`, `20-`），用于控制执行顺序
- 数字越小越先执行
- 使用有意义的名称，如 `10-install-dependencies`、`20-setup-database`

### 3. 脚本要求

- 所有脚本必须是可执行的（`chmod +x`）
- 脚本会按照 `run-parts` 的规则执行
- 脚本名称不能包含 `.` 或其他特殊字符（除了开头的数字和 `-`）

### 4. 生命周期事件说明

| 事件 | 触发时机 | 用途 |
|------|---------|------|
| `onCreateCommand` | 容器首次创建时 | 一次性初始化设置 |
| `updateContentCommand` | 容器重建或更新时 | 更新依赖、重新配置 |
| `postCreateCommand` | 容器创建完成后 | 后续配置任务 |
| `postStartCommand` | 容器每次启动时 | 启动服务、检查环境 |
| `postAttachCommand` | VS Code 附加时 | 用户界面相关的设置 |

## 调试

如果脚本执行出错，可以查看 DevContainer 创建日志：
- 在 VS Code 中：View → Output → Dev Containers
- 或者在终端中手动测试脚本：
  ```bash
  /usr/local/bin/run-lifecycle-scripts.sh onCreateCommand
  ```

## 注意事项

- 脚本在容器内以 `usr_vscode` 用户身份执行
- 如果需要 root 权限，请在脚本中使用 `sudo`
- 脚本执行失败会导致容器创建失败（除了 `postAttachCommand`）

# 应用已损坏，请移除废纸篓

请根据以下步骤尝试解决

1. 终端运行 `sudo spctl --master-disable`
2. 前往 [系统偏好设置] -> [安全性与隐私] -> [通用] -> 选中[允许从以下位置下载的 App: 任何来源]


3. 如果 2 完成后依旧失败，在终端运行 `sudo xattr -rd com.apple.quarantine {app 路径}`
例如:  `sudo xattr -rd com.apple.quarantine /Applications/Sketch.app`
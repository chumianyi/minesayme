# MineSayme - 基于 Luanti 引擎的 Android 沙盒游戏

## 项目概述
MineSayme 是基于真实 Luanti (Minetest) 引擎源码构建的 Android 游戏，全中文界面，支持单人世界创建和多人联机。

## 应用规格
- 应用名：MineSayme
- 包名：com.chumian.minesayme
- 架构：arm64-v8a 单架构
- 最低 Android 7.0 (API 24)
- 引擎：Luanti 5.17.0 (C++ + Irrlicht + SDL2)

## 功能特性

### 全中文界面
- 主菜单、设置页、游戏内 HUD 全部中文化
- 内置完整简体中文翻译 (8000+ 行)

### 单人游戏
- 创建世界，支持种子/地形参数
- 创意模式/生存模式切换
- 主持服务器 (局域网联机)

### 多人联机
- 主菜单「联机游戏」入口
- 输入服务端地址 + 端口 + 用户名连接
- 支持服务器列表、收藏夹

### 触摸控制 (设置页可切换，真实生效)
1. **移动方式**：
   - 虚拟摇杆 (默认)
   - 方向键按钮 (D-pad) — `minesayme_dpad_mode`
2. **破坏方块**：
   - 长按清除 (`touch_punch_gesture = long_tap`)
   - 按钮清除 (`touch_interaction_style = buttons_crosshair`)
3. **放置方块**：按钮放置
4. **准心模式**：
   - 准心模式 (`minesayme_crosshair_mode = true`)
   - 非准心模式 (`minesayme_crosshair_mode = false`)
5. 全屏幕触摸响应，控制按钮半透明浮层

### 控制设置存储
- 所有触摸控制选项存储在 Minetest 配置文件中
- 设置变更即时生效

## 构建方法

### 方法一：GitHub Actions (推荐)
1. 将本项目推送到 GitHub 仓库
2. 触发 `.github/workflows/minesayme_build.yml` 工作流
3. 工作流自动完成：安装 JDK 17 + Android SDK + NDK → 编译 → 签名 → 产出 APK
4. 在 Actions 页面下载 artifact

### 方法二：本地构建
```bash
# 环境要求：JDK 17, Android SDK (platform-34, build-tools 34.0.0, NDK 29.0.14206865), gettext
cd android
echo "sdk.dir=/path/to/android-sdk" > local.properties
echo "keystore=/path/to/keystore" >> local.properties
echo "keystore.password=xxx" >> local.properties
echo "key=xxx" >> local.properties
echo "key.password=xxx" >> local.properties
./gradlew app:assembleRelease
# APK 输出：android/app/build/outputs/apk/release/app-arm64-v8a-release-unsigned.apk
```

## 源码修改清单

### Android 层
- `android/app/build.gradle`：包名、minSdk 24、单架构 arm64-v8a
- `android/native/build.gradle`：单架构、CMAKE_TRY_COMPILE_TARGET_TYPE 修复
- `AndroidManifest.xml`：包名、FileProvider authority
- `java/com/chumian/minesayme/`：包名迁移
- `res/values/strings.xml`：全中文 + MineSayme 品牌

### 引擎 C++ 层
- `src/gui/touchscreenlayout.h/cpp`：新增 D-pad 按钮枚举、名称、纹理
- `src/gui/touchcontrols.h/cpp`：
  - D-pad 方向键移动模式实现
  - `minesayme_dpad_mode` 开关
  - `minesayme_crosshair_mode` 准心模式控制
  - 方向按钮按下/释放事件处理
- `src/defaultsettings.cpp`：新增默认设置项

### Lua / 配置层
- `builtin/settingtypes.txt`：新增触摸控制设置项
- `po/zh_CN/luanti.po`：新增中文翻译
- `textures/base/pack/dpad_{up,down,left,right}.png`：方向键纹理

## 服务端部署 (后续)
客户端已具备连接功能，服务端可使用标准 Luanti/Minetest 服务端：
```bash
# 下载 Luanti 服务端
./luanti --server --worldname MyWorld
```

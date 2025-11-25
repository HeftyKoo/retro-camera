# 相纸时光机·复古拍立得 (Retro Camera)

一个基于 Uni-app 和 Vue 3 开发的复古拍立得相机模拟器小程序。复刻 X 上爆火的复古拍立得应用。 https://x.com/ann_nnng/status/1991079810882265254 

Powered by Copilot + Gemini3 + ChatGPT-coder-5.1

## 在线体验

<img src="./qrcode.jpg" alt="相纸时光机" title="相纸时光机" width="260" />

## Prompt

使用了宝玉的 [prompt](https://x.com/dotey/status/1991340906646036959)，做了一点点微调。

```text
Please generate a single-file React application for a "Retro Camera Web App" with the following specifications:

1. Visual Layout & Container Strategy
- Theme: Retro aesthetic with a "Handwritten" font style for all text.
- Title: "Bao Retro Camera" displayed at the top center.
- Instructions: Display usage instructions at the bottom right.
- Main Camera Container: 
    - Create a fixed wrapper `div` that acts as the parent for the camera image, viewfinder, shutter button, and photo ejection slot.
    - Positioning: This container must be fixed at exactly 64px from the bottom and 64px from the left of the viewport (`bottom: 64px; left: 64px;`).
    - Dimensions: Width 450px, Height 450px.
    - Z-index: 20
    - All subsequent positioning coordinates (percentages) for camera elements are relative to this container.
- Background Image within Container:
    - Image Source: `https://s.baoyu.io/images/retro-camera.webp`
    - Size: 100% width and height of the container.
    - Position: Left 0, Bottom 0

2. Camera Functionality (The Viewfinder)
- Access the user's webcam.
- Viewfinder Position: The live video feed must be masked to a circle and positioned exactly over the camera lens.
- CSS for Video (Relative to Container): `bottom: 32%; left: 62%; transform: translateX(-50%); width: 27%; height: 27%; border-radius: 50%;z-index: 30`.
- Layering: The video must sit *above* the camera base image visually but within the container.

3. Shutter & Photo Interaction
- Shutter Button: Create an invisible clickable area over the camera's button.
- CSS for Button (Relative to Container): `bottom: 40%; left: 18%; width: 11%; height: 11%; cursor: pointer;z-index: 30`.
- Action: When clicked, play a shutter sound effect and trigger the "Photo Ejection" animation.

4. Photo Ejection & Development Animation
- Aspect Ratio: The generated Polaroid-style photo card must strictly follow a 3:4 portrait aspect ratio (Vertical).
- Ejection Animation: The photo paper slides UPWARDS (negative Y) from behind the camera body.
- Layering: The photo must appear to emerge from *inside* the camera (start with z-index(10) lower than camera body, then animate out).
- Ejection Container Origin CSS: `transform: translateX(-50%); top: 0; left: 50%; width: 35%;height: 100%;` (start position relative to the camera container).
- Ejection Container anmiation position from: ` translateY(0)` to ` translateY(-40%)`
- Developing Effect: Once the photo is taken, the image on the paper should transition from white/blurry to clear/sharp over a few seconds.

5. Drag & Drop "Photo Wall"
- Interaction: The user must be able to drag the ejected photo *out* of the camera slot and drop it anywhere on the rest of the screen (the "Photo Wall").
- Drag Handle: The entire Polaroid card (the white frame and the photo) must be interactive. The user should be able to click and drag from any part of the card to move it.
- Logic: While developing, the photo is attached to the camera container. Once dragged, it becomes absolutely positioned on the main screen body.
- Freedom: Once on the wall, photos can be dragged and repositioned freely.

6. AI Integration & Text Interactions
- Caption Generation: Use the Gemini Flash API to analyze the captured image content.
- Prompt: Generate a warm, short blessing or nice comment based on the photo content.
- Language Requirement: The generated text language must match the user's browser language.
- Footer Layout: The bottom of the Polaroid paper (below the image) should display the current date and the AI-generated text.
- Text Interaction & Icons:
    - When hovering specifically over the text area, display two small icons:
        1. Pencil Icon: Enters edit mode.
        2. Refresh Icon: Re-triggers the AI generation for that specific photo to get a new caption.
- Editing Logic:
    - Trigger: Edit mode can be entered by clicking the Pencil icon OR by double-clicking the text itself.
    - Behavior: When editing, replace the text display with an input/textarea showing the raw text.
    - Controls: Pressing Enter saves the changes. Pressing Esc cancels the edit and reverts to the previous text.

7. Photo Controls (Card Level)
- Hover Actions: When hovering over a developed photo card on the wall (general hover), show a small toolbar at the top of the photo with:
    - Download Button: When clicked, this must render the entire Polaroid card (including the white frame, the photo, the date, and the handwritten caption) into a single image file and download it. (Recommended: use `html2canvas` or similar logic).
    - Delete Button: Removes the photo from the screen.

Technical Stack
- uni-app + vue3 + typescript.
- canvas.
```

## ✨ 特性 (Features)

*   **📸 沉浸式拍摄体验**: 还原真实的快门声音（计划中）、取景框和照片吐出动画。
*   **🎞️ 拟真显影效果**: 照片吐出后会像真实拍立得一样慢慢显影，从模糊变清晰。
*   **🧱 互动照片墙**: 拍摄的照片会自动“贴”在墙上，支持自由拖拽布局，打造个性化照片墙。
*   **🎨 多样化相纸**: 提供多种相纸边框风格，包括经典白、相纸纹、复古黄和纹理灰。
*   **✍️ 个性化记录**:
    *   自动记录拍摄日期和时间。
    *   支持点击照片添加和编辑文字备注（Caption）。
    *   支持长按编辑，点击空白处保存。
*   **📤 分享与保存**:
    *   **高清保存**: 利用 Canvas 生成高清拍立得风格图片保存到手机相册。
    *   **好友分享**: 支持分享单张照片给微信好友，封面即为当前照片。
    *   **朋友圈分享**: 支持分享小程序到朋友圈。

## 🛠️ 技术栈 (Tech Stack)

*   **框架**: [Uni-app](https://uniapp.dcloud.net.cn/) (Vue 3 版本)
*   **语言**: TypeScript
*   **样式**: SCSS / CSS
*   **构建工具**: Vite

## 🚀 快速开始 (Getting Started)

### 前置要求

*   Node.js (推荐 v18+)
*   pnpm (推荐) 或 npm/yarn
*   微信开发者工具 (用于运行和调试小程序)

### 安装依赖

```bash
pnpm install
```

### 运行开发环境

运行微信小程序开发模式：

```bash
pnpm run dev:mp-weixin
```

运行成功后，打开微信开发者工具，导入 `dist/dev/mp-weixin` 目录即可预览。

## 📂 项目结构

*   `src/pages/index/index.vue`: 核心页面，包含相机逻辑、动画、照片墙和交互代码。
*   `src/static`: 静态资源目录，存放背景图、相纸边框等图片。
*   `src/manifest.json`: 应用配置文件，包含权限声明（如相机、相册权限）。

## 📝 待办事项 (Todo)

*   [ ] 添加快门音效
*   [ ] 更多滤镜效果
*   [ ] 优化长屏手机适配

---

Enjoy your retro photography journey! 📷

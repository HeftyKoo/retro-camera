<template>
  <view class="page-container" @touchmove="handleDragMove" @touchend="handleDragEnd">
    <!-- Title -->
    <text class="app-title">相纸时光机</text>

    <!-- Photo Wall (Rendered Photos) -->
    <view v-for="photo in photos" :key="photo.id" class="photo-card" :style="{
      left: photo.x + 'px',
      top: photo.y + 'px',
      zIndex: photo.zIndex,
      transform: `rotate(${photo.rotation}deg)`
    }" @touchstart="(e) => handleDragStart(e, photo)">
      <image v-if="photo.frameSrc" class="card-bg-image" :src="photo.frameSrc" mode="scaleToFill" />
      <!-- Toolbar (Hover/Active) -->
      <view class="card-toolbar">
        <button class="tool-btn share-btn" open-type="share" :data-photo-id="photo.id" @tap.stop>
          <image class="icon-img"
            src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjNWM0MDMzIiBzdHJva2Utd2lkdGg9IjIiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCI+PGNpcmNsZSBjeD0iMTgiIGN5PSI1IiByPSIzIi8+PGNpcmNsZSBjeD0iNiIgY3k9IjEyIiByPSIzIi8+PGNpcmNsZSBjeD0iMTgiIGN5PSIxOSIgcj0iMyIvPjxsaW5lIHgxPSI4LjU5IiB5MT0iMTMuNTEiIHgyPSIxNS40MiIgeTI9IjE3LjQ5Ii8+PGxpbmUgeDE9IjE1LjQxIiB5MT0iNi41MSIgeDI9IjguNTkiIHkyPSIxMC40OSIvPjwvc3ZnPg=="
            mode="aspectFit" />
        </button>
        <view class="tool-btn" @tap.stop="downloadPhoto(photo)">
          <image class="icon-img"
            src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjNWM0MDMzIiBzdHJva2Utd2lkdGg9IjIuNSIgc3Ryb2tlLWxpbmVjYXA9InJvdW5kIiBzdHJva2UtbGluZWpvaW49InJvdW5kIj48cGF0aCBkPSJNMTIgNXYxNE0xOSAxMmwtNyA3LTctNyIvPjwvc3ZnPg=="
            mode="aspectFit" />
        </view>
        <view class="tool-btn" @tap.stop="deletePhoto(photo.id)">
          <image class="icon-img"
            src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjNWM0MDMzIiBzdHJva2Utd2lkdGg9IjIiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCI+PHBvbHlsaW5lIHBvaW50cz0iMyA2IDUgNiAyMSA2Ii8+PHBhdGggZD0iTTE5IDZ2MTRhMiAyIDAgMCAxLTIgMmgtNGEyIDIgMCAwIDEtMi0yVjZtMyAwVjRhMiAyIDAgMCAxIDItMmg0YTIgMiAwIDAgMSAyIDJ2MiIvPjxsaW5lIHgxPSIxMCIgeTE9IjExIiB4Mj0iMTAiIHkyPSIxNyIvPjxsaW5lIHgxPSIxNCIgeTE9IjExIiB4Mj0iMTQiIHkyPSIxNyIvPjwvc3ZnPg=="
            mode="aspectFit" />
        </view>
      </view>

      <!-- Photo Content -->
      <view class="photo-frame">
        <image :src="photo.src" class="photo-img" :class="{ 'developed': photo.isDeveloped }" mode="aspectFill"></image>
        <view class="photo-footer">
          <text class="date-text" :style="{ color: photo.dateColor || '#999' }">{{ photo.dateStr }}</text>

          <!-- Caption Area -->
          <view class="caption-area" @tap.stop>
            <view v-if="photo.isEditing" class="edit-box">
              <textarea v-model="photo.caption" class="caption-input" :focus="true" :adjust-position="false"
                :disable-default-padding="true" auto-height confirm-type="done" @confirm="saveCaption(photo)"
                @blur="saveCaption(photo)" :style="{ color: photo.textColor || '#333' }" />
            </view>
            <view v-else class="caption-display" @longpress="startEdit(photo)" @tap="startEdit(photo)">
              <text class="caption-text" :style="{ color: photo.textColor || '#333' }">{{ photo.caption || '点击输入文字...'
              }}</text>
              <view class="caption-tools">
                <view class="mini-icon-btn" @tap.stop="startEdit(photo)">
                  <image class="mini-icon-img"
                    src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjNWM0MDMzIiBzdHJva2Utd2lkdGg9IjIiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCI+PHBhdGggZD0iTTExIDRINGEyIDIgMCAwIDAtMiAydjE0YTIgMiAwIDAgMCAyIDJoMTRhMiAyIDAgMCAwIDItMnYtNyIvPjxwYXRoIGQ9Ik0xOC41IDIuNWEyLjEyMSAyLjEyMSAwIDAgMSAzIDNMMTIgMTVsLTQgMSAxLTQgOS41LTkuNXoiLz48L3N2Zz4="
                    mode="aspectFit" />
                </view>
              </view>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- Camera Container -->
    <view class="camera-container">
      <!-- Ejecting Photo Animation -->
      <view v-if="ejectingPhoto" class="ejecting-photo" :class="{ 'animating': isAnimating }">
        <image v-if="ejectingPhoto.frameSrc" class="card-bg-image" :src="ejectingPhoto.frameSrc" mode="scaleToFill" />
        <view class="photo-frame">
          <image :src="ejectingPhoto.src" class="photo-img developing" mode="aspectFill"></image>
          <view class="photo-footer">
            <text class="date-text" :style="{ color: ejectingPhoto.dateColor || '#999' }">{{ ejectingPhoto.dateStr
            }}</text>
            <text class="caption-text" :style="{ color: ejectingPhoto.textColor || '#333' }">显影中...</text>
          </view>
        </view>
      </view>

      <!-- Camera Body Background -->
      <image class="camera-bg" :src="cameraBgUrl" mode="aspectFit"></image>

      <!-- Viewfinder (Live Camera) -->
      <camera class="viewfinder" device-position="front" flash="off" @error="onCameraError"></camera>

      <!-- Shutter Button (Invisible Click Area) -->
      <view class="shutter-btn" @tap="takePhoto" hover-class="shutter-pressed"></view>

      <!-- Side Toolbar -->
      <view class="side-toolbar">
        <button class="global-share-btn" open-type="share">
          <text>分享</text>
        </button>
        <view class="frame-select-btn" @tap="openFrameSelector">
          <text>换相纸</text>
        </view>
      </view>
    </view>

    <!-- Instructions -->
    <view class="instructions">
      <text>1. 点击快门拍照\n2. 拖拽照片上墙\n3. 点击文字编辑</text>
    </view>

    <!-- Frame Selector Modal -->
    <view v-if="showFrameSelector" class="modal-mask" @tap="closeFrameSelector">
      <view class="modal-content" @tap.stop>
        <text class="modal-title">选择相纸</text>

        <!-- Preview Area -->
        <view class="frame-preview-container">
          <view class="preview-card">
            <image v-if="previewFrame.src" class="card-bg-image" :src="previewFrame.src" mode="scaleToFill" />
            <view class="preview-inner">
              <view class="preview-img-placeholder"></view>
              <view class="preview-text-lines">
                <view class="line" :style="{ background: previewFrame.textColor || '#ddd' }"></view>
                <view class="line short" :style="{ background: previewFrame.textColor || '#ddd' }"></view>
              </view>
            </view>
          </view>
          <text class="preview-label">{{ previewFrame.name }}</text>
        </view>

        <!-- List -->
        <scroll-view scroll-x class="frame-list">
          <view v-for="frame in frames" :key="frame.id" class="frame-item"
            :class="{ active: previewFrame.id === frame.id }" @tap="selectPreviewFrame(frame)">
            <image v-if="frame.src" :src="frame.src" mode="aspectFill" class="frame-thumb"></image>
            <view v-else class="frame-thumb default-thumb"></view>
            <text class="frame-name">{{ frame.name }}</text>
          </view>
        </scroll-view>

        <button class="confirm-btn" @tap="confirmFrameSelection">确定使用</button>
      </view>
    </view>

    <!-- Hidden Canvas for Export -->
    <canvas canvas-id="exportCanvas" class="export-canvas"
      :style="{ width: canvasWidth + 'px', height: canvasHeight + 'px' }"></canvas>
  </view>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted, getCurrentInstance } from 'vue';
import { onShareAppMessage, onShareTimeline } from '@dcloudio/uni-app';

const cameraBgUrl = '/static/retro-camera.png';
const frame1 = '/static/frames/frame-1.jpg';
const frame2 = '/static/frames/frame-2.jpg';
const frame3 = '/static/frames/frame-3.jpg';

// Interfaces
interface Frame {
  id: string;
  name: string;
  src: string;
  textColor: string;
  dateColor: string;
}

interface Photo {
  id: number | string;
  src: string;
  dateStr: string;
  caption: string;
  isDeveloped: boolean;
  isEditing: boolean;
  rotation: number;
  frameSrc: string;
  textColor: string;
  dateColor: string;
  x: number;
  y: number;
  zIndex: number;
}

// State
const photos = ref<Photo[]>([]);
const ejectingPhoto = ref<Photo | null>(null);
const isAnimating = ref(false);
const dragTarget = ref<Photo | null>(null);
const dragOffset = reactive({ x: 0, y: 0 });
const maxZIndex = ref(100);

// Frames
const frames: Frame[] = [
  { id: 'classic', name: '经典白', src: '', textColor: '#333333', dateColor: '#999999' },
  { id: 'paper', name: '相纸纹', src: frame1, textColor: '#333333', dateColor: '#999999' },
  { id: 'vintage', name: '复古黄', src: frame2, textColor: '#5c4033', dateColor: '#8b7355' },
  { id: 'texture', name: '纹理灰', src: frame3, textColor: '#FFFFFF', dateColor: '#DDDDDD' }
];
const currentFrame = ref<Frame>(frames[0]);
const showFrameSelector = ref(false);
const previewFrame = ref<Frame>(frames[0]);

// Canvas settings
const canvasWidth = ref(300);
const canvasHeight = ref(400);

// Instance for Canvas
const instance = getCurrentInstance();

// Camera Context
let cameraCtx: UniApp.CameraContext | null = null;

onMounted(() => {
  cameraCtx = uni.createCameraContext();
});

// Frame Selection Logic
const openFrameSelector = () => {
  previewFrame.value = currentFrame.value;
  showFrameSelector.value = true;
};

const closeFrameSelector = () => {
  showFrameSelector.value = false;
};

const selectPreviewFrame = (frame: Frame) => {
  previewFrame.value = frame;
};

const confirmFrameSelection = () => {
  currentFrame.value = previewFrame.value;
  closeFrameSelector();
};

const ensureAbsolutePath = (path: string) => {
  if (!path) return path;
  return path.startsWith('/') ? path : `/${path}`;
};

// 1. Take Photo
const takePhoto = () => {
  if (isAnimating.value) return; // Prevent double click

  // Play Shutter Sound (Optional: Add audio context here)

  if (cameraCtx) {
    cameraCtx.takePhoto({
      quality: 'high',
      success: (res) => {
        startEjection(res.tempImagePath);
      },
      fail: (err) => {
        console.error('Failed to take photo', err);
        uni.showToast({ title: 'Camera Error', icon: 'none' });
      }
    });
  }
};

// 2. Ejection Animation
const startEjection = (imagePath: string) => {
  const now = new Date();
  const dateStr = now.toLocaleDateString() + ' ' + now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });

  const newPhoto: Photo = {
    id: Date.now(),
    src: imagePath,
    dateStr: dateStr,
    caption: '',
    isDeveloped: false,
    isEditing: false,
    rotation: (Math.random() - 0.5) * 10, // Random slight rotation
    frameSrc: currentFrame.value.src, // Store current frame choice
    textColor: currentFrame.value.textColor,
    dateColor: currentFrame.value.dateColor,
    x: 0, // Initial placeholder
    y: 0, // Initial placeholder
    zIndex: 0 // Initial placeholder
  };

  ejectingPhoto.value = newPhoto;

  // Trigger Animation
  setTimeout(() => {
    isAnimating.value = true;
  }, 50);

  // Animation End -> Move to Wall
  setTimeout(() => {
    finishEjection(newPhoto);
  }, 2000); // Match CSS transition duration
};

const getWindowMetrics = () => {
  if (typeof uni.getWindowInfo === 'function') {
    const { windowWidth, windowHeight } = uni.getWindowInfo();
    return { windowWidth, windowHeight };
  }
  const legacyInfo = uni.getSystemInfoSync();
  return {
    windowWidth: legacyInfo.windowWidth,
    windowHeight: legacyInfo.windowHeight
  };
};

const computeDefaultPhotoPosition = () => {
  const { windowWidth, windowHeight } = getWindowMetrics();
  const photoWidth = 160;
  const photoHeight = 260;
  const cameraTopFromBottomPx = (770 / 750) * windowWidth;

  return {
    left: (windowWidth - photoWidth) / 2,
    top: windowHeight - cameraTopFromBottomPx - photoHeight - 20
  };
};

const placePhotoOnWall = (photo: Photo, position: { left: number; top: number }) => {
  photo.x = Math.round(position.left);
  photo.y = Math.round(position.top);
  photo.zIndex = ++maxZIndex.value;

  photos.value.push(photo);
  ejectingPhoto.value = null;
  isAnimating.value = false;

  setTimeout(() => {
    const target = photos.value.find(p => p.id === photo.id);
    if (target) target.isDeveloped = true;
  }, 100);
};

const finishEjection = (photo: Photo) => {
  // Always use the computed safe position to ensure it clears the camera
  placePhotoOnWall(photo, computeDefaultPhotoPosition());
};

// 3. Drag & Drop Logic
const handleDragStart = (e: TouchEvent, photo: Photo) => {
  dragTarget.value = photo;
  photo.zIndex = ++maxZIndex.value; // Bring to front

  const touch = e.touches[0];
  dragOffset.x = touch.clientX - photo.x;
  dragOffset.y = touch.clientY - photo.y;
};

const handleDragMove = (e: TouchEvent) => {
  if (!dragTarget.value) return;

  const touch = e.touches[0];
  dragTarget.value.x = touch.clientX - dragOffset.x;
  dragTarget.value.y = touch.clientY - dragOffset.y;
};

const handleDragEnd = () => {
  dragTarget.value = null;
};

// 4. AI Caption (Removed)
// const generateCaption = async (photo) => { ... }

// 5. Editing
const startEdit = (photo: Photo) => {
  photo.isEditing = true;
};

const saveCaption = (photo: Photo) => {
  // Delay hiding to prevent "removeTextView:fail" error on real devices
  // when removing a focused native component
  uni.hideKeyboard();
  setTimeout(() => {
    photo.isEditing = false;
  }, 200);
};

// 6. Delete
const deletePhoto = (id: number | string) => {
  photos.value = photos.value.filter(p => p.id !== id);
};

// 7. Download (Canvas)
const downloadPhoto = async (photo: Photo) => {
  uni.showLoading({ title: '保存中...' });

  try {
    const ctx = uni.createCanvasContext('exportCanvas', instance);

    // Draw Background (Frame)
    if (photo.frameSrc) {
      try {
        // Ensure we get a drawable path for the static resource
        const frameRes = await uni.getImageInfo({ src: photo.frameSrc });
        const drawablePath = ensureAbsolutePath(frameRes.path) || photo.frameSrc;
        ctx.drawImage(drawablePath, 0, 0, 300, 400);
      } catch (e) {
        console.error('Frame load error:', e);
        // Fallback to white if frame fails
        ctx.setFillStyle('#ffffff');
        ctx.fillRect(0, 0, 300, 400);
      }
    } else {
      ctx.setFillStyle('#ffffff');
      ctx.fillRect(0, 0, 300, 400);
    }

    // Draw Photo with Aspect Fill
    // Target dimensions on canvas
    const targetX = 20;
    const targetY = 20;
    const targetW = 260;
    const targetH = 290;

    try {
      // Get source image info for dimensions
      const srcRes = await uni.getImageInfo({ src: photo.src });
      const srcW = srcRes.width;
      const srcH = srcRes.height;

      // Calculate Aspect Fill Crop
      const targetRatio = targetW / targetH;
      const srcRatio = srcW / srcH;

      let sx, sy, sw, sh;

      if (srcRatio > targetRatio) {
        // Source is wider than target: Crop width
        sh = srcH;
        sw = srcH * targetRatio;
        sx = (srcW - sw) / 2;
        sy = 0;
      } else {
        // Source is taller than target: Crop height
        sw = srcW;
        sh = srcW / targetRatio;
        sx = 0;
        sy = (srcH - sh) / 2;
      }

      // Draw cropped image
      ctx.drawImage(photo.src, sx, sy, sw, sh, targetX, targetY, targetW, targetH);

    } catch (e) {
      console.error('Source image load error:', e);
      // Fallback: just draw it stretched if getting info fails (unlikely)
      ctx.drawImage(photo.src, targetX, targetY, targetW, targetH);
    }

    // Draw Text
    // Use font property to set family. Note: 'Brush Script MT' is for English/Numbers. 
    // Chinese will fallback to system default unless a Chinese font is loaded.
    ctx.font = "14px 'Brush Script MT', cursive";
    ctx.setFillStyle(photo.dateColor || '#999999');
    ctx.setTextAlign('right');
    ctx.fillText(photo.dateStr, 280, 330);

    ctx.font = "18px 'Brush Script MT', cursive";
    ctx.setFillStyle(photo.textColor || '#333333');
    ctx.setTextAlign('left');
    // Simple text wrap logic would be needed here for long captions
    ctx.fillText(photo.caption.substring(0, 25) + (photo.caption.length > 25 ? '...' : ''), 20, 360);

    ctx.draw(false, () => {
      uni.canvasToTempFilePath({
        canvasId: 'exportCanvas',
        success: (res) => {
          uni.saveImageToPhotosAlbum({
            filePath: res.tempFilePath,
            success: () => {
              uni.hideLoading();
              uni.showToast({ title: '已保存!', icon: 'success' });
            },
            fail: () => {
              uni.hideLoading();
              uni.showToast({ title: '保存失败', icon: 'none' });
            }
          });
        },
        fail: (err) => {
          uni.hideLoading();
          console.error(err);
        }
      }, instance);
    });
  } catch (err) {
    uni.hideLoading();
    console.error('Download error:', err);
    uni.showToast({ title: '保存出错', icon: 'none' });
  }
};

const onCameraError = (e: unknown) => {
  console.error(e);
  uni.showToast({ title: '相机访问失败', icon: 'none' });
};

// 8. Share
onShareAppMessage((res) => {
  if (res.from === 'button') {
    const target = res.target as { dataset: { photoId: string } };
    const photoId = target.dataset.photoId;
    const photo = photos.value.find(p => p.id === photoId);
    if (photo) {
      return {
        title: '相纸时光机·复古拍立得',
        desc: photo.caption,
        path: '/pages/index/index',
        imageUrl: photo.src
      };
    }
  }
  return {
    title: '相纸时光机·复古拍立得',
    path: '/pages/index/index',
    imageUrl: cameraBgUrl
  };
});

// 9. Share Timeline (Moments)
onShareTimeline(() => {
  return {
    title: '相纸时光机·复古拍立得',
    query: '',
    imageUrl: cameraBgUrl
  };
});

</script>

<style lang="scss">
page {
  height: 100%;
  overflow: hidden;
}

/* Global Page */
.page-container {
  width: 100vw;
  height: 100vh;
  background-color: #f0e6d2;
  /* Vintage paper color */
  position: relative;
  overflow: hidden;
  font-family: 'Brush Script MT', cursive;
}

.app-title {
  position: absolute;
  top: 64px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 24px;
  font-weight: bold;
  color: #5c4033;
  z-index: 10;
}

.instructions {
  position: absolute;
  bottom: 20px;
  right: 20px;
  text-align: right;
  font-size: 12px;
  color: #888;
  z-index: 10;
}

/* Camera Container */
.camera-container {
  position: fixed;
  bottom: 120rpx;
  left: 50%;
  transform: translateX(-50%);
  width: 650rpx;
  height: 650rpx;
  z-index: 500;
  /* Border for debugging if image fails */
  /* border: 1px dashed #ccc; */
}

.camera-bg {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  bottom: 0;
  z-index: 20;
}

.viewfinder {
  position: absolute;
  bottom: 32%;
  left: 62%;
  transform: translateX(-50%);
  width: 27%;
  height: 27%;
  border-radius: 50%;
  z-index: 30;
  overflow: hidden;
}

.shutter-btn {
  position: absolute;
  bottom: 40%;
  left: 18%;
  width: 11%;
  height: 11%;
  z-index: 30;
  cursor: pointer;
  /* background: rgba(255, 0, 0, 0.2); Debugging */
}

.shutter-pressed {
  opacity: 0.5;
}

.side-toolbar {
  position: absolute;
  bottom: 10%;
  right: 0;
  display: flex;
  flex-direction: column;
  gap: 20px;
  /* Space between buttons */
  z-index: 40;
  align-items: flex-end;
}

.frame-select-btn {
  background: rgba(255, 255, 255, 0.8);
  padding: 5px 10px;
  border-radius: 15px;
  font-size: 12px;
  color: #5c4033;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.global-share-btn {
  background: rgba(255, 255, 255, 0.8);
  padding: 5px 10px;
  border-radius: 15px;
  font-size: 12px;
  color: #5c4033;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  line-height: 1.2;
  margin: 0;
}

.global-share-btn::after {
  border: none;
}

/* Ejecting Photo Animation */
.ejecting-photo {
  position: absolute;
  top: 0;
  left: 50%;
  width: 160px;
  min-height: 240px;
  transform: translateX(-50%) translateY(0);
  z-index: 10;
  /* Behind camera body initially */
  transition: transform 2s ease-out;
  background: white;
  padding: 10px 10px 30px 10px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.2);
}

.ejecting-photo.animating {
  transform: translateX(-50%) translateY(-120%);
  /* Slide up */
  z-index: 25;
  /* Pop out visually if needed, or stay behind body until dragged */
}

.card-bg-image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
}

/* Photo Card (Common Styles) */
.photo-card {
  position: absolute;
  width: 160px;
  /* Fixed width for wall photos */
  min-height: 240px;
  /* Allow caption area to grow */
  background: white;
  padding: 10px 10px 30px 10px;
  box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.2);
  display: flex;
  flex-direction: column;
  transition: filter 2s ease;
  box-sizing: border-box;
}

.photo-frame {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  /* Clip content to frame */
  position: relative;
  z-index: 1;
}

.photo-img {
  width: 100%;
  height: 160px;
  /* Square-ish area */
  background-color: #333;
  filter: blur(10px) grayscale(100%);
  transition: filter 3s ease;
  flex-shrink: 0;
  /* Prevent image from shrinking */
  display: block;
  /* Remove inline-block spacing */
}

.photo-img.developed {
  filter: blur(0) grayscale(0%);
}

.photo-footer {
  margin-top: 10px;
  flex: 1;
  display: flex;
  flex-direction: column;
}

.date-text {
  font-size: 10px;
  color: #999;
  text-align: right;
}

.caption-area {
  margin-top: 5px;
  position: relative;
}

.caption-text {
  font-size: 14px;
  color: #333;
  line-height: 1.2;
  font-family: 'Brush Script MT', cursive;
  white-space: pre-wrap;
  word-break: break-word;
}

.caption-tools {
  display: none;
  position: absolute;
  right: 0;
  top: -20px;
  background: rgba(255, 255, 255, 0.9);
  padding: 2px;
  border-radius: 4px;
}

.caption-display:hover .caption-tools {
  display: flex;
  gap: 5px;
}

.mini-icon-btn {
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.mini-icon-img {
  width: 14px;
  height: 14px;
}

.caption-input {
  width: 100%;
  /* min-height: 50px; Removed to prevent jump */
  font-size: 14px;
  /* border-bottom: 1px solid #ccc; Removed border */
  border: none;
  outline: none;
  font-family: 'Brush Script MT', cursive;
  line-height: 1.2;
  /* Match caption-text line-height */
  /* padding-bottom: 4px; Removed padding */
}

/* Card Toolbar */
.card-toolbar {
  position: absolute;
  top: -30px;
  left: 0;
  width: 100%;
  display: none;
  /* Hidden by default */
  justify-content: center;
  gap: 10px;
}

.photo-card:hover .card-toolbar {
  display: flex;
  /* Show on hover - Note: Hover works differently on touch devices */
}

/* For touch devices, we might want to show toolbar on tap or always visible for active card */

.tool-btn {
  background: white;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
}

.share-btn {
  margin: 0;
  padding: 0;
  line-height: 1;
  background: white;
}

.share-btn::after {
  border: none;
}

.icon-img {
  width: 14px;
  height: 14px;
}

/* Export Canvas */
.export-canvas {
  position: fixed;
  left: -9999px;
  top: 0;
}

/* Modal Styles */
.modal-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  width: 80%;
  background: #fff;
  border-radius: 10px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.modal-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 15px;
  color: #333;
}

.frame-preview-container {
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.preview-card {
  width: 160px;
  height: 210px;
  background: white;
  padding: 10px 10px 30px 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  position: relative;
}

.preview-inner {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  position: relative;
  z-index: 1;
}

.preview-img-placeholder {
  width: 100%;
  height: 130px;
  background: #eee;
  margin-bottom: 10px;
}

.preview-text-lines {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.line {
  height: 4px;
  background: #ddd;
  width: 80%;
}

.line.short {
  width: 50%;
}

.preview-label {
  margin-top: 10px;
  font-size: 14px;
  color: #666;
}

.frame-list {
  width: 100%;
  white-space: nowrap;
  margin-bottom: 20px;
  height: 100px;
}

.frame-item {
  display: inline-flex;
  flex-direction: column;
  align-items: center;
  margin-right: 15px;
  opacity: 0.6;
  transition: opacity 0.2s;
}

.frame-item.active {
  opacity: 1;
  font-weight: bold;
}

.frame-thumb {
  width: 60px;
  height: 60px;
  border-radius: 5px;
  border: 2px solid #eee;
  margin-bottom: 5px;
}

.default-thumb {
  background: white;
}

.frame-item.active .frame-thumb {
  border-color: #5c4033;
}

.frame-name {
  font-size: 12px;
  color: #333;
}

.confirm-btn {
  background: #5c4033;
  color: white;
  font-size: 14px;
  padding: 5px 30px;
  border-radius: 20px;
}
</style>

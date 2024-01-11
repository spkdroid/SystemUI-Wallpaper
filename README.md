# SystemUI-Wallpaper

Creating a live wallpaper for Android involves writing a `WallpaperService` that extends `WallpaperService.Engine`. Below is a simple example of a live wallpaper that uses a SystemUI background:

```java
import android.graphics.Canvas;
import android.os.Handler;
import android.service.wallpaper.WallpaperService;
import android.view.SurfaceHolder;

public class SystemUILiveWallpaper extends WallpaperService {

    @Override
    public Engine onCreateEngine() {
        return new SystemUIWallpaperEngine();
    }

    private class SystemUIWallpaperEngine extends Engine {

        private final Handler handler = new Handler();
        private boolean visible = true;

        private final Runnable drawRunnable = new Runnable() {
            @Override
            public void run() {
                draw();
            }
        };

        @Override
        public void onVisibilityChanged(boolean visible) {
            this.visible = visible;
            if (visible) {
                handler.post(drawRunnable);
            } else {
                handler.removeCallbacks(drawRunnable);
            }
        }

        @Override
        public void onSurfaceCreated(SurfaceHolder holder) {
            super.onSurfaceCreated(holder);
            handler.post(drawRunnable);
        }

        private void draw() {
            SurfaceHolder holder = getSurfaceHolder();
            Canvas canvas = null;
            try {
                canvas = holder.lockCanvas();
                if (canvas != null) {
                    // Draw your live wallpaper content here on the canvas
                    // For example, you can draw a background color
                    canvas.drawColor(/*Your Color Here*/);
                }
            } finally {
                if (canvas != null) {
                    holder.unlockCanvasAndPost(canvas);
                }
            }

            handler.removeCallbacks(drawRunnable);
            if (visible) {
                handler.postDelayed(drawRunnable, 1000); // Set your desired frame rate here
            }
        }
    }
}
```

Remember to replace `/*Your Color Here*/` with the color or background image you want to use for your live wallpaper.

After implementing the live wallpaper, you need to declare it in the AndroidManifest.xml file:

```xml
<service
    android:name=".SystemUILiveWallpaper"
    android:label="@string/live_wallpaper_name"
    android:permission="android.permission.BIND_WALLPAPER">
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
    </intent-filter>
    <meta-data
        android:name="android.service.wallpaper"
        android:resource="@xml/live_wallpaper_settings" />
</service>
```

Make sure to create the corresponding `live_wallpaper_settings.xml` file in the `res/xml` folder with any settings you might need.

Note: Live wallpapers are resource-intensive and can have an impact on device performance and battery life, so use them judiciously.

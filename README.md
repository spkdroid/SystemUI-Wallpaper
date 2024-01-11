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

```xml
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android"
    android:thumbnail="@drawable/ic_launcher_foreground"
    android:description="@string/live_wallpaper_description"
    android:settingsActivity=".SettingsActivity" />
```

In this example:

- `thumbnail`: Points to a drawable resource that serves as a thumbnail for your live wallpaper. Replace `ic_launcher_foreground` with the actual drawable resource you want to use.

- `description`: Points to a string resource that provides a description of your live wallpaper. Replace `@string/live_wallpaper_description` with the actual string resource.

- `settingsActivity`: Specifies the activity that should be launched when the user wants to configure the live wallpaper. Replace `.SettingsActivity` with the actual activity class that handles the live wallpaper settings.

Remember to create the corresponding string resource (`live_wallpaper_description`) and the drawable resource (thumbnail) in your `res/values/strings.xml` and `res/drawable` folders, respectively. Additionally, create the `SettingsActivity` class to handle the live wallpaper settings.

For example, in `res/values/strings.xml`:

```xml
<resources>
    <string name="live_wallpaper_description">My Live Wallpaper Description</string>
</resources>
```

In `res/drawable/ic_launcher_foreground.xml`:

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24.0"
    android:viewportHeight="24.0">
    <!-- Your vector drawable content here -->
</vector>
```

Make sure to replace the placeholder content with the actual content for your application.

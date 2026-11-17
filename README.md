# CONVERT ANY WEBSITE INTO A NATIVE ANDROID APP

- Convert any website into a native Android App using Android Studio and WebView. Includes file uploads, downloads, camera & microphone permissions, printing support, and a clean native app interface.
- This project is ideal for developers who want to quickly transform a website into a professional mobile application using **Android Studio and Java**.

## Why This Project Exists
- Many developers and businesses already have functional websites but need a mobile application to reach Android users. Building a fully native Android application from scratch can be time-consuming and expensive.  

- This project provides a simple solution by allowing developers to convert an existing website into a native Android application using WebView.  

- Instead of rewriting the entire platform in Java or Kotlin, the application loads the existing website inside a secure WebView container while still supporting important native device features such as file uploads, downloads, printing, and media access.  

- The goal of this repository is to provide a **clean, lightweight, and production-ready Android WebView template** that developers can easily customize and deploy.

## PREREQUISITES
> [!IMPORTANT]
> Before you start, ensure you have `Android Studio` installed in your computer (It is recommended to use the **latest stable version** with all required SDK components and dependencies properly configured.).
> If not, install the latest version of Android Studio from the official site👉: [Android Studio Download](https://developer.android.com/studio) 

### KEY FEATURES INCLUDED IN THE APK
1. `Native WebView Website Wrapper`: Allows users to load any website directly inside an Android application with optimized WebView settings and compatibility. The app integrates WebView Cache management which improves loading performance and ensures efficient resource management.
---

2. `Printing Support`: the app integrates the Android **Print Framework**, allowing users to print web pages directly from the app. Web pages can trigger printing through a **JavaScript-to-native interface bridge**.
---

3. `File Upload support`: The app integrates a  modern **Android file picker**, enabling users to upload files directly from their device storage to the website.
Supported file types include:
- Documents
- Images
- Media files
---

4. `Camera and Microphone Access:` Web pages running inside the WebView can access device hardware when required.
Permissions supported:
- Camera access
- Microphone access

This enables functionality such as:
- Image capture
- Video uploads
- Voice recording
- Media forms
---

5. `Built-in File Download System`: The app integrates Android's **DownloadManager**, allowing users to download files directly from the website.

Downloaded files:
- Are saved to the device **Downloads folder**
- Display download progress notifications
- Can be accessed using the device file manager
---

6. `JavaScript-to-Native Communication Bridge`: The app includes a **JavaScript interface bridge**, allowing web pages to interact with Android native functionality.

This bridge can be used for actions such as:
- Triggering print operations
- Executing native Android features
- Creating deeper integration between the website and the app
---

7. `Native Mobile Interface`: The app removes traditional browser UI components to provide a **clean and fully immersive mobile app experience**.

Features include:
- No browser toolbar
- No address bar
- Full-screen WebView layout
---

8. `Custom Splash Screen`: The app uses a **custom splash screen** with:
- App logo
- Text animations
- Custom gradient colors
- Smooth startup transition
---

## Application Permissions
- The application requests several permissions required for native functionality.

| Permission     | Purpose                                     |
| -------------- | ------------------------------------------- |
| Camera         | Allows websites to capture photos or videos |
| Microphone     | Enables voice recording features            |
| Storage Access | Allows file uploads and downloads           |
| Notifications  | Displays download completion alerts         |

---



# STEP BY STEP CONFIGURATION
1. Open Android Studio. (a). Click the Menu icon ≣ (top left) then choose: `File>New Project`. 
(b). Select `No Activity` then click Next (as shown below):
![1. New project|600](images/1.New-project.png)

---

2. Apply the correct details for your app in the following window then click ***Finish***:   ![2. Finish Empty Activity](images/2.Empty-activity.png)
> [!IMPORTANT]
> 1. `Name` is the name that will be displayed in the android launcher of the app e.g., Tiktok
> 2. `Package name` should be ***your-site.app-name*** where `your-site` is inverted and `app-name` is in lowercase. For instance, if *your site*= `lewismwaz.com` and *app-name*=MicrooApp, then `Package name` should be `com.lewismwaz.microoapp`.
> 3. Select `Java` as the primary **Language**.
> 4. Ensure no directory/path exists with App name (in this case **MicrooApp**) before clicking the *Finish* button. The recommended `Save location` is `C:\Users`.
> 5. Ensure minimum SDK is set to the lowest ...Android 7.0 to support most android devices.
> 6. Set build configuration language as `Groovy DSL (build.gradle)` since we'll use Java.

---

3. Wait till ***Generating sources*** and ***Importing Gradle Project*** (shown at the bottom) is complete. You'll get a notification (bottom-right) "*Microsoft Defender may affect IDE*". Select **Exclude Folders**. When done, change the `Android` view to `Project` view (as shown below) by clicking in the area marked with a green arrow:
![3. Project View](images/3.Project-view.png)
You should get a view similar to the one below. Click the drop-downs as shown with the arrows (red to expand) and (green to collapse). You should see a folder inside `java` with a typical package name as the one below:
![4. Project View](images/3.Project-view2.png)

---

4. (a). Right-click on the `package name` (in this case: *ke.co.lewismwaz.microoapp*) then select: New>Activity>Empty Views Activity. Rename the Activity Name to `MainActivity`. Leave all the other options as shown below then click **Finish**: 
![5. MainActivity](images/Views-activity.png)
![6. MainActivity Options](images/Views-activity2.png)
(b). Right-click on the `package name` again, then select: `New >Activity>Empty Views Activity`, type `SplashActivity` then press Enter. Activity name should be `SplashActivity`. Same options just like for `MainActivity` in 4(a) above. Click **Finish**.
(c). Right-click on the `package name` again, then select: `New >Java Class`, type `NetworkUtils` then press Enter. 
You should end up with something like (below):
![7. MainActivity](images/MainActivity.png)

---

5. (a). Open `MainActivity.java`. Replace all the code **below** the package (in this case "*package ke.co.ke.lewismwaz.microoapp;*") with:
```java
import android.Manifest;
import android.annotation.SuppressLint;
import android.app.DownloadManager;
import android.content.Intent;
import android.net.Uri;
import android.os.Build;
import android.os.Bundle;
import android.os.Environment;
import android.print.PrintAttributes;
import android.print.PrintManager;
import android.view.View;
import android.webkit.CookieManager;
import android.webkit.JavascriptInterface;
import android.webkit.PermissionRequest;
import android.webkit.URLUtil;
import android.webkit.ValueCallback;
import android.webkit.WebChromeClient;
import android.webkit.WebResourceRequest;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.webkit.WebViewClient;
import android.widget.Toast;

import androidx.activity.OnBackPressedCallback;
import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.contract.ActivityResultContracts;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

public class MainActivity extends AppCompatActivity {

    private static final String WEBSITE_URL = "https://lewismwaz.co.ke/index.php";
    private static final int PERMISSION_REQUEST_CODE = 101;

    private WebView webView;
    private ValueCallback<Uri[]> fileUploadCallback;

    /* ---------- FILE PICKER (MODERN API) ---------- */
    private final ActivityResultLauncher<Intent> fileChooserLauncher =
            registerForActivityResult(
                    new ActivityResultContracts.StartActivityForResult(),
                    result -> {
                        if (fileUploadCallback == null) return;

                        Uri[] uris = WebChromeClient.FileChooserParams
                                .parseResult(result.getResultCode(), result.getData());

                        fileUploadCallback.onReceiveValue(uris);
                        fileUploadCallback = null;
                    }
            );

    @SuppressLint("SetJavaScriptEnabled")
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        if (getSupportActionBar() != null) getSupportActionBar().hide();

        setContentView(R.layout.activity_main);
        webView = findViewById(R.id.webView);

        requestRuntimePermissions();
        setupWebView();
        loadWebsite();
        setupBackPressedHandler();
        setupDownloads();
    }

    /* ---------- PERMISSIONS ---------- */
    private void requestRuntimePermissions() {
        if (Build.VERSION.SDK_INT >= 33) {
            ActivityCompat.requestPermissions(
                    this,
                    new String[]{
                            Manifest.permission.CAMERA,
                            Manifest.permission.RECORD_AUDIO,
                            Manifest.permission.POST_NOTIFICATIONS
                    },
                    PERMISSION_REQUEST_CODE
            );
        }
    }

    /* ---------- WEBVIEW SETUP ---------- */
    @SuppressLint("SetJavaScriptEnabled")
    private void setupWebView() {
        WebSettings settings = webView.getSettings();

        settings.setJavaScriptEnabled(true);
        settings.setDomStorageEnabled(true);
        settings.setMediaPlaybackRequiresUserGesture(false);
        settings.setCacheMode(WebSettings.LOAD_DEFAULT);

        settings.setUseWideViewPort(true);
        settings.setLoadWithOverviewMode(true);

        settings.setBuiltInZoomControls(false);
        settings.setDisplayZoomControls(false);

        webView.setOverScrollMode(View.OVER_SCROLL_NEVER);

        // JavaScript bridge for printing
        webView.addJavascriptInterface(new WebAppBridge(), "Android");

        webView.setWebViewClient(new NativeWebViewClient());
        webView.setWebChromeClient(new NativeWebChromeClient());
    }

    /* ---------- LOAD SITE ---------- */
    private void loadWebsite() {
        if (NetworkUtils.isNetworkAvailable(this)) {
            webView.loadUrl(WEBSITE_URL);
        } else {
            Toast.makeText(this, "No internet connection.", Toast.LENGTH_LONG).show();
        }
    }

    /* ---------- DOWNLOAD SUPPORT ---------- */
    private void setupDownloads() {
        webView.setDownloadListener((url, userAgent, contentDisposition, mimeType, contentLength) -> {

            DownloadManager.Request request = new DownloadManager.Request(Uri.parse(url));
            request.setMimeType(mimeType);

            String cookies = CookieManager.getInstance().getCookie(url);
            request.addRequestHeader("cookie", cookies);
            request.addRequestHeader("User-Agent", userAgent);

            request.setTitle(URLUtil.guessFileName(url, contentDisposition, mimeType));
            request.setDescription("Downloading...");
            request.setNotificationVisibility(
                    DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED
            );
            request.setDestinationInExternalPublicDir(
                    Environment.DIRECTORY_DOWNLOADS,
                    URLUtil.guessFileName(url, contentDisposition, mimeType)
            );

            DownloadManager dm = (DownloadManager) getSystemService(DOWNLOAD_SERVICE);
            dm.enqueue(request);

            Toast.makeText(this, "Downloading file...", Toast.LENGTH_SHORT).show();
        });
    }

    /* ---------- BACK BUTTON ---------- */
    private void setupBackPressedHandler() {
        getOnBackPressedDispatcher().addCallback(this, new OnBackPressedCallback(true) {
            @Override
            public void handleOnBackPressed() {
                if (webView.canGoBack()) {
                    webView.goBack();
                } else {
                    new AlertDialog.Builder(MainActivity.this)
                            .setTitle("Exit MicrooApp")
                            .setMessage("Do you want to exit?")
                            .setPositiveButton("Exit", (d, w) -> finish())
                            .setNegativeButton("Cancel", null)
                            .show();
                }
            }
        });
    }

    /* ---------- PRINT IMPLEMENTATION ---------- */
    private void printPage() {
        PrintManager printManager = (PrintManager) getSystemService(PRINT_SERVICE);

        PrintAttributes attributes = new PrintAttributes.Builder()
                .setMediaSize(PrintAttributes.MediaSize.ISO_A4)
                .setColorMode(PrintAttributes.COLOR_MODE_COLOR)
                .build();

        printManager.print(
                "MicrooApp_Page",
                webView.createPrintDocumentAdapter("MicrooApp"),
                attributes
        );
    }

    /* ---------- JS → NATIVE BRIDGE ---------- */
    private class WebAppBridge {

        @JavascriptInterface
        @SuppressWarnings("unused")
        public void printPage() {
            runOnUiThread(MainActivity.this::printPage);
        }
    }

    /* ---------- WEBVIEW CLIENT ---------- */
    private static class NativeWebViewClient extends WebViewClient {
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, WebResourceRequest request) {
            String url = request.getUrl().toString();

            if (url.startsWith("tel:")
                    || url.startsWith("mailto:")
                    || url.startsWith("sms:")) {

                view.getContext().startActivity(
                        new Intent(Intent.ACTION_VIEW, Uri.parse(url))
                );
                return true;
            }
            return false;
        }
    }

    /* ---------- CHROME CLIENT ---------- */
    private class NativeWebChromeClient extends WebChromeClient {

        @Override
        public void onPermissionRequest(PermissionRequest request) {
            request.grant(request.getResources());
        }

        @Override
        public boolean onShowFileChooser(
                WebView webView,
                ValueCallback<Uri[]> filePathCallback,
                FileChooserParams fileChooserParams) {

            fileUploadCallback = filePathCallback;
            fileChooserLauncher.launch(fileChooserParams.createIntent());
            return true;
        }
    }
}
```

(b). Navigate to the line: 
```java
private static final String WEBSITE_URL = "https://lewismwaz.co.ke/index.php"
```
and replace `https://lewismwaz.co.ke/index.php` with the actual link to the root/home of your website (First page users should see after the splash screen loads).

(c). Navigate to the code: 
```java
public void handleOnBackPressed() {  
                if (webView.canGoBack()) {  
                    webView.goBack();  
                } else {  
                    new AlertDialog.Builder(MainActivity.this)  
                            .setTitle("Exit MicrooApp")  
                            .setMessage("Do you want to exit?")  
                            .setPositiveButton("Exit", (d, w) -> finish())  
                            .setNegativeButton("Cancel", null)  
                            .show();  
                }  
            }  
```
Replace `Exit MicrooApp` with `Exit YourAppName`.

(d). Navigate to the code:
```java
/* ---------- PRINT IMPLEMENTATION ---------- */  
    private void printPage() {  
        PrintManager printManager = (PrintManager) getSystemService(PRINT_SERVICE);  
  
        PrintAttributes attributes = new PrintAttributes.Builder()  
                .setMediaSize(PrintAttributes.MediaSize.ISO_A4)  
                .setColorMode(PrintAttributes.COLOR_MODE_COLOR)  
                .build();  
  
        printManager.print(  
                "MicrooApp_Page",  
                webView.createPrintDocumentAdapter("MicrooApp"),  
                attributes  
        );  
    }  
```
Replace `MicrooApp_Page` with `YourAppName_Page` and `MicrooApp` with `YourAppName`. The `MainActivity.java` should now be looking good!

---

6. Open `NetworkUtils.java`. Replace all the code **below** the package (in this case "*package ke.co.ke.lewismwaz.microoapp;*") with:
```java
import android.content.Context;
import android.net.ConnectivityManager;
import android.net.Network;
import android.net.NetworkCapabilities;

public class NetworkUtils {
    public static boolean isNetworkAvailable(Context context) {
        ConnectivityManager connectivityManager =
                (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);

        if (connectivityManager != null) {
            Network network = connectivityManager.getActiveNetwork();
            if (network == null) return false;
            NetworkCapabilities capabilities = connectivityManager.getNetworkCapabilities(network);
            return capabilities != null &&
                    capabilities.hasCapability(NetworkCapabilities.NET_CAPABILITY_INTERNET);
        }
        return false;
    }
}
```

---

7. Open `SplashActivity.java`. Replace all the code **below** the package (in this case "*package ke.co.ke.lewismwaz.microoapp;*") with:
```java
import android.annotation.SuppressLint;
import android.content.Intent;
import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.util.Log;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.ImageView;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityOptionsCompat;
import androidx.core.view.WindowCompat;
import androidx.core.view.WindowInsetsCompat;
import androidx.core.view.WindowInsetsControllerCompat;

@SuppressLint("CustomSplashScreen")
public class SplashActivity extends AppCompatActivity {

    private static final String TAG = "SplashActivity";
    private static final int SPLASH_DURATION = 3000; // 3 seconds

    private final Handler handler = new Handler(Looper.getMainLooper());
    private Runnable pulseRunnable;

    private ImageView logoImageView;
    private TextView sloganTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Modern fullscreen / edge-to-edge window setup
        hideSystemBars();

        setContentView(R.layout.activity_splash);

        // Initialize views
        logoImageView = findViewById(R.id.logoImageView);
        sloganTextView = findViewById(R.id.sloganTextView);

        // Start animations
        startAnimations();

        // Start splash screen timer
        startSplashTimer();
    }

    private void hideSystemBars() {
        WindowCompat.setDecorFitsSystemWindows(getWindow(), false);
        WindowInsetsControllerCompat controller = WindowCompat.getInsetsController(getWindow(), getWindow().getDecorView());
        controller.hide(WindowInsetsCompat.Type.systemBars());
        controller.setSystemBarsBehavior(WindowInsetsControllerCompat.BEHAVIOR_SHOW_TRANSIENT_BARS_BY_SWIPE);
    }

    private void startAnimations() {
        Animation scaleUpAnimation = AnimationUtils.loadAnimation(this, R.anim.scale_up);
        Animation fadeInAnimation = AnimationUtils.loadAnimation(this, R.anim.fade_in);
        Animation pulseAnimation = AnimationUtils.loadAnimation(this, R.anim.pulse);

        logoImageView.startAnimation(scaleUpAnimation);

        pulseRunnable = () -> sloganTextView.startAnimation(pulseAnimation);
        Runnable sloganRunnable = () -> {
            sloganTextView.startAnimation(fadeInAnimation);
            handler.postDelayed(pulseRunnable, 1500);
        };

        handler.postDelayed(sloganRunnable, 500);
    }

    private void startSplashTimer() {
        Runnable navigateRunnable = () -> {
            if (isFinishing() || isDestroyed()) {
                return;
            }
            try {
                Intent intent = new Intent(SplashActivity.this, MainActivity.class);
                ActivityOptionsCompat options = ActivityOptionsCompat.makeCustomAnimation(
                        SplashActivity.this,
                        android.R.anim.fade_in,
                        android.R.anim.fade_out
                );
                startActivity(intent, options.toBundle());
                finish();
            } catch (Exception e) {
                Log.e(TAG, "Error starting MainActivity: " + e.getMessage(), e);
                finish();
            }
        };

        handler.postDelayed(navigateRunnable, SPLASH_DURATION);
    }

    @Override
    protected void onDestroy() {
        // Clear pending handler messages to prevent memory leaks
        handler.removeCallbacksAndMessages(null);

        if (logoImageView != null) {
            logoImageView.clearAnimation();
        }
        if (sloganTextView != null) {
            sloganTextView.clearAnimation();
        }
        super.onDestroy();
    }
}
```

---

8. (a). Expand the `res` directory. Right-click on it: New>Android Resource Directory. Name it `anim` (as shown below):
![8. anim folder](images/anim.png)
![9. anim](images/animF.png)

(b). Right-click on `anim` directory then select: New>Animation Resource File:
![11. Animation Resource](images/animResource.png)  

Give it a filename `fade_in` then click **OK**:  
![12. Animation Resource](images/fade-in.png)

(c). Repeat the same process to create `pulse` and `scale_up` animation resource files.  

The `res` directory should now look like this + `anim` directory:
![10. New res](images/animStructure.png)

---

9. Replace **All** the code in the files `fade_in.xml`, `pulse.xml` and `scale_up.xml` with the code below:
   
(a). `fade_in.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>  
<set xmlns:android="http://schemas.android.com/apk/res/android"  
    android:fillAfter="true">  
  
    <alpha        android:fromAlpha="0.0"  
        android:toAlpha="1.0"  
        android:duration="1500" />  
  
    <translate        android:fromYDelta="20"  
        android:toYDelta="0"  
        android:duration="1500" />  
</set>
```

(b). `pulse.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>  
<set xmlns:android="http://schemas.android.com/apk/res/android"  
    android:fillAfter="true"  
    android:repeatMode="reverse"  
    android:repeatCount="infinite">  
  
    <scale        android:fromXScale="1.0"  
        android:toXScale="1.05"  
        android:fromYScale="1.0"  
        android:toYScale="1.05"  
        android:pivotX="50%"  
        android:pivotY="50%"  
        android:duration="1000" />  
  
    <alpha        android:fromAlpha="1.0"  
        android:toAlpha="0.8"  
        android:duration="1000" />  
</set>
```

(c). `scale_up.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>  
<set xmlns:android="http://schemas.android.com/apk/res/android"  
    android:fillAfter="true">  
  
    <scale        android:fromXScale="0.8"  
        android:toXScale="1.0"  
        android:fromYScale="0.8"  
        android:toYScale="1.0"  
        android:pivotX="50%"  
        android:pivotY="50%"  
        android:duration="1500" />  
  
    <alpha        android:fromAlpha="0.5"  
        android:toAlpha="1.0"  
        android:duration="1500" />  
</set>
```

---

10. (a) Expand the `drawable` directory. Right-click on it then select: New> Drawable Resource File:
![13. Drawable](images/drawable.png)
  
  Give it a file name `linear_background`:  
![14. Drawable](images/linearbg.png)

(b). Repeat the same process as 10(a) to create `text_gradient.xml` file.

(c). Create a *logo* (image) with a transparent-background for your app. When you already have the app logo, to freely remove image background click here 👉 [Canva Background Remover](https://www.canva.com/features/background-remover/), upload your logo, then download your no-bg-logo. Resize the logo image to be small for small/mobile devices (Recommended size: 150X150). The image file must be a ***.png*** file. Rename the image as `logo` thus should be `logo.png`, then copy it and paste it inside `drawable` directory.

The new `drawable` directory structure should now be similar to the one shown below:
![15. Drawable](images/drawableF.png)

---


***NB:*** For the next step (11), ensure to switch to `Code` view as shown below:
![16. Drawable](images/codeView.png)

11. Replace **All** the code in the files `ic_launcher_background.xml`, `ic_launcher_foreground.xml`, `linear_background.xml` and `text_gradient.xml` with the code below:
> [!NOTE]
> The code in the files below will be used to style the Splash Screen of the app.

(a). `ic_launcher_background.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="108dp"
    android:height="108dp"
    android:viewportWidth="108"
    android:viewportHeight="108">

    <!-- Main background - deep professional blue/gray -->
    <path
        android:fillColor="#1A1A2E"
        android:pathData="M0,0h108v108h-108z" />

    <!-- Subtle gradient effect using diagonal overlay -->
    <path
        android:fillColor="#16213E"
        android:pathData="M0,0L108,0L108,54L0,108Z" />

    <!-- Decorative curved accent at bottom -->
    <path
        android:fillColor="#0F3460"
        android:pathData="M0,90 Q27,85 54,90 T108,95 L108,108 L0,108Z" />

    <!-- Top accent line matching login card style -->
    <path
        android:fillColor="#E94560"
        android:pathData="M30,12 L78,12"
        android:strokeWidth="1.5"
        android:strokeColor="#E94560" />

    <!-- Secondary accent line -->
    <path
        android:fillColor="#E94560"
        android:pathData="M40,16 L68,16"
        android:strokeWidth="1"
        android:strokeColor="#E94560" />

    <!-- Subtle grid pattern for modern aesthetic -->
    <path
        android:fillColor="#00000000"
        android:pathData="M0,22L108,22"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M0,32L108,32"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M0,42L108,42"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M0,52L108,52"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M0,62L108,62"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M0,72L108,72"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M0,82L108,82"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />

    <!-- Vertical grid lines -->
    <path
        android:fillColor="#00000000"
        android:pathData="M22,0L22,108"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M42,0L42,108"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M62,0L62,108"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />
    <path
        android:fillColor="#00000000"
        android:pathData="M82,0L82,108"
        android:strokeWidth="0.5"
        android:strokeColor="#33FFFFFF" />

    <!-- Decorative circular elements for modern feel -->
    <path
        android:fillColor="#E94560"
        android:pathData="M90,20 A3,3 0 1,0 90,20.1"
        android:strokeWidth="2"
        android:strokeColor="#E94560" />
    <path
        android:fillColor="#E94560"
        android:pathData="M85,25 A2,2 0 1,0 85,25.1"
        android:strokeWidth="1.5"
        android:strokeColor="#E94560" />

    <!-- Bottom decorative dots -->
    <path
        android:fillColor="#E94560"
        android:pathData="M54,98 A2,2 0 1,0 54,98.1"
        android:strokeWidth="1.5"
        android:strokeColor="#E94560" />
    <path
        android:fillColor="#E94560"
        android:pathData="M58,101 A1.5,1.5 0 1,0 58,101.1"
        android:strokeWidth="1"
        android:strokeColor="#E94560" />
    <path
        android:fillColor="#E94560"
        android:pathData="M50,101 A1.5,1.5 0 1,0 50,101.1"
        android:strokeWidth="1"
        android:strokeColor="#E94560" />

</vector>
```

(b). `ic_launcher_foreground.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt"
    android:width="108dp"
    android:height="108dp"
    android:viewportWidth="108"
    android:viewportHeight="108">

    <!-- Modern terminal icon -->
    <path
        android:fillColor="#E94560"
        android:fillType="nonZero"
        android:pathData="M54,28 L78,28 L82,40 L82,52 L26,52 L26,40 L30,28 L54,28 Z"
        android:strokeWidth="1.5"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#1A1A2E"
        android:fillType="nonZero"
        android:pathData="M32,44 L76,44 L76,50 L32,50 Z"
        android:strokeWidth="1"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#FFFFFF"
        android:fillType="nonZero"
        android:pathData="M50,52 L50,62 L58,62 L58,52 L50,52 Z" />

    <!-- Text simulation -->
    <path
        android:fillColor="#333333"
        android:fillType="nonZero"
        android:pathData="M51,54 L57,54"
        android:strokeWidth="0.8"
        android:strokeColor="#333333" />
    <path
        android:fillColor="#333333"
        android:fillType="nonZero"
        android:pathData="M51,56 L56,56"
        android:strokeWidth="0.8"
        android:strokeColor="#333333" />
    <path
        android:fillColor="#333333"
        android:fillType="nonZero"
        android:pathData="M51,58 L57,58"
        android:strokeWidth="0.8"
        android:strokeColor="#333333" />
    <path
        android:fillColor="#333333"
        android:fillType="nonZero"
        android:pathData="M52,60 L55,60"
        android:strokeWidth="0.8"
        android:strokeColor="#333333" />

    <!-- Keypad buttons -->
    <path
        android:fillColor="#0F3460"
        android:fillType="nonZero"
        android:pathData="M34,28 C34,28 34,24 38,24 L44,24 L44,28 L34,28 Z" />
    <path
        android:fillColor="#0F3460"
        android:fillType="nonZero"
        android:pathData="M46,28 L46,24 L52,24 L52,28 L46,28 Z" />
    <path
        android:fillColor="#0F3460"
        android:fillType="nonZero"
        android:pathData="M54,28 L54,24 L60,24 L60,28 L54,28 Z" />
    <path
        android:fillColor="#0F3460"
        android:fillType="nonZero"
        android:pathData="M62,28 L62,24 L68,24 L68,28 L62,28 Z" />
    <path
        android:fillColor="#0F3460"
        android:fillType="nonZero"
        android:pathData="M70,28 L70,24 L74,24 L74,28 L70,28 Z" />

    <!-- Glow effect -->
    <path
        android:fillColor="#E94560"
        android:fillType="nonZero"
        android:pathData="M24,36 L84,36"
        android:strokeWidth="1.5"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#E94560"
        android:fillType="nonZero"
        android:pathData="M78,62 L86,62 L86,68 L78,68 Z"
        android:strokeWidth="1"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#FFFFFF"
        android:fillType="nonZero"
        android:pathData="M81,63 L83,63 L83,67 L81,67 Z" />

    <path
        android:fillColor="#E94560"
        android:fillType="nonZero"
        android:pathData="M81.5,64 L81.5,65 M82.5,65 L82.5,66"
        android:strokeWidth="0.6"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#FFFFFF"
        android:fillType="nonZero"
        android:pathData="M20,68 C20,68 22,64 24,64 L84,64 C86,64 88,66 88,68 L88,72 C88,74 86,76 84,76 L26,76 C24,76 22,74 22,72 L20,68 Z"
        android:strokeWidth="1.2"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#E94560"
        android:fillType="nonZero"
        android:pathData="M28,76 L28,80"
        android:strokeWidth="1.5"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#E94560"
        android:fillType="nonZero"
        android:pathData="M80,76 L80,80"
        android:strokeWidth="1.5"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#0F3460"
        android:pathData="M35,79 A3,3 0 1,1 35,79.1"
        android:strokeWidth="1"
        android:strokeColor="#E94560" />

    <path
        android:fillColor="#0F3460"
        android:pathData="M73,79 A3,3 0 1,1 73,79.1"
        android:strokeWidth="1"
        android:strokeColor="#E94560" />

    <!-- Decorative gradient overlay for depth -->
    <path
        android:pathData="M31,63.928c0,0 6.4,-11 12.1,-13.1c7.2,-2.6 26,-1.4 26,-1.4l38.1,38.1L107,108.928l-32,-1L31,63.928z">
        <aapt:attr name="android:fillColor">
            <gradient
                android:endX="85.84757"
                android:endY="92.4963"
                android:startX="42.9492"
                android:startY="49.59793"
                android:type="linear">
                <item
                    android:color="#1A000000"
                    android:offset="0.0" />
                <item
                    android:color="#00000000"
                    android:offset="1.0" />
            </gradient>
        </aapt:attr>
    </path>

    <!-- Clean overlay -->
    <path
        android:fillColor="#00FFFFFF"
        android:fillType="nonZero"
        android:pathData="M65.3,45.828l3.8,-6.6c0.2,-0.4 0.1,-0.9 -0.3,-1.1c-0.4,-0.2 -0.9,-0.1 -1.1,0.3l-3.9,6.7c-6.3,-2.8 -13.4,-2.8 -19.7,0l-3.9,-6.7c-0.2,-0.4 -0.7,-0.5 -1.1,-0.3C38.8,38.328 38.7,38.828 38.9,39.228l3.8,6.6C36.2,49.428 31.7,56.028 31,63.928h46C76.3,56.028 71.8,49.428 65.3,45.828zM43.4,57.328c-0.8,0 -1.5,-0.5 -1.8,-1.2c-0.3,-0.7 -0.1,-1.5 0.4,-2.1c0.5,-0.5 1.4,-0.7 2.1,-0.4c0.7,0.3 1.2,1 1.2,1.8C45.3,56.528 44.5,57.328 43.4,57.328L43.4,57.328zM64.6,57.328c-0.8,0 -1.5,-0.5 -1.8,-1.2s-0.1,-1.5 0.4,-2.1c0.5,-0.5 1.4,-0.7 2.1,-0.4c0.7,0.3 1.2,1 1.2,1.8C66.5,56.528 65.6,57.328 64.6,57.328L64.6,57.328z"
        android:strokeWidth="1"
        android:strokeColor="#00000000" />
</vector>
```

(c). `linear_background.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <corners android:radius="14dp" />

    <gradient
        android:startColor="@color/ph_primary"
        android:endColor="@color/primary_dark"
        android:angle="135"
        android:type="linear" />

</shape>
```

(d). `text_gradient.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <!-- Rounded corners -->
    <corners android:radius="12dp" />

    <!-- Gradient background - diagonal for modern feel -->
    <gradient
        android:startColor="@color/ph_primary"
        android:endColor="@color/primary_dark"
        android:angle="135"
        android:type="linear" />

    <!-- Optional: Add padding inside the shape -->
    <padding
        android:left="8dp"
        android:top="4dp"
        android:right="8dp"
        android:bottom="4dp" />

</shape>
```

---

12. (a). Expand the `res` directory. Right-click on it: New>Android Resource Directory (as shown below). Name the Directory `drawable-night` then click **OK**. This should the store files responsible for the styling (Text & Background) of the Splash Screen in Dark-mode:
![17. Drawable-night folder](images/drawableNight.png)
![18. Drawable night folder](images/drawableNight2.png)

(b). Next, you need to copy from `drawable`: *linear_background.xml* and *text_gradient.xml* then, paste them inside `drawable-night`. Replace **All** the code in the files with the one below. Ensure to switch to `Code` view!:  

(i). `linear_background.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <corners android:radius="14dp" />

    <gradient
        android:startColor="@color/ph_primary"
        android:endColor="@color/primary_dark"
        android:angle="135"
        android:type="linear" />

</shape>
```

(ii). `text_gradient.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <!-- Rounded corners -->
    <corners android:radius="12dp" />

    <!-- Gradient background - diagonal for modern feel -->
    <gradient
        android:startColor="@color/ph_primary"
        android:endColor="@color/primary_dark"
        android:angle="135"
        android:type="linear" />

    <!-- Optional: Add padding inside the shape -->
    <padding
        android:left="8dp"
        android:top="4dp"
        android:right="8dp"
        android:bottom="4dp" />

</shape>
```

---

13. Click to expand `layout` directory (located below `drawable-night`).
 
 ![19. Layout folder](images/layout.png)

Replace **All** the code for `activity_main.xml` and `activity_splash.xml` with the one below:
(a). `activity_main.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <WebView        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/webView"
        android:layout_alignParentTop="true"
        android:layout_alignParentStart="true"
        android:layout_alignParentBottom="true"
        android:layout_alignParentEnd="true"
        tools:ignore="MissingConstraints" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

(b). `activity_splash.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:background="@drawable/linear_background"
    tools:context=".SplashActivity">

    <!-- Logo Image -->
    <FrameLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="20dp">

        <ImageView
            android:id="@+id/logoImageView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/logo"
            android:adjustViewBounds="true"
            android:maxWidth="180dp"
            android:maxHeight="98dp"
            android:scaleType="fitCenter"
            android:contentDescription="@string/app_name" />

    </FrameLayout>

    <!-- Slogan Text with Animation - For light mode -->
    <TextView
        android:id="@+id/sloganTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/slogan"
        android:textSize="16sp"
        android:textColor="@color/white"
        android:textStyle="bold"
        android:layout_marginTop="10dp"
        android:paddingHorizontal="20dp"
        android:paddingVertical="10dp"
        android:fontFamily="sans-serif-condensed"
        android:visibility="visible"
        android:letterSpacing="0.05"
        android:shadowColor="#80000000"
        android:shadowDx="1"
        android:shadowDy="1"
        android:shadowRadius="3"/>

    <!-- Cool Progress Bar - Modern Style -->
    <ProgressBar
        android:layout_width="220dp"
        android:layout_height="10dp"
        android:layout_gravity="center_horizontal"
        style="?android:attr/progressBarStyleHorizontal"
        android:max="100"
        android:indeterminate="true"
        android:progress="0"
        android:layout_marginTop="60dp"
        android:progressTint="@color/white"
        android:progressBackgroundTint="#33FFFFFF"
        android:indeterminateTint="@color/white"
        android:indeterminateTintMode="src_atop" />

</LinearLayout>
```

---

14. (a). Right-click on `values` directory then select: New> Values Resource File:
![20. Values file Structure](images/values.png)

Name the file `ic_launcher_background`:  

![21. Values BG](images/valuesBG.png)

The new `values` structure should be as shown below:  

![22. Layout folder](images/ValuesS.png)

(b). Replace **All** the code for `colors.xml`, `ic_launcher_background.xml`, `strings.xml` and `themes.xml` with the one below. *Change only the values where necessary to fit your preference*:
(i). `colors.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- Primary Brand Colors - Pure Navy Blue Theme -->
    <color name="ph_primary">#0F3460</color>      <!-- Navy blue - Primary action color -->
    <color name="primary_1">#16213E</color>       <!-- Lighter navy for hover states -->
    <color name="primary_dark">#0A1A2E</color>    <!-- Darker navy for pressed states -->
    <color name="ph_primary_2">#1A365D</color>    <!-- Deep blue accent -->

    <!-- Secondary Colors - Deep Navy Background -->
    <color name="secondary_dark">#1A1A2E</color>  <!-- Main background -->
    <color name="secondary_medium">#16213E</color> <!-- Card background -->
    <color name="secondary_light">#0F3460</color>  <!-- Accent elements -->

    <!-- Status Colors - Navy-compatible -->
    <color name="ph_success">#2E7D64</color>      <!-- Muted teal-green for success -->
    <color name="ph_warning">#B8860B</color>      <!-- Dark golden for warnings -->
    <color name="ph_danger">#8B3A3A</color>       <!-- Muted red for errors -->
    <color name="ph_info">#2A5F7A</color>         <!-- Steel blue for info -->

    <!-- Surface Colors - Light Mode Cards -->
    <color name="ph_surface">#FFFFFF</color>      <!-- White cards -->
    <color name="ph_surface_dark">#2D3748</color>  <!-- Dark gray for light mode (visible) -->
    <color name="ph_surface_2">#F8F9FC</color>    <!-- Off-white for alternating rows -->

    <!-- Theme Surface Colors (Required by Material Design) -->
    <color name="colorSurface">#FFFFFF</color>     <!-- Light mode surface color -->
    <color name="colorOnSurface">#2D3748</color>   <!-- Text color on surfaces -->

    <!-- UI Elements Colors -->
    <color name="ph_border">#E2E8F0</color>       <!-- Subtle borders -->
    <color name="ph_divider">#EDF2F7</color>      <!-- Dividers between elements -->
    <color name="ph_shadow">#1A1A2E</color>       <!-- Shadow color with alpha -->

    <!-- Background Colors -->
    <color name="body_background">#F0F2F5</color>  <!-- App background -->
    <color name="text_primary">#2D3748</color>    <!-- Primary text -->
    <color name="text_secondary">#4A5568</color>  <!-- Secondary text -->
    <color name="text_hint">#A0AEC0</color>       <!-- Hint/placeholder text -->
    <color name="text_dark">#1A202C</color>       <!-- Dark text for emphasis -->
    <color name="text_light">#FFFFFF</color>      <!-- Light text on dark backgrounds -->
    <color name="text_muted">#718096</color>      <!-- Muted text -->

    <!-- Input Field Colors -->
    <color name="input_background">#FFFFFF</color>
    <color name="input_border">#CBD5E0</color>
    <color name="input_focused_border">#0F3460</color>
    <color name="input_error_border">#8B3A3A</color>

    <!-- Button Colors -->
    <color name="button_primary">#0F3460</color>
    <color name="button_primary_pressed">#0A1A2E</color>
    <color name="button_secondary">#16213E</color>
    <color name="button_secondary_pressed">#1A1A2E</color>
    <color name="button_disabled">#CBD5E0</color>

    <!-- Link Colors -->
    <color name="link_normal">#0F3460</color>
    <color name="link_pressed">#0A1A2E</color>
    <color name="link_visited">#1A365D</color>

    <!-- Dark mode colors (for night theme support) -->
    <color name="primary_dark_darker">#0A1A2E</color>
    <color name="dark_background">#1A1A2E</color>
    <color name="dark_surface">#16213E</color>
    <color name="dark_text_primary">#F7FAFC</color>
    <color name="dark_text_secondary">#CBD5E0</color>

    <!-- Basic Colors -->
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="transparent">#00000000</color>

    <!-- Overlay Colors -->
    <color name="overlay_light">#80FFFFFF</color>
    <color name="overlay_dark">#80000000</color>
</resources>
```

(ii). `ic_launcher_background.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="ic_launcher_background">#FFFFFF</color>
</resources>
```

(iii). `strings.xml`:
- Replace the `app_name` and `slogan` values with yours.
```xml
<resources>  
    <string name="app_name">MicrooApp</string>  
    <string name="slogan">"Your number one option!"</string>  
</resources>
```

(iv). `themes.xml`:
- Replace `Theme.MicrooApp` with `Theme.YourAppName`.
```xml
<resources xmlns:tools="http://schemas.android.com/tools">
    <!-- Base application theme for night/dark mode. -->
    <style name="Theme.MicrooApp" parent="Theme.MaterialComponents.DayNight.NoActionBar">
        <!-- Primary brand color (consistent coral accent for brand identity) -->
        <item name="colorPrimary">@color/ph_primary</item>
        <item name="colorPrimaryVariant">@color/primary_dark</item>
        <item name="colorOnPrimary">@color/white</item>

        <!-- Secondary brand color - Warm orange accent -->
        <item name="colorSecondary">@color/ph_primary_2</item>
        <item name="colorSecondaryVariant">@color/primary_dark</item>
        <item name="colorOnSecondary">@color/white</item>

        <!-- Background colors - Deep navy for night mode -->
        <item name="android:colorBackground">@color/body_background</item>

        <!-- Surface colors - Dark navy surfaces -->
        <item name="colorSurface">@color/ph_surface</item>
        <item name="colorOnSurface">@color/text_primary</item>

        <!-- Status bar color - Transparent to show gradient properly -->
        <item name="android:statusBarColor">@android:color/transparent</item>

        <!-- Make status bar icons light (for dark background) -->
        <item name="android:windowLightStatusBar">false</item>

        <!-- App bar/elevation background using linear gradient -->
        <item name="android:windowBackground">@drawable/linear_background</item>

        <!-- Navigation bar color - Match background -->
        <item name="android:navigationBarColor">@color/body_background</item>

        <!-- Customize your theme here. -->
    </style>
</resources>
```

---

The next step is to create `values-night` directory **if it doesn't already exist**.

15. (a). Right click on `res` directory. Select: `New>Android Resource Directory` and name it: `values-night`.
(b). Copy from `values` directory, `colors.xml` and `themes.xml` and paste them inside `values-night`. Replace all the code in the files with the one below:

(i). `colors.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- Darker version for night mode gradient - Deep navy base -->
    <color name="primary_dark_darker">#0F0F1A</color>

    <!-- Modern dark theme [Navy blue] -->
    <color name="body_background">#1A1A2E</color>
    <color name="text_primary">#F7FAFC</color>
    <color name="text_dark">#FFFFFF</color>
    <color name="ph_surface">#16213E</color>
    <color name="ph_surface_2">#1E2A47</color>
    <color name="ph_border">#2A3A5C</color>

    <!-- Additional dark mode refinements -->
    <color name="text_secondary">#CBD5E0</color>
    <color name="text_hint">#718096</color>
    <color name="ph_divider">#2A3A5C</color>
    <color name="input_background">#0F3460</color>
    <color name="input_border">#2A3A5C</color>

    <!-- Theme Surface Colors (Required by Material Design) -->
    <color name="colorSurface">#16213E</color>     <!-- Dark mode surface color -->
    <color name="colorOnSurface">#F7FAFC</color>   <!-- Text color on dark surfaces -->

    <!-- ============================================ -->
    <!-- PRIMARY BRAND COLORS - NAVY BLUE THEME ONLY -->
    <!-- ============================================ -->

    <!-- Primary brand color - Navy blue (no red/coral) -->
    <color name="ph_primary">#0F3460</color>

    <!-- Primary dark variant - Darker navy -->
    <color name="primary_dark">#0A1A2E</color>

    <!-- Secondary brand color - Lighter navy accent -->
    <color name="ph_primary_2">#16213E</color>

    <!-- Basic Colors -->
    <color name="white">#FFFFFFFF</color>
    <color name="black">#FF000000</color>

    <!-- Status Colors (Navy-themed) -->
    <color name="ph_success">#2E7D64</color>
    <color name="ph_warning">#B8860B</color>
    <color name="ph_danger">#8B3A3A</color>
    <color name="ph_info">#2A5F7A</color>

    <!-- Surface Colors -->
    <color name="ph_surface_dark">#FFFFFF</color>  
</resources>
```

(ii). `themes.xml`:
- Replace `Theme.MicrooApp` with `Theme.YourAppName`.
```xml
<resources xmlns:tools="http://schemas.android.com/tools">
    <!-- Base application theme for night/dark mode. -->
    <style name="Theme.MicrooApp" parent="Theme.MaterialComponents.DayNight.NoActionBar">
        <!-- Primary brand color - Navy blue -->
        <item name="colorPrimary">@color/ph_primary</item>
        <item name="colorPrimaryVariant">@color/primary_dark</item>
        <item name="colorOnPrimary">@color/white</item>

        <!-- Secondary brand color -->
        <item name="colorSecondary">@color/ph_primary_2</item>
        <item name="colorSecondaryVariant">@color/primary_dark</item>
        <item name="colorOnSecondary">@color/white</item>

        <!-- Background colors - Deep navy for night mode -->
        <item name="android:colorBackground">@color/body_background</item>

        <!-- Surface colors - Dark navy surfaces -->
        <item name="colorSurface">@color/ph_surface</item>
        <item name="colorOnSurface">@color/text_primary</item>

        <!-- Alert Dialog Theme - Make buttons white -->
        <item name="materialAlertDialogTheme">@style/Theme.MicrooApp.AlertDialog</item>
        <item name="alertDialogTheme">@style/Theme.MicrooApp.AlertDialog</item>

        <!-- Status bar color - Transparent to show gradient properly -->
        <item name="android:statusBarColor">@android:color/transparent</item>

        <!-- Make status bar icons light (for dark background) -->
        <item name="android:windowLightStatusBar">false</item>

        <!-- App bar/elevation background using linear gradient -->
        <item name="android:windowBackground">@drawable/linear_background</item>

        <!-- Navigation bar color - Match background -->
        <item name="android:navigationBarColor">@color/body_background</item>
    </style>

    <!-- Alert Dialog Theme - White buttons for dark mode -->
    <style name="Theme.MicrooApp.AlertDialog" parent="ThemeOverlay.MaterialComponents.MaterialAlertDialog">
        <!-- Override button colors to white -->
        <item name="colorPrimary">@color/white</item>
        <item name="colorOnPrimary">@color/ph_primary</item>
        <item name="colorSurface">@color/ph_surface</item>
        <item name="colorOnSurface">@color/text_primary</item>
    </style>
</resources>
```

---

16. Expand `xml` directory then replace the code: `backup_rules.xml` with the one below:

(i). `backup_rules.xml`:
```xml
<?xml version="1.0" encoding="utf-8"?><!--  
   Sample backup rules file; uncomment and customize as necessary.   See https://developer.android.com/guide/topics/data/autobackup   for details.   Note: This file is ignored for devices older than API 31   See https://developer.android.com/about/versions/12/backup-restore-->  
<full-backup-content>  
    <!--  
   <include domain="sharedpref" path="."/>   <exclude domain="sharedpref" path="device.xml"/>-->  
</full-backup-content>
```


---

17. Open `AndroidManifest.xml`:
![23. Android Manifest](images/Androidmanifest.png)

Replace all the code inside `AndroidManifest.xml` with the one below. Ensure the value for `android:theme="@style/Theme.MicrooApp"` matches `YourAppName`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!-- Core -->
    <uses-feature
        android:name="android.hardware.camera"
        android:required="false" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Media / Upload -->
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />

    <!-- Storage (legacy support only; scoped storage used) -->
    <uses-permission
        android:name="android.permission.WRITE_EXTERNAL_STORAGE"
        android:maxSdkVersion="28" />

    <!-- Notifications (Android 13+) -->
    <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

    <application 
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MicrooApp"
        tools:targetApi="36">

        <activity            
            android:name=".SplashActivity"
            android:exported="true">
            <intent-filter>                
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>        
        </activity>
        <activity            
            android:name=".MainActivity"
            android:exported="true" 
        />
    </application>
</manifest>
```

---

18. Build the Project Gradle by clicking `Build` (Left of screen) then click the sync icon (As shown below). Ensure Build is successful (having followed all previous steps correctly) otherwise fix any errors displayed in the Terminal Window:
![24. Android Manifest](images/gradle.png)
![25. Build Success](images/output.png)

---

19. Let's Create customized App Icons.   
(a). Right-click on `mipmap-anydpi-v26` then select: New> Image Asset.
![26. Mipmap](images/mipmap.png)

(b). In the next window, customize the size of App Icon and also App icon image using the `Options`, `Background Layer` and `Foreground Layer`. Choose a suitable App logo-image with no-background by choosing from `Path`. Scroll down the `Foreground Layer` in the section `Scaling` and resize the logo perfectly. When satisfied click **Next**:
![27. Customize App Icon](images/icon.png)
![28. Customize App Icon](images/icon2.png)

In the next window, just click **Finish**:
![29. Customize App Icon](images/icon3.png)

---

20. Now you need to create a unique `Apk signature` that every PlayStore app MUST have, then your Apk. Follow the onscreen instructions below:

(a). Click on the `Main Menu` button (as shown below).
![30. Main menu](images/Menu.png)

(b). Select `Build`>`Generate Signed App Bundle or Apk`:
![31. Build](images/menu2.png)

(c). Select `Apk` then click **Next**:  

![32. Signed Apk](images/apk.png)

(d). Select `Create new`:  

![33. Keystore](images/apk2.png)  

(e). Select `Key store path`:  

![34. Keystore](images/keystore.png)  

(f). Create a new folder inside the root of `YourApp` and call it `KeyStore`:  

![35. Keystore folder](images/keystore2.png)  

(g). Open the new folder you've just created called `KeyStore`, then provide a File name for your App Signature Key (It's recommended to use `YourAppName`), then click **Save**:  

![36. Key store](images/keystore3.png)  

(h).  Create a unique password for your Key. ***Use the same password for Alias*** to avoid any confusion. Rename the Alias from `key0` to `YourAppNamekey0`. Fill in the details below accordingly then click **OK**:  

![37. Key store details](images/keystore4.png)  

(i). Select `Remember passwords` (to ensure you don't a password every time you build new apps) then click **Next**.  

![38. Remember Key store](images/keystore5.png)  

(j). In the next window, select `release` then click **Create**:
![38. Apk release](images/apk-final.png)  

(k). Successful Apk Build should look like this (below):
![39. Apk release Terminal Output](images/final-outpuT.png)

---

21. (a). Locate your `Signed Apk` and test it on your mobile device. By default, the `Signed Apk` should be saved inside `YourAppName/app/release`. For instance, in this case, inside `C:/Users/LEWIS/MicrooApp/app/release` (as shown below):

![39. Apk release Terminal Output](images/final-release.png)

 (b). Send yourself a copy of  your `app-release.apk` to your mobile device, install and Test. 
 > [!NOTE]
 > Having followed all the instructions, your installed App should look professional with a Native feel unlike typical websites (even though it's actually a website).
 
 ## Splash Screen

 <p align="center"> 
     <img src="/images/splash.png" alt="MicrooApp Splash Screen" width="180" height="400" style="vertical-align: middle;">
 </p>


## License
This project is licensed under the MIT License. View License [here](https://github.com/Lewismwaz/Website-to-Apk/blob/main/LICENSE).  
More details about the License can be found [here.](https://choosealicense.com/licenses/gpl-3.0/)

## Contact
For any inquiries, please contact:
- Email - <eappointmaster@gmail.com> or <yavotu@gmail.com>
- GitHub - [@Lewismwaz](https://github.com/Lewismwaz)  or [@Yavotu](https://github.com/Yavotu)

[Website-to-Apk](https://github.com/Lewismwaz/Website-to-Apk) **©** 2025, All Rights Reserved

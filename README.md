# GMS SupportMapFragment -> IllegalArgumentException
Application for demonstration IllegalArgumentException in com.google.android.gms:play-services-maps:17.0.1

### Issue: https://issuetracker.google.com/issues/218346034

Steps to reproduce:
1. Create application from template (for example "basic activity" with 2 fragments: A -> B)
2. Add dependency:
```
dependencies {
    implementation "com.google.android.gms:play-services-maps:17.0.1"
...
}
```
3. Add this to layout of Fragment A:
```
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/map"
        android:name="com.google.android.gms.maps.SupportMapFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```
4. And you have to add API_KEY to Minifest (you can skip this step):

```
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.SupportMapFragmentLeak">

        <meta-data android:name="com.google.android.geo.API_KEY" android:value="INVALID_KEY"/>

        <activity
...
        </activity>
    </application>
```

5. Setup StrictMode (https://developer.android.com/reference/android/os/StrictMode):
```
    override fun onCreate(savedInstanceState: Bundle?) {

        StrictMode.setThreadPolicy(
            StrictMode.ThreadPolicy.Builder()
                .detectAll()
                .penaltyLog()
                .build()
        )
        StrictMode.setVmPolicy(
            VmPolicy.Builder()
                .detectAll()
                .penaltyLog()
                .build()
        )
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        setSupportActionBar(findViewById(R.id.toolbar))
    }
    </application>
```

6. Open app. Take a look on logcat:
```
    java.lang.IllegalArgumentException: Tried to access UI constants from a non-visual Context:androidx.multidex.MultiDexApplication@fdd993f
        at android.view.ViewConfiguration.get(ViewConfiguration.java:510)
        at android.view.View.<init>(View.java:5317)
        at android.view.View.<init>(View.java:5467)
        at android.view.ViewGroup.<init>(ViewGroup.java:697)
        at android.widget.LinearLayout.<init>(LinearLayout.java:254)
        at android.widget.LinearLayout.<init>(LinearLayout.java:250)
        at android.widget.LinearLayout.<init>(LinearLayout.java:246)
        at android.widget.LinearLayout.<init>(LinearLayout.java:242)
        at com.google.maps.api.android.lib6.impl.bx.<init>(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):0)
        at com.google.maps.api.android.lib6.impl.by.a(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):8)
        at com.google.maps.api.android.lib6.impl.bi.a(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):40)
        at com.google.maps.api.android.lib6.impl.co.a(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):4)
        at com.google.android.gms.maps.internal.o.a(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):21)
        at cy.onTransact(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):4)
        at android.os.Binder.transact(Binder.java:1043)
        at com.google.android.gms.internal.maps.zza.zzH(com.google.android.gms:play-services-maps@@17.0.1:2)
        at com.google.android.gms.maps.internal.zzk.onCreateView(com.google.android.gms:play-services-maps@@17.0.1:5)
        at com.google.android.gms.maps.zzau.onCreateView(com.google.android.gms:play-services-maps@@17.0.1:7)
        at com.google.android.gms.dynamic.zae.zaa(com.google.android.gms:play-services-base@@17.5.0:4)
        at com.google.android.gms.dynamic.DeferredLifecycleHelper.zaa(com.google.android.gms:play-services-base@@17.5.0:9)
        at com.google.android.gms.dynamic.DeferredLifecycleHelper.onCreateView(com.google.android.gms:play-services-base@@17.5.0:25)
        at com.google.android.gms.maps.SupportMapFragment.onCreateView(com.google.android.gms:play-services-maps@@17.0.1:1)
        at androidx.fragment.app.Fragment.performCreateView(Fragment.java:2698)
        at androidx.fragment.app.FragmentStateManager.createView(FragmentStateManager.java:320)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1187)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1356)
        at androidx.fragment.app.FragmentManager.moveFragmentToExpectedState(FragmentManager.java:1434)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1497)
        at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2625)
        at androidx.fragment.app.FragmentManager.dispatchActivityCreated(FragmentManager.java:2577)
        at androidx.fragment.app.Fragment.performActivityCreated(Fragment.java:2722)
        at androidx.fragment.app.FragmentStateManager.activityCreated(FragmentStateManager.java:346)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1188)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1356)
        at androidx.fragment.app.FragmentManager.moveFragmentToExpectedState(FragmentManager.java:1434)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1497)
        at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2625)
        at androidx.fragment.app.FragmentManager.dispatchActivityCreated(FragmentManager.java:2577)
        at androidx.fragment.app.Fragment.performActivityCreated(Fragment.java:2722)
2022-02-08 11:21:46.567 6937-6937/com.example.supportmapfragmentleak E/ViewConfiguration:     at androidx.fragment.app.FragmentStateManager.activityCreated(FragmentStateManager.java:346)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1188)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1356)
        at androidx.fragment.app.FragmentManager.moveFragmentToExpectedState(FragmentManager.java:1434)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1497)
        at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2625)
        at androidx.fragment.app.FragmentManager.dispatchActivityCreated(FragmentManager.java:2577)
        at androidx.fragment.app.FragmentController.dispatchActivityCreated(FragmentController.java:247)
        at androidx.fragment.app.FragmentActivity.onStart(FragmentActivity.java:541)
        at androidx.appcompat.app.AppCompatActivity.onStart(AppCompatActivity.java:210)
        at android.app.Instrumentation.callActivityOnStart(Instrumentation.java:1435)
        at android.app.Activity.performStart(Activity.java:8018)
        at android.app.ActivityThread.handleStartActivity(ActivityThread.java:3475)
        at android.app.servertransaction.TransactionExecutor.performLifecycleSequence(TransactionExecutor.java:221)
        at android.app.servertransaction.TransactionExecutor.cycleToPath(TransactionExecutor.java:201)
        at android.app.servertransaction.TransactionExecutor.executeLifecycleState(TransactionExecutor.java:173)
        at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:97)
        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2066)
        at android.os.Handler.dispatchMessage(Handler.java:106)
        at android.os.Looper.loop(Looper.java:223)
        at android.app.ActivityThread.main(ActivityThread.java:7656)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:592)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:947)
2022-02-08 11:21:46.575 6937-6937/com.example.supportmapfragmentleak E/ViewConfiguration: Tried to access UI constants from a non-visual Context:androidx.multidex.MultiDexApplication@fdd993fUI constants, such as display metrics or window metrics, must be accessed from Activity or other visual Context. Use an Activity or a Context created with Context#createWindowContext(int, Bundle), which are adjusted to the configuration and visual bounds of an area on screen
    java.lang.IllegalArgumentException: Tried to access UI constants from a non-visual Context:androidx.multidex.MultiDexApplication@fdd993f
        at android.view.ViewConfiguration.get(ViewConfiguration.java:510)
        at android.view.View.<init>(View.java:5317)
        at android.view.View.<init>(View.java:5467)
        at android.view.ViewGroup.<init>(ViewGroup.java:697)
        at android.view.ViewGroup.<init>(ViewGroup.java:693)
        at android.view.ViewGroup.<init>(ViewGroup.java:689)
        at android.view.ViewGroup.<init>(ViewGroup.java:685)
        at android.widget.FrameLayout.<init>(FrameLayout.java:85)
        at com.google.maps.api.android.lib6.impl.bi.a(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):49)
        at com.google.maps.api.android.lib6.impl.co.a(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):4)
        at com.google.android.gms.maps.internal.o.a(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):21)
        at cy.onTransact(:com.google.android.gms.dynamite_mapsdynamite@201817056@20.18.17 (040800-0):4)
        at android.os.Binder.transact(Binder.java:1043)
        at com.google.android.gms.internal.maps.zza.zzH(com.google.android.gms:play-services-maps@@17.0.1:2)
        at com.google.android.gms.maps.internal.zzk.onCreateView(com.google.android.gms:play-services-maps@@17.0.1:5)
        at com.google.android.gms.maps.zzau.onCreateView(com.google.android.gms:play-services-maps@@17.0.1:7)
        at com.google.android.gms.dynamic.zae.zaa(com.google.android.gms:play-services-base@@17.5.0:4)
        at com.google.android.gms.dynamic.DeferredLifecycleHelper.zaa(com.google.android.gms:play-services-base@@17.5.0:9)
        at com.google.android.gms.dynamic.DeferredLifecycleHelper.onCreateView(com.google.android.gms:play-services-base@@17.5.0:25)
        at com.google.android.gms.maps.SupportMapFragment.onCreateView(com.google.android.gms:play-services-maps@@17.0.1:1)
        at androidx.fragment.app.Fragment.performCreateView(Fragment.java:2698)
        at androidx.fragment.app.FragmentStateManager.createView(FragmentStateManager.java:320)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1187)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1356)
        at androidx.fragment.app.FragmentManager.moveFragmentToExpectedState(FragmentManager.java:1434)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1497)
        at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2625)
        at androidx.fragment.app.FragmentManager.dispatchActivityCreated(FragmentManager.java:2577)
        at androidx.fragment.app.Fragment.performActivityCreated(Fragment.java:2722)
        at androidx.fragment.app.FragmentStateManager.activityCreated(FragmentStateManager.java:346)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1188)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1356)
        at androidx.fragment.app.FragmentManager.moveFragmentToExpectedState(FragmentManager.java:1434)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1497)
        at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2625)
        at androidx.fragment.app.FragmentManager.dispatchActivityCreated(FragmentManager.java:2577)
        at androidx.fragment.app.Fragment.performActivityCreated(Fragment.java:2722)
        at androidx.fragment.app.FragmentStateManager.activityCreated(FragmentStateManager.java:346)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1188)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1356)
2022-02-08 11:21:46.575 6937-6937/com.example.supportmapfragmentleak E/ViewConfiguration:     at androidx.fragment.app.FragmentManager.moveFragmentToExpectedState(FragmentManager.java:1434)
        at androidx.fragment.app.FragmentManager.moveToState(FragmentManager.java:1497)
        at androidx.fragment.app.FragmentManager.dispatchStateChange(FragmentManager.java:2625)
        at androidx.fragment.app.FragmentManager.dispatchActivityCreated(FragmentManager.java:2577)
        at androidx.fragment.app.FragmentController.dispatchActivityCreated(FragmentController.java:247)
        at androidx.fragment.app.FragmentActivity.onStart(FragmentActivity.java:541)
        at androidx.appcompat.app.AppCompatActivity.onStart(AppCompatActivity.java:210)
        at android.app.Instrumentation.callActivityOnStart(Instrumentation.java:1435)
        at android.app.Activity.performStart(Activity.java:8018)
        at android.app.ActivityThread.handleStartActivity(ActivityThread.java:3475)
        at android.app.servertransaction.TransactionExecutor.performLifecycleSequence(TransactionExecutor.java:221)
        at android.app.servertransaction.TransactionExecutor.cycleToPath(TransactionExecutor.java:201)
        at android.app.servertransaction.TransactionExecutor.executeLifecycleState(TransactionExecutor.java:173)
        at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:97)
        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2066)
        at android.os.Handler.dispatchMessage(Handler.java:106)
        at android.os.Looper.loop(Looper.java:223)
        at android.app.ActivityThread.main(ActivityThread.java:7656)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:592)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:947)

```

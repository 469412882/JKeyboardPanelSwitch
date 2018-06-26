# The integration tutorial of The non-fullscreen theme Or The status bar is translucent with `fitsSystemWindows=true`

> This tutorial covers following two cases:

> 1. The theme is non-fullscreen (`(activity.getWindow().getAttributes().flags & WindowManager.LayoutParams.FLAG_FULLSCREEN) == 0`) 。
> 2. The status bar is translucent(`(activity.getWindow().getAttributes().flags & WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS) != 0`) and the root layout with  `fitsSystemWindows=true`。

![][non-fullscreen_resolved_gif]

## I. `AndroidManifest`

> You can directly refer to: [AndroidManifest.xml][AndroidManifest_xml_link]

> The relate Activity，in the **configuration of `AndroidManifest`**`android:windowSoftInputMode=adjustResize`

```xml
<manifest
  ...>
  <application
    ...>

    <activity
      android:name=".activity.ChattingActivity"
      android:windowSoftInputMode=adjustResize"/>
      ...
  </application>
  ...
</manifest>
```

## II. The layout xml

> You can directly refer to: [activity_chatting_resolved.xml][activity_chatting_resolved_xml_link]

1. You need to use **The top layout** ([KPSwitchRootFrameLayout][KPSwitchRootFrameLayout_link]/[KPSwitchRootLinearLayout][KPSwitchRootLinearLayout_link]/[KPSwitchRootRelativeLayout][KPSwitchRootRelativeLayout_link])
2. you need to use **The panel layout**([KPSwitchPanelFrameLayout][KPSwitchPanelFrameLayout_link]/[KPSwitchPanelLinearLayout][KPSwitchPanelLinearLayout_link]/[KPSwitchPanelRelativeLayout][KPSwitchPanelRelativeLayout_link])。

The sample demonstrate:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 可选用 KPSwitchRootLinearLayout、KPSwitchRootRelativeLayout、KPSwitchRootFrameLayout -->
<cn.dreamtobe.kpswitch.widget.KPSwitchRootLinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <!-- some content layout/view -->
    ...

    <!-- Alternative: KPSwitchPanelLinearLayout、KPSwitchPanelRelativeLayout、KPSwitchPanelFrameLayout -->
    <cn.dreamtobe.kpswitch.widget.KPSwitchPanelLinearLayout
        android:id="@+id/panel_root"
        android:layout_width="fill_parent"
        android:layout_height="@dimen/panel_height"
        android:visibility="gone">
        <!-- The content in panel layout -->
        ...
    </cn.dreamtobe.kpswitch.widget.KPSwitchPanelLinearLayout>

</cn.dreamtobe.kpswitch.widget.KPSwitchRootLinearLayout>
```

## III. Activity:

> You can directly refer to: [ChattingResolvedActivity.java][ChattingResolvedActivity_link]

1. Handle some events([KPSwitchConflictUtil][KPSwitchConflictUtil_link])
2. Listener the keyboard status(Height and whether is showing)([KeyboardUtil#attach()][KeyboardUtil_attach_link])

The sample demonstrate:

```java
...

// The panel layout.
private KPSwitchPanelLinearLayout mPanelLayout;
// The cursor focus view, used for receiving the input content
private EditText mSendEdt;
// The view used for triggering switch between the keyboard and the panel layout.
private ImageView mPlusIv;

@Override
public void onCreate(Bundle saveInstanceState){
    ...

    mPanelLayout = (KPSwitchPanelLinearLayout)findViewById(R.id.panel_root);
    mSendEdt = (EditText) findViewById(R.id.send_edt);
    mPlusIv = (ImageView) findViewById(R.id.plus_iv);

    /**
     * This Util mainly to watch the keyboard status: showing or not And the keyboard height.
     * There is also a method private a listener for upper to listener the keyboard status, the
     * detail refer to {@Link KeyboardUtil#attach(Activity, IPanelHeightTarget, OnKeyboardShowingListener)}
     */
    KeyboardUtil.attach(this, mPanelLayout);

    /**
     * This Util mainly to assist handling the conflict between the keyboard and the panel layout.
     * This method mainly to register some event, such as switch between the keyboard and the panel
     * layout etc. The source code is very simple, you can check it out by yourself.
     * There are also some toolset method: show-keyboard、show-panel-layout、switch-panel-keyboard、
     * hide-panel-keyboard、etc.
     *
     * If you have more than one panel, please refer to :
     * KPSwitchConflictUtil.attach(panelLayout, focusView, switchClickListener, subPanelAndTriggers...)
     *
     * @param panelRoot The panel layout.
     * @param switchPanelKeyboardBtn The view used for switching between the keyboard and the panel layout.
     * @param focusView The cursor focus view, this view is usually a EditText which used to receive inputing content.
     */
    KPSwitchConflictUtil.attach(mPanelLayout, mPlusIv, mSendEdt);

}

...

...

// If you want the panel can be hidden when use press the back-button
@Override
public boolean dispatchKeyEvent(KeyEvent event){
    if (event.getAction() == KeyEvent.ACTION_UP &&
            event.getKeyCode() == KeyEvent.KEYCODE_BACK) {
        if (mPanelLayout.getVisibility() == View.VISIBLE) {
            KPSwitchConflictUtil.hidePanelAndKeyboard(mPanelLayout);
            return true;
        }
    }
    return super.dispatchKeyEvent(event);
}
```

---

> More detail about the principle, please move to [README](https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/README.md)；You also can move to: [The integration tutorial of The fullscreen theme Or The status bar is translucent with `fitsSystemWindows=false`](https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/FULLSCREEN_TUTORIAL.md)。

[non-fullscreen_resolved_gif]: https://raw.githubusercontent.com/Jacksgong/JKeybordPanelSwitch/master/art/non-fullscreen_resolved.gif
[AndroidManifest_xml_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/app/src/main/AndroidManifest.xml
[activity_chatting_resolved_xml_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/app/src/main/res/layout/activity_chatting_resolved.xml
[KPSwitchRootFrameLayout_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/widget/KPSwitchRootFrameLayout.java
[KPSwitchPanelLinearLayout_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/widget/KPSwitchPanelLinearLayout.java
[KPSwitchPanelRelativeLayout_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/widget/KPSwitchPanelRelativeLayout.java
[KPSwitchPanelFrameLayout_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/widget/KPSwitchPanelFrameLayout.java
[KPSwitchRootRelativeLayout_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/widget/KPSwitchRootRelativeLayout.java
[KPSwitchRootLinearLayout_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/widget/KPSwitchRootLinearLayout.java
[ChattingResolvedActivity_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/app/src/main/java/cn/dreamtobe/kpswitch/demo/activity/ChattingResolvedActivity.java
[KPSwitchConflictUtil_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/util/KPSwitchConflictUtil.java
[KeyboardUtil_attach_link]: https://github.com/Jacksgong/JKeyboardPanelSwitch/blob/master/library/src/main/java/cn/dreamtobe/kpswitch/util/KeyboardUtil.java#L134

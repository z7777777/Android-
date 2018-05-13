# 实现设置Activity

## 使用PrefereceFragment实现设置页面
基本要求：实现如下两个图拼接而成的设置界面。总共四组设置项
- 1、In-line preferences
- CheckBoxPreference
- 2、Dialog-based preferences:
- EditTextPreference
- ListPreference
- 3、Launch preferences
- PreferenceScreen: 跳转到另一个PreferenceScreen
- PreferenceScreen: 启动一个网页
- 4、Preference attributes
- CheckBox: 父选项
- CheckBox: 子选项，当父选项勾选时呈现

### I. 工程名
PrefereceFragment
### II. 主要源代码
MainActivity.java
```
import android.preference.PreferenceFragment;
import android.support.annotation.Nullable;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //加载Fragment
        getFragmentManager().beginTransaction().replace(android.R.id.content, new PrefsFragement()).commit();
    }

    //建一个PrefFragment类继承自PreferenceFragment然后加载首选项配置的xml文件preferences.xml
    public static class PrefsFragement extends PreferenceFragment {
        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            addPreferencesFromResource(R.xml.preferences);
        }
    }
}
```

preferences.xml
```
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
    android:title="FragmentPerference">
    <PreferenceCategory android:title="In-line preferences">
        <CheckBoxPreference
            android:key="checkbox_preference"
            android:title="Checkbox preference"
            android:summary="This is a checkbox" />
    </PreferenceCategory>

    <PreferenceCategory android:title="Dialog-based preferences">
        <EditTextPreference
            android:key="edittext_preference"
            android:title="Edit text preference"
            android:summary="An example that uses an edit text dialog"
            android:dialogTitle="Enter your favorite animal"/>

        <ListPreference
            android:key="list_preference"
            android:title="List preference"
            android:summary="An example that uses a list dialog"
            android:dialogTitle="Choose one"
            android:entries="@array/codes"
            android:entryValues="@array/codeValues"/>
    </PreferenceCategory>

    <PreferenceCategory android:title="Launch preference">
        <PreferenceScreen
            android:key="screen_preference"
            android:title="Screen preference"
            android:summary="Shows another screen of preferences">
            <!-- 在这里放置更多的首选项内容，将被在下一个页面呈现出来 -->
            <CheckBoxPreference
                android:key="next_screen_checkbox_preference"
                android:title="Toggle preference"
                android:summary="Perference that is on the next screen but same hierarchy"/>
        </PreferenceScreen>

        <PreferenceScreen
            android:title="Intent preference"
            android:summary="Launches an Activity from an Intent">
            <intent
                android:action="android.intent.action.VIEW"
                android:data="http://www.baidu.com"/>
        </PreferenceScreen>
    </PreferenceCategory>

    <PreferenceCategory android:title="Preference attributes">
        <CheckBoxPreference
            android:key="parent_checkbox_preference"
            android:title="Parent checkbox preference"
            android:summary="This is visually a parent"/>
        <!-- 子类的可见类型是由样式属性定义的 -->
        <CheckBoxPreference
            android:key="child_checkbox_preference"
            android:title="Child checkbox preference"
            android:summary="This is visually a child"
            android:dependency="parent_checkbox_preference"
            android:layout="?android:attr/preferenceLayoutChild"/>

    </PreferenceCategory>

</PreferenceScreen>
```
### 实验截图
![image](https://note.youdao.com/yws/public/resource/693d04e4173598ff2e4634ce3bb4d3e4/xmlnote/878A036F52A549FC8A24166DBC681A1B/636)
![image](https://note.youdao.com/yws/public/resource/693d04e4173598ff2e4634ce3bb4d3e4/xmlnote/A0DE5A8907C14DE6B7F810B94E1A51F1/638)
![image](https://note.youdao.com/yws/public/resource/693d04e4173598ff2e4634ce3bb4d3e4/xmlnote/70A9B07F2C2E461394BF9771D0698D20/626)
![image](https://note.youdao.com/yws/public/resource/693d04e4173598ff2e4634ce3bb4d3e4/xmlnote/E9D2635C22BC46D7998FC91A50240B36/628)
![image](https://note.youdao.com/yws/public/resource/693d04e4173598ff2e4634ce3bb4d3e4/xmlnote/2F1FD385B5FC46CDBB56A02FE6AD7289/632)
![image](https://note.youdao.com/yws/public/resource/693d04e4173598ff2e4634ce3bb4d3e4/xmlnote/C4BF628826AC4D1BA3004A05178B7B51/634)

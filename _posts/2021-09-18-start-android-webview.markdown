---
layout: post
title: "[Webview] Android WebView 시작하기"
date: 2021-09-18
tags: android, kotlin, webview, react, javascript interface, execute function, 리액트, 웹뷰, 자바스크립트
---

# Android 웹뷰 시작하기 
<pre class="info-panel">
 - Android에서 웹뷰 연동 시작하기에 대해 공부해 보겠습니다.
 - 다만 kotlin 와 android 개발을 처음 접하다보니 native 개발 지식이 부족할 수 있습니다.
 - webview로 사용될 react 화면은 이전 블로그 글 소스를 이용하겠습니다.
 - https://jonghyucklee.github.io/2021/09/10/ios-webview-exchange-data/
</pre>

## Android 코드 작성
### MainActivity
{%highlight kotlin%}
package com.example.androidwebview

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.webkit.WebView

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val webView: WebView = findViewById(R.id.webView)
        webView.settings.javaScriptEnabled = true
        webView.loadUrl("[local server ip] : [port]")

    }
}
{%endhighlight%}

- Ios에 비해 Android 코드가 직관적이고 바로 보기 쉽습니다.
- WebView 관련 패키지를 import하고 사용하면 됩니다.
- javaScriptEnabled : WebView에서 js를 사용할 수 있는지에 대한 설정값입니다.
- loadUrl : webView에 로드할 url입니다.

## 실행화면 - ERROR
<img src="{{site.baseurl}}/images/Android/androidLoadError.png"/>

- webView가 제대로 로드되지 않습니다.
- http 로드를 시도하려고 할 때 안드로이드 내의 보안정책에 위배되기 때문입니다.
- net::ERR_CLEARTEXT_NOT_PERMITTED 에러가 발생합니다.
- 에러를 하기 위해 추가적인 설정을 해주어야 합니다.

## local ip 알아내기

- 커맨드 창 열기
- ifconfig 명령어 입력
- en0 > inet ip 정보를 알아냅니다.

## res > xml > network_security_config.xml 작성
{%highlight xml%}
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">[위에서 알아낸 local ip]</domain>
    </domain-config>
</network-security-config>
{%endhighlight%}

## manifests > AndroidManifest.xml
{%highlight xml%}
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.androidwebview"
    >
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:networkSecurityConfig="@xml/network_security_config" // 허용 ip 목록이 수록된 xml입니다.
        android:theme="@style/Theme.AndroidWebView">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
{%endhighlight%}

- 허용할 local ip를 network_security_config.xml에 등록해줍니다.
- 허용 목록이 든 network_security_config.xml을 AndroidManifest.xml에 등록해줍니다.

## directory 목록

 <img src="{{site.baseurl}}/images/Android/directoryList.png"/>
 
## 실행화면
 
 <img src="{{site.baseurl}}/images/Android/afterAllowSecurity.png"/>
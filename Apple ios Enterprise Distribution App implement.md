## 實作背景
* 不希望 App 可公開下載，只希望特定人士下載
* 用戶裝置不希望公司或學校裝置管理，Apple Business Manager MDM 無法使用
* 有跨國需求，在一些條件下，redeem code solution 無法使用

## 甚麼是 Enterprise Distribution?
用戶透過存取公開的網際網路連結，手動下載安裝憑證以及企業App

## 必要條件
需要開通 企業開發者帳號 (Apple Developer Enterprise Program)

## 在 Apple Developer 建立 Identifier, Certificate, Profile
Identifier 選擇 "App IDs"，在後面的步驟中也稱 bundle id
![](https://live.staticflickr.com/65535/54399319323_1b11795414_k.jpg)
後面步驟中，Capabilities, App Services 不需選擇任何選項

Certificate 選擇 "In-House and Ad Hoc"
![](https://live.staticflickr.com/65535/54399100876_024f27689f_c.jpg)

這個步驟需上傳本機產生的憑證
![](https://live.staticflickr.com/65535/54399467625_77c6f7c901_c.jpg)

透過 macos 的 Keychain 產生本機憑證
![](https://live.staticflickr.com/65535/54399110101_d77130b351_c.jpg)

Profile 選擇 "In House"
![](https://live.staticflickr.com/65535/54398224112_e1df97f658_c.jpg)
Profile 後面步驟需選擇前面步驟產生的 Identifier 以及 Certificate

## Distribute App
選擇 "Enterprise"
![](https://live.staticflickr.com/65535/54399479705_032a0a75fa_c.jpg)

在 distribution enterprice app 後，會得到
* xxx.ipa
* ExportOptions.plist
* DistributionSummary.plist

通常只需要 xxx.ipa

## 準備 manifest.plist
接下來自行創建一個 manifest.plist 檔案，manifest.plist 內容如下
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
<key>items</key>
<array>
    <dict>
        <key>assets</key>
        <array>
            <dict>
                <key>kind</key>
                <string>software-package</string>
                <key>url</key>
                <!-- 必要，ipa檔案在網路上可供下載的位置 -->
                <string>https://xxx.com/xxx.ipa</string>
            </dict>
        </array>
        <key>metadata</key>
        <dict>
            <!-- 必要，在 xcode 操作 distribution enterprice 前所設定的 bundle id -->
            <key>bundle-identifier</key>
            <string>test_bundle_identifier</string>
            <!-- 選填 -->
            <key>bundle-version</key>
            <string>1.1.0</string>
            <key>kind</key>
            <string>software</string>
             <!-- 必要，下載時顯示 -->
            <key>title</key>
            <string>example app</string>
        </dict>
    </dict>
</array>
</dict>
</plist>
```
manifest.plist 裡面有三個資訊需自行填入 `<string><string>` 裡面， `<key>` 標籤是 url, bundle-identifier, title
* url
xxx.ipa 需放置在網際網路上可供存取的位置，其 url 寫在這裡 
* bundle-identifier
在操作 distribution enterprice 前，所設置的 bundle id
* title
下載時顯示 App 的名字

## manifest.plist & certificate 需可供用戶存取
將 manifest.plist 放置在網際網路上可供存取的位置`https://xxx.com/manifest.plist`

給用戶的 url 需要加上 `itms-services://?action=download-manifest`
`itms-services://` 是一種 URL 協議，用於 iOS 設備上直接安裝應用程式。它通常用於企業或開發者向測試人員或內部用戶分發應用，而不通過 App Store。這樣的鏈接允許使用者點擊後直接在 iPhone 或 iPad 上下載並安裝應用。
`itms-services://` 表示使用 Apple 特定的安裝協議。
`?action=download-manifest` 表示該鏈接將下載一個「manifest」文件，這是一個用於描述應用資訊的 XML 文件。
`url=""` 應包含指向 manifest 文件的 URL

最後給用戶的 url 會是這樣 
`itms-services://?action=download-manifest&url=https://xxx.com/manifest.plist`

將先前生成的憑證描述檔(certificate)放置在網際網路上可供存取的位置`https://xxx.com/certificate.cer`
用戶需下載安裝憑證描述檔以及透過 Apple 特定的安裝協議下載manifest.plist才可完成手動安裝企業App


## 手動安裝企業App
用戶需先下載安裝描述檔，請注意這一步需使用 Safari瀏覽器操作
用 Safari瀏覽器存取`https://xxx.com/certificate.cer`後，會跳出
![](https://live.staticflickr.com/65535/54399293099_3bdb6da66f_c.jpg)

安裝描述檔後，我們再用瀏覽器存取`itms-services://?action=download-manifest&url=https://xxx.com/manifest.plist`
畫面上應該會跳出
![](https://live.staticflickr.com/65535/54399296914_f431fc79d8_c.jpg)
點擊後，該 App 就會自動下載安裝

接下來如果我們開啟該 App，會跳出 App 未受信任
![](https://live.staticflickr.com/65535/54398237007_510a99bbb9_c.jpg)
需到「設定」>「一般」>「VPN 與裝置管理」，在「企業 App」區段中，點一下 App 開發者的名稱
![](https://live.staticflickr.com/65535/54398242767_260f6476aa_c.jpg)
下一個畫面會列出該開發者的 App，以及這些 App 是否已經過驗證。點一下以信任 App 開發者，然後點一下「信任」來確認你的決定。
![](https://live.staticflickr.com/65535/54399308339_3355e4a84e_c.jpg)
更多有關信任的資訊可參考
https://support.apple.com/zh-tw/118254

信任後即可順利開啟企業App
我們就完成全部的實作。

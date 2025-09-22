---
title: 在rooted設備上使用Magisk進行OTA更新
date: 2025-09-22 18:35:11
tags: Android, Magisk, OTA
---


# 在rooted設備上使用Magisk進行OTA更新[保留root和TWRP]

> 您已收到OTA推送更新，但由於您的Android設備已root而無法安裝它？嗯，這是rooted的Android設備的已知缺點。
> 在本指南中，您將學習如何使用Magisk在rooted的Android設備上安裝OTA更新。
> 通過使用本指南中提供的方法，您還可以在安裝OTA更新後保留`root`和`TWRP`。在進一步了解之前，讓我們快速看一下OTA更新是什麼以及它如何運作。

## 什麼是OTA更新？

OTA更新又名空中升級(Over-the-Air Update)，是一種通過無線方式接收和安裝Android設備軟件更新的方法。

它由設備製造商（也稱為“OEM”）遠程推送，並且是將Android設備的軟件更新到最新可用版本的最簡單，最有效的方法。OTA更新到達後，它會自動安裝或提示用戶手動下載並安裝它。

那麼，如何在rooted的Android設備上安裝OTA更新？您將在下一部分中了解更多信息。

## 為什麼您不能在rooted的Android設備上自動安裝OTA更新？

rooted的Android設備很棒，它可以使用戶對設備的軟件進行極大的控制，並可以對其進行任何形式的修改。但是，它有其自身的缺點。最大的問題是失去了進行自動OTA（無線）更新的能力。而且，用戶（通常是新手）在對其Android設備進行root時往往會忽略此因素。  
![Why-Cant-Install-OTA-Updates-on-Rooted-Android-Devices.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Why-Cant-Install-OTA-Updates-on-Rooted-Android-Devices.jpg "無法更新")  
在Android 5.0及更高版本中，Google引入了基於塊的OTA的概念。這種新機制會執行某些OTA之前的塊檢查，以驗證設備軟件的完整性。用外行話來說，一旦安裝開始，將檢查設備分區（例如 `system`，`boot`，`vendor`，`dtbo` 等）是否進行了諸如root，TWRP之類的修改。如果檢測到，則OTA更新將無法安裝。

## Magisk root，A/B分區和OTA更新

但是，借助Magisk，您可以輕鬆地在rooted的Android設備上安裝OTA更新，而不會丟失root。對於那些不懂的人，Magisk是一個通用的Systemless接口，可讓您以systemless方式修改Android設備的軟件，即無需更改/system分區。

開發者設計了兩種不同的方法來安裝Magisk並root Android設備：

 1. 第一種方法遵循傳統的流程，即安裝自定義Recovery（TWRP，OrangeFox等），然後使用它來刷寫Magisk安裝程序zip（root軟件包）。但是，由於您將在此方法期間安裝自定義Recovery，因此，進行OTA更新變得更加困難。因此，要恢復自動進行OTA更新或手動安裝的功能，您需要首先卸載/刪除root以及應用於該軟件的其他修改。此外，在您的設備上恢復原始的Recovery。
 2. 第二種方法是Google在2016年首次發布Google Pixel設備時引入的。這些設備帶有A/B雙分區系統，以支援無縫更新，並從設備中完全刪除了Recovery分區(recovery合併到boot中了)。因此，第一種方法嚴重失敗。這種新方法涉及使用Magisk為原始boot映像打補丁，然後通過Fastboot刷寫打補丁後的boot映像。
使用Magisk root Android設備的最好之處在於，它使您能夠輕鬆安裝OTA更新。因此，請按照以下說明進行操作。

**注意**：如果要從Android 10升級到Android 11，請確保已更新到Magisk 21.0或最新的Magisk Canary版本。v20.4及更低版本不支持Android 11。

## 如何在rooted的Android設備上安裝OTA更新？

現在，由於大多數新的OEM Android設備都帶有A/B分區方案，因此OTA更新可無縫安裝到非活動插槽。當您使用Magisk root時，它僅安裝在Android設備的當前活動插槽中。因此，不活動的插槽/分區保持不變。

當OTA到達時，您可以簡單地從Magisk Manager中還原原始boot映像，然後將OTA更新安裝到非活動插槽。最後，就在重新啟動手機之前，您可以將Magisk安裝到不活動的插槽中，以在更新後保留root。

聽起來還是有些混亂嗎？不用擔心，以下說明將帶您詳細了解完整的過程。為了方便起見，我們將說明分為5個不同的步驟，以便您可以準確了解自己在做什麼。

### 步驟1：在設置中禁用自動系統更新

在Android 8.0 Oreo（及更高版本）中，Google引入了新的自動更新策略，該策略會在可用時立即下載並安裝OTA更新。

儘管這對於一般的Android用戶來說很棒，但是對於rooted設備的用戶而言卻不是。為了更新您root的Android手機，您必須首先阻止系統在沒有您確認的情況下自動安裝更新。

只需按照以下步驟在Android中禁用自動系統更新：

 1. 轉到Android設備上的“**設置**”菜單。
 2. 瀏覽設置，然後選擇**關於手機**菜單。
 3. 向下滾動到底部，找到“**內部版本號**” 部分。
    + 注意：對於某些OEM設備，“內部版本號”部分可能會進一步嵌套在“設置”菜單中。例如，在Samsung Galaxy設備中，您需要進入設置→關於手機→軟件信息。
 4. 現在，連續點擊“內部版本號”部分5次，以在設備上啟用“**開發人員選項**”。  
![Install-OTA-Updates-on-Rooted-Android-Devices-Enable-Developer-Options.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-OTA-Updates-on-Rooted-Android-Devices-Enable-Developer-Options.jpg)  
 5. 啟用後，轉到**設置→系統→開發人員選項**。
 6. 向下滾動並關閉“**自動系統更新**”開關。  
![Install-OTA-Updates-on-Rooted-Android-Devices-Disable-Automatic-System-Updates.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-OTA-Updates-on-Rooted-Android-Devices-Disable-Automatic-System-Updates.jpg)

### 步驟2：在OTA可用時還原庫存啟動映像

現在，您的Android設備已root並且OTA更新通知已到達。當OTA更新可用時，您將需要還原原始boot映像（和dtbo，如果適用），並從當前插槽中卸載Magisk。  
![Install-OTA-Updates-on-Rooted-Android-Devices-Restore-Stock-Boot-Image-in-Magisk-Manager.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-OTA-Updates-on-Rooted-Android-Devices-Restore-Stock-Boot-Image-in-Magisk-Manager.jpg)  
為此，請轉到應用程序抽屜並啟動**Magisk Manager**應用程序。然後點擊“**卸載**”按鈕，然後選擇“**還原原始映像**”選項。現在，您應該看到屏幕上顯示“**恢復已完成！**”消息。

**不要！絕對不要立即重啟手機**，否則Magisk將被完全卸載。

### 步驟3：在您的Root根Android設備上安裝OTA更新

現在，在還原了原始boot映像後，您可以在rooted的Android設備上自由安裝OTA更新。只需轉到設備**設置→系統→系統更新**，然後按“**下載並安裝**”按鈕。  
![Install-the-OTA-Update-on-your-Rooted-Android-Device.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-the-OTA-Update-on-your-Rooted-Android-Device.jpg)  
安裝完成後，將提示您重新啟動手機。**請勿重新啟動您的設備！**只需繼續下一步即可。

### 步驟4 [可選]：OTA安裝後保留TWRP Recovery

這是一個可選步驟，只有在使用TWRP（方法1）安裝了Magisk的情況下，才可以執行此步驟。安裝OTA更新後，它將使您能夠保留TWRP。

為此，您需要從Magisk Manager中下載並安裝“TWRP A/B Retention Script”（由XDA Recognized Developer osm0sis開發）。為此，請按照以下步驟操作：

 1. 轉到應用程序抽屜，然後啟動Magisk Manager應用程序。
 2. 點擊應用程序窗口左上角的菜單圖標（3條水平線）。  
![Download-TWRP-AB-Retention-Script-from-Magisk-Repository.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Download-TWRP-AB-Retention-Script-from-Magisk-Repository.jpg)  
 3. 選擇“**下載**”。
 4. 現在，在模塊存儲庫中搜索“**TWRP A/B Retention Script**”。  
![Install-TWRP-AB-Retention-Script-from-Magisk-Repository.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-TWRP-AB-Retention-Script-from-Magisk-Repository.jpg)  
 5. 接下來，點擊下載圖標，然後選擇**安裝**選項。

現在，該腳本應已安裝在您的手機上。現在，您無需再執行其他任何操作。該腳本將完成其工作，並在OTA安裝完成後保留TWRP。

### 步驟5：OTA安裝後保留Magisk root

將Magisk安裝到非活動插槽的最後一步。在OTA安裝完成並將設備重新引導到更新的OS之後，這將保留root。

 1. 打開**Magisk Manager**。  
![Install-Magisk-to-Inactive-Slot-01.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-Magisk-to-Inactive-Slot-01.jpg)  
 2. 點擊“**安裝**”按鈕，然後選擇“**安裝**”選項。
 3. 提示選擇安裝方法時，選擇“**安裝到非活動插槽（在OTA之後）**”。  
![Install-Magisk-to-Inactive-Slot-02.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-Magisk-to-Inactive-Slot-02.jpg)  
 4. 最後，當確認警告消息出現在屏幕上後點擊“**是**”。
 5. 現在，Magisk將安裝在非活動的插槽上。  
![Install-Magisk-to-Inactive-Slot-03.jpg](https://raw.githubusercontent.com/muink/GistLib/master/install-ota-update-rooted-android-device-guide/Install-Magisk-to-Inactive-Slot-03.jpg)  
 6. 該過程完成後，點擊“**重新啟動**”按鈕。

Magisk Manager現在將強制將您的Android設備切換到安裝OTA更新的非活動插槽。這將有助於防止系統可能進行的任何OTA後驗證。

是的！您剛剛用Magisk在rooted的Android設備上成功安裝了OTA更新。因此，下次OTA更新到達時，您可以按照相同的說明來升級設備，而且也不會丟失root。

如果您對此處列出的說明/過程有任何疑問，請隨時告訴我們。我們將盡力幫助您。確保提到設備的名稱（如果適用，則為型號）和安裝的軟件內部版本號。這將使我們能夠快速有效地幫助您。

## 文章來源 (该文章为转载)
 - [(本文)](https://gist.github.com/muink/6168ee24b2376e7073adf9825e006d49#file-install-ota-update-rooted-android-device-guide-md)
 - [(原文) How to Install OTA Updates on Rooted Android Devices using Magisk [Without Losing Root]](https://web.archive.org/web/20210111094953/https://www.thecustomdroid.com/install-ota-update-rooted-android-device-guide/)
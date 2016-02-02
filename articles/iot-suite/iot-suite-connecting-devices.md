<properties
   pageTitle="Windows で C を使用してデバイスを接続する |Microsoft Azure"
   description="C で記述され、Windows で実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート管理ソリューションに接続する方法について説明します。"
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>



# デバイスを IoT Suite リモート監視構成済みソリューションに接続する

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Windows で C のサンプル ソリューションをビルドして実行する

1. クローンを作成する、 *Microsoft Azure の IoT Sdk* GitHub リポジトリし、インストール、 *C の Microsoft Azure の IoT デバイス SDK* 、Windows デスクトップ環境で次の [Windows 開発環境 ][lnk-setup-windows] 指示します。

2. Visual Studio 2015 で開く、 **remote_monitoring.sln** でソリューション、 **c\\serializer\\samples\\remote_monitoring\\windows** フォルダー、リポジトリのローカル コピーにします。

3. **ソリューション エクスプローラー**で、**remote_monitoring** プロジェクトの **remote_monitoring.c** ファイルを開きます。

4. ファイル内で次のコードを見つけます。

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. リモート監視ソリューション ダッシュボードから、**[Device Id]** と **[Device Key]** をデバイス用の値に置き換えます。

6. ダッシュボードから **IoT Hub ホスト名**を使用して、**[IoTHub Name]** と **[IoTHub Suffix, i.e. azure-devices.net]** を置き換えます。 たとえば、**IoT Hub ホスト名**が **contoso.azure-devices.net** の場合は、次に示すように、**[IoTHub Name]** を **contoso** に置き換え、**[IoTHub Suffix, i.e. azure-devices.net]** を **azure-devices.net** に置き換えます。

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. **ソリューション エクスプローラー**で、**remote_monitoring** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックして、サンプルをビルドして実行します。 アプリケーションがサンプル テレメトリを IoT Hub に 送信すると、コンソールにメッセージが表示されます。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows 


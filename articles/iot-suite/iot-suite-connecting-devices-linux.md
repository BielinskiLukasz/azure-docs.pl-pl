<properties
   pageTitle="Linux で C を使用してデバイスを接続する |Microsoft Azure"
   description="C で記述され、Linux で実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
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

## Linux で C のサンプル ソリューションをビルドして実行する

1. クローンを作成する、 *Microsoft Azure の IoT Sdk* GitHub リポジトリし、インストール、 *C の Microsoft Azure の IoT デバイス SDK* 、Linux デスクトップ環境で次の [Linux 開発環境 (lnk セットアップ linux) を設定する][lnk-setup-linux] 指示します。

2. テキスト エディターで **c/serializer/samples/remote_monitoring/remote_monitoring.c** ファイルを開きます。

3. ファイル内で次のコードを見つけます。

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. リモート監視ソリューション ダッシュボードから、**[Device Id]** と **[Device Key]** をデバイス用の値に置き換えます。

5. ダッシュボードから **IoT Hub ホスト名**を使用して、**[IoTHub Name]** と **[IoTHub Suffix, i.e. azure-devices.net]** を置き換えます。 たとえば、**IoT Hub ホスト名**が **contoso.azure-devices.net** の場合は、次に示すように、**[IoTHub Name]** を **contoso** に置き換え、**[IoTHub Suffix, i.e. azure-devices.net]** を **azure-devices.net** に置き換えます。

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. 変更を保存し、サンプルをビルドします。 サンプルをビルドするには、**c/build_all/linux** ディレクトリにある build.sh スクリプトを実行します。 このビルド スクリプトによりコンパイル済みのサンプル プログラムを格納する **cmake** フォルダーが作成されます。

7. **cmake/serializer/samples/remote_monitoring/remote_monitoring** サンプル アプリケーションを実行します。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux 


<properties
    pageTitle="OS プラットフォームとハードウェアの互換性 | Microsoft Azure"
    description="IoT デバイス SDK と OS プラットフォームおよびデバイス ハードウェアとの互換性をまとめてあります。"
    services="iot-hub"
    documentationCenter=""
    authors="hegate"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/09/2015"
     ms.author="hegate"/>


# OS プラットフォームとハードウェアの、デバイス SDK との互換性

このドキュメントの説明に含まれている特定のデバイスの構成だけでなく、さまざまな OS プラットフォーム SDK の互換性、 [Microsoft Azure は、IoT のプログラムの認定された](#microsoft-azure-certified-for-iot)します。 既にデバイスがある場合、プログラムに含まれるデバイスの一覧を参照し、対応状況に関するデバイス固有の情報を確認してください。 使用するには、どのデバイスのことを確認していない場合は、くださいを見て、 [OS プラットフォームとライブラリ](#os-platforms) の互換性のセクションです。


## OS プラットフォーム

Azure IoT ライブラリは、次のオペレーティング システム プラットフォームでテストされています。


| Linux/Unix OS プラットフォーム| バージョン|
|:---------------|:------------:|
| Debian Linux| 7.5|
| Fedora Linux| 20|
| Raspbian Linux| 3.18|
| Ubuntu Linux| 14.04|
| Yocto Linux| 2.1|

| Windows OS プラットフォーム| バージョン|
|:---------------|:------------:|
| Windows デスクトップ| 7、8、10|
| Windows IoT Core| 10|
| Windows Server| 2012 R2|

| その他のプラットフォーム| バージョン|
|:---------------|:------------:|
| mbed| 2.0|
| TI-RTOS| 2.x|



## C ライブラリ

[C の Microsoft Azure の IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) 、次の構成でテストされています。

| OS プラットフォーム| バージョン| プロトコル|
|:---------|:----------:|:----------:|
| Debian Linux| 7.5| HTTPS、AMQP、MQTT|
| Fedora Linux| 20| HTTPS、AMQP、MQTT|
| mbed OS| 2.0| HTTPS、AMQP|
| TI-RTOS| 2.x| HTTPS|
| Ubuntu Linux| 14.04| HTTPS、AMQP、MQTT|
| Windows デスクトップ| 7、8、10| HTTPS、AMPQ、MQTT|
| Yocto Linux| 2.1| HTTPS、AMQP|



## Node.js ライブラリ

[Node.js 用の Microsoft Azure の IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) 、次の構成でテストされています。


| ランタイム| バージョン| プロトコル|
|:---------|:----------:|:----:|
| Node.js| 4.1.0| HTTPS|



## Java ライブラリ

[Microsoft Azure の IoT デバイス SDK for Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) 、次の構成でテストされています。

| ランタイム| バージョン| プロトコル|
|:---------|:----------:|----|
| Java SE (Windows)| 1.7| HTTPS、AMQP|
| Java SE (Linux)| 1.8| HTTPS、AMQP|

Java 用 Microsoft Azure IoT サービス SDK は、次の構成でテストされています。

| ランタイム| バージョン| プロトコル|
|:---------|:----------:|:-----|
| Java SE| 1.8| HTTPS、AMQP|


## CSharp

[.NET 用 Microsoft Azure の IoT デバイス SDK](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) 、次の構成でテストされています。

| OS プラットフォーム| バージョン| プロトコル|
|:---------|:----------:|:----------:|
| Windows デスクトップ| 7、8、10| HTTPS、AMPQ|
| Windows IoT Core| 10| HTTPS|

管理対象エージェントのコードには、Microsoft .NET Framework 4.5 が必要です。


## Microsoft Azure Certified for IoT

**Microsoft Azure Certified for IoT** は、開発者やアーキテクトが対応状況のシナリオを理解できるように、より広範な IoT エコシステムを Microsoft Azure に接続するパートナー プログラムです。 具体的には、IoT プロジェクト (概念実証フェーズまたはパイロット フェーズ) をすぐに始めるために役立つ、OS とデバイスの信頼できる組み合わせの一覧を提供しています。 認定された、デバイスとオペレーティング システムの組み合わせを使用することにより、デバイスが Azure IoT Suite および Azure IoT Hub に対応していることを確認するために必要な作業とカスタマイズを減らし、IoT プロジェクトをすぐに開始することができます。

## Certified for IoT デバイス

**Certified for IoT** デバイスは、Azure SDK への対応がテストされ、IoT アプリケーションで使用する準備が整っています。 具体的には、オペレーティング システム プラットフォームと、コードの言語に基づいて対応状況を特定します。

#### デバイスの一覧

各デバイスは、デバイスの製造元が選択した OS と言語で、SDK が機能することが認定されています。 たとえば、BeagleBone Black は、Debian で、C、JavaScript、および Java 言語を使用して動作します。 これは、特定のデバイスで任意の言語と OS の組み合わせにより、開発者がアプリケーションを構築できることを意味します。

| デバイス| テスト対象の OS| 言語|
|:---------|:----------|:----------|
| [ADLINK MXE 202i](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=)| Wind River| JavaScript|
| [Ankaa](http://www.e-consystems.com/iMX6-development-board.asp)| Ubuntu| C|
| [Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6)| Linux Angstrom(Yocto)| JavaScript、Java|
| [Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3)| Linux Angstrom(Yocto)| JavaScript、Java|
| [矢印 DragonBoard 410 c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/)| Windows 10 IoT Core| C#|
| [ARTIK](http://developer.samsung.com/artik)| Fedora| C|
| [BeagleBone 黒](http://beagleboard.org/black)| Debian| C、JavaScript、Java|
| [BeagleBone 緑](http://beagleboard.org/green)| Debian| C、JavaScript、Java|
| [Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6)| Linux Angstrom(Yocto)| JavaScript、Java|
| [Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2)| Linux Angstrom(Yocto)| Java|
| [Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3)| Windows 10 IoT Core| C#|
| [Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx)| Linux Angstrom(Yocto)| JavaScript、Java|
| [Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F)| mbed 2.0| C|
| [Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html)| Yocto| C、JavaScript|
| [LogicMachine シリーズ](http://openrb.com/products/)| カスタム Linux| C|
| [Minnowboard の最大数](http://www.minnowboard.org/meet-minnowboard-max/)| Windows 7、8、10| C#|
| [NEXCOM NISE 50 C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c)| Windows 10 IoT Core| C#|
| [Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/)| Raspbian| C、JavaScript、Java|
| [Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/)| Windows 10 IoT Core| C、JavaScript、C#|
| [STM32 Nucleo](http://www.st.com/stm32nucleo)| STM32Cube| C|
| [TI CC3200](http://www.ti.com/product/cc3200)| TI-RTOS 2.x| C|

[これらのデバイスを使い始める](https://azure.microsoft.com/develop/iot/get-started/) か、GitHub にアクセスして [リポジトリ](https://github.com/Azure/azure-iot-sdks) および言語ごとのデバイスのドキュメントを検索します。

## 次のステップ

使用したソリューションの開発に関する詳細については [IoT デバイス用に認定されて](http://azure.com/iotdev)します。






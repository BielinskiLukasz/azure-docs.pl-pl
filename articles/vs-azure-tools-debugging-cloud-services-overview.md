<properties 
   pageTitle="Azure Cloud Services のデバッグ | Microsoft Azure"
   description="Azure Cloud Services のデバッグ"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="tarcher" />


# クラウド サービスのデバッグ

Azure Tools for Microsoft Visual Studio および Azure SDK を使用すれば、以下のさまざまな方法で Azure アプリケーションをデバッグすることができます。

- Visual C# または Visual Basic アプリケーションの場合と同様に、Azure アプリケーションを開発しながら、Visual Studio から Azure アプリケーションをデバッグすることができます。 詳細については、次を参照してください。 [、ローカル コンピューターでクラウド サービスをデバッグ](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)します。

- Azure 診断を使用すると、ロールが開発環境または Azure のどちらで実行されているかにかかわらず、ロール内で実行されているコードから詳細な情報をログに記録できます。 詳細については、次を参照してください。 [Azure 診断を使用したログ データの収集](http://go.microsoft.com/fwlink/p/?LinkId=400450)します。

- Visual Studio Enterprise を使用して .NET Framework 4 または .NET Framework 4.5 を対象とするロールを作成する場合は、Visual Studio から Azure クラウド サービスをデプロイする時点で IntelliTrace を有効にすることができます。 IntelliTrace が提供するログを使用すると、Azure で実行しているかのようにアプリケーションを Visual Studio でデバッグすることができます。 詳細については、次を参照してください。 [IntelliTrace および Visual Studio で発行済みのクラウド サービスのデバッグ](http://go.microsoft.com/fwlink/p/?LinkId=623016)します。

- Visual Studio からクラウド サービスをデプロイするときに、そのリモート デバッグを有効にできます。 デプロイメントのリモート デバッグを有効にすると、各ロール インスタンスを実行する仮想マシンにリモート デバッグ サービスがインストールされます。 msvsmon.exe をはじめとするこれらのサービスは、パフォーマンスに影響せず、追加コストも発生しません。 詳細については、次を参照してください。 [Azure でクラウド サービスをデバッグ](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)します。









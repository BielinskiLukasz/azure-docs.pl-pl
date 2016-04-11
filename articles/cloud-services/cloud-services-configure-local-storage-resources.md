<properties
pageTitle="Azure Cloud Services でローカル ストレージ リソースを構成する"
description=""
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# ローカル ストレージ リソースを構成する

ローカル ストレージ リソースは、ロールのインスタンスが実行されている仮想マシンのファイル システム内の予約されたディレクトリです。 ファイルの書き込みまたは読み取りが必要な場合に、インスタンスで実行されるコードからローカル ストレージ リソースにアクセスできるように、仮想マシンのインスタンスに情報を格納できます。 たとえば、ローカル ストレージ リソースを使用して、サービスが Azure で実行中に再度アクセスする可能性があるデータをキャッシュできます。 また、起動中にファイルを格納するようにローカル ストレージ リソースを構成することもできます。 スタートアップの種類のローカル ストレージ リソースの構成の詳細については、次を参照してください [スタートアップ中にファイルをストアにローカル ストレージの使用。](https://msdn.microsoft.com/library/azure/hh974419.aspx)

ローカル ストレージ リソースはサービス定義ファイル内で宣言します。 ロールのローカル ストレージ リソースはいくつでも宣言できます。 各ローカル ストレージ リソースはそのロールのすべてのインスタンスに対して予約されています。 ローカル ストレージ リソースに対して割り当てることができるディスク領域の最小値は 1 MB です。 特定のローカル リソースに対して割り当てることができる最大サイズは、ロールに指定された仮想マシンのサイズによって異なります。 仮想マシンのサイズごとに対応する合計ストレージの割り当てがあり、ロールに対して宣言されたすべてのローカル ストレージ リソースに割り当てられる総領域は、その仮想マシンのサイズに割り当てられた最大サイズを超えることはできません。 各仮想マシン サイズの割り当てられたローカル ディスク領域の最大量の詳細については、次を参照してください。 [クラウド サービスのサイズを構成する](https://msdn.microsoft.com/library/azure/ee814754.aspx)です。

> [AZURE.NOTE]
>
-   ローカル ストレージ リソースに対して要求されたディスク領域のサイズが仮想マシンに割り当てられた最大サイズを超えないようにするのは、開発者の責任であることに注意してください。 許可されている最大サイズよりも大きなローカル ストレージ リソースを構成しても、最大許容サイズを超える書き込み操作を行うまでエラーは発生しません。 その場合、書き込み操作は失敗し、ディスク領域不足というエラー メッセージが表示されます。 Azure の処理モデルは、"試行/失敗" です。 ディスク領域不足のエラーが発生した場合、エラーを処理して、一部のディスク領域を空き領域にすることができます。 その後、書き込み操作を再び試みることができます。
-   インスタンスが再利用されるときに、ローカル ストレージ リソースを保持するように指定できます。 ただし、仮想マシンのローカル ファイル システムに保存されるデータは耐久性があるとは限りません。 ロールに耐久性のあるデータが必要な場合は、Azure のドライブを使用してファイル データを格納することをお勧めします。 Azure のドライブは、Azure BLOB サービスによってバックアップされるため、耐久性が保証されています。  
>


## ローカル ストレージ リソースの追加

サービス定義ファイル内でローカル ストレージ リソースを宣言するには、追加、 **LocalResources** の子要素として、 **WebRole** 要素、または **WorkerRole** 要素。 追加し、 **LocalStorage** 、リソースを表す要素です。  **LocalStorage** 要素は、次の 3 つの属性。

-   *name*
-   *sizeInMB*: このローカル ストレージ リソースに必要なサイズを指定します。
-   *cleanOnRoleRecycle*: ロール インスタンスが再利用するとき、またはロールのライフ サイクルの間で永続化するかどうかにローカル ストレージ リソースをワイプするかどうかを指定します。 既定値は **true**します。

次のサービス定義ファイルは、Web ロールに対して宣言する 2 つのローカル ストレージ リソースを示しています。

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

サービス定義ファイルの詳細については、次を参照してください。 [Azure サービス定義スキーマ (.csdef ファイル)](https://msdn.microsoft.com/library/azure/ee758711.aspx)します。

> [AZURE.NOTE] 内でローカル ストレージ リソースを定義するには Azure Tools for Microsoft Visual Studio を使用している場合、 **プロパティ** ロールのページです。 詳細については、次を参照してください。 [Visual Studio で Azure アプリケーションを構成する](https://msdn.microsoft.com/library/ee405486.aspx)です。

## プログラムによるローカル ストレージ リソースへのアクセス

ローカル ストレージ リソースにアクセスするには、アプリケーションがからのパスを取得する必要があります、 [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) メソッドです。 標準的なファイルの読み取りと書き込みの操作を使用して、ローカル ストレージ リソースの内容の読み取りと書き込みを実行できます。 たとえば、次の例はという名前のファイルの内容を読み取る方法を示しています。 **MyTest.txt** ローカル ストレージ リソースからそれを MVC 3 アプリケーションのホーム ページに表示します。

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## ローカル ストレージ リソースへの実行時アクセス

Azure マネージ ライブラリには、ロール インスタンスで実行されているコード内からローカル ストレージ リソースにアクセスするクラスが用意されています。  [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) メソッドは、名前付きへの参照を返します。 [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx) オブジェクトです。

 **LocalResource** オブジェクトはディレクトリを表すキューから読み取るし、標準の .NET ファイル I/O クラスを使用して書き込むことができます。 ローカル ストレージ リソースのディレクトリへのパスを確認するのには、使用、 [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx) プロパティです。 このプロパティは、名前付きのリソース ディレクトリを含むローカル ストレージ リソースの完全なパスを返します。 など、サービスが開発環境で実行されている場合、ローカル ストレージ リソースが、ローカル ファイル システム内で定義と **RootPath** プロパティと値を返す、次のようにします。


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

ローカル ストレージ リソースへのパスには、サービスが Azure に展開されると、展開 ID が含まれていますと **RootPath** プロパティが次のような値を返します。


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

ロール インスタンスで実行されているコードは、インスタンスがオンラインになったときからオフラインになるときまで、そのロールに定義されているローカル ストレージ リソースにアクセスできます。

## 次のステップ

- [Azure のクラウド サービスのセットアップ](https://msdn.microsoft.com/library/azure/hh124108.aspx)


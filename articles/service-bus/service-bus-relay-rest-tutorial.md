<properties
   pageTitle="リレー型メッセージングを使用した Service Bus REST チュートリアル | Microsoft Azure"
   description="REST ベースのインターフェイスを表示する簡易な Service Bus Relay ホスト アプリケーションを構築します。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="sethm" />


# Service Bus REST のチュートリアル

このチュートリアルでは、REST ベースのインターフェイスを表示する簡易な Service Bus ホスト アプリケーションを構築する方法について説明します。 REST を使用すると、Web ブラウザーなどの Web クライアントから HTTP 要求を介して Service Bus API にアクセスできるようになります。

このチュートリアルでは、Windows Communication Foundation (WCF) REST プログラミング モデルを使用して、Service Bus に REST サービスを構築します。 詳細については、次を参照してください。 [WCF REST プログラミング モデル](https://msdn.microsoft.com/library/bb412169.aspx) と [を設計および実装するサービス](https://msdn.microsoft.com/library/ms729746.aspx) WCF ドキュメントです。

## 手順 1: Azure アカウントにサインアップする

最初の手順では、サービス名前空間を作成し、Shared Access Signature (SAS) キーを取得します。 名前空間は、Service Bus によって公開される各アプリケーションのアプリケーション境界を提供します。 サービス名前空間が作成された時点で、SAS キーが生成されます。 サービス名前空間と SAS キーの組み合わせが、アプリケーションへのアクセスを Service Bus が認証する資格情報になります。

### サービス名前空間を作成し、SAS キーを取得するには

1. 名前空間の作成、 [Azure クラシック ポータルの [][], 、手順に従います [How To: 作成または Service Bus Service 名前空間を変更する](https://msdn.microsoft.com/library/hh690931.aspx)します。

2. ポータルのメイン ウィンドウで、前の手順で作成したサービス名前空間の名前をクリックします。

3. **[構成]** をクリックして、名前空間の共有アクセス ポリシーを表示します。

4. **RootManageSharedAccessKey** ポリシーのプライマリ キーを書き留めるか、クリップボードにコピーします。 この値は、チュートリアルの後半で使用します。

## 手順 2: Service Bus で使用する REST ベースの WCF サービス コントラクトを定義する

他の Service Bus Service と同様に、REST スタイルのサービスを作成するときは、コントラクトを定義する必要があります。 コントラクトには、ホストがサポートする操作を指定します。 サービス操作は、Web サービス メソッドと考えることができます。 コントラクトを作成するには、C++、C#、または Visual Basic インターフェイスを定義します。 インターフェイスの各メソッドは、特定のサービス操作に対応しています。  [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) 属性は、各インターフェイスに適用する必要があり、 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) 属性は、各操作に適用する必要があります。 持つインターフェイスのメソッドの場合、 [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) が、 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), 、メソッドは公開されません。 これらのタスクに使用されるコードの例を手順に従って説明します。

基本の Service Bus コントラクトと REST スタイル コントラクトの主な違いは、プロパティの追加、 [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx)します。 このプロパティを使用すると、インターフェイス内のメソッドを相手側のインターフェイスのメソッドにマップすることができます。 使用して、この場合、 [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) メソッドを HTTP GET にリンクします。 その結果、Service Bus は、インターフェイスに送信されたコマンドをより正確に取得および解釈できるようになります。

### インターフェイスを使用して Service Bus を作成するには

1. 管理者として Visual Studio を開きます。**[スタート]** メニューの[Visual Studio] を右クリックし、**[管理者として実行]** をクリックします。

2. 新しいコンソール アプリケーション プロジェクトを作成します。 **[ファイル]** メニューをクリックし、**[新規作成]**、**[プロジェクト]** の順に選択します。 **[新しいプロジェクト]** ダイアログ ボックスで **[Visual C#]** をクリックします (**[Visual C#]** が表示されない場合は、**[他の言語]** からクリックします)。**[コンソール アプリケーション]** テンプレートを選択し、「**ImageListener**」と名前を付けます。 既定の **[場所]** を使用します。 **[OK]** をクリックしてプロジェクトを作成します。

3. Visual Studio c# プロジェクトの場合は作成、 `Program.cs` ファイルです。 このクラスは、空を含む `Main()` を正しくビルドするコンソール アプリケーション プロジェクトに必要な方法です。

4. **System.ServiceModel.dll** への参照をプロジェクトに追加します。

    a. ソリューション エクスプローラーでプロジェクト フォルダーの **[参照]** フォルダーを右クリックし、**[参照の追加]** をクリックします。

    b. **[参照の追加]** ダイアログ ボックスの **[.NET]** タブをクリックし、**System.ServiceModel** が表示されるまで下にスクロールします。 選択して **[OK]** をクリックします。

5. 前の手順を繰り返して、**System.ServiceModel.Web.dll** アセンブリへの参照を追加します。

6. 追加 `を使用して` ステートメントを **System.ServiceModel**, 、**System.ServiceModel.Channels**, 、**System.ServiceModel.Web**, 、および **System.IO** 名前空間。

    ```c
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) WCF の基本機能にプログラムでアクセスできるようにするための名前空間です。 Service Bus は、サービス コントラクトの定義に WCF の多くのオブジェクトと属性を使用します。 ほとんどの場合、Service Bus Relay アプリケーションにはこの名前空間を使用することになります。 同様に、 [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) Service Bus とクライアントの web ブラウザーで通信を経由するオブジェクトであると、チャネルの定義に役立ちます。 最後に、 [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) web ベースのアプリケーションを作成するための型が含まれています。

7. プログラムの名前空間を Visual Studio の既定の名前から **Microsoft.ServiceBus.Samples** に変更します。

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. 直接名前空間宣言の後にという名前の新しいインターフェイス定義 **IImageContract** を適用し、 **ServiceContractAttribute** 属性の値を持つインターフェイスを `http://samples.microsoft.com/ServiceModel/Relay/`します。 この名前空間の値は、コードのスコープ全体で使用する名前空間とは異なります。 この名前空間の値は、このコントラクトの一意の識別子として使用されます。値にはバージョン情報が含まれています。 詳細については、次を参照してください。 [サービスのバージョン管理](http://go.microsoft.com/fwlink/?LinkID=180498)します。 名前空間を明示的に指定すると、既定の名前空間値がコントラクト名に追加されなくなります。

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. 内で、 `IImageContract` インターフェイスを 1 回の操作のメソッドを宣言、 `IImageContract` コントラクトがインターフェイスで公開し、適用、 `OperationContractAttribute` 属性をパブリック Service Bus コントラクトの一部として公開するメソッドです。

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. **OperationContract** 属性の次に、**WebGet** 属性を適用します。

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    適用後は、Service Bus から HTTP GET 要求を **GetImage** にルーティングし、**GetImage** の戻り値を HTTP GETRESPONSE 応答に変換できるようになります。 このチュートリアルの後半で、Web ブラウザーを使用してこのメソッドにアクセスし、ブラウザーに画像を表示します。

11. 直後に、 `IImageContract` 定義両方から継承するチャネルを宣言、 `IImageContract` と `IClientChannel` インターフェイスです。

    ```
    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    チャネルは、サービスとクライアントが相互に情報を渡すときに経由する WCF オブジェクトです。 後述の手順でホスト アプリケーションでチャネルを作成します。 Service Bus は、ブラウザーからの HTTP GET 要求を **GetImage** 実装に渡すために、このチャネルを使用します。 また、**GetImage** の戻り値を取得して、クライアント ブラウザーの HTTP GETRESPONSE に変換するためにも、このチャネルを使用します。

12. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックして、ここまでの作業に問題がないことを確認します。

### 例

次のコードは、Service Bus コントラクトを定義する基本的なインターフェイスを示しています。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## 手順 3: Service Bus を使用する REST ベースの WCF サービス コントラクトを実装する

REST スタイルの Service Bus Service を作成するには、まずコントラクトを作成する必要があります。コントラクトは、インターフェイスを使用して定義します。 次の手順はインターフェイスの実装です。 ユーザー定義の **IImageContract** インターフェイスを実装する **ImageService** というクラスを作成します。 コントラクトを実装したら、App.config ファイルを使用してインターフェイスを構成します。 構成ファイルには、サービス名、コントラクト名、Service Bus との通信に使用するプロトコルの種類など、アプリケーションに必要な情報が含まれています。 以下の手順では、これらのタスクに使用するコード例を示します。

これまでの手順と同様に、REST スタイルのコントラクトと基本の Service Bus コントラクトの実装にはほとんど違いがありません。

### REST スタイルの Service Bus コントラクトを実装するには

1. **IImageContract** インターフェイスの定義の直後に、**ImageService** という新しいクラスを作成します。 **ImageService** クラスで **IImageContract** インターフェイスを実装します。

    ```
    class ImageService : IImageContract
    {
    }
    ```
    他のインターフェイス実装と同様に、別のファイルに指定した定義を実装することができます。 ただし、このチュートリアルで実装に示されるインターフェイス定義と同じファイルと `Main()` メソッドです。

2. 適用、 [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) 属性を **IImageService** クラス、クラスが WCF コントラクトの実装であることを示します。

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    既に説明したように、この名前空間は従来の名前空間ではありません。 この名前空間は、コントラクトを特定する WCF アーキテクチャの一部です。 詳細については、次を参照してください。、 [データ コントラクト名](https://msdn.microsoft.com/library/ms731045.aspx) WCF ドキュメントのトピックです。

3. .jpg 画像をプロジェクトに追加します。

    これは、サービスが受信側ブラウザーに表示する画像です。 プロジェクトを右クリックし、**[追加]** をクリックします。 **[既存の項目]** をクリックします。 **[既存項目の追加]** ダイアログ ボックスで、使用する .jpg を参照して選択し、**[追加]** をクリックします。

    ファイルを追加するときに、**[ファイル名:]** フィールドの横にあるドロップダウン リストで **[すべてのファイル]** が選択されていることを確認します。 以降、このチュートリアルでは、画像名が "image.jpg" という前提で説明します。 別のファイルを使用する場合は、画像のファイル名を変更するか、ファイル名に合わせてコードを変更します。

4. 実行中のサービスから画像ファイルを検出できるようにするには、**ソリューション エクスプローラー**で画像ファイルを右クリックします。 **[プロパティ]** ウィンドウの **[出力ディレクトリにコピー]** を **[新しい場合はコピーする]** に設定します。

5. 参照を追加、 **System.Drawing.dll**, 、**system.runtime.serialization.dll から参照できる**, 、および **Microsoft.ServiceBus.dll** アセンブリをプロジェクトにも関連付けられている次のコードを追加 `を使用して` ステートメントです。

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. ビットマップを読み込む次のコンストラクターを **ImageService** クラスに追加し、クライアント ブラウザーに送信する準備をします。

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. 前のコードの直後に、次のように、画像を含む HTTP メッセージを返す **GetImage** メソッドを **ImageService** クラスに追加します。

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    この実装では、**MemoryStream** を使用して画像を取得し、ブラウザーにストリーミングする準備をします。 ゼロ位置からストリーミングを開始し、ストリーム コンテンツを jpeg 形式と宣言し、情報をストリーミングします。

8. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックします。

### Service Bus で Web サービスを実行するための構成を定義するには

1. **ImageListener** プロジェクトを右クリックします。 **[追加]** をクリックし、**[新しい項目]** をクリックします。

2. **ソリューション エクスプローラー**で、**App.config** をダブルクリックします。この時点で、App.config には次の XML 要素が含まれています。

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
    </configuration>
    ```

    この構成ファイルは WCF 構成ファイルと似ており、サービス名、エンドポイント (つまり、Service Bus がクライアントおよびホストと相互に通信するために公開している場所)、バインディング (通信に使用されているプロトコルの種類) が含まれています。 主な違いは、構成されたサービス エンドポイントを参照する、 [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) バインディングで、.NET Framework の一部ではありません。 Service Bus アプリケーションを構成する方法の詳細については、次を参照してください。 [Service Bus で登録する WCF サービスを構成する](https://msdn.microsoft.com/library/ee173579.aspx)します。

3. 追加、 `< system.serviceModel >` App.config ファイルに XML 要素です。これは 1 つ以上のサービスを定義する WCF 要素です。この要素は、サービス名とエンドポイントの定義に使用されます。

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <system.serviceModel>

        </system.serviceModel>

    </configuration>
    ```

4. 内で、 `system.serviceModel` 要素を追加、 `< バインド >` を次の内容を持つ要素。ここでアプリケーションに使用するバインドを定義します。複数のバインドを定義できますが、このチュートリアルで定義するバインドは 1 つのみです。

    ```
    <bindings>
        
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    この手順は、Service Bus を定義 [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) バインディング **relayClientAuthenticationType** に設定 **None**します。 この設定は、このバインドを使用するエンドポイントは、クライアントの資格情報を必要としないことを示します。

5. 後に、 `< バインド >` 要素を追加、 `< services >` 要素。バインドと同様に、1 つの構成ファイルで複数のサービスを定義できます。ただし、このチュートリアルで定義するサービスは 1 つのみです。

    ```
    <services>
        
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    この手順では、以前に定義した既定の **webHttpRelayBinding** を使用するサービスを構成します。 また、既定の **sbTokenProvider** を使用します。この値は次の手順で定義します。

6. 後に、 `< services >` 要素を作成、 `< 動作 >` "sas_key"次のコンテンツの要素を *共有アクセス署名* (SAS) から取得したキー、 [Azure クラシック ポータルの [][] 手順 1. でします。

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

7. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックしてソリューション全体をビルドします。

### 例

次のコードを使用して Service Bus で実行されている REST ベースのサービスのコントラクトとサービスの実装を示しています、 **WebHttpRelayBinding** バインドします。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

次の例は、サービスに関連付けられている App.config ファイルです。

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <bindings>
      
      <webHttpRelayBinding>
        <binding name="default">
          
          <security relayClientAuthenticationType="None" />
        </binding>
      </webHttpRelayBinding>
    </bindings>

    <services>
      
      <service name="Microsoft.ServiceBus.Samples.ImageService"
               behaviorConfiguration="default">
        <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
      </service>
    </services>

    <behaviors>
      <endpointBehaviors>
        <behavior name="sbTokenProvider">
          <transportClientEndpointBehavior>
            <tokenProvider>
              <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
            </tokenProvider>
          <transportClientEndpointBehavior>
        </behavior>
      </endpointBehaviors>
      <serviceBehaviors>
        <behavior name="default">
          <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
        </behavior>
      </serviceBehaviors>
    </behaviors>

  </system.serviceModel>
</configuration>
```

## 手順 4: Service Bus を使用する REST ベースの WCF サービスをホストする

この手順では、Service Bus でコンソール アプリケーションを使用して、Web サービスを実行する方法について説明します。 この手順で作成するコードの詳細については、手順に従って例を使用して説明します。

### サービスのベース アドレスを作成するには

1. `Main()` 関数の宣言は、Service Bus プロジェクトの名前空間を格納する変数を作成します。

    ```
    string serviceNamespace = "InsertServiceNamespaceHere";
    ```
    Service Bus は、名前空間の名前を使用して一意の URI を作成します。

2. 作成、 `Uri` 名前空間に基づいているサービスのベース アドレスのインスタンス。

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### Web サービス ホストを作成および構成するには

- このセクションで作成した URI アドレスを使用して、Web サービス ホストを作成します。

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    サービス ホストは、ホスト アプリケーションをインスタンス化する WCF オブジェクトです。 この例では、作成するホストの種類 (**ImageService**) と、ホスト アプリケーションを公開するアドレスを渡します。

### Web サービス ホストを実行するには

1. サービスを開きます。

    ```
    host.Open();
    ```
    サービスが実行されます。

2. サービスが実行中であることと、サービスの停止方法を示すメッセージが表示されます。

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. 完了したら、サービス ホストを閉じます。

    ```c
    host.Close();
    ```

## 例

次の例では、チュートリアルの前の手順で説明したサービス コントラクトと実装が含まれています。ここでは、コンソール アプリケーションでサービスをホストします。 次のコードをコンパイルして、ImageListener.exe という実行可能ファイルを作成します。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### コードのコンパイル

ソリューションをビルドしたら、次の手順でアプリケーションを実行します。

1. コマンド プロンプトからサービス (ImageListener\bin\Debug\ImageListener.exe) を実行します。

2. コマンド プロンプトのアドレスをコピーし、ブラウザーに貼り付けて画像を確認します。

## 次のステップ

ここでは、Service Bus Relay サービスを使用するアプリケーションを構築しました。リレー型メッセージングの詳細については、次の記事を参照してください。

- [Azure Service Bus アーキテクチャの概要](service-bus-fundamentals-hybrid-solutions.md#relays)

- [サービス バス リレー サービスを使用する方法](service-bus-dotnet-how-to-use-relay.md)


[azure classic portal]: http://manage.windowsazure.com 


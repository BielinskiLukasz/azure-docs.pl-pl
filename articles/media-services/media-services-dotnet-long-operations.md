<properties 
    pageTitle="長時間実行される操作のポーリング" 
    description="このトピックでは、長時間実行される操作をポーリングする方法について説明します。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/05/2015"
    ms.author="juliako"/>


#Azure Media Services を使用したライブ ストリーミング配信

##概要

Microsoft Azure Media Services は、要求を Media Services に送信して操作 (例: 作成、開始、停止、チャネルの削除) を開始する API を提供します。 これらは実行時間の長い操作です。

Media Services .NET SDK は、要求を送信し、その操作が完了するまで待機する API を提供します (内部的には、API は一定の間隔で操作の進行状況をポーリングします)。 たとえば、channel.Start() を呼び出すと、このメソッドはチャネルが開始された後に戻ります。 非同期バージョンを使用することもできます: チャネルを待機します。StartAsync() (タスク ベースの非同期パターンについては、次を参照してください。 [タップ](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx))。 操作要求を送信し、操作が完了するまで状態をポーリングする API は、"ポーリング メソッド" と呼ばれます。 これらのメソッド (特に非同期バージョン) は、リッチ クライアント アプリケーションやステートフル サービスに対して推奨されます。

ときには、アプリケーションで実行時間の長い http 要求を待機できず、操作の進行状況を手動でポーリングすることが必要な場合があります。 一般的な例としては、ステートレス Web サービスとのやり取りするブラウザーが挙げられます。ブラウザーがチャネルの作成を要求すると、Web サービスは実行時間の長い操作を開始し、操作 ID をブラウザーに返します。 その後、ブラウザーはその ID で Web サービスに照会して、操作の状態を取得できます。 Media Services .NET SDK は、こうした場合に役立つ API を提供します。 これらの API は、"非ポーリング メソッド" と呼ばれます。
「非ポーリング メソッド」は、次の名前付けパターンを持つ: 送信*OperationName*Operation (たとえば、SendCreateOperation)。 送信*OperationName*操作メソッドを返す、 **IOperation** オブジェクト。 返されたオブジェクトには、操作を追跡するために使用できる情報が含まれます。 送信*OperationName*OperationAsync メソッドが返す **タスク<IOperation>**します。

現時点では、次のクラスは、非ポーリング メソッドをサポートします。  **チャネル**, 、**StreamingEndpoint**, 、および **プログラム**します。

操作の状態をポーリングするを使用して、 **GetOperation** メソッドを **OperationBaseCollection** クラスです。 間隔を使用して、操作の状態を確認する: の **チャネル** と **StreamingEndpoint** 、操作が 30 秒を使用して; の **プログラム** 、操作が 10 秒を使用します。


##例

次の例と呼ばれるクラスを定義する **ChannelOperations**します。 このクラス定義は、Web サービス クラス定義の出発点として利用できます。 わかりやすくするために、次の例では、非同期バージョンのメソッドを使用します。

この例では、クライアントでこのクラスを使用する方法も示しています。

###ChannelOperations クラス定義

    /// <summary> 
    /// The ChannelOperations class only implements 
    /// the Channel’s creation operation. 
    /// </summary> 
    public class ChannelOperations
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;
    
        public ChannelOperations()
        {
                _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
                _context = new CloudMediaContext(_cachedCredentials);    }
    
        /// <summary>  
        /// Initiates the creation of a new channel.  
        /// </summary>  
        /// <param name="channelName">Name to be given to the new channel</param>  
        /// <returns>  
        /// Operation Id for the long running operation being executed by Media Services. 
        /// Use this operation Id to poll for the channel creation status. 
        /// </returns> 
        public string StartChannelCreation(string channelName)
        {
            var operation = _context.Channels.SendCreateOperation(
                new ChannelCreationOptions
                {
                    Name = channelName,
                    Input = CreateChannelInput(),
                    Preview = CreateChannelPreview(),
                    Output = CreateChannelOutput()
                });
    
            return operation.Id;
        }
    
        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created channel Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the created channel Id is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        public bool IsCompleted(string operationId, out string channelId)
        {
            IOperation operation = _context.Operations.GetOperation(operationId);
            bool completed = false;
    
            channelId = null;
    
            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    channelId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    break;
            }
            return completed;
        }
    
    
        private static ChannelInput CreateChannelInput()
        {
            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.RTMP,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelPreview CreateChannelPreview()
        {
            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }
    
        private static ChannelOutput CreateChannelOutput()
        {
            return new ChannelOutput
            {
                Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
            };
        }
    }

###クライアント コード

    ChannelOperations channelOperations = new ChannelOperations();
    string opId = channelOperations.StartChannelCreation("MyChannel001");
    
    string channelId = null;
    bool isCompleted = false;
    
    while (isCompleted == false)
    {
        System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
        isCompleted = channelOperations.IsCompleted(opId, out channelId);
    }
    
    // If we got here, we should have the newly created channel id.
    Console.WriteLine(channelId);
 


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


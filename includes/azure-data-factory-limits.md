Data Factory は、お客様のサブスクリプションが互いのワークロードから保護されるように、次の既定の制限が設定されているマルチテナント サービスです。 制限のほとんどは、サポートに問い合わせることで上限まで引き上げることができます。 

**リソース** | **既定の制限** | **上限**
-------- | ------------- | -------------
1 つの Data Factory 内のパイプライン数 | 100 | 2500
1 つの Data Factory 内のデータセット数 | 500 | 5000
データセットあたりの同時実行のスライス数 | 10 | 10
オブジェクトのパイプライン オブジェクトあたりのバイト数 <sup>1</sup> | 200 KB | 2,000 KB
データセットと linkedservice オブジェクトのオブジェクトあたりのバイト数 <sup>1</sup> | 30 KB | 2,000 KB
オブジェクトごとのフィールド数 | 100 | [サポートにお問い合せください](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
フィールド名前や識別子あたりのバイト数 | 2 KB | [サポートにお問い合せください](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
フィールドあたりのバイト数 | 30 KB | [サポートにお問い合せください](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
サブスクリプション内 HDInsight オンデマンドのクラスター コア <sup>2</sup> | 48 | [サポートにお問い合せください](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
パイプラインのアクティビティ実行の再試行カウント | 1,000 | MaxInt (32 ビット)

<sup>1</sup> パイプライン、データセット、およびリンクされたサービス オブジェクトは、ワークロードの論理グループを表します。 これらのオブジェクトの制限は、Azure Data Factory サービスで移動したり処理したりできるデータ量には関係ありません。 Data Factory はペタバイトのデータ処理まで拡張できるようになっています。

<sup>2</sup>データ ファクトリを含むサブスクリプション外には、オンデマンド HDInsight コアが割り当てられます。 そのため、上記の制限はオンデマンド HDInsight コアで Data Factory を実行しているコアの制限であり、Azure サブスクリプションに関連付けられているコアの制限とは異なります。


**リソース** | **既定値の下限** | **上限**
-------- | ------------------- | -------------
スケジュールの間隔 | 約 15 分 | 約 15 分
再試行の間隔 | 1 秒 | 1 秒
再試行のタイムアウト値 | 1 秒 | 1 秒


### Web サービス呼び出しの制限

Azure リソース マネージャーでは、API 呼び出しの制限があります。 内の割合で API 呼び出しを行う、 [Azure リソース マネージャー API の制限](azure-subscription-service-limits/#resource-group-limits)します。 




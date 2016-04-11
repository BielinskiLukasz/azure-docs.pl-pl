<properties
   pageTitle="Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ | Microsoft Azure"
   description="Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ"
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


# Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ

Emulator Express を使用することにより、管理者として Visual Studio を実行せずに、クラウド サービスをテストおよびデバッグできます。 クラウド サービスの要件に応じて、Emulator Express または完全なエミュレーターのどちらを使用するかをプロジェクト設定で指定できます。 フル装備のエミュレーターの詳細については、次を参照してください。 [コンピューティング エミュレーターで Azure アプリケーションを実行](../storage/storage-use-emulator.md)します。 Emulator Express は Azure SDK 2.1 で最初に付属し、Azure SDK 2.3 以降は既定のエミュレーターになりました。

## Visual Studio IDE での Emulator Express の使用

Azure SDK 2.3 以降で新しいプロジェクト作成すると、Emulator Express が既に選択されています。 以前のバージョンの SDK で作成した既存のプロジェクトは、次の手順に従って Emulator Express を選択します。

### Emulator Express を使用するようにプロジェクトを構成するには

1. Azure プロジェクトのショートカット メニューを選択 **プロパティ**, を選択し、 **Web** ] タブをクリックします。

1. [ **ローカル開発サーバー**, 、選択、 **オプションの IIS Express を使用する** ] ボタンをクリックします。 Emulator Express は IIS Web サーバーと互換性がありません。

1.  **エミュレーター**, 、選択、 **エミュレーター Express を使用する** オプション ボタンをクリックします。

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## コマンド プロンプトでの Emulator Express の起動

コマンド プロンプトで、Express バージョンの Azure コンピューティング エミュレーター csrun.exe を起動するには、/useemulatorexpress オプションを使用します。

## 制限事項

Emulator Express を使用する前に、いくつかの制限事項に注意する必要があります。

- Emulator Express は IIS Web サーバーと互換性がありません。

- クラウド サービスには複数のロールを含めることができますが、各ロールのインスタンスは 1 つに制限されます。

- 1,000 未満のポート番号にはアクセスできません。 たとえば、通常 1,000 未満のポートが使用される認証プロバイダーを使用する場合は、この値を 1,000 を超えるポート番号に変更する必要がある可能性があります。

- Azure コンピューティング エミュレーターに適用されるすべての制限は、Emulator Express にも適用されます。 たとえば、デプロイあたり 50 を超えるロール インスタンスを保持できません。 参照してください [コンピューティング エミュレーターでの Azure アプリケーションの実行](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## 次のステップ

[Cloud Services のデバッグ](https://msdn.microsoft.com/library/azure/ee405479.aspx)


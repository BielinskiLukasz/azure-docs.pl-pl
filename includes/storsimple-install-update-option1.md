
#### StorSimple 用 Windows PowerShell を使用して Update 1.2 をインストールするには

1. 次の手順を実行して、ソフトウェア更新プログラムをダウンロードします。

    1. Internet Explorer を起動しに移動 [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx)します。
    2. 初めて利用する場合は、Microsoft Update カタログをインストールするよう求められます。 **[インストール]** をクリックします。

        ![カタログのインストール](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. カタログ検索画面が表示されます。 検索ボックスに「**3063418**」と入力し、**[検索]** をクリックします。

        ![カタログの検索](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. **StorSimple Update 1.2 Appliance Update** bundle が表示されます。 **[追加]** をクリックします。 更新プログラムがバスケットに追加されます。

        ![バンドルの更新](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. **[バスケットの表示]** をクリックします。

        ![バスケットの表示](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. **[Download]** をクリックします。 ダウンロード先となるローカルの場所を指定または**参照**します。 更新プログラムが、選択した場所の **StorSimple Update 1.2 Appliance Update bundle** (KB3063418) フォルダーにダウンロードされます。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。

    この手順では、ソフトウェア デバイスの更新プログラムを修正プログラムとして、Microsoft Update サーバーからはディスク ファームウェアの更新プログラムを、Azure クラシック ポータルからは LSI ドライバーと Windows の更新プログラムをインストールする方法について説明します。 ただし、ソフトウェア、ドライバー、およびディスク ファームウェアの更新プログラムをすべて修正プログラムとしてインストールするように選択できます。 次に、StorSimple 1.2 SAS Controller Update (KB3043005) および StorSimple 1.2 Disk Firmware Update (KB3063416) をダウンロードし、同じ共有フォルダーに同じにコピーする必要があります。 修正プログラムとしては、ディスク ファームウェアの更新プログラムをインストールするに次の手順に [StorSimple 用 Windows PowerShell を使用してメンテナンス モードの修正プログラムをインストールする](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple)します。
    > [AZURE.NOTE] 修正プログラムには、ピア コントローラーから潜在的なエラー メッセージをすべて検出するために両方のコントローラーからアクセス可能である必要があります。 

2. ソフトウェア更新プログラムをインストールするには、StorSimple デバイスのシリアル コンソールで Windows PowerShell インターフェイスにアクセスします。 詳細な指示に従って [シリアル コンソールに接続するための PuTTy を使用して](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)します。

3. コマンド プロンプトで **Enter** キーを押します。

4. **[オプション 1]** を選択して、フル アクセスでデバイスにログオンします。

5. 更新プログラム パッケージをインストールするには、コマンド プロンプトで次のように入力します。

 `Start-hcshotfix-パス < ファイルの更新へのパス > の資格情報の < domain \username の形式で資格情報 >`

 上記のコマンドの共有パスでは、DNS ではなく IP を使用します。 Credential パラメーターは、認証済みの共有にアクセスする場合にのみ使用されます。

 共有にアクセスする場合は、Credential パラメーターを使用することをお勧めします。 "すべてのユーザー" に解放されている共有であっても、通常は、非認証ユーザーには解放されません。

 サンプル出力を次に示します。

     ````
     Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
     \hcsmdssoftwareupdate.exe -Credential contoso\John
    
     Confirm
    
     This operation starts the hotfix installation and could reboot one or
     both of the controllers. If the device is serving I/Os, these will not 
     be disrupted. Are you sure you want to continue?
     [Y] Yes [N] No [?] Help (default is "Y"): Y
    
     ````

6. 修正プログラムのインストールの確認を求められたら、「**Y**」と入力します。

7. 使用して、更新プログラムを監視、 `Get-hcsupdatestatus` コマンドレットです。

 次のサンプル出力は、インストール中の更新プログラムを示しています。  `RunInprogress` は `True` 、更新が進行中の場合。

     ````
     Controller0>Get-HcsUpdateStatus
     RunInprogress       : True
     LastHotfixTimestamp : 9/02/2015 10:36:13 PM
     LastUpdateTimestamp : 9/02/2015 10:35:25 PM
     Controller0Events   :
     Controller1Events   : 
     ````

  次のサンプル出力は、更新が完了したことを示しています。  `RunInProgress` は `False` 更新が完了するとします。

     ````
     Controller1>Get-HcsUpdateStatus
    
     RunInprogress       : False
     LastHotfixTimestamp : 9/02/2015 10:56:13 PM
     LastUpdateTimestamp : 9/02/2015 10:35:25 PM
     Controller0Events   :
     Controller1Events   :
    
     ````

 > [AZURE.NOTE] コマンドレットのレポートでは場合によっては、 `False` ときに、更新が進行中です。 修正プログラムが完了したためには、数分間待って次のコマンドを再実行し、いることを確認、 `RunInProgress` は `False`します。 False の場合、修正プログラムは完了しています。 

8. ソフトウェアの更新が完了したら、システムのソフトウェア バージョンを確認します。 次のコマンドを入力します。

    `Get HcsSystem`

    次のバージョンが表示されます。

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433

    更新プログラムの適用後にバージョン番号が変わらない場合は、修正プログラムの適用に失敗したことを示します。 このメッセージが表示にお問い合わせください [Microsoft サポート](storsimple-contact-microsoft-support.md) に連絡します。

9. 中断を伴うディスク ファームウェアの更新プログラムをインストールします。完了には約 30 ～ 45 分かかります。 デバイスのシリアル コンソールに接続することで、計画されたメンテナンス期間にこれらをインストールするよう選択できます。 ディスク ファームウェアの更新プログラムをインストールする手順についてで [StorSimple 用 Windows PowerShell を使用して、インストールのメンテナンス モードの更新プログラム](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)します。

10. ディスク ファームウェアの更新プログラムが正常に適用され、デバイスがメンテナンス モードを終了したら、Azure クラシック ポータルに戻ります。 メンテナンス モードの更新プログラムは、24 時間が経過するまで、ポータルでは更新されません。 Azure クラシック ポータルから残りの中断を伴わない更新プログラムを適用するまで待機する必要がある場合があります。

11. 更新プログラムを適用する準備ができたら、**[メンテナンス]** ページに移動し、ページの下部にある **[更新プログラムのスキャン]** をクリックします。 更新プログラム (ドライバーおよび Windows の更新プログラムを含む) が利用可能なことが通知されます。 **[更新プログラムのインストール]** をクリックして、インストールを開始します。 すべての更新プログラムが正常にインストールされたら、作業は終了です。














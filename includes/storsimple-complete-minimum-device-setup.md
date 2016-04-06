<!--author=alkohli last changed: 9/17/15-->

#### StorSimple の最小デバイス セットアップを完了するには

1.  **デバイス** ページでデバイスを選択して、特定のデバイス] ページに移動、デバイス名に対して矢印をクリックします。 

    ![オンライン デバイスの [デバイス] ページ](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 

2. クイック スタート アイコンをクリックして ![クイック スタート アイコン](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) デバイスのクイック スタート ページにアクセスします。 クリックして **デバイス セットアップを完了** を開始する、 **デバイスの構成** ウィザード。

    ![デバイスの [クイック スタート] ページ](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)

2.  **基本設定** ] ページで、次の操作します。
  1. 指定、 **フレンドリ名** デバイス用です。 既定のデバイス名は、デバイスのモデルやシリアル番号などの情報を反映します。 デバイスを管理するために、最大 64 文字の表示名を割り当てることができます。
  2. 設定、 **タイム ゾーン** デバイスをデプロイする地理的場所に基づいています。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
  3.  **DNS 設定**, 、アドレスを指定して **セカンダリ DNS サーバー**します。 IPv6 を使用する場合、このフィールドには、Windows PowerShell インターフェイスに提供される IPv6 プレフィックスに基づいて値が入力されます。 
  セカンダリ DNS サーバーが構成されていない場合、デバイスの構成を保存することはできません。
  4. [iSCSI 対応インターフェイス] で、iSCSI 用に少なくとも 1 つのネットワークを有効にします。 1 つ以上のネットワーク インターフェイスをクラウド対応とし、1 つのインターフェイスを iSCSI 対応とする必要があります。 DATA 0 は自動的にクラウド対応となります。
 
      ![StorSimple の最小限のデバイス セットアップ基本設定](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)

3. 矢印アイコンをクリックします。 ![StorSimple の矢印アイコン](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4.  **ネットワーク インターフェイス** ] ページで、コント ローラー 0 とコント ローラー 1 の固定の IP アドレスを指定します。 DATA 0 インターフェイスが IPv4 に対して構成されている場合、固定 IP アドレスは IPv4 形式で指定する必要があります。 IPv6 構成でプレフィックスを指定した場合、これらのフィールドには固定 IP アドレスが自動的に入力されます。


    > [AZURE.NOTE] 
    > 
    > - コントローラーの固定 IP アドレスは、デバイス の IP アドレスによってアクセス可能なサブネット内で空き IP となっている必要があります。
    > - コントローラーの固定 IP アドレスはデバイスに更新プログラムを提供するために使用されるため、ルーティング可能でインターネットに接続可能である必要があります。

    ![StorSimple minimum device setup network interfaces](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

5. チェック アイコンをクリックして ![StorSimple のチェック マーク アイコン](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png)します。
  デバイスが再び表示 **クイック スタート** ページです。

 > [AZURE.NOTE] いつでもその他のすべてのデバイス設定を変更するにはアクセスすることによって、 **構成** ページです。

![使用可能なビデオ](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **ビデオ**

デバイスの最小セットアップを完了する方法を示すビデオを見るには、次のようにクリックします。 [ここ](http://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/)します。


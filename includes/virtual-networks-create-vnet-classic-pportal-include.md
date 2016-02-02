## Azure プレビュー ポータルで従来の VNet を作成する方法

上記のシナリオに基づいて従来の VNet を作成するには、次の手順に従います。

1. ブラウザーからの [http://portal.azure.com に移動し、必要に応じて、サインイン、Azure アカウントを使用しています。
2. 次の図に示すように、[**新規**] > [**ネットワーク**] > [**仮想ネットワーク**] の順にクリックし、[**デプロイ モデルの選択**] の一覧にすでに [**従来型**] と表示されているのを確認してから [**作成**] をクリックします。

    ![プレビュー ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. [**仮想ネットワーク**] ブレードで、VNet の [**名前**] を入力してから [**アドレス空間**] をクリックします。 VNet と最初のサブネットのアドレス空間の設定を構成してから [**OK**] をクリックします。 次の図では、このシナリオでの CIDR ブロック設定を示しています。

    ![アドレス空間のブレード](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. [**リソース グループ**] をクリックし、VNet を追加するリソース グループを選択してから [**新しいリソース グループを作成する**] をクリックして VNet を新しいリソース グループに追加します。 次の図は、**TestRG** という新しいリソース グループのリソース グループ設定を示しています。 リソース グループの詳細については、次を参照してください。 [Azure リソース マネージャーの概要](resource-group-overview.md/#resource-groups)します。

    ![リソース グループ ブレードの作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. 必要に応じて、VNet の [**サブスクリプション**] と [**場所**] の設定を変更してください。

6. VNet を**スタート 画面**にタイルとして表示させたくない場合は、[**スタート画面にピン留めする**] を無効にしてください。

7. [**作成**] をクリックすると、次の図に示すような [**仮想ネットワークの作成**] という名前のタイルが表示されます。

    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. VNet が作成されるのを待ち、以下のタイルが表示されたら、クリックしてサブネットを追加します。

    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. 次に示すように、VNet の [**構成**] が表示されます。

    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. [**サブネット**] > [**追加**] の順にクリックしてからサブネットの [**名前**] を入力し、[**アドレス範囲 (CIDR ブロック)**] を指定して [**OK**] をクリックします。 次の図は、このシナリオの設定を示しています。

    ![プレビュー ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)





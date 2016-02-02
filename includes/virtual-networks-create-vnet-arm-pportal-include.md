## Azure プレビュー ポータルで VNet を作成する方法

Azure プレビュー ポータルを使用して、上記のシナリオに基づいた VNet を作成するには、次の手順に従います。

1. ブラウザーからの [http://portal.azure.com に移動し、必要に応じて、サインイン、Azure アカウントを使用しています。
2. 次の図に示すように、[**新規**] > [**ネットワーク**] > [**仮想ネットワーク**] の順にクリックしてから、[**デプロイ モデルの選択**] の一覧にある [**リソース マネージャー**] 、[**作成**] の順にクリックします。

    ![プレビュー ポータルでの VNet の作成](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. [**仮想ネットワークの作成**] ブレードで、次の図に示すように、VNet の設定を構成します。

    ![仮想ネットワーク ブレードの作成](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. [**リソース グループ**] をクリックし、VNet を追加するリソース グループを選択してから [**新規作成**] をクリックして VNet を新しいリソース グループに追加します。 次の図は、**TestRG** という新しいリソース グループのリソース グループ設定を示しています。 リソース グループの詳細については、次を参照してください。 [Azure リソース マネージャーの概要](resource-group-overview.md/#resource-groups)します。

    ![[リソース グループ]](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. 必要に応じて、VNet の [**サブスクリプション**] と [**場所**] の設定を変更してください。

6. VNet を**スタート 画面**にタイルとして表示させたくない場合は、[**スタート画面にピン留めする**] を無効にしてください。

7. [**作成**] をクリックすると、次の図に示すような [**仮想ネットワークの作成**] という名前のタイルが表示されます。

    ![仮想ネットワーク タイルの作成](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. VNet が作成されるのを待ってから、[**仮想ネットワーク**] ブレードで、[**すべての設定**] > [**サブネット**] > [**追加**] の順にクリックします。

    ![プレビュー ポータルでのサブネットの追加](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. 次に示すように、*BackEnd* サブネットのサブネット設定を指定し、[**OK**] をクリックします。

    ![サブネット設定](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. 次の図に示すように、サブネットの一覧が表示されます。

    ![VNet 内のサブネットの一覧](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)





ディスクに関する詳細については、次を参照してください。 [はディスクとバーチャル マシンの Vhd](../articles/virtual-machines-disks-vhds.md)します。

## <a id="attachempty"></a>方法: 空のディスクの接続

空のディスクを接続すると、Azure が .vhd ファイルを作成してそれをストレージ アカウントに保存するため、データ ディスクを比較的簡単に追加できます。

1. **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

2. コマンド バーで、**[ディスクの接続]**、**[空のディスクの接続]** の順にクリックします。

    ![Attach an empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.  **[空のディスクの接続]** ダイアログ ボックスが表示されます。

    ![Attach a new empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)

    以下の手順を実行します。

    - **[ファイル名]** で、ディスクに使用する .vhd ファイルのファイル名として自動的に生成される名前を受け入れるか、別の名前を入力します。 .vhd ファイルの別の名前を入力した場合でも、データ ディスクには自動的に生成された名前が使用されます。

    - データ ディスクの **[サイズ (GB)]** を入力します。

    - チェック マークをクリックして、完了します。

4.  データ ディスクが作成されて接続されると、仮想マシンのダッシュボードに表示されます。

    ![Empty data disk successfully attached](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> [AZURE.NOTE]
> 新しいデータ ディスクを追加した後は、仮想マシンにログオンし、ディスクを初期化するため、仮想マシンは、そのディスクを記憶域を使用する必要があります。 

## <a id="attachexisting"></a>方法: 既存のディスクの接続

既存のディスクを接続する場合は、ストレージ アカウントで利用できる .vhd を持っている必要があります。 使用して、 [Add-azurevhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) .vhd ファイルをストレージ アカウントにアップロードするコマンドレットです。 .vhd ファイルを作成してアップロードした後は、それを仮想マシンに接続できます。

1. **[仮想マシン]** をクリックし、適切な仮想マシンを選択します。

2. コマンド バーで **[接続]** をクリックし、**[ディスクの接続]** を選択します。

    ![Attach data disk](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

    **[ディスクの接続]** ダイアログ ボックスが表示されます。


    ![Enter data disk details](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. 仮想マシンに接続するデータ ディスクを選択します。

4. チェック マークをクリックして、仮想マシンにデータ ディスクを接続します。

5.  データ ディスクが接続されると、仮想マシンのダッシュボードに表示されます。

    ![Data disk successfully attached](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)








1. 示された手順を使用して、Azure サブスクリプションへのサインイン [Azure CLI から Azure への接続](../articles/xplat-cli-connect.md)します。

2. 以下を使用してサービス管理モードであることを確認します。

        azure config mode asm

3. 利用可能なイメージからロードする Linux イメージを探します。

        azure vm image list | grep "Linux"

   Windows コマンド プロンプト ウィンドウでは、grep ではなく find を使用します。 

4. `azure vm create` を使用して、上記のリストの Linux イメージで新しい仮想マシンを作成します。 この手順では、新しいクラウド サービスだけでなく新しいストレージ アカウントも作成されます。 `-c` オプションを使用して、この仮想マシンを既存のクラウド サービスに接続することもできます。 `-e` オプションを使用すると、Linux 仮想マシンにログインするための SSH エンドポイントも作成されます。

        ~$ azure vm create "MyTestVM" b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64 "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Linux 仮想マシンを指定する必要があります、 `-e` オプション `vm create`; バーチャル マシンを作成した後は、SSH を有効にすることはできません。 SSH の詳細については、読み取り [ssh Azure 上の Linux の使用方法](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)します。

    注意イメージ *b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64* は、前の手順ではイメージ リストから選択します。 *MyTestVM* 、新しいバーチャル マシンの名前を指定し、 *adminUser* 仮想マシンに SSH を使用するユーザー名を指定します。 これらの変数は、必要に応じて置き換えることができます。 このコマンドの詳細については、次を参照してください。、 [Azure サービス管理で Azure CLI の使用](../articles/virtual-machines/virtual-machines-command-line-tools.md)します。

5. 新しく作成された Linux 仮想マシンが、次のものによって指定される一覧に表示されます。

        azure vm list

6. 次のコマンドを使用して、仮想マシンの属性を確認できます。

        azure vm show MyTestVM

7. 新しく作成された仮想マシンは、`azure vm start` コマンドで開始できます。

これらすべての Azure CLI 仮想マシン コマンドの詳細については、参照、 [サービス管理 API で Azure CLI の使用](../articles/virtual-machines/virtual-machines-command-line-tools.md)します。


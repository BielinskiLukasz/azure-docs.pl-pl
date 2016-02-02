ドメインを実行する場合に、ドメインを購入できます [Azure 管理ポータル](https://portal.azure.com) 直接します。 次の手順を使用してドメイン名を購入し、Web アプリに割り当てます。

1. ブラウザーで開く、 [Azure 管理ポータル](https://portal.azure.com)します。

2. **[Web Apps]** タブで、Web アプリの名前をクリックし、**[設定]**、**[カスタム ドメインと SSL]** の順に選択します。

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. **[カスタム ドメインと SSL]** ブレードで **[ドメインを購入]** をクリックします。

    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)

4. **[ドメインを購入]** ブレードで、購入するドメイン名をテキスト ボックスを使用して入力します。 推奨される使用可能なドメインがテキスト ボックスの下に表示されます。 購入したいドメインを選択します。

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)

5. **[連絡先情報]** をクリックし、ドメインの連絡先情報フォームに入力します。

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)

6. **[ドメインを購入]** ブレードで **[選択]** をクリックすると、**[購入内容の確認]** ブレードに注文情報が表示されます。 法律条項に同意して **[購入]** をクリックすると、注文が送信され、**[通知]** で購入プロセスを確認できます。

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)

7. ドメインを正常に購入した場合は、ドメインを管理し、Web アプリに割り当てることができます。 ドメインの右側にある **[...]** をクリックします。 **[購入をキャンセル]** または **[ドメインの管理]** を選択できます。 **[ドメインの管理]** をクリックし、**[ドメインの管理]** ブレードで **[サブドメイン]** を Web アプリにバインドします。

    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)

    構成が完了すると、カスタム ドメイン名が Web アプリの **[ホスト名のバインド]** セクションに表示されます。

この時点でブラウザーにカスタム ドメイン名を入力して、対象の Web アプリに正常にアクセスできることを確認できます。






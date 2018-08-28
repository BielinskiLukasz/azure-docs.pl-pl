---
title: Samouczek — tworzenie niestandardowych rekordów usługi Azure DNS dla aplikacji internetowej
description: W tym samouczku utworzysz rekordy DNS domeny niestandardowej dla aplikacji internetowej przy użyciu usługi Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/20/2018
ms.author: victorh
ms.openlocfilehash: 2abe6c11b2a6fe9a9146f5c5689597fe3e29fa82
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "41918364"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Samouczek: tworzenie rekordów DNS w domenie niestandardowej dla aplikacji internetowej 

Możesz skonfigurować usługę Azure DNS, aby hostować domenę niestandardową dla aplikacji internetowych. Przykładowo możesz utworzyć aplikację internetową platformy Azure i zapewnić użytkownikom dostęp do niej przy użyciu www.contoso.com lub contoso.com jako w pełni kwalifikowanej nazwy domeny (FQDN).

> [!NOTE]
> W tym samouczku jako przykład używana jest domena contoso.com. Zastąp contoso.com swoją nazwą domeny.

Aby to zrobić, musisz utworzyć trzy rekordy:

* Rekord główny „A” wskazujący na domenę contoso.com
* Rekord główny „TXT” na potrzeby weryfikacji
* Rekord „CNAME” dla nazwy www wskazującej na rekord A

Należy pamiętać, że w przypadku utworzenia rekordu A dla aplikacji internetowej na platformie Azure rekord A musi być ręcznie zaktualizowany, jeżeli zmieni się podstawowy adres IP aplikacji internetowej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie rekordu A i TXT dla domeny niestandardowej
> * Tworzenie rekordu CNAME dla domeny niestandardowej
> * Testowanie nowych rekordów
> * Dodawanie niestandardowych nazw hostów do aplikacji internetowej
> * Testowanie niestandardowych nazw hostów


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz aplikację usługi App Service](../app-service/app-service-web-get-started-html.md) lub użyj aplikacji utworzonej w innym samouczku.

- Utwórz strefę DNS w usłudze Azure DNS i deleguj strefę u rejestratora do usługi Azure DNS.

   1. Aby utworzyć strefę DNS, wykonaj kroki opisane w sekcji [Tworzenie strefy DNS](dns-getstarted-create-dnszone.md).
   2. Aby delegować strefę do usługi Azure DNS, wykonaj kroki opisane w sekcji [Delegowanie domeny DNS](dns-domain-delegation.md).

Po utworzeniu strefy i oddelegowaniu jej do usługi Azure DNS możesz utworzyć rekordy dla domeny niestandardowej.

## <a name="create-an-a-record-and-txt-record"></a>Tworzenie rekordu A i TXT

Rekord A jest używany do mapowania nazwy na adres IP. W poniższym przykładzie należy przypisać „\@” jako rekord A przy użyciu adresu IPv4 aplikacji internetowej. Symbol \@ zwykle reprezentuje domenę katalogu głównego.

### <a name="get-the-ipv4-address"></a>Uzyskiwanie adresu IPv4

W lewym obszarze nawigacji na stronie usługi App Services w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**. 

![Menu domen niestandardowych](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stronie **Domeny niestandardowe** skopiuj adres IPv4 aplikacji:

![Nawigacja w portalu do aplikacji platformy Azure](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Tworzenie rekordu A

```powershell
New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Tworzenie rekordu TXT

Usługa App Services używa tego rekordu tylko podczas konfiguracji, aby sprawdzić, czy jesteś właścicielem domeny niestandardowej. Po zweryfikowaniu i skonfigurowaniu domeny niestandardowej w usłudze App Service możesz usunąć ten rekord TXT.

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name `"@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Tworzenie rekordu CNAME

Jeżeli domena jest już zarządzana przez usługę Azure DNS (zobacz [Delegowanie domeny DNS](dns-domain-delegation.md)), możesz użyć następującego przykładu, aby utworzyć rekord CNAME dla domeny contoso.azurewebsites.net.

Otwórz program Azure PowerShell i utwórz nowy rekord CNAME. Ten przykład tworzy typ zestawu rekordów CNAME z wartością „czasu wygaśnięcia” wynoszącą 600 sekund w strefie DNS o nazwie „contoso.com” oraz z aliasem aplikacji internetowej contoso.azurewebsites.net.

### <a name="create-the-record"></a>Tworzenie rekordu

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -cname "contoso.azurewebsites.net")
```

Poniższy przykład przedstawia odpowiedź:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Testowanie nowych rekordów

Możesz sprawdzić poprawność utworzonych rekordów poprzez wysłanie zapytania do „www.contoso.com” oraz „contoso.com” przy użyciu polecenia nslookup, jak pokazano poniżej:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Dodawanie niestandardowych nazw hostów

Teraz możesz dodać niestandardowe nazwy hostów do aplikacji internetowej:

```powershell
set-AzureRmWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testowanie niestandardowych nazw hostów

Otwórz przeglądarkę i przejdź do `http://www.<your domainname>` oraz `http://<you domain name>`.

> [!NOTE]
> Upewnij się, że został dodany prefiks `http://`. W przeciwnym razie przeglądarka może podjąć próbę przewidzenia adresu URL.

Dla obu adresów URL powinna zostać wyświetlona ta sama strona. Na przykład:

![Usługa aplikacji Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeżeli nie potrzebujesz już zasobów utworzonych w ramach tego samouczka, możesz usunąć grupę zasobów **myresourcegroup**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak tworzyć strefy prywatne usługi Azure DNS.

> [!div class="nextstepaction"]
> [Rozpoczynanie pracy ze strefami prywatnymi usługi Azure DNS przy użyciu programu PowerShell](private-dns-getstarted-powershell.md)

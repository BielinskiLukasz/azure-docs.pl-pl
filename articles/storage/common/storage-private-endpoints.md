---
title: Używanie prywatnych punktów końcowych w usłudze Azure Storage | Microsoft Docs
description: Przegląd prywatnych punktów końcowych w celu bezpiecznego dostępu do kont magazynu z sieci wirtualnych.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb1f8a1d1f8e1ebbaf3e0e9fe96e3c1bf0ba9ba6
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078751"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Używanie prywatnych punktów końcowych usługi Azure Storage (wersja zapoznawcza)

Możesz użyć [prywatnych punktów końcowych](../../private-link/private-endpoint-overview.md) dla kont usługi Azure Storage, aby umożliwić klientom w sieci wirtualnej (VNET) bezpieczne uzyskiwanie dostępu do danych za pośrednictwem [prywatnego linku](../../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi konta magazynu. Ruch sieciowy między klientami w sieci wirtualnej a kontem magazynu przechodzącym przez sieć wirtualną i prywatnym łączem w usłudze Microsoft szkielet Network, eliminując ekspozycję z publicznego Internetu.

Używanie prywatnych punktów końcowych dla konta magazynu pozwala:
- Zabezpiecz swoje konto magazynu, konfigurując zaporę magazynu do blokowania wszystkich połączeń w publicznym punkcie końcowym usługi Storage.
- Zwiększ bezpieczeństwo sieci wirtualnej (VNet), umożliwiając zablokowanie eksfiltracji danych w wirtualnej.
- Bezpiecznie łącz się z kontami magazynu z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

## <a name="conceptual-overview"></a>Omówienie pojęć
![Omówienie prywatnych punktów końcowych usługi Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla usługi platformy Azure w [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNET). Utworzenie prywatnego punktu końcowego dla konta magazynu zapewnia bezpieczną łączność między klientami w sieci wirtualnej i magazynem. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą magazynu używa bezpiecznego linku prywatnego.

Aplikacje w sieci wirtualnej mogą bezproblemowo łączyć się z usługą magazynu za pośrednictwem prywatnego punktu końcowego, **używając tych samych parametrów połączenia i mechanizmów autoryzacji, które mogą być używane w inny sposób**. Prywatnych punktów końcowych można używać ze wszystkimi protokołami obsługiwanymi przez konto magazynu, w tym REST i SMB.

Po utworzeniu prywatnego punktu końcowego dla usługi magazynu w sieci wirtualnej do zatwierdzenia dla właściciela konta magazynu jest wysyłane żądanie zgody. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem konta magazynu, to żądanie zgody jest automatycznie zatwierdzane.

Właściciele kont magazynu mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pomocą karty "*prywatne punkty końcowe*" dla konta magazynu w [Azure Portal](https://portal.azure.com).

> [!TIP]
> Jeśli chcesz ograniczyć dostęp do konta magazynu tylko za pomocą prywatnego punktu końcowego, skonfiguruj zaporę magazynu tak, aby odmówić wszystkim dostępowi za pomocą publicznego punktu końcowego.

Możesz zabezpieczyć konto magazynu tak, aby akceptowało tylko połączenia z sieci wirtualnej, [konfigurując zaporę magazynu](storage-network-security.md#change-the-default-network-access-rule) tak, aby domyślnie nie zezwala na dostęp za pośrednictwem jego publicznego punktu końcowego. Nie musisz mieć reguły zapory, aby zezwalać na ruch z sieci wirtualnej, która ma prywatny punkt końcowy, ponieważ Zapora magazynu kontroluje dostęp tylko za pośrednictwem publicznego punktu końcowego. Prywatne punkty końcowe zamiast tego polegają na przepływie zgody na przyznanie podsieci dostępu do usługi magazynu.

### <a name="private-endpoints-for-storage-service"></a>Prywatne punkty końcowe usługi Storage

Podczas tworzenia prywatnego punktu końcowego należy określić konto magazynu i usługę magazynu, z którą nawiąże połączenie. Potrzebujesz osobnego prywatnego punktu końcowego dla każdej usługi magazynu na koncie magazynu, do której należy uzyskać dostęp, czyli [obiektów BLOB](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [plików](../files/storage-files-introduction.md), [kolejek](../queues/storage-queues-introduction.md), [tabel](../tables/table-storage-overview.md)lub [statycznych witryn sieci Web](../blobs/storage-blob-static-website.md).

> [!TIP]
> Utwórz oddzielny prywatny punkt końcowy dla dodatkowego wystąpienia usługi Storage, aby uzyskać lepszą wydajność odczytu na kontach RA-GRS.

Aby uzyskać informacje o dostępności na [koncie magazynu geograficznie nadmiarowego do odczytu](storage-redundancy-grs.md#read-access-geo-redundant-storage), musisz oddzielić prywatne punkty końcowe zarówno dla wystąpienia podstawowego, jak i pomocniczego usługi. Nie musisz tworzyć prywatnego punktu końcowego dla wystąpienia dodatkowego do **pracy w trybie failover**. Prywatny punkt końcowy będzie automatycznie łączyć się z nowym wystąpieniem podstawowym po przejściu w tryb failover.

#### <a name="resources"></a>Zasoby

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia prywatnego punktu końcowego dla konta magazynu, zapoznaj się z następującymi artykułami:

- [Połącz się prywatnie z kontem magazynu z poziomu środowiska konta magazynu w Azure Portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Utwórz prywatny punkt końcowy przy użyciu prywatnego centrum linków w Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../../private-link/create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych

Klienci w sieci wirtualnej powinni używać tych samych parametrów połączenia dla konta magazynu, nawet w przypadku korzystania z prywatnego punktu końcowego.

Podczas tworzenia prywatnego punktu końcowego aktualizujemy rekord zasobu CNAME DNS dla tego punktu końcowego magazynu do aliasu w poddomenie z prefiksem "*privatelink*". Domyślnie tworzymy również [prywatną strefę DNS](../../dns/private-dns-overview.md) dołączoną do sieci wirtualnej. Ta prywatna strefa DNS odpowiada poddomeny z prefiksem "*privatelink*" i zawiera rekordy zasobów DNS a dla prywatnych punktów końcowych.

Po rozwiązaniu adresu URL punktu końcowego magazynu spoza sieci wirtualnej z prywatnym punktem końcowym jest on rozpoznawany jako publiczny punkt końcowy usługi magazynu. Po rozwiązaniu problemu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, adres URL punktu końcowego magazynu jest rozpoznawany jako adres IP prywatnego punktu końcowego.

W przykładzie przedstawionym powyżej, rekordy zasobów DNS dla konta magazynu "StorageAccountA", po rozwiązaniu spoza sieci wirtualnej obsługującej prywatny punkt końcowy, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<publiczny punkt końcowy usługi Storage\>                   |
| \<publiczny punkt końcowy usługi Storage\>                   | A     | \<publiczny adres IP usługi Storage\>                 |

Jak wspomniano wcześniej, można odmówić dostępu przez publiczny punkt końcowy przy użyciu zapory magazynu.

Rekordy zasobów DNS dla StorageAccountA, po rozwiązaniu przez klienta w sieci wirtualnej hostującym prywatnego punktu końcowego, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Takie podejście umożliwia dostęp do konta magazynu **przy użyciu tych samych parametrów połączenia** z sieci wirtualnej obsługującej prywatne punkty końcowe, a także klientów spoza sieci wirtualnej. Możesz użyć zapory magazynu, aby odmówić dostępu wszystkim klientom spoza sieci wirtualnej.

> [!IMPORTANT]
> Użyj tych samych parametrów połączenia, aby nawiązać połączenie z kontem magazynu przez prywatne punkty końcowe, jak w przeciwnym razie. Nie łącz się z kontem magazynu przy użyciu adresu URL poddomeny "*privatelink*".

> [!TIP]
> W przypadku korzystania z niestandardowego lub lokalnego serwera DNS należy skonfigurować rekordy zasobów DNS dla prywatnych punktów końcowych w strefie DNS odpowiadającej poddomenie "privatelink" usługi magazynu.

Zalecane nazwy stref DNS dla prywatnych punktów końcowych usług magazynu to:

| Usługa magazynu        | Nazwa strefy                            |
| :--------------------- | :----------------------------------- |
| Blob service           | `privatelink.blob.core.windows.net`  |
| Usługa Data Lake Storage 2. generacji | `privatelink.dfs.core.windows.net`   |
| Usługa plików           | `privatelink.file.core.windows.net`  |
| usługa kolejki          | `privatelink.queue.core.windows.net` |
| Table service          | `privatelink.table.core.windows.net` |
| Statyczne witryny sieci Web        | `privatelink.web.core.windows.net`   |

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Znane problemy

### <a name="copy-blob-support"></a>Kopiuj obsługę obiektów BLOB

W trakcie okresu zapoznawczego nie obsługujemy [kopiowania poleceń obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) dla kont magazynu, do których można uzyskać dostęp za pomocą prywatnych punktów końcowych, jeśli konto magazynu źródłowego jest chronione przez zaporę.

### <a name="subnets-with-service-endpoints"></a>Podsieci z punktami końcowymi usługi
Obecnie nie można utworzyć prywatnego punktu końcowego w podsieci, która ma punkty końcowe usługi. Jako obejście można utworzyć oddzielne podsieci w tej samej sieci wirtualnej dla punktów końcowych usługi i prywatnych punktów końcowych.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Ograniczenia dostępu do magazynu dla klientów w sieci wirtualnych z prywatnymi punktami końcowymi

Klienci w programie sieci wirtualnych z istniejącymi prywatnymi punktami końcowymi ograniczeniami podczas uzyskiwania dostępu do innych kont magazynu, które mają prywatne punkty końcowe. Na przykład załóżmy, że sieć wirtualna N1 ma prywatny punkt końcowy dla konta magazynu a1 dla, powiedz, usługa BLOB Service. Jeśli konto magazynu a2 ma prywatny punkt końcowy w sieci wirtualnej N2 dla usługi BLOB, wówczas klienci w sieci wirtualnej N1 muszą również uzyskać dostęp do usługi BLOB Service dla konta a2 przy użyciu prywatnego punktu końcowego. Jeśli konto magazynu a2 nie ma żadnych prywatnych punktów końcowych dla usługi BLOB, klienci w sieci wirtualnej N1 mogą uzyskać dostęp do swojej usługi BLOB bez prywatnego punktu końcowego.

To ograniczenie jest wynikiem zmian wprowadzonych w systemie DNS, gdy konto a2 tworzy prywatny punkt końcowy.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Reguły sieciowej grupy zabezpieczeń dla podsieci z prywatnymi punktami końcowymi

Obecnie nie można skonfigurować zasad [sieciowych grup zabezpieczeń](../../virtual-network/security-overview.md) (sieciowej grupy zabezpieczeń) dla podsieci z prywatnymi punktami końcowymi. Ograniczone obejście tego problemu polega na implementacji reguł dostępu dla prywatnych punktów końcowych w podsieciach źródłowych, chociaż takie podejście może wymagać wyższego obciążenia zarządzania.

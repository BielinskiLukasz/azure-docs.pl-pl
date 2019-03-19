---
title: Jednostki SKU rejestru kontenerów platformy Azure
description: Porównaj z różnymi warstwami usług dostępnych w usłudze Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2018
ms.author: danlep
ms.openlocfilehash: 8fc364eac619c2f23ad2db1051a9b3ffa1bd38d6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138086"
---
# <a name="azure-container-registry-skus"></a>Jednostki SKU rejestru kontenerów platformy Azure

Usługa Azure Container Registry (ACR) jest dostępna w wielu warstwach usług, znane jako jednostki SKU. Te jednostki SKU zapewniają przewidywalne ceny i kilka opcji umożliwiających dopasowanie do wzorców pojemność i użycie prywatnego rejestru platformy Docker na platformie Azure.

| SKU | Zarządzane | Opis |
| --- | :-------: | ----------- |
| **Podstawowa** | Yes | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Rejestry podstawowe mają te same możliwości programowe jako Standard i Premium (Integracja uwierzytelniania usługi Azure Active Directory, usunięcie obrazu i elementy webhook). Jednak uwzględnionego magazynu i przepływności obrazu są najbardziej odpowiednie dla dolnej scenariusze użycia. |
| **Standardowa** | Yes | Standardowymi rejestrami oferuje te same możliwości jak podstawowe, za pomocą zwiększona przepływność dołączony magazyn i obrazów. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| **Premium** | Yes | Rejestrach w warstwie Premium zapewniają największa ilość magazynu w pakiecie i obsługi jednoczesnych, scenariusze dużej liczby. Oprócz większą przepływność obrazu, Premium dodaje funkcje, w tym [geografickou replikaci] [ container-registry-geo-replication] Zarządzanie pojedynczym rejestrem w wielu regionach, [zawartości zaufania(wersjazapoznawcza)](container-registry-content-trust.md) podpisywania tag obrazu i [zapory i sieci wirtualne (wersja zapoznawcza)](container-registry-vnet.md) do ograniczania dostępu do rejestru. |
| Klasyczne<sup>1</sup> | Nie | Ta jednostka SKU włączone wstępną wersję usługi Azure Container Registry na platformie Azure. Rejestry klasyczne są wspierane przez konto magazynu, które platforma Azure tworzy się w ramach subskrypcji, który ogranicza możliwość rejestru Azure container Registry w celu zapewnienia możliwości wyższego poziomu, takie jak zwiększona przepływność i replikacji geograficznej. |

<sup>1</sup> będzie klasycznego SKU **przestarzałe** w **marca 2019**. Dla wszystkich nowych rejestry kontenerów, należy użyć podstawowa, standardowa lub Premium.

Wybieranie wyższego poziomu jednostki SKU zapewnia większą wydajność i skalę, jednak wszystkie zarządzane jednostki SKU zapewniają takie same możliwości programowe. Z wieloma warstwami usług możesz zacząć korzystać z Basic, a następnie przekonwertować Standard i Premium wzrostu użycia rejestru.

## <a name="managed-vs-unmanaged"></a>Zarządzane i niezarządzane

Podstawowa, standardowa i Premium jednostek SKU są nazywane zbiorczo *zarządzane* rejestrów i rejestry Classic jako *niezarządzanych*. Główną różnicą między tymi dwoma jest sposób przechowywania obrazów kontenerów.

### <a name="managed-basic-standard-premium"></a>Zarządzany (Basic, Standard i Premium)

Zarządzane rejestry korzyści z magazynu obrazu zarządzane w całości na platformie Azure. Oznacza to, że konto magazynu, które są przechowywane obrazy nie pojawia się w ramach subskrypcji platformy Azure. Ma wiele zalet uzyskane przy użyciu jednej jednostki SKU rejestru zarządzanego, omówiono szczegółowe w [magazyn obrazów kontenerów w usłudze Azure Container Registry][container-registry-storage]. Ten artykuł koncentruje się na jednostki SKU rejestru zarządzanego i ich funkcji.

### <a name="unmanaged-classic"></a>Niezarządzane (wersja klasyczna)

> [!IMPORTANT]
> Klasyczne jednostki SKU jest wycofywany, a będzie niedostępna po marca 2019 r. Dla wszystkich nowych rejestrów, należy użyć podstawowa, standardowa lub Premium.

Rejestry klasyczne są "unmanaged" w tym sensie, że konto magazynu, która będzie tworzyć kopię rejestru klasycznego znajduje się w *swoje* subskrypcji platformy Azure. W efekcie odpowiedzialność za zarządzanie konta magazynu, w której są przechowywane obrazy kontenera. Za pomocą rejestrów niezarządzanych, nie można przełączyć między jednostkami SKU stosownie do potrzeb (inne niż [uaktualnianie] [ container-registry-upgrade] do rejestru zarządzanego), i kilka funkcji rejestry zarządzane są niedostępne (np. Usuwanie obrazu kontenera, [geografickou replikaci][container-registry-geo-replication], i [elementów webhook][container-registry-webhook]).

Aby uzyskać więcej informacji na temat uaktualnianie rejestru klasycznego do jednego z zarządzanymi jednostkami SKU, zobacz [uaktualnianie rejestru klasycznego][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Tabela funkcji jednostki SKU

W poniższej tabeli przedstawiono funkcje i limity warstwy usług podstawowa, standardowa i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Zmiana jednostki SKU

Można zmienić jednostki SKU rejestru, przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal. Możesz przenosić za darmo między zarządzanymi jednostkami SKU tak długo, jak jednostki SKU na ma wymagane maksymalnej pojemności. Po przełączeniu się do jednego z zarządzanymi jednostkami SKU z wersji klasycznej, nie można przenieść ponownie Classic — jest konwersją jednokierunkowe.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść między jednostkami SKU w interfejsie wiersza polecenia platformy Azure, użyj [az acr update] [ az-acr-update] polecenia. Na przykład, aby przełączyć się do warstwy Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

W rejestrze kontenerów **Przegląd** w witrynie Azure portal wybierz **aktualizacji**, następnie wybierz nowy **jednostki SKU** z listy rozwijanej jednostki SKU.

![Aktualizacja jednostki SKU rejestru kontenerów w witrynie Azure portal][update-registry-sku]

W przypadku rejestrze klasycznym nie można wybrać zarządzaną jednostką SKU w witrynie Azure portal. Zamiast tego należy się najpierw [uaktualnienia] [ container-registry-upgrade] do rejestru zarządzanego (zobacz [zmiany z wersji klasycznej](#changing-from-classic)).

## <a name="changing-from-classic"></a>Zmiana z wersji klasycznej

Istnieją dodatkowe informacje, weź pod uwagę podczas migrowania niezarządzanych rejestru klasycznego do jednego z zarządzanego podstawowa, standardowa lub jednostek SKU Premium. Jeśli rejestru klasycznego zawiera dużą liczbę obrazów i rozmiarze wielu gigabajtów, proces migracji może trochę potrwać. Ponadto `docker push` operacje są wyłączone do czasu ukończenia migracji.

Aby uzyskać szczegółowe informacje na temat uaktualniania rejestru klasycznego do jednego z zarządzanymi jednostkami SKU, zobacz [uaktualnianie rejestru klasycznego kontenera][container-registry-upgrade].

## <a name="pricing"></a>Cennik

Aby uzyskać informacje na każdym z jednostki SKU rejestru kontenerów platformy Azure, zobacz [ceny Container Registry][container-registry-pricing].

Aby uzyskać szczegóły cennika transferów danych, zobacz [przepustowość — szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Kolejne kroki

**Harmonogram działania dla usługi Azure Container Registry**

Odwiedź stronę [plan ACR] [ acr-roadmap] w serwisie GitHub, aby znaleźć informacje o nadchodzących funkcjach w usłudze.

**Usługa Azure Container Registry w witrynie UserVoice**

Przesyłaj i głosuj na propozycje dotyczące nowych funkcji w [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md

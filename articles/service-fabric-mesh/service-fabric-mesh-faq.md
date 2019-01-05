---
title: Często zadawane pytania dotyczące usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o najczęściej zadawanych pytań i odpowiedzi dla usługi Azure Service Fabric siatki.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 12/12/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 39709377a83226c90626221f0f5a98f3d7d5c74a
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051207"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Często zadawane pytania usługi Service Fabric siatki

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. W tym artykule znajdują się odpowiedzi na często zadawane pytania.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Jak zgłosić problem lub Zadaj pytanie?

Zadawaj pytania, uzyskuj odpowiedzi od inżynierów firmy Microsoft i zgłaszania problemów w [repozytorium GitHub service-fabric siatki preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Limit przydziału i kosztów

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Jaki jest koszt uczestniczenia w wersji zapoznawczej?

Obecnie nie są żadne opłaty za wdrażanie aplikacji lub kontenerów do siatki w wersji zapoznawczej. Jednak firma Microsoft zachęca do usuwania zasobów, wdrażania i nie pozostawić je systemem aktywnie podczas testów.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Czy istnieje limit przydziału liczby rdzeni i pamięci RAM?

Tak. Przydziały dla każdej subskrypcji są:

- Liczba aplikacji: 5
- Liczba rdzeni na aplikacji: 12
- Całkowita ilość pamięci RAM na aplikację: 48 GB
- Punkty końcowe sieci i transferu danych przychodzących: 5
- Woluminy systemu Azure, które można dołączyć: 10
- Liczba replik usługi: 3
- Największy kontenera, które można wdrożyć jest ograniczony do 4 rdzeni i 16 GB pamięci RAM.
- Częściowe rdzeni można przydzielić do kontenerów w przyrostach o rozmiarze 0,5 rdzenia, maksymalnie 6 rdzeni.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Jak długo można pozostawić Moja aplikacja wdrożona?

Firma Microsoft ma obecnie ograniczony okres istnienia aplikacji do dwóch dni. Jest to w celu optymalnego wykorzystania wolnych rdzeni przydzielonych do wersji zapoznawczej. W rezultacie tylko mogła działać danego wdrożenia stale w ciągu 48 godzin, po tym czasie zostanie on zamknięty.

Jeśli tak się dzieje się tak zdarzyć, można sprawdzić, czy system zamknij go, uruchamiając `az mesh app show` polecenia w interfejsie wiersza polecenia platformy Azure. Zaznacz, aby sprawdzić, czy zwraca `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Na przykład: 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Aby usunąć grupę zasobów, użyj `az group delete <nameOfResourceGroup>` polecenia.

## <a name="supported-container-os-images"></a>Obrazy obsługiwane kontenerów systemu operacyjnego

Jeśli tworzysz na komputerze aktualizacji Windows Fall Creators Update (wersja 1709), można używać tylko obrazy platformy docker systemu Windows w wersji 1709.

Jeśli tworzysz w systemie Windows 10 kwietnia 2018 r. Zaktualizuj maszynę (w wersji 1803), możesz użyć Windows w wersji 1709 lub obrazów platformy docker w wersji 1803 Windows.

Następujące obrazy systemu operacyjnego kontener może służyć do wdrożenia usług:

- Windows — windowsservercore i nanoserver
    - Windows Server w wersji 1709
    - Windows Server w wersji 1803
- Linux
    - Nie znane ograniczenia

## <a name="developer-experience-issues"></a>Problemy z środowisko dla deweloperów

### <a name="dns-resolution-and-outgoing-traffic-from-a-container-doesnt-work"></a>Rozpoznawanie nazw DNS i ruchu wychodzącego z kontenera nie działa

Jeśli masz kontener, który wysyła ruch wychodzący, w tym próby skontaktowania się z usługą DNS go może zakończyć się niepowodzeniem w pewnych okolicznościach. To jest badana. Aby uniknąć:

- Korzystanie z usługi Windows Fall Creators update (wersja 1709) lub nowszego jako obraz podstawowy kontenera.
- Jeśli sama nazwa usługi nie rozwiąże problemu, spróbuj w pełni kwalifikowana nazwa: ServiceName.ApplicationName.
- W pliku platformy Docker dla usługi, dodać `EXPOSE <port>` w przypadku, gdy jest to port są udostępnianie usługi w. Na przykład:

```DockerFile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS nie działać tak samo, jak klastrów usługi Service Fabric rozwoju i siatka

Konieczne może być inaczej w lokalnego klastra projektowego niż w Azure siatki odwoływać się do usługi.

W przypadku lokalnego klastra projektowego `{serviceName}.{applicationName}`. W Azure Service Fabric siatki `{servicename}`. 

Azure siatki aktualnie nie obsługuje rozpoznawanie nazw DNS w aplikacjach.

Aby uzyskać innych znanych problemach DNS z systemem klaster projektowy usługi Service Fabric w systemie Windows 10 zobacz: [Debugowanie kontenerów Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) i [znane problemy z usługą DNS](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Networking

Sieć ServiceFabric translatora adresów Sieciowych może zniknąć podczas korzystania z tą aplikacją na komputerze lokalnym. Aby zdiagnozować, czy to się stało, uruchom następujące polecenie w wierszu polecenia:

`docker network ls` i zwróć uwagę, czy `servicefabric_nat` znajduje się na liście.  W przeciwnym razie następnie uruchom następujące polecenie: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Spowoduje to rozwiązać problem, nawet jeśli aplikacja została już wdrożona lokalnie, jak i w złej kondycji.

### <a name="issues-running-multiple-apps"></a>Problemy z uruchamianiem wielu aplikacji

Może wystąpić dostępności procesora CPU i limity zostanie naprawiony we wszystkich aplikacjach. Aby uniknąć:
- Utwórz klaster z pięcioma węzłami.
- Ogranicz użycie procesora CPU w usługach w aplikacji, które zostało wdrożone. Na przykład w pliku service.yaml Twojej usługi, należy zmienić `cpu: 1.0` do `cpu: 0.5`

Nie można wdrożyć wiele aplikacji do klastra z jednym węzłem. Aby uniknąć:
- W przypadku wdrażania wielu aplikacji w lokalnym klastrze, należy użyć klastra z pięcioma węzłami.
- Usuń aplikacje, które nie są obecnie testy.

## <a name="feature-gaps-and-other-known-issues"></a>Funkcja luki i innych znanych problemach

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Po wdrożeniu aplikacji, zasobów sieciowych skojarzonych z nim nie ma adresu IP

Istnieje znany problem, w której adres IP nie stanie się dostępna natychmiast. Sprawdź stan zasobów sieciowych w ciągu kilku minut, aby wyświetlić skojarzony adres IP.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Moja aplikacja zakończy się niepowodzeniem, dostęp do zasobów sieci prawo/woluminu

W modelu aplikacji aby można było uzyskać dostęp do skojarzonego zasobu należy użyć pełny identyfikator zasobu sieci i woluminy. Oto przykład z przykładu szybkiego startu:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Gdy I skalowanie w poziomie, wszystkie moje kontenerów jest narażony, łącznie z uruchamianiem tych

Jest to błąd, a zastosowana poprawka.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Przegląd](service-fabric-mesh-overview.md).

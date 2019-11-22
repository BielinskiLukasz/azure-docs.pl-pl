---
title: Jak udostępnić Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: 2c85625a4f61b701bc1e8b0a4a06f71dc0989ce0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279993"
---
# <a name="share-azure-dev-spaces"></a>Udostępnianie obszarów Azure Dev Spaces

Za pomocą Azure Dev Spaces możesz udostępnić swoje miejsce deweloperskie innym członkom zespołu. Każdy deweloper może pracować w swoim własnym miejscu bez obaw o rozrywanie innych. Ponadto współpraca w jednym miejscu może umożliwić przetestowanie kompleksu kodu bez konieczności tworzenia makiet lub symulowania zależności. Aby uzyskać więcej informacji, zobacz Przewodnik po [programowaniu zespołu](../team-development-nodejs.md) .

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Skonfiguruj miejsce dla deweloperów dla wielu deweloperów

1. Utwórz miejsce deweloperskie na platformie Azure. Wybierz pozycję [.NET Core i vs Code](../get-started-netcore.md), [.NET Core i Visual Studio](../get-started-netcore-visualstudio.md)lub [Node. js i vs Code](../get-started-nodejs.md). Musisz mieć dostęp właściciela lub współautora do wybranej subskrypcji platformy Azure.
1. Skonfiguruj **grupę zasobów** obszaru deweloperskiego platformy Azure, aby [przyznać dostęp współautora](/azure/active-directory/role-based-access-control-configure) do każdego członka zespołu. Aby sprawdzić grupę zasobów obszaru deweloperskiego, należy uruchomić następujące polecenie: `azds list-up`
1. Poproszenie członków zespołu o **wybranie przestrzeni deweloperskiej** w celu jej opracowania.
   * **Wiersz polecenia lub vs Code**: aby zobaczyć istniejące Azure dev Spaces masz do nich dostęp: `azds space list`. Aby wybrać przestrzeń dev: `azds space select`.
   * **Środowisko IDE programu Visual Studio**: Otwórz projekt w programie Visual Studio, wybierz **Azure dev Spaces** z listy rozwijanej uruchamianie ustawień. W otwartym oknie dialogowym wybierz istniejący klaster.

     ![Lista rozwijana ustawień uruchamiania programu Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz informacje [o projektowaniu zespołu](../team-development-nodejs.md) .

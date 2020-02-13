---
title: Migrowanie kont magazynu StorSimple Menedżer urządzeń, subskrypcje
description: Dowiedz się, jak migrować subskrypcje, konta magazynu dla usługi StorSimple Menedżer urządzeń.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 428c336d98e278910b229e9c0d877a9ae6268c96
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169719"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrowanie subskrypcji i kont magazynu skojarzonych z usługą StorSimple Menedżer urządzeń

Może być konieczne przeniesienie usługi StorSimple na nową rejestrację lub nową subskrypcję. Te scenariusze migracji to zmiany konta lub zmiany centrum danych. Skorzystaj z poniższej tabeli, aby zrozumieć, które z tych scenariuszy są obsługiwane, w tym szczegółowe kroki do przeniesienia.

## <a name="account-changes"></a>Zmiany konta

| Czy można przenieść...| Obsługiwane| Przestój| Proces pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Cała subskrypcja (obejmuje konta usług StorSimple i magazynu) do innej rejestracji? | Yes       | Nie       | **Transfer rejestracji**<br>Używanych<li>Zakup nowego zobowiązania platformy Azure w ramach nowej umowy.</li><li>Chcesz migrować wszystkie konta i subskrypcje ze starej rejestracji do nowej. Obejmuje to wszystkie usługi platformy Azure w ramach starej subskrypcji.</li> | **Krok 1. Otwieranie biletu pomocy technicznej platformy Azure Enterprise.**<li>Przejdź do obszaru [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) (Ustawienia — Integracje i usługi).</li><li> Wybierz pozycję **Administracja rejestracji** , a następnie wybierz pozycję **transfer z jednej rejestracji do nowej rejestracji**.<br>**Krok 2. podanie żądanych informacji**<br>Być<li>Numer rejestracji źródła</li><li> Numer rejestracji docelowej</li><li>Data wprowadzenia przeniesienia|
| StorSimple usługę z istniejącego konta do nowej rejestracji?    | Yes       | Nie       | **Transfer konta**<br>Używanych<li>Jeśli nie chcesz, aby przetransferować pełną rejestrację.</li><li>Chcesz przenieść określone konta tylko do nowej rejestracji.</li>| **Krok 1. Otwieranie biletu pomocy technicznej platformy Azure Enterprise.**<li>Przejdź do obszaru [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) (Ustawienia — Integracje i usługi).</li><li>Wybierz pozycję **Administracja rejestracji** , a następnie wybierz pozycję **Przenieś konto EA do nowej rejestracji**.<br>**Krok 2. podanie żądanych informacji**<br>Być<li>Numer rejestracji źródła</li><li> Numer rejestracji docelowej</li><li>Data wprowadzenia przeniesienia|
| StorSimple usługę z jednej subskrypcji do innej subskrypcji?      | Nie        |    Yes         | Brak, proces ręczny|<li>Migruj dane z urządzenia StorSimple.</li><li>Przeprowadzenie resetowania urządzenia do ustawień fabrycznych spowoduje usunięcie wszystkich danych lokalnych na urządzeniu.</li><li>Zarejestruj urządzenie w nowej subskrypcji w usłudze StorSimple Menedżer urządzeń.</li><li>Przeprowadź migrację danych z powrotem do urządzenia.|
|Czy mogę przenieść własność subskrypcji platformy Azure do innego katalogu? | Yes       | Nie       | Kojarzenie istniejącej subskrypcji z katalogiem usługi Azure AD | Zapoznaj się z tematem [kojarzenie istniejącej subskrypcji z katalogiem usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Może upłynąć do 10 minut, zanim wszystko zostanie poprawnie wyświetlone.|
| StorSimple urządzenie z jednej usługi StorSimple Menedżer urządzeń do innej usługi w innym regionie?      | Nie        | Yes            | Brak, proces ręczny |Tak samo jak powyżej.|
| Czy konto magazynu ma nową subskrypcję lub grupę zasobów?     | Yes        | Nie             |Przenieś konto magazynu do innej subskrypcji lub grupy zasobów |Po przeniesieniu, jeśli klucze dostępu do konta magazynu zostaną zaktualizowane, użytkownik będzie musiał ręcznie skonfigurować klucze dostępu dla zmigrowanego konta magazynu za pomocą usługi StorSimple Menedżer urządzeń.|
| Klasyczne konto magazynu na konto magazynu Azure Resource Manager      | Yes        | Nie             |Migrowanie z wersji klasycznej do Azure Resource Manager |<li>Aby uzyskać szczegółowe instrukcje dotyczące migracji konta magazynu z klasycznego do Azure Resource Manager, przejdź do [obszaru Migrowanie klasycznego konta magazynu](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Jeśli klucze dostępu do konta magazynu zostaną zaktualizowane po migracji, użytkownik będzie musiał zsynchronizować klucze dostępu dla zmigrowanego konta magazynu za pomocą usługi StorSimple Menedżer urządzeń. Ma to na celu zapewnienie, że urządzenia StorSimple nadal działają normalnie i umożliwiają warstwy podstawowe/zapasowe danych na platformie Azure. Aby uzyskać szczegółowe instrukcje dotyczące synchronizowania kluczy dostępu, przejdź do obszaru [rotacja przepływu pracy](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> W przypadku urządzenia w chmurze StorSimple, jeśli klasyczne konto magazynu zostanie zmigrowane, ale źródłowa maszyna wirtualna nadal pozostaje w wersji klasycznej, urządzenie powinno działać prawidłowo. W przypadku migrowania podstawowej maszyny wirtualnej dla urządzenia w chmurze funkcja Dezaktywuj i Usuń nie będzie działać.</li><li> W Azure Portal należy utworzyć nowe urządzenia w chmurze StorSimple, a następnie przełączyć je w tryb failover ze starszych urządzeń w chmurze. Nie można utworzyć urządzenia w chmurze StorSimple w nowym Azure Portal przy użyciu klasycznego konta magazynu, które muszą mieć konto usługi Azure Resource Manager Storage. Aby uzyskać więcej informacji, przejdź do [wdrażania urządzenia w chmurze StorSimple i zarządzania nim](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Zmiany centrum danych

| Czy można przenieść...| Obsługiwane|Przestój| Proces pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Usługa StorSimple z jednego centrum danych platformy Azure na inną? | Nie | Yes |Brak, proces ręczny  |<li>Migruj dane z urządzenia StorSimple.</li><li>Przeprowadzenie resetowania urządzenia do ustawień fabrycznych spowoduje usunięcie wszystkich danych lokalnych na urządzeniu.</li><li>Zarejestruj urządzenie z nową subskrypcją w nowej subskrypcji usługi StorSimple Menedżer urządzeń.</li><li>Przeprowadź migrację danych z powrotem do urządzenia.|
| Konto magazynu z jednego centrum danych platformy Azure na inne? | Nie |Yes  |Brak, proces ręczny  | Tak samo jak powyżej.|

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie usługi StorSimple Menedżer urządzeń](storsimple-8000-manage-service.md)
* [Wdrażanie urządzenia z serii StorSimple 8000 w Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)

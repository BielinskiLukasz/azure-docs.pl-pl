---
title: Przywracanie z kopii zapasowej — usłudze Azure App Service
description: Dowiedz się, jak przywrócić aplikację z migawki.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.custom: seodec18
ms.openlocfilehash: 174fbd8f3e4c652c25a78911185ef8a98a80bb50
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2018
ms.locfileid: "53260042"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Przywracanie aplikacji na platformie Azure z migawki
W tym artykule pokazano, jak przywrócić aplikację w [usługi Azure App Service](../app-service/app-service-web-overview.md) z migawki. Aplikację można przywrócić do poprzedniego stanu, na podstawie jednej z aplikacji migawki. Nie trzeba włączyć tworzenie kopii zapasowych migawek, platforma automatycznie zapisuje migawkę wszystkich aplikacji na potrzeby odzyskiwania danych.

Migawki to kopie przyrostowe w tle, a także oferują wiele zalet w porównaniu do zwykłych [kopie zapasowe](web-sites-backup.md):
- Brak błędów kopiowania pliku z powodu blokady plików.
- Nie ograniczenie rozmiaru magazynu.
- Nie jest wymagana żadna konfiguracja.

Przywracanie z migawki jest dostępny do aplikacji działających **Premium** warstwy lub nowszej. Aby uzyskać informacji na temat skalowania aplikacji w górę, zobacz [skalowanie aplikacji na platformie Azure](web-sites-scale.md).

## <a name="limitations"></a>Ograniczenia

- Ta funkcja jest obecnie dostępna w wersji zapoznawczej.
- Można przywracać tylko do tej samej aplikacji lub w miejscu należących do tej aplikacji.
- Usługa App Service zatrzymuje aplikację docelową lub miejsca docelowego podczas przywracania.
- Usługa App Service zapewnia trzy miesiące warte migawek na potrzeby odzyskiwania danych platformy.
- Migawki można przywrócić tylko w ciągu ostatnich 30 dni.
 

## <a name="restore-an-app-from-a-snapshot"></a>Przywracanie aplikacji z migawki

1. Na **ustawienia** strony aplikacji w [witryny Azure portal](https://portal.azure.com), kliknij przycisk **kopie zapasowe** do wyświetlenia **kopie zapasowe** strony. Następnie kliknij przycisk **przywrócić** w obszarze **Snapshot(Preview)** sekcji.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. W **przywrócić** wybierz migawek do przywrócenia.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Określ miejsce docelowe przywracania aplikacji w **miejsce docelowe przywracania**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Jeśli wybierzesz **Zastąp**, wszystkie istniejące dane w bieżącym system plików aplikacji jest usuwane i zastąpić. Przed kliknięciem przycisku **OK**, upewnij się, że to, co chcesz zrobić.
   > 
   > 
      
   > [!Note]
   > Ze względu na bieżące ograniczenia techniczne można przywracać tylko do aplikacji w tej samej jednostce skalowania. To ograniczenie zostanie usunięta w przyszłej wersji.
   > 
   > 
   
    Możesz wybrać **istniejącą aplikację** można przywrócić w miejscu. Przed użyciem tej opcji należy utworzono już miejsca w aplikacji.

4. Można przywrócić konfigurację lokacji.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Kliknij przycisk **OK**.

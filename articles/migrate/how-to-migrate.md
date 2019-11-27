---
title: Dodaj narzędzia migracji w Azure Migrate
description: Dowiedz się, jak dodać narzędzia migracji w Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185959"
---
# <a name="add-migration-tools"></a>Dodawanie narzędzi migracji

W tym artykule opisano sposób dodawania narzędzi migracji w programie [Azure Migrate](migrate-overview.md).

Azure Migrate oferuje centrum narzędzi do oceny i migracji na platformę Azure. Obejmuje ona narzędzia natywne, Narzędzia udostępniane przez inne usługi platformy Azure oraz oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Jeśli chcesz dodać narzędzie do migracji i nie został jeszcze skonfigurowany projekt Azure Migrate, postępuj zgodnie z tym [artykułem](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Wybieranie narzędzia niezależnego dostawcy oprogramowania

Jeśli wybierzesz [Narzędzie niezależnego dostawcy oprogramowania](migrate-services-overview.md#isv-integration) do migracji, możesz zacząć od uzyskania licencji lub zarejestrować się w celu skorzystania z bezpłatnej wersji próbnej zgodnie z zasadami dotyczącymi niezależnych dostawców oprogramowania. W każdym narzędziu jest dostępna opcja nawiązywania połączenia z Azure Migrate. Wdróż narzędzie i postępuj zgodnie z instrukcjami i dokumentacją narzędzia, aby połączyć obszar roboczy narzędzia z Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Wybierz scenariusz migracji

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**.
2. Wybierz scenariusz migracji, którego chcesz użyć:

    - Aby przeprowadzić migrację maszyn i obciążeń na platformę Azure, wybierz pozycję **Oceń i Przeprowadź migrację serwerów**.
    - Aby przeprowadzić migrację lokalnych maszyn SQL, wybierz pozycję **Oceń i Przeprowadź migrację baz danych**.
    - Aby przeprowadzić migrację lokalnych aplikacji sieci Web, wybierz pozycję **Oceń i Migruj aplikacje sieci Web**.
    - Aby migrować duże ilości danych lokalnych na platformę Azure w trybie offline, wybierz pozycję **zamów urządzenie Data Box**.

    ![Scenariusz oceny](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Wybierz narzędzie do migracji serwera

1. Kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.
2. Jeśli jeszcze nie dodano narzędzi do migracji w obszarze **Azure Migrate-Server**, w obszarze **Narzędzia migracji**wybierz **pozycję kliknij tutaj, aby dodać narzędzie do migracji**. Jeśli dodano już narzędzia migracji, w obszarze **Dodaj więcej narzędzi migracji**wybierz pozycję **Zmień**.

    > [!NOTE]
    > Jeśli chcesz przejść do innego projektu, w obszarze **serwery Azure Migrate**, kliknij **przycisk kliknij tutaj**, aby **wyświetlić szczegóły dotyczące innego projektu migracji**.

3. W **Azure Migrate**wybierz narzędzie do migracji, którego chcesz użyć.
    - W przypadku korzystania z migracji serwera Azure Migrate można skonfigurować i uruchomić migracje bezpośrednio w projekcie Azure Migrate.
    - Jeśli używasz narzędzia do oceny innej firmy, przejdź do linku podanego dla niezależnego dostawcy oprogramowania i uruchom migrację zgodnie z instrukcjami, które zapewnia.

## <a name="select-a-database-migration-tool"></a>Wybierz narzędzie do migracji bazy danych

1. Kliknij pozycję **Oceń i Przeprowadź migrację baz danych**
2. W obszarze **bazy danych**kliknij pozycję **Dodaj narzędzia**.
3. W obszarze Dodaj narzędzie > **Wybierz narzędzie migracji**wybierz narzędzie, którego chcesz użyć do migrowania bazy danych.

## <a name="select-a-web-app-migration-tool"></a>Wybierz narzędzie do migracji aplikacji sieci Web

1. Kliknij pozycję **Oceń i Migruj aplikacje sieci Web**.
2. Użyj linku do narzędzia migracji dla Azure App Service. Użyj narzędzia migracji, aby:

    - **Oceń aplikacje online**: możesz ocenić i zmigrować aplikacje za pomocą publicznego adresu URL w trybie online przy użyciu Asystent migracji Azure App Service.
    - **.NET/php**: w przypadku wewnętrznych aplikacji platformy .NET i php można pobrać i uruchomić Asystent migracji.

## <a name="order-an-azure-data-box"></a>Zamów Azure Data Box

Aby migrować duże ilości danych na platformę Azure, możesz zamówić pole danych platformy Azure na potrzeby transferu danych w trybie offline.

1. Kliknij pozycję **zamów urządzenie Data Box**.
2. W obszarze **wybierz Azure Data Box**określ swoją subskrypcję. 
3. Transfer będzie zaimportowany do platformy Azure. Określ źródło danych oraz miejsce docelowe dla danych w regionie platformy Azure.

## <a name="next-steps"></a>Następne kroki

Wypróbowanie migracji przy użyciu Azure Migrate migracji serwera dla maszyn wirtualnych [funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub programu [VMware](tutorial-migrate-vmware.md) .

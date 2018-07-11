---
title: Tworzyć i dostosowywać plany odzyskiwania na potrzeby trybu failover i odzyskiwania w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i dostosowywać plany odzyskiwania w usłudze Azure Site Recovery. W tym artykule opisano tryb failover i odzyskiwanie maszyn wirtualnych i serwerów fizycznych.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 853b9c2f50967d7a203e5627bdd2fc6f14e0e4f6
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921005"
---
# <a name="create-and-customize-recovery-plans"></a>Tworzenie i dostosowywanie planów odzyskiwania

W tym artykule opisano sposób tworzenia i dostosowywania planu odzyskiwania w [usługi Azure Site Recovery](site-recovery-overview.md). Przed rozpoczęciem [więcej](recovery-plan-overview.md) informacje o planach odzyskiwania.

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania

1. W magazynie usługi Recovery Services, wybierz **plany odzyskiwania (Usługa Site Recovery)** > **+ planu odzyskiwania**.
2. W **Tworzenie planu odzyskiwania**, określ nazwę dla tego planu.
3. Wybierz źródłowy i docelowy oparty na maszynach w planie, a następnie wybierz pozycję **usługi Resource Manager** dla modelu wdrażania. Lokalizacja źródłowa musi mieć maszyn, które są włączone dla trybu failover i odzyskiwania. 

   **Tryb failover** | **Element źródłowy** | **Docelowy** 
   --- | --- | ---
   Azure–Azure | Region platformy Azure |Region platformy Azure
   VMware do platformy Azure | Serwer konfiguracji | Azure
   Maszyny fizyczne na platformie Azure | Serwer konfiguracji | Azure   
   Funkcji Hyper-V zarządzanych przez program VMM do platformy Azure  | Nazwa wyświetlana w programie VMM | Azure
   Funkcji Hyper-V bez programu VMM na platformie Azure | Nazwa lokacji funkcji Hyper-V | Azure
   Program VMM do programu VMM |Przyjazna nazwa programu VMM | Nazwa wyświetlana w programie VMM 

   > [!NOTE]
   > Plan odzyskiwania może zawierać maszyn za pomocą tego samego źródłowego i docelowego. Oprogramowanie VMware i maszyny wirtualne funkcji Hyper-V zarządzanych przez program VMM nie może być w tym samym planie. Maszyny wirtualne VMware i serwerów fizycznych, może być w ten sam plan, w którym źródłem jest serwer konfiguracji.

2. W **wybierz elementy wirtualne**, wybierz komputery (lub grupy replikacji), który chcesz dodać do planu. Następnie kliknij przycisk **OK**.
    - Domyślna grupa (Grupa 1) są dodawane maszyny w planie. Po przejściu w tryb failover wszystkich maszyn w tej grupie rozpoczynają się o tym samym czasie.
    - Możesz wybrać tylko te maszyny są w lokalizacji źródłowej i docelowej, określone. 
1. Kliknij przycisk **OK** Aby utworzyć plan.

## <a name="add-a-group-to-a-plan"></a>Dodawanie grupy do planu

Możesz utworzyć dodatkowe grupy i Dodaj maszyny do różnych grup, tak że można określić różne zachowania na podstawie grupy według grupy. Na przykład można określić, gdy maszyn w grupie należy uruchomić po włączeniu trybu failover lub określić niestandardowe akcje dla każdej grupy.

1. W **plany odzyskiwania**, kliknij plan prawym przyciskiem myszy > **Dostosuj**. Domyślnie po utworzeniu planu maszyn, które dodano do niego są wszystkie znajdujące się w domyślnej grupy 1.
2. Kliknij przycisk **+ grupa**. Domyślnie nowa grupa ponumerowane w kolejności, w której zostanie dodany. Może mieć maksymalnie siedem grup.
3. Wybierz maszynę, aby przejść do nowej grupy, kliknij przycisk **Zmień grupę**, a następnie wybierz nową grupę. Alternatywnie, kliknij prawym przyciskiem myszy nazwę grupy > **chronionego elementu**i Dodaj maszyny do grupy. Maszyny lub replikacji grupy może wyłącznie należeć do jednej grupy w planie odzyskiwania.


## <a name="add-a-script-or-manual-action"></a>Dodawanie skryptów lub akcji ręcznej

Plan odzyskiwania można dostosować, dodając skryptów lub akcji ręcznej. Należy pamiętać, że:

- Jeśli przeprowadzasz replikację do platformy Azure można zintegrować elementów runbook usługi Azure automation do planu odzyskiwania. [Dowiedz się więcej](site-recovery-runbook-automation.md).
- Jeśli replikujesz maszyny wirtualne funkcji Hyper-V zarządzanych przez program System Center VMM, możesz utworzyć skrypt na serwerze programu VMM w środowisku lokalnym i uwzględnić go w ramach planu odzyskiwania.
- Po dodaniu skryptu, dodaje nowy zestaw akcji dla grupy. Na przykład zestaw kroki przed operacją dla 1 grupy jest tworzony z nazwą *Grupa 1: kroki przed operacją*. Wszystkie kroki przed operacją są wymienione w tym zestawie. Tylko wtedy, gdy jest wdrożony serwer programu VMM, można dodać skrypt w lokacji głównej.
- Po dodaniu akcji ręcznej po uruchomieniu planu odzyskiwania, zatrzymuje się w punkcie, w jakim wstawione akcji ręcznej. Okno dialogowe wyświetli monit o określenie, że akcja ręczna została zakończona.
- Aby utworzyć skrypt na serwerze programu VMM, postępuj zgodnie z instrukcjami [w tym artykule](hyper-v-vmm-recovery-script.md).
- Skrypty można zastosować podczas trybu failover do lokacji dodatkowej, a podczas powrotu po awarii z lokacji dodatkowej do podstawowej. Obsługa zależy od danego scenariusza replikacji:
    
    **Scenariusz** | **Tryb failover** | **Powrót po awarii**
    --- | --- | --- 
    Azure–Azure  | Element Runbook | Element Runbook
    Z programu VMware do platformy Azure | Element Runbook | Nie dotyczy 
    Funkcji Hyper-V za pomocą programu VMM na platformie Azure | Element Runbook | Skrypt
    Z lokacji funkcji Hyper-V na platformę Azure | Element Runbook | Nie dotyczy
    Program VMM do dodatkowej z programem VMM | Skrypt | Skrypt

1. W planie odzyskiwania, kliknij ten krok, do którego ma zostać dodany akcji i określ, kiedy powinny występować akcji:. Jeśli chcesz, aby akcja została wykonana przed maszyn w grupie są uruchamiane po przejściu w tryb failover wybierz **Dodaj akcję wstępną**.
    b. Akcja została wykonana po maszyn w menu start grupy po włączeniu trybu failover, zaznacz **Dodaj akcję po**. Aby zmienić położenie akcji, wybierz **Przenieś w górę** lub **Przenieś w dół** przycisków.
2. W **Wstawianie akcji**, wybierz opcję **skryptu** lub **akcji ręcznej**.
3. Jeśli chcesz dodać akcję ręczną, wykonaj następujące czynności". Wpisz nazwę dla akcji, a następnie wpisz w instrukcjach akcji. Uruchamiających przełączenie w tryb failover zostaną wyświetlone następujące instrukcje.
    b. Określ, czy chcesz dodać akcji ręcznej dla wszystkich typów przejścia w tryb failover (Testowanie pracy w trybie Failover planowana praca awaryjna (jeśli dotyczy)). Następnie kliknij przycisk **OK**.
4. Jeśli chcesz dodać skrypt, wykonaj następujące czynności:. W przypadku dodawania skryptów programu VMM, zaznacz **przejściu w tryb Failover skryptu VMM programu**, a następnie w w **ścieżka skryptu** wpisz względną ścieżkę do udziału. Na przykład jeśli udział znajduje się w lokalizacji \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, określ ścieżkę: \RPScripts\RPScript.PS1.
    b. W przypadku dodawania usługi Azure automation Uruchom książki, określ **konta usługi Azure Automation** w którym znajduje się element runbook i wybierz odpowiedni **skrypt elementu Runbook usługi Azure**.
5. Uruchom testowanie trybu failover planu odzyskiwania, aby upewnić się, że skrypt działa zgodnie z oczekiwaniami.

## <a name="watch-a-video"></a>Obejrzyj wideo

Obejrzyj film wideo przedstawiający sposób tworzenia planu odzyskiwania.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [uruchamiania trybu failover](site-recovery-failover.md).  

    

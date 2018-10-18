---
title: Zarządzanie użytkownikami w usłudze Azure Data Box Gateway | Microsoft Docs
description: W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: 3bd368cf19edf1370aaeef54d3ba0e4fd26ac239
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070679"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Zarządzanie użytkownikami w usłudze Azure Data Box Gateway przy użyciu witryny Azure Portal 

W tym artykule opisano sposób zarządzania użytkownikami w usłudze Azure Data Box Gateway. Usługą Azure Data Box Gateway można zarządzać w witrynie Azure Portal lub za pomocą lokalnego internetowego interfejsu użytkownika. Witryna Azure Portal umożliwia dodawanie, modyfikowanie i usuwanie użytkowników.

> [!IMPORTANT]
> - Usługa Data Box Gateway jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie użytkownika
> * Modyfikowanie użytkownika
> * Usuwanie użytkownika 

## <a name="about-users"></a>Informacje o użytkownikach

Użytkownicy mogą mieć prawo tylko do odczytu lub pełne uprawnienia. Jak sugerują nazwy, użytkownicy z prawem tylko do odczytu mogą wyłącznie wyświetlać dane udziału. Użytkownicy z pełnymi uprawnieniami mogą odczytywać dane udziału, dokonywać zapisu w tych udziałach oraz modyfikować lub usuwać dane udziału. 

 - **Użytkownik z pełnymi uprawnieniami** — użytkownik lokalny z pełnym dostępem. 
 - **Użytkownik z prawem tylko do odczytu** — użytkownik lokalny z dostępem tylko do odczytu. Ci użytkownicy są powiązani z udziałami, które umożliwiają wykonywanie operacji tylko do odczytu.

Uprawnienia użytkownika są definiowane podczas jego tworzenia w czasie tworzenia udziału. Po zdefiniowaniu uprawnień związanych z użytkownikiem można je zmodyfikować za pomocą Eksploratora plików. 


## <a name="add-a-user"></a>Dodawanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby dodać użytkownika.

1. W witrynie Azure Portal przejdź do zasobu usługi Data Box Gateway, a następnie do sekcji **Przegląd**. Na pasku poleceń kliknij przycisk **+ Dodaj użytkownika**.

    ![Klikanie przycisku Dodaj użytkownika](media/data-box-gateway-manage-users/add-user-1.png)

2. Określ nazwę i hasło użytkownika, którego chcesz dodać. Potwierdź hasło, a następnie kliknij przycisk **Dodaj**.

    ![Klikanie przycisku Dodaj użytkownika](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Oto użytkownicy zarezerwowani przez system, których nie należy używać: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Otrzymasz powiadomienie, gdy tworzenie użytkownika zostanie rozpoczęte i zakończone. Po utworzeniu użytkownika na pasku poleceń kliknij przycisk **Odśwież**, aby wyświetlić zaktualizowaną listę użytkowników.


## <a name="modify-user"></a>Modyfikowanie użytkownika

Po utworzeniu użytkownika możesz zmienić przypisane do niego hasło. Kliknij wybraną pozycję na liście użytkowników. Podaj i potwierdź nowe hasło. Zapisz zmiany.
 
![Modyfikowanie użytkownika](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Usuwanie użytkownika

Wykonaj następujące czynności w witrynie Azure Portal, aby usunąć użytkownika.

1. Kliknij wybranego użytkownika na liście, a następnie kliknij przycisk **Usuń**.  

   ![Usuwanie użytkownika](media/data-box-gateway-manage-users/delete-user-1.png)

2. Po wyświetleniu monitu potwierdź usunięcie. 

   ![Usuwanie użytkownika](media/data-box-gateway-manage-users/delete-user-2.png)

Lista użytkowników zostanie zaktualizowana w celu uwzględnienia usuniętego użytkownika.

![Usuwanie użytkownika](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać przepustowością](data-box-gateway-manage-bandwidth-schedules.md).

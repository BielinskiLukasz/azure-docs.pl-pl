---
title: "Dodawanie przykładowych modelu tabelarycznego serwera usług Azure Analysis Services | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak dodać modelu próbki z usług Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/28/2018
ms.author: owend
ms.openlocfilehash: df83f5dd86d1edf857378ae69b16a86b57f9a2fe
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-add-a-sample-model"></a>Samouczek: Dodaj model próbki

W tym samouczku można dodać modelu Adventure Works próbki do serwera. Model próbki jest ukończoną wersję modelowania samouczek danych (1200) sprzedaży Internet Adventure Works. Model próbki jest przydatne w przypadku testowania modelu administracyjnego, nawiązywania połączenia z narzędzia i aplikacje klienckie i badanie danych modelu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Serwer usług Azure Analysis Services
- Uprawnienia administratora serwera

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>Tworzenie modelu próbki

1. Na serwerze **omówienie**, kliknij przycisk **nowy model**.

    ![Tworzenie modelu próbki](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. W **nowy model** > **wybierz źródło danych**, sprawdź **przykładowe dane** jest zaznaczone, a następnie kliknij przycisk **Dodaj**.

    ![Wybierz przykładowe dane](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. W **omówienie**, sprawdź `adventureworks` próbki jest tworzony.

    ![Wybierz przykładowe dane](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Przykładowy model korzysta z zasobów pamięci podręcznej. Jeśli nie używasz modelu próbki do testowania, należy ją usunąć z serwera.

> [!NOTE]
> Te kroki opisują sposób usuwania modelu z serwera za pomocą narzędzia SSMS. Możesz także usunąć modelu przy użyciu projektanta funkcji sieci Web w wersji zapoznawczej.

1. W programie SSMS > **Eksplorator obiektów**, kliknij przycisk **Connect** > **usług Analysis Services**.

2. W **Połącz z serwerem**, następnie wkleić w polu Nazwa serwera w **uwierzytelniania**, wybierz **usługi Active Directory - uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**wprowadź nazwy użytkownika, a następnie kliknij przycisk **Połączyć**.

    ![Logowanie](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. W **Eksplorator obiektów**, kliknij prawym przyciskiem myszy `adventureworks` przykładowej bazy danych, a następnie kliknij przycisk **usunąć**.

    ![Usuń przykładowej bazy danych](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Kolejne kroki 

[Połącz w programie Power BI Desktop](analysis-services-connect-pbi.md)   
[Zarządzanie ról bazy danych i użytkowników](analysis-services-database-users.md)



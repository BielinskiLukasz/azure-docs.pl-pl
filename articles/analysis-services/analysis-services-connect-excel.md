---
title: Nawiązywanie połączenia z usługą Azure Analysis Services przy użyciu programu Excel | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z serwerem Azure Analysis Services za pomocą programu Excel. Po nawiązaniu połączenia użytkownicy mogą tworzyć tabele przestawne, aby eksplorować dane.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 123e271ae1b83603d599b9ef0381e25b3c963def
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361561"
---
# <a name="connect-with-excel"></a>Łączenie z programem Excel

Po utworzeniu serwera i wdrożeniu modelu tabelarycznego klienci mogą nawiązywać połączenie i rozpocząć Eksplorowanie danych. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Konto, za pomocą którego logujesz się, musi należeć do roli bazy danych modelu z co najmniej uprawnieniami do odczytu. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Połącz w programie Excel

Łączenie z serwerem w programie Excel jest obsługiwane przy użyciu polecenia Pobierz dane w programie Excel 2016 i nowszych. Łączenie przy użyciu Kreatora importu tabeli w Power Pivot nie jest obsługiwane. 

1. W programie Excel na Wstążce **dane** kliknij pozycję **Pobierz dane zewnętrzne**  >  **z innych źródeł**  >  **z Analysis Services**.

2. W Kreatorze połączenia danych w polu **Nazwa serwera**wprowadź nazwę serwera, w tym protokół i identyfikator URI. Na przykład asazure://westcentralus.asazure.windows.net/advworks. Następnie w obszarze **poświadczenia logowania**wybierz opcję **Użyj następującej nazwy użytkownika i hasła**, a następnie wpisz nazwę użytkownika organizacji, na przykład nancy@adventureworks.com i hasło.

    > [!IMPORTANT]
    > Jeśli zalogujesz się przy użyciu konta Microsoft, usługi Live ID, usługi Yahoo, usługi Gmail itp. lub musisz się zalogować przy użyciu uwierzytelniania wieloskładnikowego, pozostaw pole hasła puste. Po kliknięciu przycisku Dalej zostanie wyświetlony monit o podanie hasła. 

    ![Nawiązywanie połączenia z logowaniem do programu Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. W obszarze **Wybierz bazę danych i tabelę**wybierz bazę danych i model lub perspektywę, a następnie kliknij przycisk **Zakończ**.
   
    ![Połącz z wybierz model z programu Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Zobacz także

[Biblioteki klienckie](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[Zarządzanie serwerem](analysis-services-manage.md)     



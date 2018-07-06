---
title: Samouczek — nawiązywanie połączenia z usługami Azure Analysis Services za pomocą programu Power BI Desktop | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 4096b9f77ba841abfcb4f29f2827aefacf22103f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446592"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Samouczek: nawiązywanie połączenia za pomocą programu Power BI Desktop

W tym samouczku użyjesz programu Power BI Desktop do nawiązania połączenia z przykładową modelową bazą danych adventureworks na serwerze. Zadania, które należy wykonać, symulują typowe połączenie użytkownika z modelem i tworzenie podstawowego raportu na podstawie danych z modelu.

> [!div class="checklist"]
> * Uzyskiwanie nazwy serwera z portalu
> * Nawiązywanie połączenia przy użyciu programu Power BI Desktop
> * Tworzenie podstawowego raportu

## <a name="prerequisites"></a>Wymagania wstępne

- [Dodaj przykładową modelową bazą danych adventureworks](../analysis-services-create-sample-model.md) do serwera.
- Nadaj uprawnienia do [*odczytu*](../analysis-services-server-admins.md) przykładowej modelowej bazy danych adventureworks.
- [Zainstaluj najnowszy program Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
W tym samouczku logowanie do portalu jest konieczne tylko w celu uzyskania nazwy serwera. Zazwyczaj użytkownicy uzyskują nazwę serwera od administratora serwera.

Zaloguj się do [portalu](https://portal.azure.com/).

## <a name="get-server-name"></a>Uzyskiwanie nazwy serwera
Aby móc nawiązać połączenie z serwerem z poziomu programu Power BI Desktop, najpierw należy uzyskać nazwę serwera. Możesz uzyskać nazwę serwera w portalu.

Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
   ![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Nawiązywanie połączenie w programie Power BI Desktop

1. W programie Power BI Desktop kliknij pozycję **Pobierz dane** > **Azure** > **Baza danych usług Azure Analysis Services**.

   ![Łączenie w oknie Pobieranie danych](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. W polu **Serwer** wklej nazwę serwera, w polu **Baza danych** wprowadź **adventureworks** i kliknij przycisk **OK**.

   ![Określanie nazwy serwera i modelowej bazy danych](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Po wyświetleniu monitu wprowadź swoje poświadczenia logowania. Używane konto musi mieć co najmniej uprawnienia do odczytu przykładowej modelowej bazy danych adventureworks.

    Model adventureworks zostanie otwarty w programie Power BI Desktop z pustym raportem w widoku Raport. Na liście **Pola** zostaną wyświetlone wszystkie nieukryte obiekty modelu. Stan połączenia jest wyświetlany w prawym dolnym rogu.

4. Na panelu **WIZUALIZACJE** wybierz pozycję **Wykres słupkowy grupowany**, a następnie kliknij przycisk **Format** (ikona wałka malarskiego) i włącz **Etykiety danych**. 

   ![Wizualizacje](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. W tabeli **Pola** > **Internet Sales** wybierz miary **Internet Sales Total** i **Margin**. W tabeli **Product Category** wybierz miarę **Product Category Name**.

   ![Ukończony raport](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Poświęć kilka minut, aby poznać przykładowy model adventureworks. Możesz utworzyć różne wizualizacje i wyciąć niektóre dane oraz metryki. Po zakończeniu modyfikowania raportu pamiętaj, aby go zapisać.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli raport nie będzie już potrzebny, nie zapisuj go lub usuń pliki, jeśli raport został już zapisany.

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób użycia programu Power BI Desktop do nawiązywania połączenia z modelem danych na serwerze i tworzenia podstawowego raportu. Jeśli nie masz doświadczenia w tworzeniu modelu danych, zobacz [Adventure Works Internet Sales tabular data modeling tutorial](aas-adventure-works-tutorial.md) (Samouczek dotyczący modelowania danych tabelarycznych sprzedaży internetowych w przykładzie Adventure Works).
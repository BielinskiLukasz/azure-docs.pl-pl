---
title: Samouczek — archiwizowanie dzienników usługi Azure Active Directory na koncie magazynu (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak skonfigurować diagnostykę Azure w celu wypychania dzienników usługi Azure Active Directory na konto magazynu (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 861239eeffe7665f252cb7a096b487da71eec93e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197419"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account-preview"></a>Samouczek: archiwizowanie dzienników usługi Azure AD na koncie usługi Azure Storage (wersja zapoznawcza)

W tym samouczku dowiesz się, jak skonfigurować ustawienia diagnostyki usługi Azure Monitor w celu kierowania dzienników usługi Azure Active Directory (Azure AD) do konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne 

Aby używać tej funkcji, potrzebujesz następujących elementów:

* Subskrypcja platformy Azure z kontem usługi Azure Storage. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure AD.
* Użytkownik będący *administratorem globalnym* lub *administratorem zabezpieczeń* dla tej dzierżawy usługi Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiwizowanie dzienników na koncie usługi Azure Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz kolejno pozycje **Azure Active Directory** > **Aktywność** > **Dzienniki inspekcji**. 

3. Wybierz pozycję **Eksportuj ustawienia**. 

4. W okienku **Ustawienia diagnostyczne** wykonaj jedną z następujących czynności:
    * Aby zmienić istniejące ustawienia, wybierz polecenie **Edytuj ustawienie**.
    * Aby dodać nowe ustawienie, wybierz polecenie **Dodaj ustawienia diagnostyki**.  
      Możesz mieć maksymalnie trzy ustawienia. 

    ![Eksportowanie ustawień](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Wprowadź przyjazną nazwę ustawienia, która będzie przypominała o jego przeznaczeniu (na przykład *Wyślij na konto usługi Azure Storage*). 

6. Zaznacz pole wyboru **Zarchiwizuj na koncie magazynu**, a następnie wybierz pozycję **Konto magazynu**. 

7. Wybierz subskrypcję platformy Azure i konto magazynu, do którego chcesz kierować dzienniki.
 
8. Wybierz pozycję **OK**, aby zakończyć konfigurację.

9. Wykonaj co najmniej jedną z następujących czynności:
    * Aby wysłać dzienniki inspekcji na konto magazynu, zaznacz pole wyboru **AuditLogs** (Dzienniki inspekcji). 
    * Aby wysłać dzienniki logowania na konto magazynu, zaznacz pole wyboru **SignInLogs** (Dzienniki logowania).

10. Za pomocą suwaka ustaw czas przechowywania danych dziennika. Domyślnie ta wartość wynosi *0*, co oznacza że dzienniki będą przechowywane na koncie magazynu przez czas nieokreślony. Jeśli ustawisz inną wartość, zdarzenia starsze niż ta liczba dni zostaną automatycznie czyszczone.

11. Wybierz pozycję **Zapisz**, aby zapisać ustawienie.

    ![Ustawienia diagnostyczne](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Po około 15 minutach sprawdź, czy dzienniki zostały wypchnięte na konto magazynu. Przejdź do witryny [Azure Portal](https://portal.azure.com), wybierz pozycję **Konta magazynu**, wybierz konto magazynu, które było używane wcześniej, i kliknij pozycję **Obiekty blob**. W przypadku **dzienników inspekcji** wybierz pozycję **insights-log-audit**. W przypadku **dzienników logowania** wybierz pozycję **insights-logs-signin**.

    ![Konto magazynu](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Następne kroki

* [Interpret audit logs schema in Azure Monitor (Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor)](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)

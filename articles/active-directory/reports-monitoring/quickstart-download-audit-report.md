---
title: Szybki start — pobieranie raportów z inspekcji w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak pobrać raport z inspekcji przy użyciu witryny Azure Portal
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f3e5dd1c42537ce6ff419d7d81d69d824242ec4
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989676"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Szybki start: pobieranie raportu z inspekcji przy użyciu witryny Azure Portal

W tym przewodniku szybki start dowiesz się, jak pobrać plik CSV dzienników inspekcji dla dzierżawy w ciągu ostatnich 24 godzin. Możesz pobrać do 250 000 rekordów z Azure Portal. Rekordy są domyślnie sortowane według najnowszych, dlatego można pobrać najnowsze 250 000 rekordów. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure Active Directory. 
* Użytkownik mający rolę **administratora zabezpieczeń**, **czytelnika zabezpieczeń** lub **administratora globalnego** dla dzierżawy. Ponadto każdy użytkownik w dzierżawie może uzyskiwać dostęp do własnych dzienników inspekcji.

## <a name="quickstart-download-an-audit-report"></a>Szybki start: Pobieranie raportu inspekcji

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługę **Azure Active Directory** z okienka nawigacji po lewej stronie i za pomocą przycisku **Przełącz katalog** wybierz swój katalog Active Directory.
3. Na pulpicie nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie **Dzienniki inspekcji**. 
4. Wybierz pozycję **Ostatnie 24 godziny** z listy rozwijanej filtru **Zakres dat** i wybierz przycisk **Zastosuj**, aby wyświetlić dzienniki inspekcji z ostatnich 24 godzin. 
5. Wybierz przycisk **Pobierz** , wybierz opcję **CSV** jako format pliku i określ nazwę pliku, aby pobrać plik CSV zawierający filtrowane rekordy. 

![Raportowanie](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Następne kroki

* [Raporty dotyczące logowań w portalu Azure Active Directory](concept-sign-ins.md)
* [Azure Active Directory reporting retention (Przechowywanie raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md)
* [Azure Active Directory reporting latencies](reference-reports-latencies.md) (Opóźnienia raportowania w usłudze Azure Active Directory)

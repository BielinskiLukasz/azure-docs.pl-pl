---
title: Rozwiązywanie problemów z usługi BizTalk Services przy użyciu dzienników operacji | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługi BizTalk Services przy użyciu dzienników operacji. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: f69035202a3358af38ebaf8e94abdd3b030e633f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256072"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Usługa BizTalk Services: Rozwiązywanie problemów przy użyciu dzienników operacji

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Co to są dzienniki operacji
Dzienniki operacji jest funkcją usług zarządzania, która służy do wyświetlania historycznych dzienniki operacji wykonywanych na usługi platformy Azure, w tym usługi BizTalk Services. Dzięki temu można wyświetlić dane historyczne związane z operacjami zarządzania sprzed nawet 180 dni subskrypcji usługi BizTalk.

> [!NOTE]
> Ta funkcja przechwytuje tylko dzienniki operacji zarządzania w usłudze BizTalk Services, takich jak podczas uruchomienia usługi kopii działa, i tak dalej. Operacje takie są śledzone za pomocą [interfejsów API REST usługi BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx). Aby uzyskać pełną listę operacji, które są śledzone za pomocą usługi zarządzania, zobacz [operacje śledzonych przy użyciu usługi Azure Management Services](#bizops).<br/><br/>
> Nie przechwytuje dzienniki działań związanych z środowisko wykonawcze usług BizTalk (np. komunikat o przetwarzane przez mostków itd.). Aby wyświetlić dzienniki, użyj widoku śledzenia z portalu usługi BizTalk Services. Aby uzyskać więcej informacji, zobacz [komunikaty śledzenia](https://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Wyświetl dzienniki operacji usługi BizTalk Services
1. W portalu, wybierz **usług zarządzania**, a następnie wybierz pozycję **dzienniki operacji** kartę.
2. Można filtrować dzienniki na podstawie różnych parametrów, takich jak subskrypcji, zakres dat, typ usługi (np. usługi BizTalk Services), nazwa usługi lub stanu operacji (Powodzenie, niepowodzenie).
3. Wybierz znacznik wyboru, aby wyświetlić listy filtrowane. Na poniższej ilustracji przedstawiono działań związanych z testbiztalkservice: ![wyświetlanie dzienników operacji][ViewLogs] 
4. Aby wyświetlić więcej o określonej operacji, zaznacz wiersz, a następnie kliknij przycisk **szczegóły** na pasku zadań u dołu.

## <a name="bizops"></a>Operacjach rejestrowanych przy użyciu usługi zarządzania platformą Azure
W poniższej tabeli przedstawiono operacje, które są śledzone za pomocą usługi zarządzania platformy Azure:

| Nazwa operacji | Zadanie |
| --- | --- |
| CreateBizTalkService |Operację, aby utworzyć nową usługę BizTalk |
| DeleteBizTalkService |Operacja usuwania usługi BizTalk |
| RestartBizTalkService |Operacja ponownego uruchomienia usługi BizTalk |
| StartBizTalkService |Operacja można uruchomić usługi BizTalk |
| StopBizTalkService |Operacja można zatrzymać usługi BizTalk |
| DisableBizTalkService |Operacja wyłączania usługi BizTalk |
| EnableBizTalkService |Operacja, aby włączyć usługi BizTalk |
| BackupBizTalkService |Operacja tworzenia kopii zapasowej usługi BizTalk |
| RestoreBizTalkService |Operacja przywrócenia usługi BizTalk z określonej kopii zapasowej |
| SuspendBizTalkService |Operacja do wstrzymania usługi BizTalk |
| ResumeBizTalkService |Operacja, aby wznowić działanie usługi BizTalk |
| ScaleBizTalkService |Operacja skalowania usługi BizTalk w górę lub w dół |
| ConfigUpdateBizTalkService |Operację, aby zaktualizować konfigurację usługi BizTalk |
| ServiceUpdateBizTalkService |Operacja, aby uaktualnić lub obniżyć wersję usługi BizTalk do innej wersji |
| PurgeBackupBizTalkService |Operacja przeczyszczania usługi BizTalk poza okresem przechowywania kopii zapasowych |

## <a name="see-also"></a>Zobacz też
* [Usługa BizTalk kopii zapasowej](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Przywracanie usługi BizTalk z kopii zapasowej](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Usługa BizTalk Services: Deweloper, podstawowa, standardowa i Premium Editions Chart](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Usługa BizTalk Services: Inicjowanie obsługi administracyjnej](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Provisioning Status Chart (Usługa BizTalk Services: aprowizowanie wykresu stanu)](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png


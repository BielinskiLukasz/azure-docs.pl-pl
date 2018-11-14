---
title: Rozpoczynanie pracy z usługą Azure AD, interfejsu API raportowania | Dokumentacja firmy Microsoft
description: Jak rozpocząć pracę z usługą Azure Active Directory, interfejsu API raportowania
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 860d602ecba257ed9015d1e080e5dcb1aa5ab872
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624628"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Rozpoczynanie pracy z usługą Azure Active Directory, interfejsu API raportowania

Usługa Azure Active Directory oferuje szereg [raporty](overview-reports.md), zawierający informacje przydatne w przypadku aplikacji, takich jak systemy SIEM, inspekcji i narzędzia do analizy biznesowej. 

Za pomocą interfejsu API programu Microsoft Graph dla raportów usługi Azure AD, możesz uzyskać programowy dostęp do danych za pomocą zestawu interfejsów API, oparte na protokole REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania.

Ten artykuł zawiera omówienie interfejsu API raportowania, w tym o metodach dostępu do niego.

Jeśli napotkasz problemy, zobacz [jak uzyskać pomoc techniczną dotyczącą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Wymagania wstępne

Dostęp do raportowania interfejsu API, z lub bez interwencji użytkownika, należy:

1. Przypisz role (Administrator globalny Czytelnik zabezpieczeń, administratora zabezpieczeń)
2. Rejestrowanie aplikacji
3. Udzielenie uprawnień
4. Zbieranie ustawień konfiguracji

Aby uzyskać szczegółowe instrukcje, zobacz [wymagania wstępne dotyczące dostępu do usługi Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Punkty końcowe interfejsu API 

Punkt końcowy interfejsu API Microsoft Graph dla dzienników inspekcji jest `https://graph.microsoft.com/beta/auditLogs/directoryAudits` i punkt końcowy interfejsu API Microsoft Graph do logowania jest `https://graph.microsoft.com/beta/auditLogs/signIns`. Aby uzyskać więcej informacji, zobacz [inspekcji dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) i [logowania dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

## <a name="apis-with-graph-explorer"></a>Interfejsy API za pomocą Eksploratora programu Graph

Możesz użyć [MSGraph explorer](https://developer.microsoft.com/graph/graph-explorer) w celu sprawdzenia logowania i inspekcji danych interfejsu API. Upewnij się, że Zaloguj się do swojego konta przy użyciu zarówno przycisków logowania w Interfejsie użytkownika programu Graph Explorer i ustaw **AuditLog.Read.All** i **Directory.Read.All** uprawnienia dla swojej dzierżawy, jak pokazano.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Modyfikowanie uprawnień interfejsu użytkownika](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Dostęp do interfejsu API raportowania usługi Azure AD za pomocą przystawki Certyfikaty 

Za pomocą usługi Azure AD Reporting API certyfikaty Jeśli planowane jest pobrać dane raportów bez interwencji użytkownika.

Aby uzyskać szczegółowe instrukcje, zobacz [pobieranie danych przy użyciu usługi Azure API raportowania usługi AD z certyfikatami](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Kolejne kroki

 * [Wymagania wstępne dotyczące dostępu do interfejsu API raportowania](howto-configure-prerequisites-for-reporting-api.md) 
 * [Pobieranie danych przy użyciu usługi Azure AD interfejsu API raportowania przy użyciu certyfikatów](tutorial-access-api-with-certificates.md)
 * [Rozwiązywanie problemów z błędami w usłudze Azure AD, interfejsu API raportowania](troubleshoot-graph-api.md)



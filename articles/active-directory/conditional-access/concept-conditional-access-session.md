---
title: Kontrolki sesji w zasadach dostępu warunkowego — Azure Active Directory
description: Co to są kontrolki sesji w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84c794e6fe751bc1c12b90353ef7b285f31a2331
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192144"
---
# <a name="conditional-access-session"></a>Dostęp warunkowy: sesja

W ramach zasad dostępu warunkowego administrator może korzystać z kontrolek sesji w celu włączenia ograniczonych środowisk w określonych aplikacjach w chmurze.

![Zasady dostępu warunkowego z kontrolką Grant wymagające uwierzytelniania wieloskładnikowego](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Wymuszone ograniczenia aplikacji

Organizacje mogą używać tego formantu, aby wymagać od usługi Azure AD przekazywania informacji o urządzeniach do wybranych aplikacji w chmurze. Informacje o urządzeniu umożliwiają aplikacjom w chmurze dowiedzieć się, czy połączenie jest inicjowane z urządzenia zgodnego lub przyłączonego do domeny. Ta kontrolka obsługuje tylko usługi SharePoint Online i Exchange Online jako wybrane aplikacje w chmurze. Po wybraniu tej opcji aplikacja w chmurze korzysta z informacji o urządzeniu, aby zapewnić użytkownikom, w zależności od stanu urządzenia, z ograniczoną lub pełnym doświadczeniem.

Aby uzyskać więcej informacji o używaniu i konfigurowaniu ograniczeń wymuszonych przez aplikację, zobacz następujące artykuły:

- [Włączanie ograniczonego dostępu za pomocą usługi SharePoint Online](https://docs.microsoft.com/sharepoint/control-access-from-unmanaged-devices)
- [Włączanie ograniczonego dostępu za pomocą usługi Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Kontrola aplikacji dostępu warunkowego

Kontrola dostępu warunkowego aplikacji używa architektury zwrotnego serwera proxy i jest unikatowo zintegrowany z dostępem warunkowym usługi Azure AD. Dostęp warunkowy usługi Azure AD umożliwia wymuszanie kontroli dostępu w aplikacjach organizacji na podstawie określonych warunków. Warunki definiują użytkowników lub grupę użytkowników oraz informacje o tym, co (w których aplikacjach w chmurze) oraz o tym, gdzie (które lokalizacje i sieci) są stosowane zasady dostępu warunkowego. Po ustaleniu warunków można kierować użytkowników do [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) , gdzie można chronić dane przy użyciu kontrola dostępu warunkowego aplikacji, stosując kontrolki dostępu i sesji.

Kontroli dostępu warunkowego aplikacji umożliwia dostęp do aplikacji użytkownika i sesji, aby dało się monitorować i kontrolowane w czasie rzeczywistym na podstawie zasad dostępu i sesji. Zasady dostępu i sesji są używane w portalu usługi Cloud App Security do filtrów i połącz dane z zestawu akcje do wykonania na koncie użytkownika. Za pomocą zasad dostępu i sesji możesz wykonywać następujące czynności:

- Zapobieganie eksfiltracji danych: można zablokować pobieranie, wycinanie, kopiowanie i drukowanie poufnych dokumentów na przykład na urządzeniach niezarządzanych.
- Ochrona przy pobieraniu: zamiast blokowania pobierania poufnych dokumentów, można wymagać, aby dokumenty były etykietowane i chronione przy użyciu Azure Information Protection. Ta akcja zapewnia ochronę dokumentu i dostęp użytkowników jest ograniczony do potencjalnie ryzykownej sesji.
- Zapobiegaj przekazywaniu plików bez etykiet: przed przekazaniem poufnego pliku, rozpowszechnienym i używanym przez inne osoby ważne jest, aby upewnić się, że plik ma odpowiednią etykietę i ochronę. Można upewnić się, że pliki bez etykiet z poufną zawartością są blokowane przed przekazywaniem, dopóki użytkownik nie zaklasyfikuje zawartości.
- Monitoruj sesje użytkowników pod kątem zgodności: Ryzykowni użytkownicy są monitorowani po zalogowaniu się do aplikacji, a ich akcje są rejestrowane w ramach sesji. Można badać i analizować zachowanie użytkownika, aby zrozumieć, w którym i pod jakimi warunkami sesji zasady powinny być stosowane w przyszłości.
- Blokuj dostęp: możesz szczegółowo blokować dostęp dla określonych aplikacji i użytkowników w zależności od kilku czynników ryzyka. Można na przykład zablokować je, jeśli używają certyfikatów klienta jako formy zarządzania urządzeniami.
- Blokuj działania niestandardowe: niektóre aplikacje mają unikatowe scenariusze, które przenoszą ryzyko, na przykład wysyłanie komunikatów z poufną zawartością w aplikacjach, takich jak Microsoft Teams lub zapasowy. W tych rodzajach scenariuszy można skanować komunikaty pod kątem poufnej zawartości i blokować je w czasie rzeczywistym.

Aby uzyskać więcej informacji, zobacz artykuł [wdrażanie kontrola dostępu warunkowego aplikacji dla polecanych aplikacji](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Częstotliwość logowania (wersja zapoznawcza)

Częstotliwość logowania określa czas, po którym użytkownik zostanie poproszony o ponowne zalogowanie przy próbie uzyskania dostępu do zasobu.

Ustawienie częstotliwości logowania działa z aplikacjami, które mają wdrożone protokoły OAUTH2 lub OIDC zgodnie ze standardami. Większość aplikacji natywnych firmy Microsoft dla systemów Windows, Mac i Mobile, w tym z uwzględnieniem następujących aplikacji sieci Web, jest zgodnych z tym ustawieniem.

- Word, Excel, PowerPoint Online
- OneNote online
- Office.com
- Portal administracyjny usługi O365
- Exchange Online
- Program SharePoint i usługa OneDrive
- Klient sieci Web dla zespołów
- Dynamics CRM Online
- Portalu Azure

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zarządzania sesją uwierzytelniania przy użyciu dostępu warunkowego](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Sesja trwałej przeglądarki (wersja zapoznawcza)

W trwałej sesji przeglądarki użytkownicy mogą pozostać zalogowani po zamknięciu i ponownym otwarciu okna przeglądarki.

Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zarządzania sesją uwierzytelniania przy użyciu dostępu warunkowego](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Następne kroki

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb tylko do raportowania](concept-conditional-access-report-only.md)

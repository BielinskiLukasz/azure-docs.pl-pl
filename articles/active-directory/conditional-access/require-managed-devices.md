---
title: Dostęp warunkowy wymaga urządzenia zarządzanego — Azure Active Directory
description: Dowiedz się, jak skonfigurować zasady dostępu warunkowego oparte na urządzeniach Azure Active Directory (Azure AD), które wymagają zarządzanych urządzeń w celu uzyskania dostępu do aplikacji w chmurze.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd3a0341d9cf041155b09d41d8ff84d0b0cc3dc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424816"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Instrukcje: wymaganie zarządzanych urządzeń dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego

W świecie w chmurze na urządzeniach przenośnych Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do aplikacji i usług z dowolnego miejsca. Autoryzowani użytkownicy mogą uzyskiwać dostęp do aplikacji w chmurze z szeroką gamę urządzeń, w tym urządzeń przenośnych i osobistych. Jednak wiele środowisk ma co najmniej kilka aplikacji, do których mają dostęp tylko urządzenia spełniające standardy dotyczące zabezpieczeń i zgodności. Te urządzenia są również znane jako urządzenia zarządzane. 

W tym artykule wyjaśniono, jak można skonfigurować zasady dostępu warunkowego, które wymagają, aby zarządzane urządzenia mogły uzyskiwać dostęp do niektórych aplikacji w chmurze w danym środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

Wymaganie, aby zarządzane urządzenia dla dostępu do aplikacji w chmurze były połączone z **dostępem warunkowym usługi Azure AD** i **zarządzaniem urządzeniami usługi Azure AD** . Jeśli nie znasz jeszcze jednego z tych obszarów, najpierw Przeczytaj następujące tematy:

- **[Dostęp warunkowy w Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** — ten artykuł zawiera omówienie pojęć dotyczących dostępu warunkowego i powiązanej terminologii.
- **[Wprowadzenie do zarządzania urządzeniami w Azure Active Directory](../devices/overview.md)** — ten artykuł zawiera omówienie różnych opcji, które należy wykonać, aby uzyskać dostęp do urządzeń objętych kontrolą organizacyjną. 

## <a name="scenario-description"></a>Opis scenariusza

Głównym kompromisem jest równowaga między bezpieczeństwem a produktywnością. Rozprzestrzenianie się obsługiwanych urządzeń w celu uzyskania dostępu do zasobów w chmurze ułatwia zwiększenie produktywności użytkowników. Na stronie odwracania prawdopodobnie nie chcesz, aby niektóre zasoby w danym środowisku były dostępne na urządzeniach z nieznanym poziomem ochrony. W przypadku zasobów, których to dotyczy, należy wymagać, aby użytkownicy mieli do nich dostęp tylko przy użyciu urządzenia zarządzanego. 

Za pomocą dostępu warunkowego usługi Azure AD można rozwiązać ten wymóg przy użyciu jednej zasady, która udziela dostępu:

- Do wybranych aplikacji w chmurze
- Dla wybranych użytkowników i grup
- Wymaganie urządzenia zarządzanego

## <a name="managed-devices"></a>Urządzenia zarządzane  

W przypadku prostych warunków zarządzanymi urządzeniami są urządzenia, które podlegają *pewnemu sortowaniu* kontroli organizacyjnej. W usłudze Azure AD wymaganie wstępne dla zarządzanego urządzenia polega na tym, że zostało ono zarejestrowane w usłudze Azure AD. Zarejestrowanie urządzenia tworzy tożsamość urządzenia w postaci obiektu urządzenia. Ten obiekt jest używany przez platformę Azure do śledzenia informacji o stanie urządzenia. Jako administrator usługi Azure AD można już używać tego obiektu do przełączania (włączania/wyłączania) stanu urządzenia.
  
![Warunki oparte na urządzeniach](./media/require-managed-devices/32.png)

Aby uzyskać urządzenie zarejestrowane w usłudze Azure AD, masz trzy opcje: 

- **Zarejestrowane urządzenia usługi Azure AD** — Aby uzyskać osobiste urządzenie zarejestrowane w usłudze Azure AD
- **Urządzenia przyłączone do usługi Azure AD** — Aby uzyskać organizacyjne urządzenie z systemem Windows 10, które nie jest przyłączone do lokalnej usługi AD zarejestrowanej w usłudze Azure AD. 
- **Hybrydowe urządzenia dołączone do usługi Azure AD** — Aby uzyskać system Windows 10 lub obsługiwane urządzenie niskiego poziomu, które jest przyłączone do lokalnej usługi AD zarejestrowanej w usłudze Azure AD.

Te trzy opcje zostały omówione w artykule [co to jest tożsamość urządzenia?](../devices/overview.md)

Aby zostać urządzeniem zarządzanym, zarejestrowane urządzenie musi być **urządzeniem przyłączonym do hybrydowej usługi Azure AD** lub **urządzeniem, które zostało oznaczone jako zgodne**.  

![Warunki oparte na urządzeniach](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Wymagaj hybrydowych urządzeń przyłączonych do usługi Azure AD

W zasadach dostępu warunkowego można wybrać opcję **Wymagaj urządzenia dołączonego do hybrydowej usługi Azure AD** , aby można było uzyskać dostęp do wybranych aplikacji w chmurze tylko przy użyciu urządzenia zarządzanego. 

![Warunki oparte na urządzeniach](./media/require-managed-devices/10.png)

To ustawienie dotyczy tylko urządzeń z systemem Windows 10 lub niższego poziomu, takich jak Windows 7 lub Windows 8, które są przyłączone do lokalnej usługi AD. Te urządzenia można zarejestrować tylko w usłudze Azure AD przy użyciu hybrydowego sprzężenia usługi Azure AD, które jest [zautomatyzowanym procesem](../devices/hybrid-azuread-join-plan.md) uzyskiwania zarejestrowanego urządzenia z systemem Windows 10. 

![Warunki oparte na urządzeniach](./media/require-managed-devices/45.png)

Co sprawia, że urządzenie dołączone do hybrydowej usługi Azure AD jest urządzeniem zarządzanym?  W przypadku urządzeń, które są przyłączone do lokalnej usługi AD, zakłada się, że kontrola nad tymi urządzeniami jest wymuszana przy użyciu rozwiązań do zarządzania, takich jak **System Center Configuration Manager (SCCM)** lub **zasady grupy (GP)** w celu zarządzania nimi. Ponieważ nie ma metody usługi Azure AD w celu ustalenia, czy którakolwiek z tych metod została zastosowana do urządzenia, wymaganie hybrydowego urządzenia dołączonego do usługi Azure AD jest stosunkowo słabym mechanizmem wymaganym przez urządzenie zarządzane. Administratorzy mogą ocenić, czy metody, które są stosowane do lokalnych urządzeń przyłączonych do domeny, są wystarczająco duże, aby stanowiły urządzenie zarządzane, jeśli takie urządzenie jest również urządzeniem przyłączonym do hybrydowej usługi Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Opcja wymagania, aby *urządzenie było oznaczone jako zgodne* , jest najmocniejszą formą żądania urządzenia zarządzanego.

![Warunki oparte na urządzeniach](./media/require-managed-devices/11.png)

Ta opcja wymaga, aby urządzenie było zarejestrowane w usłudze Azure AD, a także oznaczone jako zgodne przez:
         
- Intune
- System zarządzania urządzeniami przenośnymi (MDM, Mobile Device Management) służący jako urządzenie z systemem Windows 10 w ramach integracji z usługą Azure AD. Systemy zarządzania urządzeniami przenośnymi innych firm dla typów systemów operacyjnych innych niż Windows 10 nie są obsługiwane.
 
![Warunki oparte na urządzeniach](./media/require-managed-devices/46.png)

W przypadku urządzenia oznaczonego jako zgodnego można założyć, że: 

- Urządzenia przenośne używane przez pracowników do uzyskiwania dostępu do danych firmowych są zarządzane
- Zarządzanie aplikacjami mobilnymi używanymi przez pracowników
- Informacje o firmie są chronione przez pomoc w kontroli sposobu, w jaki pracownicy uzyskują do nich dostęp i udostępniają ją
- Urządzenie i jego aplikacje są zgodne z wymaganiami firmy dotyczącymi zabezpieczeń

### <a name="known-behavior"></a>Znane zachowanie

W systemach Windows 7, iOS, Android, macOS i niektórych przeglądarkach sieci Web innych firm usługa Azure AD identyfikuje urządzenie przy użyciu certyfikatu klienta, który jest inicjowany, gdy urządzenie jest zarejestrowane w usłudze Azure AD. Gdy użytkownik po raz pierwszy zaloguje się za pomocą przeglądarki, użytkownik jest monitowany o wybranie certyfikatu. Użytkownik końcowy musi wybrać ten certyfikat, zanim będzie mógł kontynuować korzystanie z przeglądarki.

## <a name="next-steps"></a>Następne kroki

Przed skonfigurowaniem zasad dostępu warunkowego opartego na urządzeniach w środowisku należy zapoznać się z [najlepszymi rozwiązaniami dotyczącymi dostępu warunkowego w Azure Active Directory](best-practices.md).

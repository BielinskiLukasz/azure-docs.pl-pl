---
title: Planowanie sprzężenia Azure Active Directory hybrydowego — Azure Active Directory
description: Dowiedz się, jak skonfigurować urządzenia dołączone hybrydowo do usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7c4a0e64e1f08bb3e80eaf67937da10906bfce0
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591612"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Instrukcje: planowanie implementacji dołączania hybrydowego Azure Active Directory

Podobnie jak w przypadku użytkownika, urządzenie jest kolejną tożsamością podstawową, która ma być chroniona, i używanie jej do ochrony zasobów w dowolnym momencie i z dowolnej lokalizacji. Aby osiągnąć ten cel, można przełączać tożsamości urządzeń i zarządzać nimi w usłudze Azure AD przy użyciu jednej z następujących metod:

- Dołączenie do usługi Azure AD
- Dołączenie hybrydowe do usługi Azure AD
- Rejestracja w usłudze Azure AD

Przenosząc urządzenia do usługi Azure AD, można zmaksymalizować wydajność użytkowników dzięki zastosowaniu logowania jednokrotnego (SSO) w zasobach chmury i zasobach lokalnych. W tym samym czasie można zabezpieczyć dostęp do zasobów w chmurze i lokalnych przy użyciu [dostępu warunkowego](../active-directory-conditional-access-azure-portal.md).

Jeśli masz lokalne środowisko Active Directory (AD) i chcesz dołączyć komputery przyłączone do domeny usługi AD do usługi Azure AD, możesz to zrobić, wykonując sprzężenie hybrydowe usługi Azure AD. Ten artykuł zawiera powiązane kroki umożliwiające zaimplementowanie hybrydowego sprzężenia usługi Azure AD w środowisku. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz już [wprowadzenie do zarządzania tożsamościami urządzeń w Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minimalna wymagana wersja kontrolera domeny dla hybrydowego sprzężenia usługi Azure AD systemu Windows 10 to Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planowanie implementacji

Aby zaplanować implementację hybrydowej usługi Azure AD, zapoznaj się z:

|   |   |
| --- | --- |
| ![Sprawdź][1] | Przejrzyj obsługiwane urządzenia |
| ![Sprawdź][1] | Przejrzyj interesujące Cię rzeczy |
| ![Sprawdź][1] | Przeglądanie kontrolowanej weryfikacji hybrydowego sprzężenia usługi Azure AD |
| ![Sprawdź][1] | Wybierz swój scenariusz w oparciu o infrastrukturę tożsamości |
| ![Sprawdź][1] | Zapoznaj się z lokalną obsługą UPN usługi AD na potrzeby hybrydowego dołączania do usługi Azure AD |

## <a name="review-supported-devices"></a>Przejrzyj obsługiwane urządzenia

Sprzężenie hybrydowe usługi Azure AD obsługuje szeroką gamę urządzeń z systemem Windows. Ponieważ konfiguracja urządzeń ze starszymi wersjami systemu Windows wymaga dodatkowych lub różnych kroków, obsługiwane urządzenia są pogrupowane w dwie kategorie:

### <a name="windows-current-devices"></a>Bieżące urządzenia z systemem Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

W przypadku urządzeń z systemem operacyjnym Windows dla komputerów stacjonarnych obsługiwana wersja jest wymieniona w tym artykule [Informacje o wersji systemu Windows 10](https://docs.microsoft.com/windows/release-information/). Najlepszym rozwiązaniem, firma Microsoft zaleca uaktualnienie do najnowszej wersji systemu Windows 10.

### <a name="windows-down-level-devices"></a>Urządzenia niskiego poziomu systemu Windows

- Windows 8.1
- Obsługa systemu Windows 7 zakończyła się 14 stycznia 2020. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna dla systemu Windows 7 została zakończona](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Informacje o pomocy technicznej dotyczące systemów Windows Server 2008 i 2008 R2 znajdują się w temacie [przygotowanie do systemu Windows server 2008 End of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

Jako pierwszy krok planowania należy przejrzeć środowisko i określić, czy należy obsługiwać urządzenia niskiego poziomu systemu Windows.

## <a name="review-things-you-should-know"></a>Przejrzyj interesujące Cię rzeczy

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze
- Sprzężenie hybrydowe usługi Azure AD nie jest obecnie obsługiwane, jeśli środowisko składa się z jednego lasu usługi Active Directory, który synchronizuje dane tożsamości z więcej niż jedną dzierżawą usługi Azure AD.

- Hybrydowe dołączanie usługi Azure AD nie jest obsługiwane w przypadku systemu Windows Server z uruchomioną rolą kontrolera domeny (DC).

- Funkcja hybrydowego przyłączania do usługi Azure AD nie jest obsługiwana na urządzeniach niskiego poziomu systemu Windows podczas korzystania z roamingu poświadczeń lub roamingu profilu użytkownika lub profilu obowiązkowego.

### <a name="os-imaging-considerations"></a>Zagadnienia dotyczące tworzenia obrazu systemu operacyjnego
- Jeśli korzystasz z narzędzia przygotowywania systemu (Sysprep) i używasz obrazu **sprzed systemu Windows 10 1809** do instalacji, upewnij się, że obraz nie pochodzi z urządzenia, które jest już zarejestrowane w usłudze Azure AD jako sprzężenie hybrydowe usługi Azure AD.

- Jeśli korzystasz z migawki maszyny wirtualnej w celu utworzenia dodatkowych maszyn wirtualnych, upewnij się, że migawka nie pochodzi z maszyny wirtualnej, która jest już zarejestrowana w usłudze Azure AD jako sprzężenie hybrydowe usługi Azure AD.

- Jeśli używasz [ujednoliconego filtru zapisu](https://docs.microsoft.com/windows-hardware/customize/enterprise/unified-write-filter) i podobnych technologii, które usuwają zmiany na dysku po ponownym uruchomieniu, muszą one być stosowane po przyłączeniu urządzenia do hybrydowej usługi Azure AD. Włączenie takich technologii przed ukończeniem hybrydowego sprzężenia usługi Azure AD spowoduje odłączenie urządzenia do każdego ponownego uruchomienia

### <a name="handling-devices-with-azure-ad-registered-state"></a>Obsługa urządzeń z zarejestrowanym stanem usługi Azure AD
Jeśli urządzenia przyłączone do domeny systemu Windows 10 są zarejestrowane w dzierżawie [usługi Azure AD](overview.md#getting-devices-in-azure-ad) , może to doprowadzić do podwójnego stanu hybrydowego przyłączonego do usługi Azure AD i zarejestrowanego urządzenia usługi Azure AD. Zalecamy uaktualnienie do systemu Windows 10 1803 (z zastosowaniem programu KB4489894) lub nowszego, aby automatycznie rozwiązać ten scenariusz. W wersjach wcześniejszych niż 1803 należy ręcznie usunąć zarejestrowany stan usługi Azure AD przed włączeniem hybrydowego sprzężenia usługi Azure AD. W 1803 i nowszych wersjach wprowadzono następujące zmiany, aby uniknąć tego podwójnego stanu:

- Wszystkie istniejące zarejestrowane Stany usługi Azure AD zostaną automatycznie usunięte <i>po przyłączeniu urządzenia do hybrydowej usługi Azure AD</i>.
- Aby uniemożliwić urządzeniu przyłączonym do domeny możliwość rejestracji w usłudze Azure AD, Dodaj ten klucz rejestru — HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = DWORD: 00000001.
- W systemie Windows 10 1803, jeśli masz skonfigurowaną usługi Windows Hello dla firm, użytkownik musi zmienić konfigurację usługi Windows Hello dla firm po oczyszczeniu podwójnego stanu. Ten problem został rozwiązany z KB4512509

> [!NOTE]
> Zarejestrowane urządzenie usługi Azure AD nie zostanie automatycznie usunięte, jeśli jest zarządzane przez usługę Intune.

### <a name="additional-considerations"></a>Dodatkowe kwestie do uwzględnienia
- Jeśli środowisko używa infrastruktury pulpitów wirtualnych (VDI), zobacz [tożsamość urządzenia i Wirtualizacja pulpitu](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

- Hybrydowe dołączanie usługi Azure AD jest obsługiwane w przypadku modułu TPM zgodnego ze standardem FIPS 2,0 i nie jest obsługiwane dla modułu TPM 1,2 Jeśli urządzenia są zgodne ze standardem FIPS 1,2, należy je wyłączyć przed przejściem do hybrydowego sprzężenia usługi Azure AD. Firma Microsoft nie udostępnia żadnych narzędzi do wyłączania trybu FIPS dla moduły TPM, ponieważ jest on zależny od producenta modułu TPM. Aby uzyskać pomoc techniczną, skontaktuj się z producentem OEM. Począwszy od wersji Windows 10 1903, moduły TPM 1,2 nie są używane na potrzeby hybrydowego przyłączenia do usługi Azure AD, a urządzenia z tymi moduły tpmami będą uznawane za niemające modułu TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Przeglądanie kontrolowanej weryfikacji hybrydowego sprzężenia usługi Azure AD

Gdy wszystkie wymagania wstępne są stosowane, urządzenia z systemem Windows będą automatycznie rejestrowane jako urządzenia w dzierżawie usługi Azure AD. Stan tych tożsamości urządzeń w usłudze Azure AD jest określany jako sprzężenie hybrydowe usługi Azure AD. Więcej informacji na temat pojęć uwzględnionych w tym artykule można znaleźć w artykule [wprowadzenie do zarządzania tożsamościami urządzeń w Azure Active Directory](overview.md).

Organizacje mogą chcieć przeprowadzić kontrolowane sprawdzenie poprawności hybrydowego sprzężenia usługi Azure AD przed włączeniem jej w całej organizacji. Zapoznaj się z artykułem [Sprawdzanie poprawności funkcji dołączania hybrydowego usługi Azure AD](hybrid-azuread-join-control.md) , aby zrozumieć, jak to zrobić.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Wybierz swój scenariusz w oparciu o infrastrukturę tożsamości

Sprzężenie hybrydowe usługi Azure AD współdziała z systemami, zarządzanymi i federacyjnymi, w zależności od tego, czy nazwa UPN jest w trakcie routingu czy bez obsługi routingu. W dolnej części strony tabeli zapoznaj się z obsługiwanymi scenariuszami.  

### <a name="managed-environment"></a>Środowisko zarządzane

Środowisko zarządzane można wdrożyć przy użyciu opcji [Synchronizacja skrótów haseł (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) lub [uwierzytelnianie przekazywane (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) z [bezproblemowym logowaniem jednokrotnym](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Te scenariusze nie wymagają konfigurowania serwera federacyjnego na potrzeby uwierzytelniania.

### <a name="federated-environment"></a>Środowisko federacyjne

Środowisko federacyjne powinno mieć dostawcę tożsamości, który obsługuje poniższe wymagania. Jeśli masz środowisko federacyjne wykorzystujące Active Directory Federation Services (AD FS), poniższe wymagania są już obsługiwane.

- **WIAORMULTIAUTHN:** To żądanie jest wymagane do hybrydowego przyłączenia do usługi Azure AD dla urządzeń niższego poziomu systemu Windows.
- **Protokół WS-Trust:** Ten protokół jest wymagany do uwierzytelniania bieżących urządzeń przyłączonych hybrydowo usługi Azure AD za pomocą usługi Azure AD. W przypadku korzystania z AD FS należy włączyć następujące punkty końcowe protokołu WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **Usługi ADFS/Services/Trust/2005/windowstransport** lub **ADFS/Services/Trust/13/windowstransport** powinny być włączone tylko jako punkty końcowe dostępne dla intranetu i nie mogą być udostępniane jako punkty końcowe dla ekstranetu za pośrednictwem serwera proxy aplikacji sieci Web. Aby dowiedzieć się więcej na temat wyłączania punktów końcowych usługi WS-Trust systemu Windows, zobacz temat [wyłączanie punktów końcowych systemu Windows WS-Trust na serwerze proxy](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Możesz zobaczyć, jakie punkty końcowe są włączone, za pomocą konsoli zarządzania usług AD FS w obszarze **Usługi** > **Punkty końcowe**.

> [!NOTE]
> Usługa Azure AD nie obsługuje kart inteligentnych ani certyfikatów w domenach zarządzanych.

Począwszy od wersji 1.1.819.0, program Azure AD Connect zapewnia kreator umożliwiający konfigurowanie dołączania hybrydowego do usługi Azure AD. Kreator pozwala znacznie uprościć proces konfiguracji. Jeśli instalacja wymaganej wersji Azure AD Connect nie jest opcją dla Ciebie, zobacz [Ręczne konfigurowanie rejestracji urządzeń](hybrid-azuread-join-manual.md). 

W zależności od scenariusza, który jest zgodny z infrastrukturą tożsamości, zobacz:

- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
- [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Zapoznaj się z lokalną obsługą UPN usługi AD na potrzeby hybrydowego dołączania do usługi Azure AD

Czasami lokalne UPN usługi AD mogą różnić się od nazw UPN usługi Azure AD. W takich przypadkach hybrydowe dołączenie usługi Azure AD systemu Windows 10 zapewnia ograniczoną obsługę lokalnych nazw UPN usługi AD na podstawie [metody uwierzytelniania](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), typu domeny i wersji systemu Windows 10. Istnieją dwa typy lokalnych nazw UPN usługi AD, które mogą istnieć w Twoim środowisku:

- Nazwa UPN z obsługą routingu: nazwa UPN z obsługą routingu ma poprawną zweryfikowaną domenę, która jest zarejestrowana w rejestratorze domeny. Na przykład jeśli contoso.com jest domeną podstawową w usłudze Azure AD, contoso.org jest domeną podstawową w lokalnej usłudze AD należącej do firmy Contoso i [zweryfikowaną w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Nazwa UPN bez obsługi routingu: nazwa UPN bez obsługi routingu nie ma zweryfikowanej domeny. Ma to zastosowanie tylko w sieci prywatnej organizacji. Na przykład jeśli contoso.com jest domeną podstawową w usłudze Azure AD, contoso. local jest domeną podstawową w lokalnej usłudze AD, ale nie jest domeną zweryfikowaną w Internecie i używaną tylko w sieci firmy Contoso.

Poniższa tabela zawiera szczegółowe informacje na temat pomocy technicznej dotyczącej lokalnych nazw UPN w systemie Windows 10 hybrydowego sprzężenia usługi Azure AD

| Typ lokalnej nazwy UPN usługi AD | Typ domeny | Wersja systemu Windows 10 | Opis |
| ----- | ----- | ----- | ----- |
| Wzajemn | Federacyjny | Od wersji 1703 | Ogólnie dostępna |
| Bez obsługi routingu | Federacyjny | Od wersji 1803 | Ogólnie dostępna |
| Wzajemn | Zarządzanych | Od wersji 1803 | Ogólnie dostępna usługa Azure AD SSPR w systemie Windows ekranu blokady nie jest obsługiwana |
| Bez obsługi routingu | Zarządzanych | Nieobsługiwane | |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj sprzężenie hybrydowe Azure Active Directory dla środowiska federacyjnego](hybrid-azuread-join-federated-domains.md)
> [skonfigurować sprzężenie Azure Active Directory hybrydowego dla środowiska zarządzanego](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png

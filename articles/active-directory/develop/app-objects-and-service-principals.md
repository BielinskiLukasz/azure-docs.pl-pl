---
title: Aplikacje & nazwy główne usługi w usłudze Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Informacje o relacji między obiektami obiektów głównych aplikacji i usług w Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: d8ff8ced1723a3cdc969b4a951c31ea1da2d3093
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518301"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory

W tym artykule opisano rejestrację aplikacji, obiekty aplikacji i jednostki usługi w Azure Active Directory: co to jest, jak są używane oraz jak są ze sobą powiązane. Przykładowy scenariusz z wieloma dzierżawcami został również przedstawiony do zilustrowania relacji między obiektem aplikacji aplikacji i odpowiadającymi obiektami głównych usług.

## <a name="application-registration"></a>Rejestracja aplikacji
Aby delegować funkcje zarządzania tożsamościami i dostępem do usługi Azure AD, należy zarejestrować aplikację w [dzierżawie](developer-glossary.md#tenant)usługi Azure AD. Po zarejestrowaniu aplikacji w usłudze Azure AD tworzysz konfigurację tożsamości dla aplikacji, która umożliwia integrację z usługą Azure AD. Po zarejestrowaniu aplikacji w [Azure Portal][AZURE-Portal]należy określić, czy jest ona pojedynczą dzierżawą (dostępną tylko w dzierżawie) czy z wieloma dzierżawcami (dostępną dla innych dzierżawców) i opcjonalnie mogą ustawić identyfikator URI przekierowania (do którego jest wysyłany token dostępu).

Po zakończeniu rejestracji aplikacji masz globalne, unikatowe wystąpienie aplikacji (obiektu aplikacji), które znajdują się w dzierżawie lub katalogu głównym.  Masz również globalnie unikatowy identyfikator dla aplikacji (Identyfikator aplikacji lub klienta).  W portalu możesz dodać wpisy tajne lub certyfikaty i zakresy, aby umożliwić działanie aplikacji, dostosować znakowanie aplikacji w oknie dialogowym logowania i nie tylko.

Jeśli aplikacja zostanie zarejestrowana w portalu, obiekt aplikacji oraz obiekt główny usługi są tworzone automatycznie w dzierżawie głównej.  Jeśli zarejestrujesz/utworzysz aplikację przy użyciu Microsoft Graph interfejsów API, tworzenie obiektu głównego usługi jest osobnym krokiem.

## <a name="application-object"></a>Obiekt aplikacji
Aplikacja usługi Azure AD jest definiowana przez jeden i tylko obiekt aplikacji, który znajduje się w dzierżawie usługi Azure AD, w której zarejestrowano aplikację (znaną jako dzierżawa "główna" aplikacji).  Obiekt aplikacji jest używany jako szablon lub plan do tworzenia co najmniej jednego obiektu jednostki usługi.  Nazwa główna usługi jest tworzona w każdej dzierżawie, w której jest używana aplikacja. Podobnie jak Klasa w programowaniu zorientowanym obiektom, obiekt aplikacji ma pewne właściwości statyczne, które są stosowane do wszystkich utworzonych nazw głównych usługi (lub wystąpień aplikacji). 

Obiekt Application opisuje trzy aspekty aplikacji: sposób, w jaki usługa może wystawiać tokeny w celu uzyskania dostępu do aplikacji, zasoby, do których aplikacja może potrzebować dostępu, oraz akcje, które może wykonać aplikacja. 

Blok **rejestracje aplikacji** w [Azure Portal][AZURE-Portal] służy do wyświetlania obiektów aplikacji w dzierżawie domowej i zarządzania nimi.

[Jednostka aplikacji][MS-Graph-App-Entity] Microsoft Graph definiuje schemat właściwości obiektu aplikacji.

## <a name="service-principal-object"></a>Obiekt główny usługi
Aby uzyskać dostęp do zasobów zabezpieczonych przez dzierżawę usługi Azure AD, jednostka wymagająca dostępu musi być reprezentowana przez podmiot zabezpieczeń. Dotyczy to zarówno użytkowników (głównej nazwy użytkownika), jak i aplikacji (nazwy głównej usługi). Podmiot zabezpieczeń definiuje zasady dostępu i uprawnienia dla użytkownika/aplikacji w dzierżawie usługi Azure AD. Umożliwia to korzystanie z podstawowych funkcji, takich jak uwierzytelnianie użytkownika/aplikacji podczas logowania, oraz autoryzacja w trakcie dostępu do zasobów.

Jednostka usługi to lokalna reprezentacja lub wystąpienie aplikacji globalnego obiektu aplikacji w pojedynczej dzierżawie lub katalogu. Nazwa główna usługi jest konkretnym wystąpieniem utworzonym na podstawie obiektu aplikacji i dziedziczy pewne właściwości z tego obiektu aplikacji.  Jednostka usługi jest tworzona w każdej dzierżawie, w której używana jest aplikacja, i odwołuje się do globalnego, unikatowego obiektu aplikacji.  Obiekt główny usługi definiuje, co aplikacja może w rzeczywistości wykonać w określonej dzierżawie, kto może uzyskiwać dostęp do aplikacji i jakie zasoby mogą uzyskać do niej dostęp. 

Gdy aplikacja uzyskuje uprawnienia dostępu do zasobów w dzierżawie (po rejestracji lub [zgodzie](developer-glossary.md#consent)), tworzony jest obiekt jednostki usługi. Możesz również utworzyć obiekt jednostki usługi w dzierżawie przy użyciu [Azure PowerShell](howto-authenticate-service-principal-powershell.md), interfejsu wiersza polecenia platformy Azure, [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http), [Azure Portal][AZURE-Portal]i innych narzędzi.  W przypadku korzystania z portalu jednostka usługi jest tworzona automatycznie podczas rejestrowania aplikacji.

Blok **aplikacje przedsiębiorstwa** w portalu służy do wyświetlania i zarządzania jednostkami usługi w dzierżawie. Można wyświetlić uprawnienia jednostki usługi, uprawnienia użytkowników, którzy wykonali tę zgodę, informacje logowania i inne.

Jednostka Microsoft Graph [serviceprincipal][MS-Graph-Sp-Entity] definiuje schemat właściwości obiektu głównego usługi.

## <a name="relationship-between-application-objects-and-service-principals"></a>Relacja między obiektami aplikacji a jednostkami usługi

Obiekt aplikacji to *globalna* reprezentacja aplikacji do użycia we wszystkich dzierżawcach, a jednostka usługi to *lokalna* reprezentacja do użycia w określonej dzierżawie.

Obiekt aplikacji służy jako szablon, z którego *pochodzą* typowe i domyślne właściwości do stosowania podczas tworzenia odpowiednich obiektów jednostki usługi. W związku z tym obiekt aplikacji ma 1:1 relację z aplikacją oprogramowania i 1: wiele relacji z odpowiadającymi im obiektami głównych usług.

Należy utworzyć jednostkę usługi w każdej dzierżawie, w której jest używana aplikacja, umożliwiając jej ustalenie tożsamości logowania i/lub dostęp do zasobów zabezpieczonych przez dzierżawcę. Aplikacja jednej dzierżawy ma tylko jedną jednostkę usługi (w swojej głównej dzierżawie) utworzoną do użycia podczas rejestracji aplikacji i mającą na to zgodę. Wielodostępna aplikacja sieci Web/interfejs API ma także nazwę główną usługi utworzoną w każdej dzierżawie, w której użytkownik z tej dzierżawy wyraził zgodę na jego użycie.

> [!NOTE]
> Wszelkie zmiany wprowadzone w obiekcie aplikacji również są odzwierciedlone w jego obiekcie głównym usługi tylko w dzierżawie głównym aplikacji (dzierżawie, w której został zarejestrowany). W przypadku aplikacji wielodostępnych zmiany w obiekcie aplikacji nie są odzwierciedlane w obiektach głównych usługi dzierżawców klientów, dopóki dostęp nie zostanie usunięty za pomocą [panelu dostępu do aplikacji](https://myapps.microsoft.com) i ponownie udzielony.
>
> Należy również zauważyć, że natywne aplikacje są domyślnie zarejestrowane jako wielodostępne.

## <a name="example"></a>Przykład

Na poniższym diagramie przedstawiono relację między obiektem aplikacji aplikacji i odpowiednimi obiektami głównej usługi w kontekście przykładowej aplikacji wielodostępnej o nazwie **HR**. W tym przykładowym scenariuszu istnieją trzy dzierżawy usługi Azure AD:

- **Adatum** — dzierżawca używany przez firmę, która opracowała **aplikację kadrową**
- **Contoso** — dzierżawa używana przez organizację firmy Contoso, która jest klientem **aplikacji kadrowej**
- **Fabrikam** — dzierżawca używany przez organizację firmy Fabrikam, która również korzysta z **aplikacji kadrowej**

![Relacja między obiektem aplikacji a obiektem głównym usługi](./media/app-objects-and-service-principals/application-objects-relationship.svg)

W tym przykładowym scenariuszu:

| Krok | Opis |
|------|-------------|
| 1    | Jest procesem tworzenia obiektów głównych aplikacji i usług w dzierżawie głównej aplikacji. |
| 2    | Gdy administratorzy Contoso i fabrikam ukończyją zgodę, obiekt główny usługi jest tworzony w dzierżawie usługi Azure AD swojej firmy i ma przypisane uprawnienia przyznane przez administratora. Należy również pamiętać, że aplikacja KADRowa mogła zostać skonfigurowana/zaprojektowana tak, aby zezwalać użytkownikom na zgodę na użycie indywidualnych. |
| 3    | Dzierżawy odbiorców aplikacji KADRowej (Contoso i fabrikam) mają własne obiekty główne usługi. Każdy z nich reprezentuje użycie wystąpienia aplikacji w czasie wykonywania, zgodnie z uprawnieniami przydzielonymi przez odpowiedniego administratora. |

## <a name="next-steps"></a>Następne kroki

- Za pomocą [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) można wysyłać zapytania do obiektów głównych aplikacji i usług.
- Dostęp do obiektu aplikacji aplikacji można uzyskać za pomocą interfejsu API Microsoft Graph, edytora manifestu aplikacji [Azure Portal][AZURE-Portal] lub [poleceń cmdlet programu POWERSHELL usługi Azure AD](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), które są reprezentowane przez [jednostkę aplikacji][MS-Graph-App-Entity]OData.
- Dostęp do obiektu głównego usługi aplikacji można uzyskać za pomocą interfejsu API Microsoft Graph lub [poleceń cmdlet programu PowerShell usługi Azure AD](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)reprezentowanego przez jego [jednostkę główną][MS-Graph-Sp-Entity]OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com

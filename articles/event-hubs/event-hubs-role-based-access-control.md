---
title: Oparta na rolach kontrola dostępu (wersja zapoznawcza) — usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące kontroli dostępu opartej na rolach dla usługi Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c7d38538b3876ae91c0ae3794e14ab11f08993c6
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162889"
---
# <a name="active-directory-role-based-access-control-preview"></a>Kontrola dostępu w usłudze Active Directory Role-Based (wersja zapoznawcza)

Microsoft Azure oferuje zarządzanie kontrolą dostępu zintegrowanej dla zasobów i aplikacji opartych na usłudze Azure Active Directory (Azure AD). Za pomocą usługi Azure AD specjalnie dla aplikacji opartych na platformie Azure albo można zarządzać kontami użytkowników i aplikacji, lub może tworzyć federacje istniejącą infrastrukturę usługi Active Directory z usługą Azure AD dla całej firmy single-sign-on zajmującego zasobów platformy Azure a aplikacje hostowane w systemie Azure. Następnie można przypisać te tożsamości użytkowników i aplikacji usługi Azure AD do globalnych i specyficznych dla usługi ról w celu udzielenia dostępu do zasobów platformy Azure.

Dla usługi Azure Event Hubs zarządzania przestrzenie nazw i wszystkie pokrewne zasoby za pośrednictwem witryny Azure portal i zarządzanie zasobami platformy Azure, interfejsu API jest już chroniony przy użyciu *kontroli dostępu opartej na rolach* modelu (RBAC). RBAC dla operacji środowiska uruchomieniowego jest funkcją teraz w publicznej wersji zapoznawczej. 

Aplikacja, która korzysta z usługi Azure AD RBAC nie trzeba obsługiwać zasady sygnatury dostępu Współdzielonego i klucze lub innych tokenów dostępu, specyficzne dla usługi Event Hubs. Aplikacja kliencka wchodzi w interakcję z usługą Azure AD, aby ustanowić kontekstu uwierzytelniania, a następnie uzyskuje token dostępu dla usługi Event Hubs. Przy użyciu kont użytkowników domeny, które wymagają logowania interakcyjnego aplikacja nigdy nie obsługuje żadnych poświadczeń bezpośrednio.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs role i uprawnienia

Początkowe publicznej wersji zapoznawczej można tylko dodać konta usługi Azure AD i nazwy główne usług role "Właściciel" lub "Współautor" w przestrzeni nazw usługi Event Hubs. Ta operacja spowoduje przydzielenie tożsamości pełną kontrolę wszystkich jednostek w przestrzeni nazw. Operacje zarządzania, które zmienić topologii przestrzeni nazw są początkowo tylko obsługiwane do usługi Azure resource management i nie przy użyciu natywnego interfejsu zarządzania REST centrów zdarzeń. Ta obsługa również oznacza, że klienta .NET Framework [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) obiektu nie można używać z kontem usługi Azure AD.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Usługa Event Hubs przy użyciu konta użytkownika domeny usługi Azure AD

W poniższej sekcji opisano kroki wymagane do tworzenia i uruchamiania przykładowej aplikacji, która wyświetla monit dotyczący interaktywnego platformy Azure użytkownika usługi AD w celu logowania się, jak udzielić dostępu usługi Event Hubs do tego konta użytkownika i jak dostęp do usługi Event Hubs za pomocą tej tożsamości. 

Tego wprowadzenia opisano prostej aplikacji konsolowej, [kodu, dla którego znajduje się w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Tworzenie konta użytkownika usługi Active Directory

Ten pierwszy krok jest opcjonalny. Każda subskrypcja platformy Azure jest automatycznie skojarzone z dzierżawą usługi Azure Active Directory, i jeśli użytkownik ma dostęp do subskrypcji platformy Azure, Twoje konto użytkownika jest już zarejestrowany. Oznacza to, że wystarczy użyć swojego konta. 

Jeśli nadal chcesz utworzyć konto określone w tym scenariuszu [wykonaj następujące kroki](../automation/automation-create-aduser-account.md). Musi mieć uprawnienia do tworzenia kont w dzierżawie usługi Azure Active Directory, która nie może być w przypadku większych scenariuszach dla przedsiębiorstw.

### <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Następnie [tworzenie przestrzeni nazw usługi Event Hubs](event-hubs-create.md) w jednym z regionów świadczenia usługi Azure, które mają wersję zapoznawczą obsługi kontroli RBAC usługi Event Hubs: **Wschodnie stany USA**, **wschodnie stany USA 2**, lub **Europa Zachodnia**. 

Po utworzeniu przestrzeni nazw, przejdź do jej **kontrola dostępu (IAM)** strony w portalu, a następnie kliknij przycisk **Dodaj przypisanie roli** można dodać konto użytkownika usługi Azure AD do roli właściciel. Jeśli używasz konta użytkownika, a następnie utworzono przestrzeń nazw, użytkownik jest już w roli właściciela. Aby dodać innego konta do roli, wyszukaj nazwę aplikacji sieci web w **Dodaj uprawnienia** panelu **wybierz** pola, a następnie kliknij pozycję. Następnie kliknij przycisk **Save** (Zapisz). Konto użytkownika ma teraz dostęp do przestrzeni nazw usługi Event Hubs i Centrum zdarzeń została wcześniej utworzona.
 
### <a name="register-the-application"></a>Rejestrowanie aplikacji

Przed uruchomieniem przykładowej aplikacji, zarejestruj go w usłudze Azure AD i zatwierdź monit o wyrażenie zgody, która umożliwia aplikacji dostęp do usługi Event Hubs w jej imieniu. 

Ponieważ Przykładowa aplikacja to aplikacja konsoli, musisz zarejestrować aplikacji natywnej i Dodaj uprawnienia do interfejsu API dla **elementu Microsoft.EventHub** do zestawu "required uprawnienia". Natywne aplikacje wymagają także **identyfikatora URI przekierowania** w usłudze Azure AD, która służy jako identyfikatora; identyfikator URI musi być miejsce docelowe w sieci. Użyj `http://eventhubs.microsoft.com` w tym przykładzie ponieważ próbki kodu już używa tego identyfikatora URI.

Kroki szczegółowe rejestracji są wyjaśnione w [w tym samouczku](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Postępuj zgodnie z instrukcjami Aby zarejestrować **natywnych** aplikacji, a następnie postępuj zgodnie z instrukcjami aktualizacji, aby dodać **elementu Microsoft.EventHub** interfejsu API, aby wymagane uprawnienia. Kroki, zanotuj **TenantId** i **ApplicationId**, ponieważ będzie potrzebny tych wartości, aby uruchomić aplikację.

### <a name="run-the-app"></a>Uruchamianie aplikacji

Przed uruchomieniem próbki, Edytuj plik App.config i zależnie od scenariusza, ustaw następujące wartości:

- `tenantId`: Ustaw **TenantId** wartości.
- `clientId`: Ustaw **ApplicationId** wartość. 
- `clientSecret`: Jeśli chcesz zalogować się przy użyciu klucza tajnego klienta, należy go utworzyć w usłudze Azure AD. Ponadto używane zamiast aplikacji natywnej aplikacji sieci web lub interfejsu API. Ponadto Dodaj aplikację w obszarze **kontrola dostępu (IAM)** w przestrzeni nazw utworzone wcześniej.
- `eventHubNamespaceFQDN`: Ustaw na w pełni kwalifikowana nazwa DNS nowo utworzonej przestrzeni nazw usługi Event Hubs; na przykład `example.servicebus.windows.net`.
- `eventHubName`: Ustaw nazwę Centrum zdarzeń, który został utworzony.
- Identyfikator URI przekierowania określone w aplikacji w poprzednich krokach.
 
Po uruchomieniu aplikacji konsoli, monit o wybór scenariusza; Kliknij przycisk **interakcyjnego logowania użytkownika** , wpisując jego numer, a następnie naciskając klawisz ENTER. Aplikacja wyświetli okno logowania, poprosi o podanie Twojej zgody na dostęp do usługi Event Hubs i następnie używa usługi, aby uruchamiać funkcję wysyłania i odbierania scenariusza, przy użyciu tożsamości logowania.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Usługa Event Hubs szczegóły cennika](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

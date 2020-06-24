---
title: Konfigurowanie Access Control opartej na rolach lokalnych (RBAC) dla interfejsu API platformy Azure dla FHIR
description: W tym artykule opisano sposób konfigurowania usługi Azure API for FHIR w celu używania zewnętrznej dzierżawy usługi Azure AD na potrzeby płaszczyzny danych
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: a8c1b36d6a439297dfb0bbcb34efe059349fc5a2
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "84871910"
---
# <a name="configure-local-rbac-for-fhir"></a>Konfigurowanie lokalnego RBAC dla FHIR 

W tym artykule wyjaśniono, jak skonfigurować usługę Azure API for FHIR do korzystania z zewnętrznej, pomocniczej dzierżawy Azure Active Directory do zarządzania dostępem do płaszczyzny danych. Tego trybu można używać tylko wtedy, gdy nie jest możliwe korzystanie z dzierżawy Azure Active Directory skojarzonej z Twoją subskrypcją.

> [!NOTE]
> Jeśli płaszczyzna danych usługi FHIR jest skonfigurowana do używania głównej dzierżawy Azure Active Directory skojarzonej z subskrypcją, [Użyj funkcji RBAC platformy Azure, aby przypisać role płaszczyzny danych](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Dodawanie jednostki usługi

Lokalna kontrola RBAC umożliwia korzystanie z zewnętrznej dzierżawy Azure Active Directory z serwerem FHIR. Aby umożliwić systemowi RBAC sprawdzenie przynależności do grupy w tej dzierżawie, interfejs API platformy Azure dla FHIR musi mieć nazwę główną usługi w dzierżawie. Ta nazwa główna usługi zostanie utworzona automatycznie w dzierżawach powiązanych z subskrypcjami, w których wdrożono interfejs API platformy Azure dla usługi FHIR, ale w przypadku, gdy dzierżawa nie ma powiązanej subskrypcji, Administrator dzierżawy będzie musiał utworzyć tę jednostkę usługi przy użyciu jednego z następujących poleceń:

Przy użyciu `Az` modułu programu PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

można też użyć `AzureAd` modułu programu PowerShell:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

Możesz też użyć interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Konfigurowanie lokalnego RBAC

Interfejs API platformy Azure dla FHIR można skonfigurować tak, aby korzystał z zewnętrznej lub pomocniczej dzierżawy Azure Active Directory w bloku **uwierzytelniania** :

![Lokalne przypisania RBAC](media/rbac/local-rbac-guids.png).

W polu urząd Wprowadź prawidłową dzierżawę Azure Active Directory. Po sprawdzeniu poprawności dzierżawy należy aktywować pole **dozwolone identyfikatory obiektów** i można wprowadzić listę identyfikatorów obiektów tożsamości. Identyfikatory te mogą być identyfikatorami obiektów tożsamości:

* Azure Active Directory użytkownika.
* Nazwa główna usługi Azure Active Directory.
* Grupa zabezpieczeń usługi Azure Active Directory.

Aby uzyskać więcej informacji, zobacz artykuł jak [znaleźć identyfikatory obiektów tożsamości](find-identity-object-ids.md) .

Po wprowadzeniu wymaganych identyfikatorów obiektów kliknij przycisk **Zapisz** i poczekaj na zapisanie zmian przed próbą uzyskania dostępu do płaszczyzny danych przy użyciu przypisanych użytkowników, podmiotów usługi lub grup.

## <a name="caching-behavior"></a>Zachowanie buforowania

Interfejs API platformy Azure dla usługi FHIR będzie buforować decyzje przez maksymalnie 5 minut. W przypadku przyznania użytkownikowi dostępu do serwera FHIR przez dodanie ich do listy dozwolonych identyfikatorów obiektów lub usunięcie ich z listy, należy oczekiwać, aż zmiany w uprawnieniach do propagowania mają trwać do pięciu minut.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób przypisywania dostępu do płaszczyzny danych FHIR przy użyciu zewnętrznej (pomocniczej) dzierżawy Azure Active Directory. Kolejne informacje o dodatkowych ustawieniach interfejsu API platformy Azure dla usługi FHIR:
 
>[!div class="nextstepaction"]
>[Dodatkowe ustawienia interfejsu API platformy Azure dla usługi FHIR](azure-api-for-fhir-additional-settings.md)


---
title: Znajdowanie identyfikatorów obiektów tożsamości na potrzeby uwierzytelniania — Azure API for FHIR
description: W tym artykule opisano sposób lokalizowania identyfikatorów obiektów tożsamości wymaganych do skonfigurowania uwierzytelniania dla usługi Azure API for FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 5b42d61d59a3c816c3b664297470cfbf91f17439
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851770"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Znajdowanie identyfikatorów obiektów tożsamości na potrzeby konfiguracji uwierzytelniania

W tym artykule dowiesz się, jak znaleźć identyfikatory obiektów tożsamości, które są potrzebne podczas konfigurowania interfejsu API platformy Azure dla usługi FHIR w celu [używania zewnętrznej lub pomocniczej dzierżawy Active Directory](configure-local-rbac.md) na potrzeby płaszczyzny danych.

## <a name="find-user-object-id"></a>Znajdź identyfikator obiektu użytkownika

Jeśli masz użytkownika o nazwie użytkownika, możesz `myuser@consoso.com` zlokalizować użytkowników `ObjectId` przy użyciu następującego polecenia programu PowerShell:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

Możesz też użyć interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az ad user show --id myuser@consoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>Znajdź identyfikator obiektu jednostki usługi

Załóżmy, że zarejestrowano [aplikację klienta usługi](register-service-azure-ad-client-app.md) i chcesz zezwolić temu klientowi usługi na dostęp do interfejsu API platformy Azure dla FHIR, można znaleźć identyfikator obiektu dla jednostki usługi klienta przy użyciu następującego polecenia programu PowerShell:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

gdzie `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` jest identyfikator aplikacji klienta usługi. Alternatywnie możesz użyć `DisplayName` klienta usługi:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Jeśli używasz interfejsu wiersza polecenia platformy Azure, możesz użyć:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Znajdowanie identyfikatora obiektu grupy zabezpieczeń

Jeśli chcesz zlokalizować identyfikator obiektu grupy zabezpieczeń, możesz użyć następującego polecenia programu PowerShell:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Gdzie `mygroup` to nazwa grupy, która Cię interesuje.

Jeśli używasz interfejsu wiersza polecenia platformy Azure, możesz użyć:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak znaleźć identyfikatory obiektów tożsamości potrzebne do skonfigurowania interfejsu API platformy Azure dla usługi FHIR w celu korzystania z dzierżawy zewnętrznej lub pomocniczej Azure Active Directory. Następnym zapoznaj się z informacjami dotyczącymi sposobu używania identyfikatorów obiektów do konfigurowania lokalnych ustawień RBAC:
 
>[!div class="nextstepaction"]
>[Konfigurowanie ustawień lokalnej kontroli RBAC](configure-local-rbac.md)

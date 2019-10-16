---
title: Korzystanie z tożsamości zarządzanych dla zasobów w Azure Cloud Shell
description: Uwierzytelnianie kodu przy użyciu pliku MSI w Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: a5d49a16324a5a97f4a0507f9abf47ea602ea072
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328716"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure w Azure Cloud Shell

Azure Cloud Shell obsługuje autoryzację z tożsamościami zarządzanymi dla zasobów platformy Azure. Użyj tej metody, aby pobrać tokeny dostępu w celu bezpiecznego komunikowania się z usługami platformy Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Informacje o tożsamościach zarządzanych dla zasobów platformy Azure
Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób bezpiecznego zarządzania poświadczeniami, które muszą znajdować się w kodzie do uwierzytelniania w usługach w chmurze. W Cloud Shell może być konieczne uwierzytelnienie pobierania z Key Vault w celu uzyskania poświadczeń, które może potrzebować skrypt.

Zarządzane tożsamości dla zasobów platformy Azure ułatwiają rozwiązanie tego problemu, dzięki czemu usługi platformy Azure są automatycznie zarządzane tożsamości w Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności przechowywania poświadczeń w kodzie.

## <a name="acquire-access-token-in-cloud-shell"></a>Uzyskiwanie tokenu dostępu w Cloud Shell

Wykonaj następujące polecenia, aby ustawić token dostępu MSI jako zmienną środowiskową, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Obsługa wygaśnięcia tokenu

Lokalny podsystem MSI buforuje tokeny. W związku z tym możesz wywoływać ją tak często, jak chcesz, a połączenie w usłudze Azure AD jest wykonywane tylko wtedy, gdy:
- Chybienia w pamięci podręcznej występuje z powodu braku tokenu w pamięci podręcznej
- token wygasł

Jeśli w kodzie zostanie zbuforowany token, należy przygotować się do obsługi scenariuszy, w których zasób wskazuje, że token wygasł.

Aby obsłużyć błędy tokenu, odwiedź [stronę MSI dotyczącą przystawiania tokenów dostępu MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Uzyskiwanie tokenów dostępu z maszyn wirtualnych MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)

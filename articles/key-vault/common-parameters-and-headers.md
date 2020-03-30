---
title: Typowe parametry i nagłówki
description: Parametry i nagłówki wspólne dla wszystkich operacji, które można wykonać związane z zasobami usługi Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197593"
---
# <a name="common-parameters-and-headers"></a>Typowe parametry i nagłówki

Następujące informacje są wspólne dla wszystkich operacji, które można wykonać związane z zasobami usługi Key Vault:

- Zamień `{api-version}` na wersję api w identyfikatorze URI.
- Zamień `{subscription-id}` na identyfikator subskrypcji w identyfikatorze URI
- Zamień `{resource-group-name}` na grupę zasobów. Aby uzyskać więcej informacji, zobacz Zarządzanie zasobami platformy Azure przy użyciu grup zasobów.
- Zamień `{vault-name}` na nazwę magazynu kluczy w identyfikatorze URI.
- Ustaw nagłówek typ zawartości na application/json.
- Ustaw nagłówek autoryzacji na token sieci Web JSON, który można uzyskać z usługi Azure Active Directory (AAD). Aby uzyskać więcej informacji, zobacz Uwierzytelnianie żądań [usługi Azure Resource Manager.](authentication-requests-and-responses.md)

## <a name="common-error-response"></a>Częsta odpowiedź na błąd
Usługa użyje kodów stanu HTTP, aby wskazać sukces lub niepowodzenie. Ponadto błędy zawierają odpowiedź w następującym formacie:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nazwa elementu | Typ | Opis |
|---|---|---|
| kod | ciąg | Typ błędu, który wystąpił.|
| message | ciąg | Opis, co spowodowało błąd. |



## <a name="see-also"></a>Zobacz też
 [Odwołanie interfejsu API rest usługi Azure Key Vault](/rest/api/keyvault/)

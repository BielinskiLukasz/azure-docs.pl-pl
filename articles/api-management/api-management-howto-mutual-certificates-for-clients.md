---
title: Zabezpieczanie interfejsów API przy użyciu klienta uwierzytelniania certyfikatów w usłudze API Management — usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zabezpieczyć dostęp do interfejsów API przy użyciu certyfikatów klienta
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 3307ea391734828cb83c927e8df8aca79685279a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441540"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Zabezpieczanie interfejsów API przy użyciu klienta uwierzytelniania certyfikatów w usłudze API Management

Usługa API Management umożliwia bezpieczny dostęp do interfejsów API (czyli od klienta do usługi API Management) przy użyciu certyfikatów klienta. Obecnie można sprawdzić odcisk palca certyfikatu klienta na żądaną wartość. Możesz również sprawdzić odcisk palca dla istniejących certyfikatów przekazanych do usługi API Management.  

Aby uzyskać informacje o zabezpieczaniu dostępu do usługi zaplecza interfejsu API przy użyciu certyfikatów klientów (czyli API Management back-end), zobacz [sposób zabezpieczania usług zaplecza za pomocą klienta uwierzytelniania certyfikatów](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>Sprawdzanie daty wygaśnięcia

Aby sprawdzić, jeśli certyfikat wygasł można skonfigurować poniższych zasad:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Sprawdzanie wystawcy i tematu

Poniższe zasady można skonfigurować w celu sprawdzenia wystawcy i tematu certyfikatu klienta:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Sprawdzanie odcisk palca

Poniższe zasady można skonfigurować w celu Sprawdź odcisk palca certyfikatu klienta:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Sprawdzanie odcisku palca względem certyfikatów przekazanych do usługi API Management

Poniższy przykład pokazuje, jak Sprawdź odcisk palca certyfikatu klienta względem certyfikatów przekazanych do usługi API Management: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Następny krok

*  [Zabezpieczanie usług zaplecza za pomocą klienta uwierzytelniania certyfikatów](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Sposób przekazywania certyfikatów](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)


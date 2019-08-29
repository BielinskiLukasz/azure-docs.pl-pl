---
title: Przykład zasad usługi Azure API Management — używanie OAuth2 do autoryzacji między bramą a zapleczem | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak używać OAuth2 do autoryzacji między bramą a zapleczem. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fac10b728e4b7f09ec1019c3257f7c9e5d6e7714
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071856"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Korzystanie z OAuth2 do autoryzacji między bramą a zapleczem

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak używać OAuth2 do autoryzacji między bramą a zapleczem. Pokazuje sposób uzyskiwania tokenu dostępu z usługi AAD i przekazywania go do zaplecza. 

Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

Poniższy skrypt używa właściwości, które pojawiają się w {{Property}}. Aby dowiedzieć się więcej o właściwościach i sposobach ich używania w zasadach API Management, zobacz [ten](../api-management-howto-properties.md) temat.
 
## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)


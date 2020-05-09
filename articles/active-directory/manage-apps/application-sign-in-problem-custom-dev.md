---
title: Problemy z logowaniem do aplikacji opracowanej niestandardowo | Microsoft Docs
description: Typowe błędy, które mogą spowodować, że nie można zalogować się do aplikacji opracowanej przy użyciu usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01a424129abf88c18500c96bd1889fc6dcce2ec6
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890702"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemy z logowaniem do aplikacji opracowanej niestandardowo

Istnieje kilka błędów, które mogą spowodować, że nie można zalogować się do aplikacji. Największą przyczyną występowania tego problemu jest błędna konfiguracja aplikacji.

## <a name="errors-related-to--misconfigured-apps"></a>Błędy związane ze niepoprawnie skonfigurowanymi aplikacjami

* Sprawdź, czy konfiguracje w portalu pasują do zawartości w aplikacji. W tym celu Porównaj identyfikatory klienta/aplikacji, adresy URL odpowiedzi, klucze tajne klienta/klucza i identyfikator URI aplikacji.

* Porównaj zasób, do którego żądasz dostępu, w kodzie ze skonfigurowanymi uprawnieniami na karcie **wymagane zasoby** , aby upewnić się, że zażądano tylko skonfigurowanych zasobów.

* Zobacz [usługi Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) , aby poznać podobne błędy lub problemy.

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Wyrażanie zgody i Integrowanie aplikacji z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Usługa Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)

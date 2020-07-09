---
title: Serializacja niestandardowa pamięci podręcznej tokenów (MSAL4j)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak serializować pamięć podręczną tokenów dla MSAL for Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 31b908eacd5f9f8610ad2c0a985fa7e0be8d1505
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85479338"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serializacja niestandardowego buforu tokenów w MSAL dla języka Java

Aby zachować pamięć podręczną tokenów między wystąpieniami aplikacji, należy dostosować serializację. Klasy i interfejsy języka Java wykorzystywane w serializacji pamięci podręcznej tokenu są następujące:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): interfejs reprezentujący pamięć podręczną tokenów zabezpieczających.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): interfejs reprezentujący działanie wykonywania kodu przed i po Access. @Override *BeforeCacheAccess* i *afterCacheAccess* z logiką odpowiedzialną za Serializowanie i deserializacja pamięci podręcznej.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): interfejs reprezentujący kontekst, w którym uzyskuje się dostęp do pamięci podręcznej tokenów. 

Poniżej znajduje się algorytmie implementacja niestandardowa serializacji serializacji/deserializacji pamięci podręcznej tokenów. Nie należy kopiować i wklejać do środowiska produkcyjnego.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Więcej tutaj

Dowiedz się więcej na temat [pobierania i usuwania kont z pamięci podręcznej tokenów przy użyciu programu MSAL for Java](msal-java-get-remove-accounts-token-cache.md).

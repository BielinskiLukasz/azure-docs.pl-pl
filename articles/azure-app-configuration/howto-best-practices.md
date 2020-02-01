---
title: Najlepsze rozwiązania dotyczące konfiguracji aplikacji platformy Azure | Microsoft Docs
description: Dowiedz się, jak najlepiej używać konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 37f93099027f810e8089119536e089e07080d0bc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898637"
---
# <a name="azure-app-configuration-best-practices"></a>Najlepsze rozwiązania dotyczące konfiguracji aplikacji platformy Azure

W tym artykule omówiono typowe wzorce i najlepsze rozwiązania w przypadku korzystania z usługi Azure App Configuration.

## <a name="key-groupings"></a>Grupowanie kluczy

Konfiguracja aplikacji oferuje dwie opcje organizowania kluczy:

* Prefiksy kluczy
* Etykiety

Aby zgrupować klucze, można użyć jednej lub obu opcji.

*Prefiksy kluczy* są początkowymi częściami kluczy. Można logicznie grupować zestaw kluczy przy użyciu tego samego prefiksu w nazwach. Prefiksy mogą zawierać wiele składników połączonych przez ogranicznik, takie jak `/`, podobnie jak w przypadku ścieżki URL, do tworzenia przestrzeni nazw. Takie hierarchie są przydatne w przypadku przechowywania kluczy dla wielu aplikacji, usług składowych i środowisk w jednym magazynie konfiguracji aplikacji.

Ważne jest, aby pamiętać, że klucze są do których odwołuje się kod aplikacji w celu pobrania wartości odpowiednich ustawień. Klucze nie powinny się zmieniać lub w przeciwnym razie trzeba będzie zmodyfikować swój kod.

*Etykiety* są atrybutem kluczy. Są one używane do tworzenia wariantów klucza. Można na przykład przypisać etykiety do wielu wersji klucza. Wersja może być iteracją, środowiskiem lub innymi informacjami kontekstowymi. Aplikacja może zażądać całkowicie różnego zestawu wartości kluczowych, określając inną etykietę. W związku z tym wszystkie odwołania do kluczy pozostaną niezmienione w kodzie.

## <a name="key-value-compositions"></a>Kompozycje klucz-wartość

Konfiguracja aplikacji traktuje wszystkie klucze przechowywane z nią jako jednostki niezależne. W obszarze Konfiguracja aplikacji nie jest podejmowana próba wywnioskowania żadnej relacji między kluczami lub dziedziczenia wartości kluczy na podstawie ich hierarchii. Można jednak agregować wiele zestawów kluczy, używając etykiet sprzężonych z prawidłowym stosem konfiguracji w kodzie aplikacji.

Przyjrzyjmy się przykładowi. Załóżmy, że masz ustawienie o nazwie **Asset1**, którego wartość może się różnić w zależności od środowiska deweloperskiego. Tworzysz klucz o nazwie "Asset1" z pustą etykietą i etykietą o nazwie "Development". W pierwszej etykiecie zostanie umieszczona wartość domyślna dla **Asset1**i zostanie umieszczona określona wartość "Programowanie" w tym drugim.

W kodzie należy najpierw pobrać wartości klucza bez żadnych etykiet, a następnie pobrać ten sam zestaw wartości klucza po raz drugi z etykietą "Programowanie". Po pobraniu wartości po raz drugi poprzednie wartości kluczy są zastępowane. System konfiguracji .NET Core umożliwia "stos" wielu zestawów danych konfiguracji na siebie nawzajem. Jeśli klucz istnieje w więcej niż jednym zestawie, używany jest ostatni zestaw, który zawiera. Dzięki nowoczesnej strukturze programistycznej, takiej jak .NET Core, można bezpłatnie skorzystać z tej możliwości tworzenia stosu, jeśli używasz natywnego dostawcy konfiguracji do uzyskiwania dostępu do konfiguracji aplikacji. Poniższy fragment kodu przedstawia sposób implementacji stosu w aplikacji .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Konfiguracja aplikacji — Bootstrap

Aby uzyskać dostęp do magazynu konfiguracji aplikacji, można użyć jego parametrów połączenia, który jest dostępny w Azure Portal. Ponieważ parametry połączenia zawierają informacje o poświadczeniach, są one uznawane za wpisy tajne. Te klucze tajne muszą być przechowywane w Azure Key Vault, a kod musi być uwierzytelniany do Key Vault, aby je pobrać.

Lepszym rozwiązaniem jest użycie funkcji tożsamości zarządzane w programie Azure Active Directory. W przypadku tożsamości zarządzanych do magazynu konfiguracji aplikacji potrzebny jest tylko adres URL punktu końcowego konfiguracji aplikacji. Adres URL można osadzić w kodzie aplikacji (na przykład w pliku *appSettings. JSON* ). Szczegóły można znaleźć w temacie [integracja z tożsamościami zarządzanymi przez platformę Azure](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Dostęp do aplikacji lub funkcji do konfiguracji aplikacji

Możesz zapewnić dostęp do konfiguracji aplikacji dla aplikacji lub funkcji sieci Web przy użyciu dowolnej z następujących metod:

* Za pomocą Azure Portal wprowadź parametry połączenia do magazynu konfiguracji aplikacji w ustawieniach aplikacji App Service.
* Zapisz parametry połączenia z magazynem konfiguracji aplikacji w Key Vault i [odwołując się do niego z App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Użyj tożsamości zarządzanych przez platformę Azure, aby uzyskać dostęp do magazynu konfiguracji aplikacji. Aby uzyskać więcej informacji, zobacz [Integrowanie z tożsamościami zarządzanymi przez platformę Azure](howto-integrate-azure-managed-service-identity.md).
* Konfiguracja wypychana z konfiguracji aplikacji do App Service. Konfiguracja aplikacji udostępnia funkcję eksportu (w Azure Portal i interfejsu wiersza polecenia platformy Azure), która wysyła dane bezpośrednio do App Service. W przypadku tej metody nie trzeba zmieniać kodu aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Klucze i wartości](./concept-key-value.md)

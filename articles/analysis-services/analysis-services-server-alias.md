---
title: Nazwy serwerów aliasów Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak utworzyć aliasy nazw serwerów Azure Analysis Services. Następnie użytkownicy mogą połączyć się z serwerem przy użyciu krótszej nazwy aliasu zamiast nazwy serwera.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73572292"
---
# <a name="alias-server-names"></a>Nazwy serwerów aliasów

Używając aliasu nazwy serwera, użytkownicy mogą łączyć się z serwerem Azure Analysis Services przy użyciu krótszego *aliasu* zamiast nazwy serwera. Podczas nawiązywania połączenia z aplikacji klienckiej alias jest określany jako punkt końcowy przy użyciu formatu protokołu **link://** . Punkt końcowy zwraca rzeczywistą nazwę serwera w celu nawiązania połączenia.

Nazwy serwerów aliasów są dobre dla:

- Migrowanie modeli między serwerami bez wpływu na użytkowników. 
- Przyjazne nazwy serwerów są łatwiejsze dla użytkowników. 
- Kierowanie użytkowników do różnych serwerów w różnych porach dnia. 
- Kierowanie użytkowników w różnych regionach do wystąpień, które znajdują się geograficznie bliżej, tak jak w przypadku korzystania z usługi Azure Traffic Manager. 

Każdy punkt końcowy HTTPS, który zwraca poprawną nazwę serwera Azure Analysis Services może być używany jako alias. Punkt końcowy musi obsługiwać protokół HTTPS przez port 443, a port nie może być określony w identyfikatorze URI.

![Alias przy użyciu formatu linku](media/analysis-services-alias/aas-alias-browser.png)

Podczas nawiązywania połączenia z poziomu klienta nazwa serwera aliasu jest wprowadzana przy użyciu formatu protokołu **link://** . Na przykład w Power BI Desktop:

![Połączenie Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Tworzenie aliasu

Aby utworzyć alias punktu końcowego, można użyć dowolnej metody, która zwraca prawidłową nazwę serwera Azure Analysis Services. Na przykład odwołanie do pliku na platformie Azure Blob Storage zawierające rzeczywistą nazwę serwera lub tworzenie i publikowanie aplikacji ASP.NET Web Forms.

W tym przykładzie aplikacja formularzy sieci Web ASP.NET jest tworzona w programie Visual Studio. Odwołanie do strony głównej i kontrolka użytkownika są usuwane ze strony Default. aspx. Zawartość default. aspx jest po prostu następująca Dyrektywa Page:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Zdarzenie Page_Load w Default.aspx.cs używa metody Response. Write () do zwrócenia nazwy serwera Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Zobacz także

[Biblioteki klienckie](analysis-services-data-providers.md)   
[Łączenie z programu Power BI Desktop](analysis-services-connect-pbi.md)

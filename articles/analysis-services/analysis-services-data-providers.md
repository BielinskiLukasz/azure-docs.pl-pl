---
title: "Klient biblioteki wymagane do nawiązywania połączenia z usług Azure Analysis Services | Dokumentacja firmy Microsoft"
description: "W tym artykule opisano biblioteki klienta wymagane dla aplikacji klienckich i narzędzi można połączyć z usług Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/21/2018
ms.author: owend
ms.openlocfilehash: 2149330eb711fea76a144f5ec748ae6760c7746a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Biblioteki klienta do nawiązywania połączenia z usług Azure Analysis Services

Biblioteki klienta są niezbędne dla aplikacji klienckich i narzędzia do połączenia się z serwerami usług Analysis Services. 

## <a name="download-the-latest-client-libraries"></a>Pobierz najnowsze biblioteki klienta  

|Do pobrania  |Wersja  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.300.129.01      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.300.129.01      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.0.300.129.01      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    115.0.300.129.01      |

## <a name="amo-and-adomd-on-nuget"></a>AMO i ADOMD na NuGet

Usługi Analysis Services Management Objects (AMO) i ADOMD bibliotek klienta są dostępne jako możliwe do zainstalowania pakietów z [NuGet.org](https://www.nuget.org/). 

|Pakiet  |Wersja  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.0.2      |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.0.2      |

Zaleca się, że migrację do odwołań NuGet zamiast za pomocą Instalatora MSI. 

Zestawy pakietu NuGet AssemblyVersion wykonaj wersjonowania semantycznego: główne. POMOCNICZE. POPRAWKI. Odwołań NuGet załadować oczekiwanej wersji, nawet jeśli jest w innej wersji w pamięci podręcznej GAC (wynikające z instalacji pliku MSI). POPRAWKA jest zwiększany dla każdej wersji. Wersje AMO i ADOMD są przechowywane w synchronizacji.

## <a name="understanding-client-libraries"></a>Opis bibliotek klienta

Trzy bibliotek klienta, znanej także jako dostawcy danych korzystać z usług Analysis Services. ADOMD.NET i usług Analysis Services Management Objects (AMO) są zarządzanego klienta biblioteki. OLE DB dostawca usług Analysis Services (MSOLAP DLL) jest biblioteka klienta natywnego. Zazwyczaj wszystkie trzy są instalowane w tym samym czasie. **Najnowsze wersje wszystkich trzech bibliotek wymaga usług Azure Analysis Services**. 

Aplikacje klienta firmy Microsoft, takich jak Power BI Desktop i Excel zainstalowanie wszystkich trzech klientów biblioteki i zaktualizować je, gdy są dostępne nowe wersje. W zależności od wersji lub częstotliwości aktualizacji niektóre biblioteki klienta nie może być najnowsze wersje, które są wymagane przez usług Azure Analysis Services. To samo dotyczy aplikacji niestandardowych lub innych interfejsów, takich jak AsCmd, TOM czy ADOMD.NET. Te aplikacje wymagają ręcznie lub programowo instalowanie biblioteki. Z bibliotekami ręcznej instalacji klienta znajdują się w pakiety funkcji programu SQL Server jako dystrybucyjnego pakietów. Jednak te biblioteki klienta są powiązane z wersji programu SQL Server i nie może być r.  

Biblioteki klienta dla połączeń klienckich różnią się od dostawców danych wymagane do nawiązania połączenia z serwerem usług Azure Analysis Services źródła danych. Aby dowiedzieć się więcej na temat połączenia źródła danych, zobacz [połączenia źródła danych](analysis-services-datasource.md).

## <a name="client-library-types"></a>Klient biblioteki typów

### <a name="analysis-services-ole-db-provider-msolap"></a>Dostawca OLE DB usług Analysis (MSOLAP) 

 Analizy usługi OLE DB Provider (MSOLAP) jest biblioteka klienta natywnego dla połączenia z bazą danych usług Analysis Services. Jest on używany pośrednio przez ADOMD.NET i AMO delegowanie żądań połączenia do dostawcy danych. Dostawca OLE DB można również wywołać bezpośrednio z kodu aplikacji.  
  
 OLE DB dostawca usług Analysis Services jest instalowana automatycznie przez większość narzędzi i aplikacji klienckich, które umożliwiają dostęp do bazy danych usług Analysis Services. Musi być zainstalowany na komputerach, które umożliwiają dostęp do danych usług Analysis Services.  
  
 Dostawcy OLE DB często są określone w parametrach połączenia. Parametry połączenia usług Analysis Services używa innego nomenklaturę do odwoływania się do dostawcy OLE DB: MSOLAP. \<wersji > .dll.

### <a name="amo"></a>AMO  

 AMO jest biblioteką zarządzanego klienta używany do zarządzania serwerem i definicji danych. Zostanie on zainstalowany i używana przez narzędzia i aplikacje klienckie. Na przykład SQL Server Management Studio (SSMS) używa AMO do nawiązania połączenia usług Analysis Services.  
  
 Połączenie przy użyciu AMO jest zwykle minimalny, składające się z `“data source=\<servername>”`. Po nawiązaniu połączenia należy użyć interfejsu API do pracy z bazy danych, kolekcji i obiektów głównych. Zarówno program SSDT, jak i SSMS Użyj AMO do nawiązywania połączenia z wystąpieniem usług Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET jest biblioteka klienta zarządzanych danych używane do wykonywania zapytań danych usług Analysis Services. Zostanie on zainstalowany i używana przez narzędzia i aplikacje klienckie. 
  
 Podczas nawiązywania połączenia z bazą danych, właściwości parametrów połączenia dla wszystkich trzech bibliotek są podobne. Prawie wszystkie parametry połączenia należy zdefiniować dla ADOMD.NET przy użyciu [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) działa także dla AMO i analizy usługi OLE DB Provider (MSOLAP). Aby dowiedzieć się więcej, zobacz [właściwości parametrów połączenia &#40; Usługi Analysis Services &#41; ](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Sposób określania wersji biblioteki klienta   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Przejdź do pozycji `C:\Program Files\Microsoft Analysis Services\AS OLEDB\140` (Plik > Nowy > Inny). Jeśli masz więcej niż jednym folderze, wybierz większej liczby.
  
2.  Kliknij prawym przyciskiem myszy **msolap.dll** > **właściwości** > **szczegóły**. Jeśli plik dll, który ma nazwę msolap140.dll, jest starsza niż najnowszej wersji i powinny zostać uaktualnione.
    
    ![Szczegóły biblioteki klienta](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Przejdź do pozycji `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\v4.0_14.0.0.0__89845dcd8080cc91` (Plik > Nowy > Inny).
2. Kliknij prawym przyciskiem myszy **Microsoft.AnalysisServices** > **właściwości** > **szczegóły**.  

### <a name="adomd"></a>ADOMD

1. Przejdź do pozycji `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\v4.0_14.0.0.0__89845dcd8080cc91` (Plik > Nowy > Inny).
2. Kliknij prawym przyciskiem myszy **Microsoft.AnalysisServices.AdomdClient** > **właściwości** > **szczegóły**.  


## <a name="next-steps"></a>Kolejne kroki
[Połącz przy użyciu programu Excel](analysis-services-connect-excel.md)    
[Łączenie z usługą Power BI](analysis-services-connect-pbi.md)

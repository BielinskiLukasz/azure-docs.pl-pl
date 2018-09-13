---
title: Limity platformy Azure Analysis Services zasobu i obiektu | Dokumentacja firmy Microsoft
description: W tym artykule opisano limity zasobów i obiektu usług Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4c2cebe2225e475ccd40460e7b10a6ba3ed428d5
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724130"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limity zasobów i obiektu usługi analizy

W tym artykule opisano zasobu i model obiektu limitów.

## <a name="tier-limits"></a>Limity warstwy

### <a name="developer-tier"></a>Warstwa Deweloper

Ta warstwa jest zalecana w przypadku scenariuszy związanych z oceną, programowaniem i testowaniem. Pojedynczy plan uwzględnia wszystkie funkcje warstwy Standardowa, ale ma ograniczenia dotyczące mocy obliczeniowej, jednostek QPU i rozmiaru pamięci. Skalowanie repliki zapytania *jest niedostępne* w tej warstwie. Ta warstwa nie oferuje umowy SLA.

|Planowanie  |Jednostki QPU  |Pamięć (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Warstwa Podstawowa

Warstwa polecana w przypadku rozwiązań produkcyjnych z niewielkimi modelami tabelarycznymi, ograniczoną współbieżnością użytkowników i prostymi wymaganiami dotyczącymi odświeżania danych. Skalowanie repliki zapytania *jest niedostępne* w tej warstwie. Perspektywy, wiele partycji ani funkcje modelu tabelarycznego DirectQuery *nie są obsługiwane* w tej warstwie.  

|Planowanie  |Jednostki QPU  |Pamięć (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Warstwa standardowa

Ta warstwa jest najlepszym rozwiązaniem w przypadku aplikacji produkcyjnych o kluczowym znaczeniu, które wymagają elastycznej współbieżności użytkowników i korzystają z szybko rozrastających się modeli danych. Obsługuje ona zaawansowane odświeżanie danych dla aktualizacji modelu danych przeprowadzanych prawie w czasie rzeczywistym i obsługuje wszystkie tabelaryczne funkcje modelowania.

|Planowanie  |Jednostki QPU  |Pamięć (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Niedostępna we wszystkich regionach.  

## <a name="object-limits"></a>Limity obiektu

Są to teoretyczne limity. Wydajność będzie mniejsza o niższych numerach.

|Obiekt|Maksymalne rozmiary/liczby|  
|------------|----------------------------|  
|Bazy danych w wystąpieniu usługi|16,000|  
|Łączna liczba tabel i kolumn w bazie danych|16,000|  
|Wiersze w tabeli|Nieograniczona liczba<br /><br /> **Ostrzeżenie:** z ograniczeniem, że nie pojedynczą kolumnę w tabeli mogą mieć więcej niż 1,999,999,997 różne wartości.|  
|Hierarchie w tabeli|15,999|  
|Poziomy w hierarchii|15,999|  
|Relacje|8000|  
|Kluczowe kolumny w tabeli wszystkie|15,999|  
|Miary w tabelach|2 ^ 31-1 = 2 147 483 647|  
|Komórki zwróconych przez zapytanie|2 ^ 31-1 = 2 147 483 647|  
|Rozmiar rekordu zapytania źródłowego|64K|  
|Długość nazwy obiektów|512 znaków|  



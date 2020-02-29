---
title: Dynamiczne maskowanie danych
description: Dynamiczne maskowanie danych ogranicza narażenie na dane poufne przez zamaskowanie ich użytkownikom nieuprzywilejowanym dla SQL Database i platformy Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192918"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dynamiczne maskowanie danych dla Azure SQL Database i usługi Azure Synapse Analytics

SQL Database Dynamiczne maskowanie danych ogranicza narażenie na dane poufne przez zamaskowanie go dla użytkowników bez uprawnień. 

Dynamiczne maskowanie danych pomaga zapobiec nieautoryzowanemu dostępowi do danych poufnych, umożliwiając klientom wyznaczenie, jaka część danych poufnych może zostać odsłonięta, przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, przy czym dane w bazie danych pozostają bez zmian.

Na przykład przedstawiciel usługi w centrum telefonicznym może identyfikować wywoływania według kilku cyfr numerów kart kredytowych, ale te elementy danych nie powinny być w pełni ujawnione dla przedstawiciela usługi. Można zdefiniować regułę maskowania, która będzie maskować wszystkie oprócz ostatnich czterech cyfr dowolnego numeru karty kredytowej w zestawie wyników dowolnego zapytania. Innym przykładem jest możliwość zdefiniowania odpowiedniej maski danych w celu ochrony danych osobowych (dane osobowe), dzięki czemu deweloper może wysyłać zapytania dotyczące środowisk produkcyjnych do celów związanych z rozwiązywaniem problemów bez naruszania przepisów dotyczących zgodności.

## <a name="dynamic-data-masking-basics"></a>Dynamiczne maskowanie danych — podstawy

Aby skonfigurować zasady dynamicznego maskowania danych w Azure Portal, należy wybrać operację dynamicznego maskowania danych w bloku SQL Database lub ustawień konfiguracji. Nie można ustawić tej funkcji za pomocą portalu Azure Synapse (Użyj programu PowerShell lub interfejsu API REST)

### <a name="dynamic-data-masking-permissions"></a>Uprawnienia Dynamiczne maskowanie danych

Dynamiczne maskowanie danych można skonfigurować za pomocą ról Administrator Azure SQL Database, administrator serwera lub [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Zasady dynamicznego maskowania danych

* **Użytkownicy SQL wykluczeni z maskowania** — zestaw użytkowników SQL lub tożsamości usługi AAD, które uzyskują niemaskowane dane w wynikach zapytania SQL. Użytkownicy z uprawnieniami administratora są zawsze wykluczeni z maskowania i mogą zobaczyć oryginalne dane bez żadnej maski.
* **Reguły maskowania** — zestaw reguł definiujących wskazane pola, które mają być maskowane, oraz używaną funkcję maskowania. Wyznaczonymi polami można zdefiniować przy użyciu nazwy schematu bazy danych, nazwy tabeli i nazwy kolumny.
* **Funkcje maskowania** — zestaw metod kontrolujących narażenie danych w różnych scenariuszach.

| Funkcja maskowania | Maskowanie logiki |
| --- | --- |
| **Domyślne** |**Pełne maskowanie według typów danych określonych pól**<br/><br/>• Użyj XXXX lub mniejszej wartości XS, jeśli rozmiar pola jest krótszy niż 4 znaki dla typów danych ciągu (nchar, ntext, nvarchar).<br/>• Użyj wartości zerowej dla liczbowych typów danych (bigint, bit, decimal, int, Money, numeric, smallint, smallmoney, tinyint, float, Real).<br/>• Użyj 01-01-1900 dla typów danych Data/godzina (Date, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• Dla wariantu SQL, używana jest wartość domyślna bieżącego typu.<br/>• W przypadku kodu XML jest używany \<zamaskowany/>.<br/>• Użyj pustej wartości dla specjalnych typów danych (tabela znaczników czasu, hierarchyid, GUID, Binary, Image, typy przestrzenne varbinary). |
| **Karta kredytowa** |**Metoda maskowania, która ujawnia cztery ostatnie cyfry wydzielonych pól** i dodaje stały ciąg jako prefiks w postaci karty kredytowej.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Wiadomość e-mail** |**Metoda maskowania, która uwidacznia pierwszą literę i zastępuje domenę xxx.com** przy użyciu stałego prefiksu ciągu w postaci adresu e-mail.<br/><br/>aXX@XXXX.com |
| **Liczba losowa** |**Metoda maskowania, która generuje liczbę losową** zgodnie z wybranymi granicami i rzeczywistymi typami danych. Jeśli wyznaczono granice są równe, funkcja maskowania jest liczbą stałą.<br/><br/>![okienku nawigacji](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Tekst niestandardowy** |**Metoda maskowania, która uwidacznia pierwsze i ostatnie znaki** i dodaje niestandardowy ciąg uzupełniania w środku. Jeśli oryginalny ciąg jest krótszy niż uwidoczniony prefiks i sufiks, zostanie użyty tylko ciąg uzupełniający. <br/>prefix[padding]suffix<br/><br/>![okienku nawigacji](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Zalecane pola do maskowania

Aparat zaleceń DDM, który flaguje niektóre pola z bazy danych jako potencjalnie poufne pola, które mogą być dobrymi kandydatami do maskowania. W bloku Dynamiczne maskowanie danych w portalu zostaną wyświetlone zalecane kolumny dla bazy danych. Wystarczy kliknąć pozycję **Dodaj maskę** dla jednej lub większej liczby kolumn, a następnie **zapisać** , aby zastosować maskę dla tych pól.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Skonfiguruj Dynamiczne maskowanie danych dla bazy danych przy użyciu poleceń cmdlet programu PowerShell

Zobacz [polecenia cmdlet Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurowanie dynamicznego maskowania danych dla bazy danych przy użyciu interfejsu API REST

Zobacz [operacje dla Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).

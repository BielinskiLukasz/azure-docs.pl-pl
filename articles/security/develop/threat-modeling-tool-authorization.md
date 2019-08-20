---
title: Autoryzacja — Microsoft Threat Modeling Tool — Azure | Microsoft Docs
description: środki zaradcze dla zagrożeń ujawnionych w Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728383"
---
# <a name="security-frame-authorization--mitigations"></a>Ramka zabezpieczeń: Autoryzacja | Środki zaradcze 
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że skonfigurowano odpowiednie listy ACL, aby ograniczyć nieautoryzowany dostęp do danych na urządzeniu](#acl-restricted-access)</li><li>[Upewnij się, że zawartość aplikacji specyficznej dla użytkownika jest przechowywana w katalogu profilu użytkownika](#sensitive-directory)</li><li>[Upewnij się, że wdrożone aplikacje są uruchamiane z najniższymi uprawnieniami](#deployed-privileges)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Wymuszaj kolejność kolejnych etapów podczas przetwarzania przepływów logiki biznesowej](#sequential-logic)</li><li>[Zaimplementuj mechanizm ograniczania szybkości, aby zapobiec wyliczeniu](#rate-enumeration)</li><li>[Upewnij się, że jest stosowana właściwa autoryzacja, a zasada najniższych uprawnień jest wykonywana](#principle-least-privilege)</li><li>[Decyzja o logice biznesowej i autoryzacji dostępu do zasobów nie powinna być oparta na przychodzących parametrach żądania](#logic-request-parameters)</li><li>[Upewnij się, że zawartość i zasoby nie są wyliczalne lub niedostępne za pośrednictwem przeglądania wymuszania](#enumerable-browsing)</li></ul> |
| **Baza danych** | <ul><li>[Aby nawiązać połączenie z serwerem bazy danych, należy się upewnić, że są używane konta z najniższymi uprawnieniami](#privileged-server)</li><li>[Zaimplementuj zabezpieczenia na poziomie wiersza zabezpieczeń, aby uniemożliwić dzierżawcom uzyskiwanie dostępu do wszystkich danych](#rls-tenants)</li><li>[Rola sysadmin powinna mieć tylko prawidłowych wymaganych użytkowników](#sysadmin-users)</li></ul> |
| **Brama usługi IoT Cloud** | <ul><li>[Nawiązywanie połączenia z bramą w chmurze przy użyciu tokenów z najniższymi uprawnieniami](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Użyj klucza sygnatury dostępu współdzielonego tylko do wysyłania do generowania tokenów urządzeń](#sendonly-sas)</li><li>[Nie używaj tokenów dostępu zapewniających bezpośredni dostęp do centrum zdarzeń](#access-tokens-hub)</li><li>[Łączenie z centrum zdarzeń przy użyciu kluczy SAS, które mają minimalne wymagane uprawnienia](#sas-minimum-permissions)</li></ul> |
| **Baza danych dokumentów platformy Azure** | <ul><li>[Użyj tokenów zasobów do łączenia się z Azure Cosmos DB, jeśli to możliwe](#resource-docdb)</li></ul> |
| **Granica zaufania platformy Azure** | <ul><li>[Włącz szczegółowe zarządzanie dostępem do subskrypcji platformy Azure przy użyciu RBAC](#grained-rbac)</li></ul> |
| **Service Fabric granic zaufania** | <ul><li>[Ograniczanie dostępu klienta do operacji klastra przy użyciu RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Przeprowadzenie modelowania zabezpieczeń i użycie zabezpieczeń na poziomie pola, gdy jest to wymagane](#modeling-field)</li></ul> |
| **Portal programu Dynamics CRM** | <ul><li>[Należy przeprowadzić modelowanie zabezpieczeń kont portalu, pamiętając, że model zabezpieczeń portalu różni się od reszty programu CRM](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Przyznawanie szczegółowych uprawnień dla zakresu jednostek w usłudze Azure Table Storage](#permission-entities)</li><li>[Włączanie Access Control opartego na rolach (RBAC) do konta usługi Azure Storage przy użyciu Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Klient mobilny** | <ul><li>[Implementowanie niejawnego wykrywania Zdjęcia zabezpieczeń systemu lub korzenia](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Słabe odwołanie do klasy w programie WCF](#weak-class-wcf)</li><li>[WCF — implementowanie kontroli autoryzacji](#wcf-authz)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Implementowanie właściwego mechanizmu autoryzacji w interfejsie API sieci Web ASP.NET](#authz-aspnet)</li></ul> |
| **Urządzenie IoT** | <ul><li>[Przeprowadzanie kontroli autoryzacji na urządzeniu, jeśli obsługuje różne akcje, które wymagają różnych poziomów uprawnień](#device-permission)</li></ul> |
| **Brama pola IoT** | <ul><li>[Przeprowadzaj sprawdzanie autoryzacji w bramie pola, jeśli obsługuje ona różne akcje, które wymagają różnych poziomów uprawnień](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Upewnij się, że skonfigurowano odpowiednie listy ACL, aby ograniczyć nieautoryzowany dostęp do danych na urządzeniu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Upewnij się, że skonfigurowano odpowiednie listy ACL, aby ograniczyć nieautoryzowany dostęp do danych na urządzeniu|

## <a id="sensitive-directory"></a>Upewnij się, że zawartość aplikacji specyficznej dla użytkownika jest przechowywana w katalogu profilu użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Upewnij się, że zawartość aplikacji specyficznej dla użytkownika jest przechowywana w katalogu profilu użytkownika. Jest to konieczne, aby uniemożliwić wielu użytkownikom komputera dostęp do innych danych.|

## <a id="deployed-privileges"></a>Upewnij się, że wdrożone aplikacje są uruchamiane z najniższymi uprawnieniami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Upewnij się, że wdrożona aplikacja jest uruchamiana z najniższymi uprawnieniami. |

## <a id="sequential-logic"></a>Wymuszaj kolejność kolejnych etapów podczas przetwarzania przepływów logiki biznesowej

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Aby sprawdzić, czy ten etap został uruchomiony przez oryginalny użytkownik, dla którego chcesz wymusić, aby aplikacja mogła przetwarzać przepływy logiki biznesowej w kolejności sekwencyjnej, ze wszystkimi krokami przetwarzanymi w realistycznym czasie ludzkim, a nie przetwarzać poza kolejnością, pominięte kroki , przetworzone kroki od innego użytkownika lub zbyt szybko przesłane transakcje.|

## <a id="rate-enumeration"></a>Zaimplementuj mechanizm ograniczania szybkości, aby zapobiec wyliczeniu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Upewnij się, że poufne identyfikatory są losowe. Zaimplementuj kontrolę CAPTCHA na anonimowych stronach. Upewnij się, że błąd i wyjątek nie powinny ujawniać określonych danych|

## <a id="principle-least-privilege"></a>Upewnij się, że jest stosowana właściwa autoryzacja, a zasada najniższych uprawnień jest wykonywana

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Zasada oznacza nadanie kontu użytkownika tylko tych uprawnień, które są niezbędne do pracy. Na przykład użytkownik kopii zapasowej nie musi instalować oprogramowania: w związku z tym użytkownik kopii zapasowej ma uprawnienia tylko do uruchamiania aplikacji do tworzenia kopii zapasowych i związanych z kopiami zapasowymi. Wszystkie inne uprawnienia, takie jak instalowanie nowego oprogramowania, są blokowane. Zasada ma zastosowanie również do użytkownika komputera osobistego, który zwykle działa w normalnym koncie użytkownika, i otwiera uprzywilejowane konto chronione hasłem (czyli administratora) tylko wtedy, gdy sytuacja absolutnie wymaga. </p><p>Ta zasada może być również stosowana do aplikacji sieci Web. Zamiast korzystać wyłącznie z metod uwierzytelniania opartych na rolach przy użyciu sesji, chcemy przypisywać uprawnienia użytkownikom za pomocą systemu uwierzytelniania opartego na bazie danych. Nadal korzystamy z sesji, aby określić, czy użytkownik był zalogowany prawidłowo, ale tylko zamiast przypisywać tego użytkownika do określonej roli, przypisują mu uprawnienia, aby sprawdzić, które akcje są uprzywilejowane w systemie. Ponadto w przypadku dużej liczby operacji, gdy użytkownik musi mieć przypisaną mniejszą liczbę uprawnień, zmiany zostaną zastosowane na bieżąco, ponieważ przypisanie nie zależy od sesji, która w przeciwnym razie musiała wygasnąć w pierwszej kolejności.</p>|

## <a id="logic-request-parameters"></a>Decyzja o logice biznesowej i autoryzacji dostępu do zasobów nie powinna być oparta na przychodzących parametrach żądania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Po sprawdzeniu, czy użytkownik jest ograniczony do przeglądania określonych danych, należy przetworzyć ograniczenia dostępu po stronie serwera. Identyfikator użytkownika powinien być przechowywany w zmiennej sesji podczas logowania i powinien być używany do pobierania danych użytkownika z bazy danych |

### <a name="example"></a>Przykład
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Teraz możliwa osoba atakująca nie może modyfikować i zmieniać operacji aplikacji, ponieważ identyfikator pobierania danych jest obsługiwany po stronie serwera.

## <a id="enumerable-browsing"></a>Upewnij się, że zawartość i zasoby nie są wyliczalne lub niedostępne za pośrednictwem przeglądania wymuszania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Poufne pliki statyczne i konfiguracyjne nie powinny być przechowywane w katalogu głównym sieci Web. W przypadku zawartości, która nie musi być publiczna, należy zastosować odpowiednie metody kontroli dostępu lub usunąć samą zawartość.</p><p>Ponadto wymuszenie przeglądania jest zwykle połączone z technikami z oddziałami, aby zebrać informacje, próbując uzyskać dostęp do możliwie największej liczby adresów URL w celu wyliczenia katalogów i plików na serwerze. Osoby atakujące mogą sprawdzić wszystkie odmiany często istniejących plików. Na przykład wyszukiwanie plików z hasłem będzie obejmować pliki, w tym psswd. txt, Password. htm, Password. dat i inne różnice.</p><p>Aby wyeliminować ten problem, należy uwzględnić możliwości wykrywania prób wymuszenia.</p>|

## <a id="privileged-server"></a>Aby nawiązać połączenie z serwerem bazy danych, należy się upewnić, że są używane konta z najniższymi uprawnieniami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Hierarchia uprawnień SQL Database](https://msdn.microsoft.com/library/ms191465), [Zabezpieczanie bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Do nawiązania połączenia z bazą danych należy używać kont z najniższymi uprawnieniami. Logowanie do aplikacji powinno być ograniczone w bazie danych i należy wykonać tylko wybrane procedury składowane. Logowanie aplikacji nie powinno mieć bezpośredniego dostępu do tabeli. |

## <a id="rls-tenants"></a>Zaimplementuj zabezpieczenia na poziomie wiersza zabezpieczeń, aby uniemożliwić dzierżawcom uzyskiwanie dostępu do wszystkich danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Sql Azure, OnPrem |
| **Atrybuty**              | SQL Version-V12, SQL Version-MsSQL2016 |
| **Wołują**              | [Zabezpieczenia na poziomie wiersza SQL Server](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Kroki** | <p>Zabezpieczenia na poziomie wiersza umożliwiają klientom kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (np. członkostwa w grupie lub kontekstu wykonania).</p><p>Zabezpieczenia na poziomie wiersza upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Możliwe jest na przykład zapewnienie, że pracownicy mają dostęp tylko do tych wierszy danych, które są odpowiednie do ich działu, lub ograniczenie dostępu do danych klienta tylko do danych odpowiednich dla firmy.</p><p>Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie na danych w innej warstwie aplikacji. System bazy danych stosuje ograniczenia dostępu za każdym razem, gdy próbuje się uzyskać dostęp do danych z dowolnej warstwy. Dzięki temu system zabezpieczeń jest bardziej niezawodny i niezawodny poprzez zmniejszenie powierzchni systemu zabezpieczeń.</p><p>|

Należy pamiętać, że na poziomie wiersza jako gotowy do użycia funkcja bazy danych ma zastosowanie tylko do SQL Server uruchamiania 2016 i usługi Azure SQL Database. Jeśli Wbudowana funkcja nie jest zaimplementowana, należy upewnić się, że dostęp do danych jest ograniczony przy użyciu widoków i procedur

## <a id="sysadmin-users"></a>Rola sysadmin powinna mieć tylko prawidłowych wymaganych użytkowników

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Hierarchia uprawnień SQL Database](https://msdn.microsoft.com/library/ms191465), [Zabezpieczanie bazy danych SQL](https://msdn.microsoft.com/library/ms190401) |
| **Kroki** | Członkowie stałej roli serwera sysadmin muszą mieć bardzo ograniczoną i nigdy nie zawierać kont używanych przez aplikacje.  Przejrzyj listę użytkowników w roli i Usuń wszelkie niepotrzebne konta|

## <a id="cloud-least-privileged"></a>Nawiązywanie połączenia z bramą w chmurze przy użyciu tokenów z najniższymi uprawnieniami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama usługi IoT Cloud | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — IoT Hub platformy Azure |
| **Wołują**              | [Access Control IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Kroki** | Zapewnianie najniższych uprawnień do różnych składników, które łączą się z bramą w chmurze (IoT Hub). Typowy przykład to: funkcja zarządzania urządzeniami/inicjowania obsługi używa registryread/Write, procesor zdarzeń (ASA) używa połączenia usługi. Poszczególne urządzenia łączą się przy użyciu poświadczeń urządzenia|

## <a id="sendonly-sas"></a>Użyj klucza sygnatury dostępu współdzielonego tylko do wysyłania do generowania tokenów urządzeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Event Hub | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Omówienie modelu uwierzytelniania i zabezpieczeń Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Klucz sygnatury dostępu współdzielonego służy do generowania poszczególnych tokenów urządzeń. Użyj klucza sygnatury dostępu współdzielonego tylko do wysyłania podczas generowania tokenu urządzenia dla danego wydawcy|

## <a id="access-tokens-hub"></a>Nie używaj tokenów dostępu zapewniających bezpośredni dostęp do centrum zdarzeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Event Hub | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Omówienie modelu uwierzytelniania i zabezpieczeń Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Token, który przyznaje bezpośredni dostęp do centrum zdarzeń, nie powinien być określony dla urządzenia. Użycie najmniejszego uprzywilejowanego tokenu dla urządzenia zapewniającego dostęp tylko do wydawcy może ułatwić identyfikację i odnalezienie go, jeśli jest to urządzenie nieautoryzowane lub zagrożone.|

## <a id="sas-minimum-permissions"></a>Łączenie z centrum zdarzeń przy użyciu kluczy SAS, które mają minimalne wymagane uprawnienia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Event Hub | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Omówienie modelu uwierzytelniania i zabezpieczeń Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Zapewnianie najniższych uprawnień do różnych aplikacji zaplecza, które łączą się z centrum zdarzeń. Generuj oddzielne klucze SAS dla każdej aplikacji zaplecza i podaj tylko wymagane uprawnienia — Wyślij, Odbierz lub Zarządzaj do nich.|

## <a id="resource-docdb"></a>Użyj tokenów zasobów do łączenia się z Cosmos DB, jeśli to możliwe

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Token zasobu jest skojarzony z zasobem uprawnień Azure Cosmos DB i przechwytuje relację między użytkownikiem bazy danych a uprawnieniem, które użytkownik ma dla określonego zasobu Azure Cosmos DB aplikacji (np. kolekcji, dokumentu). Zawsze używaj tokenu zasobów, aby uzyskać dostęp do Azure Cosmos DB, jeśli klient nie może być zaufany z obsługą kluczy Master lub tylko do odczytu, takich jak aplikacja użytkownika końcowego, taka jak klient mobilny lub komputer stacjonarny. Użyj klucza głównego lub kluczy tylko do odczytu z aplikacji zaplecza, które mogą bezpiecznie przechowywać te klucze.|

## <a id="grained-rbac"></a>Włącz szczegółowe zarządzanie dostępem do subskrypcji platformy Azure przy użyciu RBAC

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Kroki** | Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań.|

## <a id="cluster-rbac"></a>Ograniczanie dostępu klienta do operacji klastra przy użyciu RBAC

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Service Fabric granic zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Wołują**              | [Kontrola dostępu oparta na rolach dla klientów Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Kroki** | <p>Usługa Azure Service Fabric obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są połączeni z klastrem Service Fabric: administrator i użytkownik. Kontrola dostępu pozwala administratorowi klastra ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników, co sprawia, że klaster jest bezpieczniejszy.</p><p>Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu i zapisu). Użytkownicy domyślnie mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytania) i możliwość rozpoznawania aplikacji i usług.</p><p>Podczas tworzenia klastra należy określić dwie role klienta (administrator i klient), dostarczając oddzielne certyfikaty dla każdego z nich.</p>|

## <a id="modeling-field"></a>Przeprowadzenie modelowania zabezpieczeń i użycie zabezpieczeń na poziomie pola, gdy jest to wymagane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Przeprowadzenie modelowania zabezpieczeń i użycie zabezpieczeń na poziomie pola, gdy jest to wymagane|

## <a id="portal-security"></a>Należy przeprowadzić modelowanie zabezpieczeń kont portalu, pamiętając, że model zabezpieczeń portalu różni się od reszty programu CRM

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Portal programu Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Należy przeprowadzić modelowanie zabezpieczeń kont portalu, pamiętając, że model zabezpieczeń portalu różni się od reszty programu CRM|

## <a id="permission-entities"></a>Przyznawanie szczegółowych uprawnień dla zakresu jednostek w usłudze Azure Table Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | StorageType — tabela |
| **Wołują**              | [Jak delegować dostęp do obiektów na koncie usługi Azure Storage przy użyciu sygnatury dostępu współdzielonego](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Kroki** | W niektórych scenariuszach firmy usługa Azure Table Storage może być wymagana do przechowywania danych poufnych, które są przeznaczone dla różnych stron. Na przykład poufne dane odnoszące się do różnych krajów/regionów. W takich przypadkach sygnatury dostępu współdzielonego mogą być tworzone przez określenie zakresów partycji i klucza wiersza, aby użytkownik mógł uzyskać dostęp do danych specyficznych dla określonego kraju/regionu.| 

## <a id="rbac-azure-manager"></a>Włączanie Access Control opartego na rolach (RBAC) do konta usługi Azure Storage przy użyciu Azure Resource Manager

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Jak zabezpieczyć konto magazynu za pomocą Access Control opartego na rolach (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Kroki** | <p>Podczas tworzenia nowego konta magazynu należy wybrać model wdrożenia klasyczny lub Azure Resource Manager. Klasyczny model tworzenia zasobów na platformie Azure zezwala tylko na dostęp do subskrypcji i z kolei na koncie magazynu.</p><p>W modelu Azure Resource Manager należy umieścić konto magazynu w grupie zasobów i kontrolować dostęp do płaszczyzny zarządzania tego konkretnego konta magazynu przy użyciu Azure Active Directory. Na przykład możesz nadać określonym użytkownikom możliwość uzyskiwania dostępu do kluczy konta magazynu, a inni użytkownicy mogą wyświetlać informacje o koncie magazynu, ale nie mogą uzyskać dostępu do kluczy konta magazynu.</p>|

## <a id="rooting-detection"></a>Implementowanie niejawnego wykrywania Zdjęcia zabezpieczeń systemu lub korzenia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Aplikacja powinna chronić własną konfigurację i dane użytkowników w przypadku, gdy telefon jest w katalogu głównym lub złamanymi. Przerwanie operacji root/złamanymi oznacza nieautoryzowany dostęp, który normalnych użytkowników nie będzie robić na własnych telefonach. W związku z tym aplikacja powinna mieć niejawną logikę wykrywania podczas uruchamiania aplikacji, aby wykryć, czy telefon został odblokowany.</p><p>Logika wykrywania może po prostu uzyskać dostęp do plików, które zwykle tylko użytkownik root może uzyskać dostęp, na przykład:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Jeśli aplikacja może uzyskać dostęp do dowolnego z tych plików, oznacza to, że aplikacja działa jako użytkownik główny.</p>|

## <a id="weak-class-wcf"></a>Słabe odwołanie do klasy w programie WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Wołują**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroki** | <p>System używa słabego odwołania do klasy, co może pozwolić osobie atakującej na wykonanie nieautoryzowanego kodu. Program odwołuje się do klasy zdefiniowanej przez użytkownika, która nie została jednoznacznie zidentyfikowana. Gdy platforma .NET załaduje tę słabo określoną klasę, moduł ładujący typu CLR wyszukuje klasę w następujących lokalizacjach w określonej kolejności:</p><ol><li>Jeśli zestaw typu jest znany, moduł ładujący przeszukuje lokalizacje przekierowań pliku konfiguracji, GAC, bieżący zestaw przy użyciu informacji o konfiguracji i katalogu podstawowego aplikacji</li><li>Jeśli zestaw jest nieznany, moduł ładujący przeszukuje bieżący zestaw, mscorlib i lokalizację zwróconą przez program obsługi zdarzeń TypeResolve</li><li>Tę kolejność wyszukiwania CLR można modyfikować za pomocą punktów zaczepienia, takich jak mechanizm przekazywania typów i zdarzenie AppDomain. TypeResolve</li></ol><p>Jeśli osoba atakująca korzysta z kolejności wyszukiwania CLR przez utworzenie alternatywnej klasy o tej samej nazwie i umieszczenie jej w lokalizacji alternatywnej, która zostanie załadowana jako pierwsza, środowisko CLR przestanie wykonywać kod dostarczony przez osobę atakującą.</p>|

### <a name="example"></a>Przykład
W `<behaviorExtensions/>` poniższym elemencie pliku konfiguracji WCF nakazuje programowi WCF dodanie niestandardowej klasy zachowań do określonego rozszerzenia WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Używanie w pełni kwalifikowanych (silnie) nazw jednoznacznie identyfikuje typ i zwiększa bezpieczeństwo systemu. Użyj w pełni kwalifikowanych nazw zestawów podczas rejestrowania typów w plikach Machine. config i App. config.

### <a name="example"></a>Przykład
Poniższy `<behaviorExtensions/>` element pliku konfiguracji WCF instruuje program WCF, aby dodał niestandardową klasę zachowań, do której istnieje odwołanie do określonego rozszerzenia WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>WCF — implementowanie kontroli autoryzacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, NET Framework 3 |
| **Atrybuty**              | ND  |
| **Wołują**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Kroki** | <p>Ta usługa nie używa kontroli autoryzacji. Gdy klient wywołuje konkretną usługę WCF, program WCF udostępnia różne systemy autoryzacji, które sprawdzają, czy obiekt wywołujący ma uprawnienia do wykonywania metody usługi na serwerze. Jeśli nie włączono kontroli autoryzacji dla usług WCF, uwierzytelniony użytkownik może uzyskać eskalację uprawnień.</p>|

### <a name="example"></a>Przykład
Poniższa konfiguracja instruuje program WCF, aby nie sprawdzać poziomu autoryzacji klienta podczas wykonywania usługi:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Użyj schematu autoryzacji usługi, aby sprawdzić, czy obiekt wywołujący metodę usługi jest autoryzowany do wykonania. Funkcja WCF oferuje dwa tryby i umożliwia Definiowanie niestandardowych schematów autoryzacji. W trybie UseWindowsGroups są stosowane role i użytkownicy systemu Windows, a tryb UseAspNetRoles używa dostawcy roli ASP.NET, takiego jak SQL Server, do uwierzytelniania.

### <a name="example"></a>Przykład
Poniższa konfiguracja instruuje program WCF, aby upewnić się, że klient należy do grupy administratorów przed wykonaniem dodawania usługi:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Następnie usługa jest zadeklarowana jako następująca:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementowanie właściwego mechanizmu autoryzacji w interfejsie API sieci Web ASP.NET

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Generic, MVC5 |
| **Atrybuty**              | N/A, dostawca tożsamości — ADFS, dostawca tożsamości — Azure AD |
| **Wołują**              | [Uwierzytelnianie i autoryzacja w interfejsie API sieci Web ASP.NET](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Kroki** | <p>Informacje o roli dla użytkowników aplikacji mogą pochodzić z oświadczeń usługi Azure AD lub usług AD FS, jeśli aplikacja opiera się na nich jako dostawca tożsamości lub sama aplikacja. W każdym z tych przypadków niestandardowa implementacja autoryzacji powinna sprawdzać poprawność informacji o roli użytkownika.</p><p>Informacje o roli dla użytkowników aplikacji mogą pochodzić z oświadczeń usługi Azure AD lub usług AD FS, jeśli aplikacja opiera się na nich jako dostawca tożsamości lub sama aplikacja. W każdym z tych przypadków niestandardowa implementacja autoryzacji powinna sprawdzać poprawność informacji o roli użytkownika.</p>

### <a name="example"></a>Przykład
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Wszystkie kontrolery i metody akcji, które muszą być chronione, powinny mieć wyższy atrybut.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Przeprowadzanie kontroli autoryzacji na urządzeniu, jeśli obsługuje różne akcje, które wymagają różnych poziomów uprawnień

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Urządzenie powinno autoryzować obiekt wywołujący, aby sprawdzić, czy obiekt wywołujący ma wymagane uprawnienia, aby wykonać żądaną akcję. Na przykład pozwala na to, że urządzenie jest inteligentną blokadą drzwi, którą można monitorować z chmury, a także zapewnia funkcje, takie jak zdalne blokowanie drzwi.</p><p>Inteligentna blokada drzwi zapewnia funkcję odblokowywania tylko wtedy, gdy ktoś fizycznie znajdzie się blisko drzwi z kartą. W takim przypadku implementacja zdalnego polecenia i kontroli należy wykonać w taki sposób, aby nie zapewniało żadnych funkcji do odblokowania drzwi, ponieważ Brama chmury nie jest autoryzowana do wysłania polecenia w celu odblokowania drzwi.</p>|

## <a id="field-permission"></a>Przeprowadzaj sprawdzanie autoryzacji w bramie pola, jeśli obsługuje ona różne akcje, które wymagają różnych poziomów uprawnień

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Brama pola powinna autoryzować obiekt wywołujący, aby sprawdzić, czy obiekt wywołujący ma wymagane uprawnienia, aby wykonać żądaną akcję. Na przykład powinny istnieć różne uprawnienia do interfejsu użytkownika/interfejsu API administratora służącego do konfigurowania urządzeń z bramą pola.|

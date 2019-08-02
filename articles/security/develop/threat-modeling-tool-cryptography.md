---
title: Kryptografia-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
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
ms.openlocfilehash: c9116472af5b400ded0fea24f98b07bad9d9039b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728187"
---
# <a name="security-frame-cryptography--mitigations"></a>Ramka zabezpieczeń: Kryptografia | Środki zaradcze 

| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Używaj tylko zatwierdzonych szyfrów bloków symetrycznych i długości kluczy](#cipher-length)</li><li>[Użyj zatwierdzonych trybów szyfrowania bloku i wektorów inicjalizacji dla szyfrów symetrycznych](#vector-ciphers)</li><li>[Użyj zatwierdzonych algorytmów asymetrycznych, długości kluczy i wypełnienia](#padding)</li><li>[Użyj zatwierdzonych generatorów liczb losowych](#numgen)</li><li>[Nie używaj szyfrów strumienia symetrycznego](#stream-ciphers)</li><li>[Użyj zatwierdzonych algorytmów wyznaczania wartości skrótu dla komputerów MAC/HMAC](#mac-hash)</li><li>[Używaj tylko zatwierdzonych funkcji skrótu kryptograficznego](#hash-functions)</li></ul> |
| **Baza danych** | <ul><li>[Szyfrowanie danych w bazie danych przy użyciu algorytmów silnego szyfrowania](#strong-db)</li><li>[Pakiety SSIS powinny być szyfrowane i podpisane cyfrowo](#ssis-signed)</li><li>[Dodawanie podpisu cyfrowego do krytycznych zabezpieczanych baz danych](#securables-db)</li><li>[Używanie programu SQL Server EKM do ochrony kluczy szyfrowania](#ekm-keys)</li><li>[Użyj funkcji AlwaysEncrypted, jeśli klucze szyfrowania nie powinny być ujawnione w aparacie bazy danych](#keys-engine)</li></ul> |
| **Urządzenie IoT** | <ul><li>[Bezpieczne przechowywanie kluczy kryptograficznych na urządzeniu IoT](#keys-iot)</li></ul> | 
| **Brama usługi IoT Cloud** | <ul><li>[Wygeneruj losowy klucz symetryczny o wystarczającej długości, aby można było uwierzytelnić IoT Hub](#random-hub)</li></ul> | 
| **Klient mobilny programu Dynamics CRM** | <ul><li>[Upewnij się, że zasady zarządzania urządzeniami wymagają użycia numeru PIN i umożliwiają zdalne czyszczenie](#pin-remote)</li></ul> | 
| **Klient programu Dynamics CRM Outlook** | <ul><li>[Upewnij się, że zasady zarządzania urządzeniami wymagają użycia numeru PIN/hasła/AutoLock i szyfrują wszystkie dane (np. Funkcja BitLocker)](#bitlocker)</li></ul> | 
| **Serwer tożsamości** | <ul><li>[Upewnij się, że klucze podpisywania są rzutowane podczas korzystania z serwera tożsamości](#rolled-server)</li><li>[Upewnij się, że na serwerze tożsamości są używane kryptograficzne silne identyfikatory klienta i klucz tajny klienta.](#client-server)</li></ul> | 

## <a id="cipher-length"></a>Używaj tylko zatwierdzonych szyfrów bloków symetrycznych i długości kluczy

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Produkty muszą używać tylko tych szyfrów bloków symetrycznych i skojarzonych długości kluczy, które zostały jawnie zatwierdzone przez klasyfikator kryptograficzny w organizacji. Zatwierdzone Algorytmy symetryczne w firmie Microsoft obejmują następujące Szyfry blokowe:</p><ul><li>W przypadku nowych kodów AES-128, AES-192 i AES-256</li><li>W celu zapewnienia zgodności z poprzednimi wersjami z istniejącym kodem, akceptowalny jest algorytm 3DES.</li><li>W przypadku produktów korzystających z szyfrów bloków symetrycznych:<ul><li>Do nowego kodu jest wymagany Advanced Encryption Standard (AES)</li><li>Trzy kluczowe potrójne szyfrowanie danych (3DES) jest dozwolone w istniejącym kodzie w celu zapewnienia zgodności z poprzednimi wersjami</li><li>Wszystkie inne Szyfry blokowe, w tym RC2, DES, 2 Key 3DES, DESX i Skipjack, mogą być używane tylko do odszyfrowywania starych danych i muszą zostać zastąpione, jeśli są używane do szyfrowania</li></ul></li><li>Dla algorytmów szyfrowania bloku symetrycznego wymagana jest minimalna długość klucza wynosząca 128 bitów. Jedyny algorytm szyfrowania blokowego zalecany dla nowego kodu to AES (AES-128, AES-192 i AES-256 — wszystkie akceptowane)</li><li>Trzy kluczowe algorytm 3DES jest obecnie akceptowalny, jeśli jest już używany w istniejącym kodzie; zalecane jest przejście do algorytmu AES. DES, DESX, RC2 i Skipjack nie są już uznawane za bezpieczne. Algorytmy te mogą być używane tylko do odszyfrowywania istniejących danych w celu zapewnienia zgodności z poprzednimi wersjami, a dane powinny być ponownie szyfrowane przy użyciu zalecanego szyfru blokowego</li></ul><p>Należy pamiętać, że wszystkie szyfry bloku symetrycznego muszą być używane z zatwierdzonym trybem szyfru, który wymaga użycia odpowiedniego wektora inicjującego (IV). Odpowiednia IV jest zazwyczaj liczbą losową i nigdy nie jest wartością stałą</p><p>Użycie starszych lub niezatwierdzonych algorytmów kryptograficznych i mniejszych długości kluczy do odczytu istniejących danych (w przeciwieństwie do zapisu nowych danych) może być dozwolone po przeprowadzeniu przeglądu tablicy kryptograficznej w organizacji. Należy jednak pamiętać o wyjątku względem tego wymagania. Ponadto w przypadku wdrożeń w przedsiębiorstwie produkty powinny uwzględniać administratorów ostrzeżeń, gdy do odczytu danych jest używany słaby Kryptografia. Takie ostrzeżenia powinny mieć na celu wyjaśnienie i podjęcie odpowiednich działań. W niektórych przypadkach może być odpowiednie, aby zasady grupy kontrolować użycie słabego szyfrowania</p><p>Dozwolone algorytmy .NET do zarządzanej elastyczności kryptograficznej (w kolejności preferencji)</p><ul><li>AesCng (zgodny ze standardem FIPS)</li><li>AuthenticatedAesCng (zgodny ze standardem FIPS)</li><li>AESCryptoServiceProvider (zgodny ze standardem FIPS)</li><li>AESManaged (niezgodny ze standardem FIPS)</li></ul><p>Należy pamiętać, że żaden z tych algorytmów nie może być `SymmetricAlgorithm.Create` określony `CryptoConfig.CreateFromName` za pomocą lub metod bez wprowadzania zmian w pliku Machine. config. Należy również pamiętać, że algorytm AES w wersjach programu .NET starszych niż .NET 3,5 `RijndaelManaged`ma nazwę `AesCng` `AuthenticatedAesCng` i > dostępne za pomocą CodePlex i wymagają użycia CNG w podstawowym systemie operacyjnym</p>

## <a id="vector-ciphers"></a>Użyj zatwierdzonych trybów szyfrowania bloku i wektorów inicjalizacji dla szyfrów symetrycznych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Wszystkie szyfry bloku symetrycznego muszą być używane z zatwierdzonym trybem szyfrowania symetrycznego. Jedyne zatwierdzone tryby to CBC i CTS. W szczególności należy unikać trybu działania elektronicznej książki kodu (EBC); Korzystanie z EBC wymaga przeglądu tablicy kryptograficznej w organizacji. Wszystkie użycie OFB, CFB, Rob, CCM i GCM lub dowolnego innego trybu szyfrowania musi zostać sprawdzone przez tablicę kryptograficzną w organizacji. Ponowne użycie tego samego wektora inicjalizacji (IV) z szyframi blokowymi w trybach szyfrów przesyłania strumieniowego, takich jak Rob, może spowodować wypróbowanie zaszyfrowanych danych. Wszystkie szyfry bloków symetrycznych muszą być również używane z odpowiednim wektorem inicjalizacji (IV). Odpowiednia IV to kryptograficznie silnie, liczba losowa i nigdy nie jest wartością stałą. |

## <a id="padding"></a>Użyj zatwierdzonych algorytmów asymetrycznych, długości kluczy i wypełnienia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Użycie zabronionych algorytmów kryptograficznych wprowadza znaczne ryzyko dla bezpieczeństwa produktu i należy je unikać. Produkty muszą używać tylko algorytmów kryptograficznych i skojarzonych długości kluczy i dopełnień, które zostały jawnie zatwierdzone przez tablicę kryptograficzną w organizacji.</p><ul><li>**RSA —** może być używany do szyfrowania, wymiany kluczy i podpisywania. Szyfrowanie RSA musi używać tylko trybów uzupełniania OAEP lub RSA-KEM. Istniejący kod może używać trybu uzupełniania PKCS #1 v 1.5 tylko w celu zapewnienia zgodności. Użycie dopełnienia wartości null jest jawnie zabronione. Klucze > = 2048 bitów jest wymagana dla nowego kodu. Istniejący kod może obsługiwać klucze < 2048 bitowy tylko w celu zapewnienia zgodności z poprzednimi wersjami po zakończeniu przeglądu przez tablicę kryptograficzną w organizacji. Klucze < 1024 BITS mogą być używane tylko do odszyfrowywania/weryfikowania starych danych i muszą zostać zastąpione, jeśli są używane do szyfrowania lub operacji podpisywania</li><li>**ECDSA —** może być używany tylko do podpisu. ECDSA z > = 256-bitowe klucze są wymagane dla nowego kodu. Podpisy ECDSA muszą używać jednej z trzech zatwierdzonych krzywych NIST (P-256, P-384 lub P521). Krzywe, które zostały dokładnie przeanalizowane, mogą być używane tylko po przeprowadzeniu przeglądu z tablicą kryptograficzną w organizacji.</li><li>**ECDH —** może być używany tylko do wymiany kluczy. ECDH z > = 256-bitowe klucze są wymagane dla nowego kodu. Wymiana kluczy oparta na ECDH musi korzystać z jednej z trzech zatwierdzonych krzywych NIST (P-256, P-384 lub P521). Krzywe, które zostały dokładnie przeanalizowane, mogą być używane tylko po przeprowadzeniu przeglądu z tablicą kryptograficzną w organizacji.</li><li>**DSA —** może być akceptowalny po przeprowadzeniu przeglądu i zatwierdzenia z tablicy kryptograficznej w organizacji. Skontaktuj się z doradcą zabezpieczeń, aby zaplanować przegląd tablicy kryptograficznej w organizacji. Jeśli Twoje użycie DSA zostało zatwierdzone, należy pamiętać, że należy zabronić używania kluczy o długości mniejszej niż 2048 bitów. CNG obsługuje 2048-bitowe i większe długości kluczy w systemie Windows 8.</li><li>**Diffie-Hellmana —** można używać tylko do zarządzania kluczami sesji. Długość klucza > = 2048 bitów jest wymagana dla nowego kodu. Istniejący kod może obsługiwać długość kluczy < 2048 bitowy tylko w przypadku zgodności z poprzednimi wersjami po zakończeniu przeglądu przez tablicę kryptograficzną w organizacji. Klucze < 1024 bitów nie mogą być używane.</li><ul>

## <a id="numgen"></a>Użyj zatwierdzonych generatorów liczb losowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Produkty muszą używać zatwierdzonych generatorów liczb losowych. Funkcje pseudolosowych, takie jak funkcja środowiska uruchomieniowego C Rand, .NET Framework klasy System. Random lub funkcje systemowe, takie jak GetTickCount, nie mogą być używane w tym kodzie. Użycie algorytmu dwuwarstwowego generatora liczb losowych (DUAL_EC_DRBG) jest zabronione</p><ul><li>**CNG —** BCryptGenRandom (użycie flagi BCRYPT_USE_SYSTEM_PREFERRED_RNG zalecane, chyba że obiekt wywołujący może być uruchomiony w żadnym IRQL większym niż 0 [to jest, PASSIVE_LEVEL])</li><li>**CAPI —** cryptGenRandom</li><li>**Win32/64-** RtlGenRandom (nowe implementacje powinny używać BCryptGenRandom lub CryptGenRandom) * rand_s * SystemPrng (dla trybu jądra)</li><li>**. NET-** RNGCryptoServiceProvider lub RNGCng</li><li>**Aplikacje ze sklepu Windows —** Windows. Security. Cryptography. CryptographicBuffer. GenerateRandom lub. GenerateRandomNumber</li><li>**Apple OS X (10,7 +)/iOS (2.0 +)-** int SecRandomCopyBytes (SecRandomRef losowo, size_t Count, uint8_t \*bajtów)</li><li>**Apple OS X (< 10,7) —** Pobieranie liczb losowych za pomocą/dev/Random</li><li>**Java (w tym kod systemu Google Android Java) —** Java. Security. SecureRandom. Należy pamiętać, że w przypadku systemu Android 4,3 (galaretki) deweloperzy muszą przestrzegać zalecanego obejścia systemu Android i aktualizować aplikacje, aby jawnie inicjować PRNG z entropią z/dev/urandom lub/dev/Random</li></ul>|

## <a id="stream-ciphers"></a>Nie używaj szyfrów strumienia symetrycznego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Symetryczne szyfry strumienia, takie jak RC4, nie mogą być używane. Zamiast symetrycznych szyfrów strumieniowych, produkty powinny używać szyfru blokowego, w odniesieniu do algorytmu AES, z kluczem o długości co najmniej 128 bitów. |

## <a id="mac-hash"></a>Użyj zatwierdzonych algorytmów wyznaczania wartości skrótu dla komputerów MAC/HMAC

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Produkty muszą używać tylko zaakceptowanych algorytmów kodu uwierzytelniania komunikatów (MAC) lub skrótu kodu uwierzytelniania komunikatów (HMAC).</p><p>Kod uwierzytelniania wiadomości (MAC) to informacje dołączone do wiadomości, które umożliwiają jej odbiorcy zweryfikowanie zarówno autentyczności nadawcy, jak i integralności komunikatu przy użyciu klucza tajnego. Korzystanie z systemu Mac opartego na skrótach ([HMAC](https://csrc.nist.gov/publications/nistpubs/800-107-rev1/sp800-107-rev1.pdf)) lub [Mac opartych](https://csrc.nist.gov/publications/nistpubs/800-38B/SP_800-38B.pdf) na szyfrowaniu blokowym jest dozwolone, tak długo, jak wszystkie bazowe algorytmy szyfrowania lub szyfrowanie symetryczne są również zatwierdzone do użycia; obecnie obejmuje to funkcje HMAC-algorytmu SHA2 (HMAC-SHA256, HMAC-SHA384 i HMAC-SHA512) oraz CMAC/OMAC1 i OMAC2 blokowe (są one oparte na standardzie AES).</p><p>Użycie algorytmu HMAC-SHA1 może być dozwolone w przypadku zgodności platformy, ale konieczne będzie przeprowadzenie tego wyjątku do tej procedury i przeprowadzenie kontroli kryptograficznej w organizacji. Obcinanie HMACs o wartości mniejszej niż 128 bitów nie jest dozwolone. Użycie metod klienta do mieszania klucza i danych nie jest zatwierdzone i musi zostać poddane przeglądowi z tablicą kryptograficzną organizacji przed użyciem.</p>|

## <a id="hash-functions"></a>Używaj tylko zatwierdzonych funkcji skrótu kryptograficznego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Produkty muszą używać rodziny SHA-2 algorytmów wyznaczania wartości skrótu (SHA256, SHA384 i SHA512). Jeśli jest wymagany krótszy skrót, taki jak 128-bitowa długość wyjściowa, aby dopasować strukturę danych zaprojektowaną przy użyciu krótszego skrótu MD5, zespoły produktu mogą obciąć jeden z wartości skrótu algorytmu SHA2 (zazwyczaj SHA256). Należy pamiętać, że SHA384 jest obcięta wersja SHA512. Obcinanie wartości skrótów kryptograficznych ze względów bezpieczeństwa do mniej niż 128 bitów nie jest dozwolone. Nowy kod nie może korzystać z algorytmów wyznaczania wartości skrótu MD2, MD4, MD5, SHA-0, SHA-1 lub RIPEMD. Kolizje skrótów są z tego względu przydatne dla tych algorytmów, co efektywnie je przerywa.</p><p>Dozwolone algorytmy wyznaczania wartości skrótu platformy .NET dla zarządzanej elastyczności kryptograficznej (w kolejności preferencji):</p><ul><li>SHA512Cng (zgodny ze standardem FIPS)</li><li>SHA384Cng (zgodny ze standardem FIPS)</li><li>SHA256Cng (zgodny ze standardem FIPS)</li><li>SHA512Managed (niezgodny ze standardem FIPS) (Użyj SHA512 jako nazwy algorytmu w wywołaniach do algorytm. Create lub obiektu CryptoConfig.</li><li>SHA384Managed (niezgodny ze standardem FIPS) (Użyj SHA384 jako nazwy algorytmu w wywołaniach do algorytm. Create lub obiektu CryptoConfig.</li><li>SHA256Managed (niezgodny ze standardem FIPS) (Użyj SHA256 jako nazwy algorytmu w wywołaniach do algorytm. Create lub obiektu CryptoConfig.</li><li>SHA512CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA256CryptoServiceProvider (zgodny ze standardem FIPS)</li><li>SHA384CryptoServiceProvider (zgodny ze standardem FIPS)</li></ul>| 

## <a id="strong-db"></a>Szyfrowanie danych w bazie danych przy użyciu algorytmów silnego szyfrowania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Wybieranie algorytmu szyfrowania](https://technet.microsoft.com/library/ms345262(v=sql.130).aspx) |
| **Kroki** | Algorytmy szyfrowania definiują przekształcenia danych, których nie można łatwo odwrócić przez nieautoryzowanych użytkowników. SQL Server pozwala administratorom i deweloperom wybierać spośród kilku algorytmów, w tym DES, Triple DES, TRIPLE_DES_3KEY, RC2, RC4, 128-bit RC4, DESX, 128-bit AES, 192-bit AES i 256-bit AES |

## <a id="ssis-signed"></a>Pakiety SSIS powinny być szyfrowane i podpisane cyfrowo

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Zidentyfikuj źródło pakietów z podpisami cyfrowymi](https://msdn.microsoft.com/library/ms141174.aspx), [zagrożeniem i lukami w zabezpieczeniach (usługi integracji)](https://msdn.microsoft.com/library/bb522559.aspx) |
| **Kroki** | Źródłem pakietu jest osoba lub organizacja, która utworzyła pakiet. Uruchamianie pakietu z nieznanego lub niezaufanego źródła może być ryzykowne. Aby uniemożliwić nieautoryzowane manipulowanie pakietami SSIS, należy użyć podpisów cyfrowych. Ponadto, aby zapewnić poufność pakietów podczas magazynu i przesyłania, pakiety SSIS muszą być szyfrowane |

## <a id="securables-db"></a>Dodawanie podpisu cyfrowego do krytycznych zabezpieczanych baz danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Dodaj SYGNATURę (Transact-SQL)](https://msdn.microsoft.com/library/ms181700) |
| **Kroki** | W przypadkach, gdy integralność krytycznej zabezpieczonej bazy danych musi być zweryfikowana, należy użyć podpisów cyfrowych. Zabezpieczanie bazy danych, takie jak procedura składowana, funkcja, zestaw lub wyzwalacze, może być podpisane cyfrowo. Poniżej znajduje się przykład, kiedy może być przydatny: Poinformuj nas o tym, że dostawca ISV (niezależny dostawca oprogramowania) podał wsparcie dla oprogramowania dostarczonego do jednego z klientów. Przed zapewnieniem pomocy technicznej dostawca ISV chce upewnić się, że baza danych, której Zabezpieczanie w oprogramowaniu nie została naruszona przez pomyłkę lub przez złośliwą próbę. Jeśli Zabezpieczanie jest podpisane cyfrowo, dostawca ISV może zweryfikować swój podpis cyfrowy i zweryfikować jego integralność.| 

## <a id="ekm-keys"></a>Używanie programu SQL Server EKM do ochrony kluczy szyfrowania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [SQL Server rozszerzalne zarządzanie kluczami (EKM)](https://msdn.microsoft.com/library/bb895340), [rozszerzalne zarządzanie kluczami za pomocą Azure Key Vault (SQL Server)](https://msdn.microsoft.com/library/dn198405) |
| **Kroki** | SQL Server rozszerzalne zarządzanie kluczami włącza klucze szyfrowania chroniące pliki bazy danych, które mają być przechowywane na urządzeniu z systemem poza Box, takim jak karta inteligentna, urządzenie USB lub moduł EKM/HSM. Umożliwia to również ochronę danych przed administratorami baz danych (z wyjątkiem elementów członkowskich grupy sysadmin). Dane można szyfrować przy użyciu kluczy szyfrowania, do których tylko użytkownik bazy danych ma dostęp w zewnętrznym module EKM/HSM. |

## <a id="keys-engine"></a>Użyj funkcji AlwaysEncrypted, jeśli klucze szyfrowania nie powinny być ujawnione w aparacie bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | SQL Azure, OnPrem |
| **Atrybuty**              | SQL w wersji — V12, MsSQL2016 |
| **Wołują**              | [Always Encrypted (aparat bazy danych)](https://msdn.microsoft.com/library/mt163865) |
| **Kroki** | Always Encrypted to funkcja przeznaczona do ochrony poufnych danych, takich jak numery kart kredytowych lub numery identyfikacyjne Narodowego (np. numery ubezpieczenia społecznego w Stanach Zjednoczonych), przechowywane w bazie danych Azure SQL Database lub SQL Server. Always Encrypted pozwala klientom szyfrować poufne dane wewnątrz aplikacji klienckich i nigdy nie ujawniać kluczy szyfrowania do aparatu bazy danych (SQL Database lub SQL Server). W związku z tym Always Encrypted zapewnia rozdzielenie między osobami, które są właścicielami danych (i mogą je wyświetlać) oraz tymi, które zarządzają danymi (ale nie powinny mieć dostępu) |

## <a id="keys-iot"></a>Bezpieczne przechowywanie kluczy kryptograficznych na urządzeniu IoT

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | System operacyjny urządzenia — Windows IoT Core, łączność urządzeń — zestawy SDK urządzeń Azure IoT |
| **Wołują**              | [Moduł TPM w systemie Windows IoT Core](https://developer.microsoft.com/windows/iot/docs/tpm), [Konfigurowanie modułu TPM w systemie Windows IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/setuptpm), [moduł TPM zestawu SDK urządzeń Azure IoT](https://github.com/Azure/azure-iot-hub-vs-cs/wiki/Device-Provisioning-with-TPM) |
| **Kroki** | Bezpieczne klucze prywatne lub certyfikaty certyfikatów są bezpiecznie przechowywane w chronionym sprzęcie sprzętowym, takim jak moduł TPM lub wióry kart inteligentnych. System Windows 10 IoT Core obsługuje użytkownika modułu TPM i istnieje kilka zgodnych moduły TPM, które mogą być używane: https://docs.microsoft.com/windows/iot-core/secure-your-device/tpm#discrete-tpm-dtpm. Zaleca się używanie oprogramowania układowego lub dyskretnego modułu TPM. Moduł TPM oprogramowania powinien być używany tylko do celów deweloperskich i testowych. Gdy moduł TPM jest dostępny i w nim są udostępniane klucze, kod generujący token powinien być zapisany bez twardej informacji poufnych. | 

### <a name="example"></a>Przykład
```
TpmDevice myDevice = new TpmDevice(0);
// Use logical device 0 on the TPM 
string hubUri = myDevice.GetHostName(); 
string deviceId = myDevice.GetDeviceId(); 
string sasToken = myDevice.GetSASToken(); 

var deviceClient = DeviceClient.Create( hubUri, AuthenticationMethodFactory. CreateAuthenticationWithToken(deviceId, sasToken), TransportType.Amqp); 
```
Jak widać, klucz podstawowy urządzenia nie znajduje się w kodzie. Zamiast tego jest on przechowywany w module TPM w gnieździe 0. Urządzenie TPM generuje token sygnatury dostępu współdzielonego o krótkim czasie, który jest następnie używany do nawiązywania połączenia z IoT Hub. 

## <a id="random-hub"></a>Wygeneruj losowy klucz symetryczny o wystarczającej długości, aby można było uwierzytelnić IoT Hub

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama usługi IoT Cloud | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — IoT Hub platformy Azure |
| **Wołują**              | ND  |
| **Kroki** | IoT Hub zawiera rejestr tożsamości urządzeń i podczas aprowizacji urządzenia automatycznie generuje losowy klucz symetryczny. Zaleca się używanie tej funkcji rejestru tożsamości usługi Azure IoT Hub w celu wygenerowania klucza używanego do uwierzytelniania. IoT Hub umożliwia również określenie klucza podczas tworzenia urządzenia. Jeśli klucz jest generowany poza IoT Hub podczas aprowizacji urządzenia, zaleca się utworzenie losowego klucza symetrycznego lub co najmniej 256 bitów. |

## <a id="pin-remote"></a>Upewnij się, że zasady zarządzania urządzeniami wymagają użycia numeru PIN i umożliwiają zdalne czyszczenie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM Mobile Client | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Upewnij się, że zasady zarządzania urządzeniami wymagają użycia numeru PIN i umożliwiają zdalne czyszczenie |

## <a id="bitlocker"></a>Upewnij się, że zasady zarządzania urządzeniami wymagają użycia numeru PIN/hasła/AutoLock i szyfrują wszystkie dane (np. Funkcja BitLocker)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient programu Dynamics CRM Outlook | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | Upewnij się, że zasady zarządzania urządzeniami wymagają użycia numeru PIN/hasła/AutoLock i szyfrują wszystkie dane (np. Funkcja BitLocker) |

## <a id="rolled-server"></a>Upewnij się, że klucze podpisywania są rzutowane podczas korzystania z serwera tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | [Serwer tożsamości — klucze, sygnatury i Kryptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) |
| **Kroki** | Upewnij się, że klucze podpisywania są rzutowane podczas korzystania z serwera tożsamości. Link w sekcji References wyjaśnia, jak to powinno być planowane bez powodowania awarii dla aplikacji korzystających z serwera tożsamości. |

## <a id="client-server"></a>Upewnij się, że na serwerze tożsamości są używane kryptograficzne silne identyfikatory klienta i klucz tajny klienta.

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | ND  |
| **Wołują**              | ND  |
| **Kroki** | <p>Upewnij się, że na serwerze tożsamości są używane kryptograficzne silne identyfikatory klienta. Podczas generowania identyfikatora klienta i klucza tajnego należy użyć następujących wytycznych:</p><ul><li>Generuj losowy identyfikator GUID jako identyfikator klienta</li><li>Generuj kryptograficzny losowo losowy klucz 256-bitowy jako klucz tajny</li></ul>|

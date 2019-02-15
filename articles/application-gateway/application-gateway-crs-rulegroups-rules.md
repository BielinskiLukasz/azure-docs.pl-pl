---
title: Usługa Azure Application Gateway zapory aplikacji sieci web CRS grup reguł i reguł
description: Ta strona zawiera informacje dotyczące grup reguł CRS zapory aplikacji sieci web i reguł.
documentationcenter: na
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 4/16/2018
ms.author: victorh
ms.openlocfilehash: b45e782984f637f1b084ec757000dde9dd3a5777
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56302044"
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Oferowana listę grup reguł CRS zapory aplikacji sieci web i reguł

Zapory aplikacji sieci web (WAF) Application Gateway chroni aplikacje sieci web z najpopularniejszych luk w zabezpieczeniach i luki w zabezpieczeniach. Jest to realizowane za pomocą reguł, które są zdefiniowane w oparciu o podstawowych zestawów reguł OWASP 2.2.9 lub 3.0. Te reguły można wyłączyć na podstawie przez reguły. Ten artykuł zawiera bieżące reguły i oferowana zestawów reguł.

Poniższe tabele są grup reguł i reguł, które są dostępne podczas korzystania z bramy aplikacji przy użyciu zapory aplikacji sieci web.  Każda tabela dotyczy znaleziono w grupie reguły dla określonej wersji CRS reguł.

## <a name="owasp30"></a> OWASP_3.0

### <a name="General"></a> <p x-ms-format-detection="none">Ogólne</p>

|Identyfikator reguły|Opis|
|---|---|
|200004|Możliwe wieloczęściowego niedopasowane granic.|

### <a name="crs911"></a> <p x-ms-format-detection="none">ŻĄDANIE 911 — METODA WYMUSZANIA</p>

|Identyfikator reguły|Opis|
|---|---|
|911011|Reguła 911011|
|911012|Reguła 911012|
|911100|Metoda nie jest dozwolona przez zasady|
|911013|Reguła 911013|
|911014|Reguła 911014|
|911015|Reguła 911015|
|911016|Reguła 911016|
|911017|Reguła 911017|
|911018|Reguła 911018|


### <a name="crs913"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|Identyfikator reguły|Opis|
|---|---|
|913011|Reguła 913011|
|913012|Reguła 913012|
|913100|Znaleziono agenta użytkownika skojarzony z skaner zabezpieczeń|
|913110|Znaleziono nagłówek żądania skojarzony z skaner zabezpieczeń|
|913120|Znaleziono żądania/argument pliku skojarzone z skaner zabezpieczeń|
|913013|Reguła 913013|
|913014|Reguła 913014|
|913101|Znaleziono agenta użytkownika skojarzony z klienta HTTP skryptów/ogólne|
|913102|Znaleziono agenta użytkownika skojarzony z sieci web przeszukiwarki/bota|
|913015|Reguła 913015|
|913016|Reguła 913016|
|913017|Reguła 913017|
|913018|Reguła 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|Identyfikator reguły|Opis|
|---|---|
|920011|Reguła 920011|
|920012|Reguła 920012|
|920100|Nieprawidłowe żądanie HTTP wiersza|
|920130|Nie można przeanalizować treści żądania.|
|920140|Wieloczęściowy żądania, treści ścisła Weryfikacja nie powiodła się = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} %@{MULTIPART_HEADER_FOLDING KF} LF % @ {MULTIPART_LF_LINE}     %@{MULTIPART_FILE_LIMIT_EXCEEDED FINLANDIA %@{MULTIPART_INVALID_HEADER_FOLDING BĘDZIE %@{MULTIPART_INVALID_QUOTING IQ SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|920160|Nagłówek Content-Length protokołu HTTP nie jest liczbą.|
|920170|Pobierz ani HEAD żądania z treść.|
|920180|Brak nagłówka Content-Length żądania POST.|
|920190|Zakres = nieprawidłowy ostatni bajt.|
|920210|Powodujące konflikt/wielu znaleziono dane nagłówka połączenia.|
|920220|Próby ataku nadużycie kodowania adresu URL|
|920240|Próby ataku nadużycie kodowania adresu URL|
|920250|UTF8 Kodowanie próby ataku nadużyć|
|920260|Próby ataku nadużycie pełnej/połowę szerokości Unicode|
|920270|Nieprawidłowy znak w żądaniu (znaku null)|
|920280|Żądania brakuje nagłówka hosta|
|920290|Nagłówek hosta pusty|
|920310|Żądanie ma pustą nagłówek Accept|
|920311|Żądanie ma pustą nagłówek Accept|
|920330|Nagłówek Agent użytkownika pusty|
|920340|Żądanie zawierające zawartości, ale nagłówek Content-Type Brak|
|920350|Nagłówek hosta jest adresem IP liczbowe|
|920380|Zbyt wiele argumentów w żądaniu|
|920360|Zbyt długa nazwa argumentu|
|920370|Wartość argumentu jest za długa|
|920390|Przekroczono rozmiar całkowitej argumentów|
|920400|Przekazany plik jest zbyt duży|
|920410|Całkowita liczba przekazanych plików jest zbyt duży|
|920420|Typ zawartości żądania nie jest dozwolony przez zasady|
|920430|Wersja protokołu HTTP nie jest dozwolony przez zasady|
|920440|Rozszerzenie pliku adres URL jest ograniczona przez zasady|
|920450|Nagłówek HTTP jest ograniczona przez zasady (%@{MATCHED_VAR})|
|920013|Reguła 920013|
|920014|Reguła 920014|
|920200|Zakres = zbyt wiele pól (6- lub więcej)|
|920201|Zakres = zbyt wiele pól dla żądania pdf (35 lub więcej)|
|920230|Wykryto wiele kodowania adresu URL|
|920300|Żądania brakuje nagłówek Accept|
|920271|Nieprawidłowy znak w żądaniu (innych niż drukowalne znaki)|
|920320|Brak nagłówka agenta użytkownika|
|920015|Reguła 920015|
|920016|Reguła 920016|
|920272|Nieprawidłowy znak w żądaniu (poza drukowalnych znaków poniżej ascii 127 znaków)|
|920017|Reguła 920017|
|920018|Reguła 920018|
|920202|Zakres = zbyt wiele pól dla żądania pdf (6- lub więcej)|
|920273|Nieprawidłowy znak w żądaniu (poza bardzo rygorystyczny set)|
|920274|Nieprawidłowy znak w nagłówkach żądania (poza bardzo rygorystyczny set)|
|920460|Reguła 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|Identyfikator reguły|Opis|
|---|---|
|921011|Reguła 921011|
|921012|Reguła 921012|
|921100|Żądanie HTTP, Przemycaniem ataku.|
|921110|Atak przemycie żądania HTTP|
|921120|Odpowiedź HTTP podział ataku|
|921130|Odpowiedź HTTP podział ataku|
|921140|Ataku polegającego na iniekcji nagłówków HTTP za pośrednictwem nagłówki|
|921150|Ataku polegającego na iniekcji nagłówków HTTP za pośrednictwem ładunek (Wykryto CR/LF)|
|921160|Ataku polegającego na iniekcji nagłówków HTTP za pośrednictwem ładunek (CR/LF i wykryto nazwę nagłówka)|
|921013|Reguła 921013|
|921014|Reguła 921014|
|921151|Ataku polegającego na iniekcji nagłówków HTTP za pośrednictwem ładunek (Wykryto CR/LF)|
|921015|Reguła 921015|
|921016|Reguła 921016|
|921170|Reguła 921170|
|921180|Zanieczyszczenie parametru HTTP (% @{TX.1})|
|921017|Reguła 921017|
|921018|Reguła 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|Identyfikator reguły|Opis|
|---|---|
|930011|Reguła 930011|
|930012|Reguła 930012|
|930100|Ścieżka przechodzenia ataku (/... /)|
|930110|Ścieżka przechodzenia ataku (/... /)|
|930120|Próba dostępu do pliku systemu operacyjnego|
|930130|Próba dostępu do pliku ograniczone|
|930013|Reguła 930013|
|930014|Reguła 930014|
|930015|Reguła 930015|
|930016|Reguła 930016|
|930017|Reguła 930017|
|930018|Reguła 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|Identyfikator reguły|Opis|
|---|---|
|931011|Reguła 931011|
|931012|Reguła 931012|
|931100|Możliwości ataku zdalnego plików (RFI) = parametr adresu URL przy użyciu adresu IP|
|931110|Możliwości ataku zdalnego plików (RFI) = RFI narażone parametru Nazwa pospolita używana w/URL ładunku|
|931120|Możliwe zdalnego pliku dołączania (RFI) ataku = adres URL ładunku używane w/końcowe pytanie znakiem zapytania (?)|
|931013|Reguła 931013|
|931014|Reguła 931014|
|931130|Możliwe zdalnego pliku dołączania (RFI) ataku = łącze odwołania Wyłącz domeny|
|931015|Reguła 931015|
|931016|Reguła 931016|
|931017|Reguła 931017|
|931018|Reguła 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|Identyfikator reguły|Opis|
|---|---|
|932011|Reguła 932011|
|932012|Reguła 932012|
|932120|Wykonanie polecenia zdalnego = znaleziono programu Windows PowerShell polecenia|
|932130|Wykonanie polecenia zdalnego = wyrażenie powłoki systemu Unix, znaleziono|
|932140|Wykonanie polecenia zdalnego = Windows dla /, jeśli znaleziono polecenia|
|932160|Wykonanie polecenia zdalnego = znaleziono kod powłoki systemu Unix|
|932170|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932171|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932013|Reguła 932013|
|932014|Reguła 932014|
|932015|Reguła 932015|
|932016|Reguła 932016|
|932017|Reguła 932017|
|932018|Reguła 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|Identyfikator reguły|Opis|
|---|---|
|933011|Reguła 933011|
|933012|Reguła 933012|
|933100|Ataku polegającego na iniekcji PHP = znaleziono tagu otwierającego/zamykającego|
|933110|Ataku polegającego na iniekcji PHP = przekazywanie pliku skryptu języka PHP znaleziono|
|933120|Ataku polegającego na iniekcji PHP = dyrektywy konfiguracji znaleziono|
|933130|Ataku polegającego na iniekcji PHP = znaleziono zmiennych|
|933150|Ataku polegającego na iniekcji PHP = można odnaleźć nazwy funkcji PHP o wysokim ryzyku|
|933160|Ataku polegającego na iniekcji PHP = o wysokim ryzyku PHP wywołania funkcji znanego|
|933180|Ataku polegającego na iniekcji PHP = wywołania funkcji zmiennej znanego|
|933013|Reguła 933013|
|933014|Reguła 933014|
|933151|Ataku polegającego na iniekcji PHP = można odnaleźć nazwy funkcji PHP średniego ryzyka|
|933015|Reguła 933015|
|933016|Reguła 933016|
|933131|Ataku polegającego na iniekcji PHP = znaleziono zmiennych|
|933161|Ataku polegającego na iniekcji PHP = znaleziono wywołanie funkcji PHP niskiej wartości|
|933111|Ataku polegającego na iniekcji PHP = przekazywanie pliku skryptu języka PHP znaleziono|
|933017|Reguła 933017|
|933018|Reguła 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|Identyfikator reguły|Opis|
|---|---|
|941011|Reguła 941011|
|941012|Reguła 941012|
|941100|Wykrył ataku XSS, za pośrednictwem libinjection|
|941110|Filtr XSS - kategorii 1 = wektor Tag skryptu|
|941130|Filtr XSS - kategorii 3 = wektor atrybutu|
|941140|Filtr XSS — kategoria 4 = wektor URI języka Javascript|
|941150|Filtr XSS — kategoria 5 = atrybutów HTML niedozwolonych|
|941180|Słowa kluczowe lista zablokowanych modułów weryfikacji węzła|
|941190|Filtry XSS programu Internet Explorer — wykryto atak.|
|941200|Filtry XSS programu Internet Explorer — wykryto atak.|
|941210|Filtry XSS programu Internet Explorer — wykryto atak.|
|941220|Filtry XSS programu Internet Explorer — wykryto atak.|
|941230|Filtry XSS programu Internet Explorer — wykryto atak.|
|941240|Filtry XSS programu Internet Explorer — wykryto atak.|
|941260|Filtry XSS programu Internet Explorer — wykryto atak.|
|941270|Filtry XSS programu Internet Explorer — wykryto atak.|
|941280|Filtry XSS programu Internet Explorer — wykryto atak.|
|941290|Filtry XSS programu Internet Explorer — wykryto atak.|
|941300|Filtry XSS programu Internet Explorer — wykryto atak.|
|941310|US ASCII źle sformułowane kodowania Filtr XSS - atak.|
|941330|Filtry XSS programu Internet Explorer — wykryto atak.|
|941340|Filtry XSS programu Internet Explorer — wykryto atak.|
|941350|UTF-7 kodowania IE XSS - atak.|
|941013|Rule 941013|
|941014|Reguła 941014|
|941320|Wykryto ataku XSS możliwe - procedurę obsługi tagów HTML|
|941015|Reguła 941015|
|941016|Reguła 941016|
|941017|Reguła 941017|
|941018|Reguła 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|Identyfikator reguły|Opis|
|---|---|
|942011|Reguła 942011|
|942012|Reguła 942012|
|942100|Wykryto atak w programie SQL iniekcji za pośrednictwem libinjection|
|942110|Ataku polegającego na iniekcji SQL: Iniekcja wspólne testowanie wykryte|
|942130|Ataku polegającego na iniekcji SQL: Wykryto Tautology SQL.|
|942140|Ataku polegającego na iniekcji SQL = wykryto nazw pospolitych bazy danych|
|942160|Wykrywa ukryta sqli testów przy użyciu sleep() lub benchmark().|
|942170|Wykrywa prób iniekcji SQL testu wydajności i uśpienia, takich jak zapytania warunkowego|
|942190|Wykrywa próby zbierania informacji i wykonywanie kodu MSSQL|
|942200|Wykrywa MySQL komentarz- / zaciemniony miejsce w plikach wstrzyknięć kodu i kończenie działania początkowych|
|942230|Wykrywa warunkowego prób iniekcji kodu SQL|
|942260|Wykrywa, że podstawowe pomijania uwierzytelniania SQL próbuje 2/3|
|942270|Szukasz wstrzyknięcie podstawowego kodu sql. Wspólne parametry ataku dla mysql, oracle i innych.|
|942290|Znajduje podstawowe SQL bazy danych MongoDB prób iniekcji kodu|
|942300|Wykrywa MySQL komentarze, warunki i iniekcji ch r ()|
|942320|Wykrywa, MySQL i PostgreSQL wstrzyknięć kodu funkcji/procedury przechowywane|
|942330|Wykrywa klasycznego probings iniekcji SQL 1/2|
|942340|Wykrywa, że podstawowe pomijania uwierzytelniania SQL próbuje 3/3|
|942350|Wykrywa iniekcji MySQL funkcji zdefiniowanej przez użytkownika i inne struktury danych/manipulowanie prób|
|942360|Wykrywa połączonych podstawowe wstrzyknięcie kodu SQL i prób SQLLFI|
|942370|Wykrywa klasycznego probings iniekcji SQL 2/2|
|942013|Reguła 942013|
|942014|Reguła 942014|
|942150|Ataku polegającego na iniekcji SQL|
|942410|Ataku polegającego na iniekcji SQL|
|942430|Wykrywanie anomalii znak SQL (argumenty) z ograniczeniami: liczba znaków specjalnych przekracza (12)|
|942440|Wykryto sekwencja Komentarz SQL.|
|942450|SQL szesnastkowy kodowanie określone|
|942015|Reguła 942015|
|942016|Reguła 942016|
|942251|Wykrywa HAVING wstrzyknięć kodu|
|942460|Alert wykrywania anomalii meta-znak — powtarzające się znaki niebędące znakami słowa|
|942017|Reguła 942017|
|942018|Reguła 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|Identyfikator reguły|Opis|
|---|---|
|943011|Reguła 943011|
|943012|Reguła 943012|
|943100|Atak użyciem spreparowanych stałych identyfikatorów sesji możliwe = ustawienie wartości plików Cookie w formacie HTML|
|943110|Atak użyciem spreparowanych stałych identyfikatorów sesji możliwe = Nazwa parametru identyfikatora sesji z odwołania Wyłącz domeny|
|943120|Atak użyciem spreparowanych stałych identyfikatorów sesji możliwe = Nazwa parametru identyfikatora sesji z nie odwołania|
|943013|Rule 943013|
|943014|Reguła 943014|
|943015|Reguła 943015|
|943016|Reguła 943016|
|943017|Reguła 943017|
|943018|Reguła 943018|

## <a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|Identyfikator reguły|Opis|
|---|---|
|960911|Nieprawidłowe żądanie HTTP wiersza|
|981227|Błąd Apache = nieprawidłowy identyfikator URI żądania.|
|960912|Nie można przeanalizować treści żądania.|
|960914|Wieloczęściowy żądania, treści ścisła Weryfikacja nie powiodła się = PE %@{REQBODY_PROCESSOR_ERROR} BQ %@{MULTIPART_BOUNDARY_QUOTED} BW %@{MULTIPART_BOUNDARY_WHITESPACE} DB %@{MULTIPART_DATA_BEFORE} DA %@{MULTIPART_DATA_AFTER} %@{MULTIPART_HEADER_FOLDING KF} LF % @ {MULTIPART_LF_LINE}     %@{MULTIPART_FILE_LIMIT_EXCEEDED FINLANDIA %@{MULTIPART_INVALID_HEADER_FOLDING BĘDZIE %@{MULTIPART_INVALID_QUOTING IQ SM %@{MULTIPART_SEMICOLON_MISSING}}}}|
|960915|Parser wieloczęściowej wiadomości wykryto możliwe niedopasowane granicy.|
|960016|Nagłówek Content-Length protokołu HTTP nie jest liczbą.|
|960011|Pobierz ani HEAD żądania z treść.|
|960012|Brak nagłówka Content-Length żądania POST.|
|960902|Nieprawidłowe użycie kodowania tożsamości.|
|960022|Oczekuje nagłówka nie jest dozwolona dla protokołu HTTP 1.0.|
|960020|Nagłówek pragma wymaga nagłówka Cache-Control dla żądania HTTP/1.1.|
|958291|Zakres = pole istnieje i rozpoczyna się od 0.|
|958230|Zakres = nieprawidłowy ostatni bajt.|
|958295|Powodujące konflikt/wielu znaleziono dane nagłówka połączenia.|
|950107|Próby ataku nadużycie kodowania adresu URL|
|950109|Wykryto wiele kodowania adresu URL|
|950108|Próby ataku nadużycie kodowania adresu URL|
|950801|UTF8 Kodowanie próby ataku nadużyć|
|950116|Próby ataku nadużycie pełnej/połowę szerokości Unicode|
|960901|Nieprawidłowy znak w żądaniu|
|960018|Nieprawidłowy znak w żądaniu|

### <a name="crs21"></a> crs_21_protocol_anomalies

|Identyfikator reguły|Opis|
|---|---|
|960008|Żądania brakuje nagłówka hosta|
|960007|Nagłówek hosta pusty|
|960015|Żądania brakuje nagłówek Accept|
|960021|Żądanie ma pustą nagłówek Accept|
|960009|Brak nagłówka agenta użytkownika żądania|
|960006|Nagłówek Agent użytkownika pusty|
|960904|Żądanie zawierające zawartości, ale nagłówek Content-Type Brak|
|960017|Nagłówek hosta jest adresem IP liczbowe|

### <a name="crs23"></a> crs_23_request_limits

|Identyfikator reguły|Opis|
|---|---|
|960209|Zbyt długa nazwa argumentu|
|960208|Wartość argumentu jest za długa|
|960335|Zbyt wiele argumentów w żądaniu|
|960341|Przekroczono rozmiar całkowitej argumentów|
|960342|Przekazany plik jest zbyt duży|
|960343|Całkowita liczba przekazanych plików jest zbyt duży|

### <a name="crs30"></a> crs_30_http_policy

|Identyfikator reguły|Opis|
|---|---|
|960032|Metoda nie jest dozwolona przez zasady|
|960010|Typ zawartości żądania nie jest dozwolony przez zasady|
|960034|Wersja protokołu HTTP nie jest dozwolony przez zasady|
|960035|Rozszerzenie pliku adres URL jest ograniczona przez zasady|
|960038|Nagłówek HTTP jest ograniczona przez zasady|

### <a name="crs35"></a> crs_35_bad_robots

|Identyfikator reguły|Opis|
|---|---|
|990002|Wskazuje żądanie skaner zabezpieczeń zeskanowanych lokacji|
|990901|Wskazuje żądanie skaner zabezpieczeń zeskanowanych lokacji|
|990902|Wskazuje żądanie skaner zabezpieczeń zeskanowanych lokacji|
|990012|Nieautoryzowany witryny sieci web przeszukiwarki|

### <a name="crs40"></a> crs_40_generic_attacks

|Identyfikator reguły|Opis|
|---|---|
|960024|Alert wykrywania anomalii meta-znak — powtarzające się znaki niebędące znakami słowa|
|950008|Iniekcja nieudokumentowane ColdFusion tagów|
|950010|Ataku polegającego na iniekcji LDAP|
|950011|Iniekcja SSI ataku|
|950018|Wykryto adres URL Universal PDF XSS.|
|950019|Ataku polegającego na iniekcji poczty e-mail|
|950012|Żądanie HTTP, Przemycaniem ataku.|
|950910|Odpowiedź HTTP podział ataku|
|950911|Odpowiedź HTTP podział ataku|
|950117|Atak dołączenie pliku zdalnego|
|950118|Atak dołączenie pliku zdalnego|
|950119|Atak dołączenie pliku zdalnego|
|950120|Możliwe zdalnego pliku dołączania (RFI) ataku = łącze odwołania Wyłącz domeny|
|981133|Reguła 981133|
|981134|Reguła 981134|
|950009|Atak użyciem spreparowanych stałych identyfikatorów sesji|
|950003|Utrwalanie sesji|
|950000|Utrwalanie sesji|
|950005|Próba dostępu do pliku zdalnego|
|950002|Dostęp do poleceń systemu|
|950006|Polegające na iniekcji poleceń systemu|
|959151|Ataku polegającego na iniekcji PHP|
|958976|Ataku polegającego na iniekcji PHP|
|958977|Ataku polegającego na iniekcji PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|Identyfikator reguły|Opis|
|---|---|
|981231|Wykryto sekwencja Komentarz SQL.|
|981260|SQL szesnastkowy kodowanie określone|
|981320|Ataku polegającego na iniekcji SQL = wykryto nazw pospolitych bazy danych|
|981300|Reguła 981300|
|981301|Reguła 981301|
|981302|Reguła 981302|
|981303|Reguła 981303|
|981304|Reguła 981304|
|981305|Reguła 981305|
|981306|Reguła 981306|
|981307|Reguła 981307|
|981308|Reguła 981308|
|981309|Reguła 981309|
|981310|Reguła 981310|
|981311|Reguła 981311|
|981312|Reguła 981312|
|981313|Reguła 981313|
|981314|Reguła 981314|
|981315|Reguła 981315|
|981316|Reguła 981316|
|981317|Alert wykrycia anomalii w usłudze SQL w instrukcji SELECT|
|950007|Ataku polegającego na iniekcji SQL ukryta|
|950001|Ataku polegającego na iniekcji SQL|
|950908|Ataku polegającego na iniekcji SQL.|
|959073|Ataku polegającego na iniekcji SQL|
|981272|Wykrywa ukryta sqli testów przy użyciu sleep() lub benchmark().|
|981250|Wykrywa prób iniekcji SQL testu wydajności i uśpienia, takich jak zapytania warunkowego|
|981241|Wykrywa warunkowego prób iniekcji kodu SQL|
|981276|Szukasz wstrzyknięcie podstawowego kodu sql. Wspólne parametry ataku dla mysql, oracle i innych.|
|981270|Znajduje podstawowe SQL bazy danych MongoDB prób iniekcji kodu|
|981253|Wykrywa, MySQL i PostgreSQL wstrzyknięć kodu funkcji/procedury przechowywane|
|981251|Wykrywa iniekcji MySQL funkcji zdefiniowanej przez użytkownika i inne struktury danych/manipulowanie prób|

### <a name="crs41xss"></a> crs_41_xss_attacks

|Identyfikator reguły|Opis|
|---|---|
|973336|Filtr XSS - kategorii 1 = wektor Tag skryptu|
|973338|Filtr XSS - kategorii 3 = wektor URI języka Javascript|
|981136|Reguła 981136|
|981018|Reguła 981018|
|958016|Atak skryptów między witrynami (XSS)|
|958414|Atak skryptów między witrynami (XSS)|
|958032|Atak skryptów między witrynami (XSS)|
|958026|Atak skryptów między witrynami (XSS)|
|958027|Atak skryptów między witrynami (XSS)|
|958054|Atak skryptów między witrynami (XSS)|
|958418|Atak skryptów między witrynami (XSS)|
|958034|Atak skryptów między witrynami (XSS)|
|958019|Atak skryptów między witrynami (XSS)|
|958013|Atak skryptów między witrynami (XSS)|
|958408|Atak skryptów między witrynami (XSS)|
|958012|Atak skryptów między witrynami (XSS)|
|958423|Atak skryptów między witrynami (XSS)|
|958002|Atak skryptów między witrynami (XSS)|
|958017|Atak skryptów między witrynami (XSS)|
|958007|Atak skryptów między witrynami (XSS)|
|958047|Atak skryptów między witrynami (XSS)|
|958410|Atak skryptów między witrynami (XSS)|
|958415|Atak skryptów między witrynami (XSS)|
|958022|Atak skryptów między witrynami (XSS)|
|958405|Atak skryptów między witrynami (XSS)|
|958419|Atak skryptów między witrynami (XSS)|
|958028|Atak skryptów między witrynami (XSS)|
|958057|Atak skryptów między witrynami (XSS)|
|958031|Atak skryptów między witrynami (XSS)|
|958006|Atak skryptów między witrynami (XSS)|
|958033|Atak skryptów między witrynami (XSS)|
|958038|Atak skryptów między witrynami (XSS)|
|958409|Atak skryptów między witrynami (XSS)|
|958001|Atak skryptów między witrynami (XSS)|
|958005|Atak skryptów między witrynami (XSS)|
|958404|Atak skryptów między witrynami (XSS)|
|958023|Atak skryptów między witrynami (XSS)|
|958010|Atak skryptów między witrynami (XSS)|
|958411|Atak skryptów między witrynami (XSS)|
|958422|Atak skryptów między witrynami (XSS)|
|958036|Atak skryptów między witrynami (XSS)|
|958000|Atak skryptów między witrynami (XSS)|
|958018|Atak skryptów między witrynami (XSS)|
|958406|Atak skryptów między witrynami (XSS)|
|958040|Atak skryptów między witrynami (XSS)|
|958052|Atak skryptów między witrynami (XSS)|
|958037|Atak skryptów między witrynami (XSS)|
|958049|Atak skryptów między witrynami (XSS)|
|958030|Atak skryptów między witrynami (XSS)|
|958041|Atak skryptów między witrynami (XSS)|
|958416|Atak skryptów między witrynami (XSS)|
|958024|Atak skryptów między witrynami (XSS)|
|958059|Atak skryptów między witrynami (XSS)|
|958417|Atak skryptów między witrynami (XSS)|
|958020|Atak skryptów między witrynami (XSS)|
|958045|Atak skryptów między witrynami (XSS)|
|958004|Atak skryptów między witrynami (XSS)|
|958421|Atak skryptów między witrynami (XSS)|
|958009|Atak skryptów między witrynami (XSS)|
|958025|Atak skryptów między witrynami (XSS)|
|958413|Atak skryptów między witrynami (XSS)|
|958051|Atak skryptów między witrynami (XSS)|
|958420|Atak skryptów między witrynami (XSS)|
|958407|Atak skryptów między witrynami (XSS)|
|958056|Atak skryptów między witrynami (XSS)|
|958011|Atak skryptów między witrynami (XSS)|
|958412|Atak skryptów między witrynami (XSS)|
|958008|Atak skryptów między witrynami (XSS)|
|958046|Atak skryptów między witrynami (XSS)|
|958039|Atak skryptów między witrynami (XSS)|
|958003|Atak skryptów między witrynami (XSS)|
|973300|Wykryto ataku XSS możliwe - procedurę obsługi tagów HTML|
|973301|Wykryto atak XSS|
|973302|Wykryto atak XSS|
|973303|Wykryto atak XSS|
|973304|Wykryto atak XSS|
|973305|Wykryto atak XSS|
|973306|Wykryto atak XSS|
|973307|Wykryto atak XSS|
|973308|Wykryto atak XSS|
|973309|Wykryto atak XSS|
|973311|Wykryto atak XSS|
|973313|Wykryto atak XSS|
|973314|Wykryto atak XSS|
|973331|Filtry XSS programu Internet Explorer — wykryto atak.|
|973315|Filtry XSS programu Internet Explorer — wykryto atak.|
|973330|Filtry XSS programu Internet Explorer — wykryto atak.|
|973327|Filtry XSS programu Internet Explorer — wykryto atak.|
|973326|Filtry XSS programu Internet Explorer — wykryto atak.|
|973346|Filtry XSS programu Internet Explorer — wykryto atak.|
|973345|Filtry XSS programu Internet Explorer — wykryto atak.|
|973324|Filtry XSS programu Internet Explorer — wykryto atak.|
|973323|Filtry XSS programu Internet Explorer — wykryto atak.|
|973348|Filtry XSS programu Internet Explorer — wykryto atak.|
|973321|Filtry XSS programu Internet Explorer — wykryto atak.|
|973320|Filtry XSS programu Internet Explorer — wykryto atak.|
|973318|Filtry XSS programu Internet Explorer — wykryto atak.|
|973317|Filtry XSS programu Internet Explorer — wykryto atak.|
|973329|Filtry XSS programu Internet Explorer — wykryto atak.|
|973328|Filtry XSS programu Internet Explorer — wykryto atak.|

### <a name="crs42"></a> crs_42_tight_security

|Identyfikator reguły|Opis|
|---|---|
|950103|Ścieżka przechodzenia ataku|

### <a name="crs45"></a> crs_45_trojans

|Identyfikator reguły|Opis|
|---|---|
|950110|Udostępniający Tylne wejście do|
|950921|Udostępniający Tylne wejście do|
|950922|Udostępniający Tylne wejście do|

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wyłączyć reguły zapory aplikacji sieci Web, odwiedzając: [Dostosowywanie reguł zapory aplikacji sieci Web](application-gateway-customize-waf-rules-portal.md)

[1]: ./media/application-gateway-integration-security-center/figure1.png

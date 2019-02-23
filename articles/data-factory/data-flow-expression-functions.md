---
title: Mapowanie funkcji wyrażenia przepływu danych w usłudze Azure Data Factory
description: Mapowanie funkcji wyrażenia przepływu danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 5b409063d2fac874bb6ea4d14aa1d8ad2fea4ec2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734646"
---
# <a name="mapping-data-flow-expression-functions"></a>Mapowanie funkcji wyrażenia przepływu danych

Data Factory mapowanie przepływu danych mają język wyrażeń, które mogą być używane do konfigurowania przekształcenia danych.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Sprawdza, czy wartość jest wartością NULL * ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
Zwraca wartość NULL. Użyj syntax(null()) funkcji, jeśli znajduje się kolumna o nazwie "null". Każdej operacji, która używa spowoduje o wartości NULL * ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Na podstawie warunku stosuje jedną wartość, lub innych. "Other" uznaje o wartości NULL, jeśli jest nieokreślony. Obie wartości muszą być zgodne (liczbową, ciąg...) * ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Na podstawie alternatywnych warunki mają zastosowanie jednej wartości lub innych. Jeśli liczba wejść nawet, drugi ma wartość NULL dla ostatniego warunku * ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Porównanie jest równa operator ignoruje wielkość liter. Taki sam jak <> = — operator * ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Łączy ze sobą zmienną liczbę parametrów. Taki sam jak + — operator z ciągami * ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Łączy zmienną liczbę parametrów wraz z separatorem. Pierwszy parametr jest separator * ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) -> ``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Usuwa ciągiem znaków początkowe i końcowe. Jeśli drugi parametr jest nieokreślony, przycina odstępu. Inne jej przycina dowolny znak określony w drugim parametrze * ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Po lewej stronie przycina początkowe znaki ciągu. Jeśli drugi parametr jest nieokreślony, przycina odstępu. Inne jej przycina dowolny znak określony w drugim parametrze * ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Po prawej stronie przycina początkowe znaki ciągu. Jeśli drugi parametr jest nieokreślony, przycina odstępu. Inne jej przycina dowolny znak określony w drugim parametrze * ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Wyodrębnianie podciągu o określonej długości od pozycji. Położenie jest na podstawie 1. W przypadku pominięcia długość ustawiana domyślnie do końca ciągu * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lowercases ciąg * ``lower('GunChus') -> 'gunchus'``
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Uppercases ciąg * ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Zwraca długość ciągu * ``length('kiddo') -> 5``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Po prawej stronie dopełnia ciągu, dopełniając dostarczony do czasu jego o określonej długości. Jeśli ciąg jest równa lub większa niż długość jest pusta * ``rpad('great', 10, '-') -> 'great-----'`` 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
*********************************
<code>lpad</code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Po lewej stronie dopełnia ciągu, dopełniając dostarczony do czasu jego o określonej długości. Jeśli ciąg jest równa lub większa niż długość jest pusta * ``lpad('great', 10, '-') -> '-----great'`` 
* ``lpad('great', 4, '-') -> 'great'`` 
* '' lpad ("doskonałe" 8 "<>") -> "<><great'``
*********************************
<code>po lewej stronie</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Wyodrębnianie podciągu rozpoczęcia pod indeksem 1 o liczbie znaków. Taki sam jak SUBSTRING (str, 1, n) * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Zwraca podciąg z liczbą znaków z prawej strony. Takie same jak SUBSTRING (str LENGTH(str) - n, n) * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, czy ciąg rozpoczyna się od podany ciąg * ``startsWith('great', 'gr') -> true``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, czy ciąg kończy się wyrazem podany ciąg * ``endsWith('great', 'eat') -> true``
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Znajduje pozycję (na podstawie 1) podciągu wewnątrz ciągu niektórych pozycji początkowej. W przypadku pominięcia pozycja uznaje się od początku ciągu. 0 jest zwracany, jeśli nie znaleziono * ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Znajduje pozycję (na podstawie 1) podciągu wewnątrz ciągu. 0 jest zwracany, jeśli nie znaleziono * ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Zastąp jeden zestaw znaków przez inny zestaw znaków w ciągu. Znaki mają zastąpienie 1 do 1 * ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Odwraca ciąg * ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Konwertuje pierwszą literę każdego wyrazu na wielkie litery. Wyrazy są uznawani za rozdzielonych odstępu * ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Zamień wszystkie wystąpienia podciągu inny podciąg do danego ciągu * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Zamień wszystkie wystąpienia do wzorca wyrażenia regularnego inny podciąg w ciągu danego Użyj "<regex>'(back quote) próby dopasowania ciągu bez anulowania zapewnianego element * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Wyodrębnij podciąg dopasowania dla wzorca wyrażenia regularnego danego. Ostatni parametr identyfikuje grupę dopasowania i jest ustawiana domyślnie do 1 w przypadku pominięcia. Użyj "<regex>'(back quote) próby dopasowania ciągu bez anulowania zapewnianego element * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, jeśli ciąg pasuje do wzorca wyrażenia regularnego danego. Użyj "<regex>'(back quote) próby dopasowania ciągu bez anulowania zapewnianego element * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Wzorzec jest to ciąg, który jest literału, z wyjątkiem następujące znaki specjalne: _ dopasowuje dowolny znak w danych wejściowych (podobnie jak. w wyrażeniach regularnych posix) % dopasowuje zero lub więcej znaków w danych wejściowych (podobnie jak. * w wyrażeniach regularnych posix).
Znak ucieczki ". Jeśli znak ucieczki poprzedza symbol specjalny lub inny znak ucieczki, następujący znak jest literału. Jest on nieprawidłowy na jakikolwiek inny znak ucieczki.
* ``like('icecream', 'ice%') -> true``
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Sprawdza, jeśli ciąg pasuje do wzorca wyrażenia regularnego danego * ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Sprawdza, czy element w tablicy * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Konwertuje element podstawowy datatype. W przypadku liczb i dat można określić formatu. Jeśli nie zostanie podany, domyślny system jest pobierany. Format dziesiętny języka Java jest używana w przypadku liczb. Domyślny format daty to RRRR MM-dd * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Dzieli ciąg na podstawie ogranicznika i zwraca tablicę ciągów * ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Dzieli ciąg na podstawie ogranicznika oparte na wyrażenia regularnego i zwraca tablicę ciągów * ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Pobiera kod soundex dla ciągu * ``soundex('genius') -> 'G520'``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Pobiera levenshtein odległość między dwa ciągi * ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Wyodrębnia podzbiór tablicy, od pozycji. Położenie jest na podstawie 1. W przypadku pominięcia długość ustawiana domyślnie do końca ciągu * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
Zawsze zwraca wartość true. Użyj syntax(true()) — funkcja, przypadku nazwę kolumny o nazwie "prawda" * ``isDiscounted == true()``
* ``isDiscounted() == true``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
Zawsze zwraca wartość false. Użyj syntax(false()) — funkcja, przypadku nazwę kolumny o nazwie "fałsz" * ``isDiscounted == false()``
* ``isDiscounted() == false``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Konwertuje wartość (t ","true","y","tak","1") na wartość true i ("f","false", n", "no", "0") na false i NULL dla każdej innej wartości * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dodaje parę ciągów lub liczb. Dodaje wartość typu date do liczby dni. Dołącza tablicy typu podobne do innego. Taki sam jak + — operator * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Odejmuje liczb. Odejmij od daty liczbę dni. Taki sam jak — operator * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Mnoży dwóch cyfr. Taki sam jak * — operator * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dzieli dwóch cyfr. Taki sam jak / — operator * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Modulo dwóch cyfr. Takie same jak % — operator * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dodatnia modulo par liczb.
* ``pmod(-20, 8) -> 4``
*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Dodatnia modulo par liczb.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operator logiczny AND. Taki sam jak & & * ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operator logiczny OR. Taki sam jak || * ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operatora logicznego XOR. Taki sam jak ^ — operator * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operator logiczny negacji * ``not(true) -> false``
* ``not(premium)``
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Jest zgodny z typem kolumny. Można używać tylko we wzorcu expressions.number odpowiada krótki, liczba całkowita, long, double, float lub decimal, całkowitego odpowiada krótko, liczby całkowitej, długie, ułamkowe dopasowania double, float, decimal i daty/godziny dopasowań daty lub sygnatury czasowej typ * ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
Konwertuje wszelkie numeryczny lub ciąg krótszej wartości. Opcjonalne formatu dziesiętnego Java może służyć do konwersji. Obcina wszelkie integer, długie, float, double * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
Konwertuje wszelkie numeryczny lub ciąg na wartość całkowitą. Opcjonalne formatu dziesiętnego Java może służyć do konwersji. Obcina dowolnej długości, float, double * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
Konwertuje wszelkie numeryczny lub ciąg na wartość typu long. Opcjonalne formatu dziesiętnego Java może służyć do konwersji. Obcina wszelkie float, double * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
Konwertuje wszelkie numeryczny lub ciąg wartością zmiennoprzecinkową. Opcjonalne formatu dziesiętnego Java może służyć do konwersji. Obcina wszelkie podwójnej precyzji * ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
Konwertuje wszelkie numeryczny lub ciąg na wartość typu double. Opcjonalne formatu dziesiętnego Java może służyć do konwersji.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Konwertuje wszelkie numeryczny lub ciąg na wartość dziesiętną. Jeśli nie określono dokładności i skali, jest ustawiana domyślnie do (10,2). Opcjonalne formatu dziesiętnego Java może służyć do konwersji.
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie jest równa operatora. Taka sama, jak == — operator * ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'abc'=='abc' -> true``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie nie równa się operatora. Taki sam jak! = — operator * ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Większa operator porównania. Taki sam jak > — operator * ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie mniej operatora. Taki sam jak < — operator * ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie jest większa niż lub równe — operator. Taki sam jak > = — operator * ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porównanie mniejsze niż lub równe — operator. Taki sam jak < = — operator * ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Zwraca największą wartość na liście wartości jako dane wejściowe. Zwraca wartość null, jeśli wszystkie dane wejściowe mają wartość null * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Porównanie mniejsze niż lub równe — operator. Taki sam jak < = — operator * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Wywołuje jedną liczbę do potęgi równej innej * ``power(10, 2) -> 100``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza pierwiastek kwadratowy z liczby * ``sqrt(9) -> 3``
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicz kwadratowy modułu z liczby * ``cbrt(8) -> 2.0``
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Neguje liczbą. Włącza liczb dodatnich, ujemnych i na odwrót * ``negate(13) -> -13``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji cosinus * ``cos(10) -> -0.83907152907``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji arcus cosinus * ``acos(1) -> 0.0``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza cosinus hiperboliczny wartości * ``cosh(0) -> 1.0``
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji sinus * ``sin(2) -> 0.90929742682``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji arcus sinus * ``asin(0) -> 0.0``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji sinus hiperboliczny * ``sinh(0) -> 0.0``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji tangens * ``tan(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji tangens odwrotna * ``atan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość funkcji tangens hiperboliczny * ``tanh(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Zwraca kąt w radianach między dodatnią osi x płaszczyznę i punktu określonego przez właściwość współrzędnych * ``atan2(0, 0) -> 0.0``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Obliczyć silnię liczby * ``factorial(5) -> 120``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Zwraca największą liczbę całkowitą nie większą niż liczba * ``floor(-0.1) -> -1``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Zwraca najmniejszą liczbę całkowitą nie mniejszą niż liczba * ``ceil(-0.1) -> 0``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Konwertuje radiany na stopnie * ``degrees(3.141592653589793) -> 180``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Oblicza wartość funkcji dziennika. Opcjonalne podstawowy może być inne podany numer Eulera Jeśli używany * ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza logarytm oparte na podstawowej 10 * ``log10(100) -> 2``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Zaokrągla liczbę w podanej opcjonalne skalowania i opcjonalnie trybu zaokrąglania. W przypadku pominięcia skali ustawiana na 0.  Pominięcie tego trybu jest ustawiana domyślnie do ROUND_HALF_UP(5). Wartości, które zaokrąglania to 1 - ROUND_UP 2 - 3 ROUND_DOWN - ROUND_CEILING 4 - 5 ROUND_FLOOR - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Pobiera bieżącą datę, kiedy to zadanie zaczyna być uruchamiana. Można przekazać opcjonalny strefy czasowej w formie 'GMT', "PST", "UTC", "Ameryka/Kajmany". Lokalnej strefy czasowej jest używany jako domyślny.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Pobiera bieżącą sygnaturę czasową, kiedy zadanie zaczyna być uruchamiana przy użyciu lokalnej strefy czasowej * ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Konwertuje ciąg na wartość typu date, biorąc pod uwagę opcjonalny daty w formacie. W przypadku pominięcia format daty następujących kombinacji są akceptowane. [rrrr, rrrr-[M] M, rrrr-[M] min [d]-d, rrrr-[M] M-[d] d, rrrr-[M] M-[d] d, rrrr-[M] M-[d] dT *] * ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konwertuje ciąg na wartość typu date podany format znacznika czasu opcjonalne. W przypadku pominięcia sygnaturę czasową Domyślny wzorzec rrrr-[M] M-[d] d hh: mm: [. f...] jest używany * ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Konwertuje sygnaturę czasową UTC. Można przekazać opcjonalny strefy czasowej w formie 'GMT', "PST", "UTC", "Ameryka/Kajmany". Ustawiana domyślnie do bieżącej strefy czasowej * ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Pobiera bieżącą sygnaturę czasową jako czas UTC. Można przekazać opcjonalny strefy czasowej w formie 'GMT', "PST", "UTC", "Ameryka/Kajmany". Ustawiana domyślnie do bieżącej strefy czasowej * ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera wartość miesiąca daty lub sygnatury czasowej * ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera wartość roku od daty * ``year(toDate('2012-8-8')) -> 2012``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Pobiera wartość godziny z sygnatury czasowej. Można przekazać opcjonalny strefy czasowej w formie 'GMT', "PST", "UTC", "Ameryka/Kajmany". Lokalnej strefy czasowej jest używany jako domyślny.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Pobiera wartość minuty sygnaturę czasową. Można przekazać opcjonalny strefy czasowej w formie 'GMT', "PST", "UTC", "Ameryka/Kajmany". Lokalnej strefy czasowej jest używany jako domyślny.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Pobiera drugi wartość daty. Można przekazać opcjonalny strefy czasowej w formie 'GMT', "PST", "UTC", "Ameryka/Kajmany". Lokalnej strefy czasowej jest używany jako domyślny.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Określa dzień miesiąca danej wartości typu date * ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera dzień tygodnia, w danej wartości typu date. 1 — niedziela, 2 -... w każdy poniedziałek, 7 — sobota * ``dayOfWeek(toDate('2018-06-08')) -> 7``
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera dzień roku danej wartości typu date * ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera tydzień roku danej wartości typu date * ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Pobiera datę ostatniego miesiąca danej wartości typu date * ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Pobiera liczbę miesięcy między dwoma datesYou można przekazać opcjonalny strefy czasowej w formie 'GMT', "PST", "UTC", "Ameryka/Kajmany". Lokalnej strefy czasowej jest używany jako domyślny.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Dodaj miesięcy do daty lub sygnatury czasowej * ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Dodaj dni do daty lub sygnatury czasowej. Taki sam jak + — operator daty * ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odejmij miesięcy od daty. Taki sam jak - operator daty * ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odejmij miesięcy od daty lub sygnatury czasowej * ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
Zwraca następny unikatowy ciąg. Liczba jest kolejnych tylko w ramach partycji i jest poprzedzony partitionId * ``nextSequence() -> 12313112``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Oblicza Skrót MD5 zestawu kolumn o różnym poziomie pierwotne typy danych i zwraca ciąg szesnastkowy 32 znaków. Może służyć do obliczania odcisku palca dla wiersza * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Oblicza Skrót SHA-1 zestawu kolumn o różnym poziomie pierwotne typy danych i zwraca ciąg szesnastkowy 40 znaków. Może służyć do obliczania odcisku palca dla wiersza * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Oblicza Skrót SHA-2 zestawu kolumn o różnym poziomie pierwotne typy danych, biorąc pod uwagę długość w bitach, które mogą być tylko wartości 0(256), 224, 256, 384, 512. Może służyć do obliczania odcisku palca dla wiersza * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Oblicza skrót CRC32 zestawu kolumn o różnym poziomie pierwotne typy danych, biorąc pod uwagę długość w bitach, które mogą być tylko 0(256) wartości 224, 256, 384, 512. Może służyć do obliczania odcisku palca dla wiersza * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy jeśli wiersz jest oznaczony do wstawienia. Przekształcenia, które przyjmują więcej niż jeden strumień wejściowy może przekazywać indeks strumienia (w oparciu o jeden). Wartość domyślna dla indeks strumienia jest jeden * ``isInsert() -> true``
* ``isInsert(1) -> false``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy jeśli wiersz jest oznaczony do aktualizacji. Przekształcenia, które przyjmują więcej niż jeden strumień wejściowy może przekazywać indeks strumienia (w oparciu o jeden). Wartość domyślna dla indeks strumienia jest jeden * ``isUpdate() -> true``
* ``isUpdate(1) -> false``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy jeśli wiersz jest oznaczona do usunięcia. Przekształcenia, które przyjmują więcej niż jeden strumień wejściowy może przekazywać indeks strumienia (w oparciu o jeden). Wartość domyślna dla indeks strumienia jest jeden * ``isDelete() -> true``
* ``isDelete(1) -> false``
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy jeśli wiersz jest dopasowywany w wyszukiwaniu. Przekształcenia, które przyjmują więcej niż jeden strumień wejściowy może przekazywać indeks strumienia (w oparciu o jeden). Wartość domyślna dla indeks strumienia to 1 * ``isMatch() -> true``
* ``isMatch(1) -> false``
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy jeśli wiersz jest oznaczona jako błąd. Przekształcenia, które przyjmują więcej niż jeden strumień wejściowy może przekazywać indeks strumienia (w oparciu o jeden). Wartość domyślna dla indeks strumienia jest jeden * ``isError() -> true``
* ``isError(1) -> false``
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Sprawdza, czy jeśli wiersz jest oznaczony do zignorowania. Trwa więcej niż jeden strumień wejściowy przekształcenia można przekazać indeks strumienia (w oparciu o jeden). Wartość domyślna dla indeks strumienia jest jeden * ``isIgnore() -> true``
* ``isIgnore(1) -> false``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera łącznej sumy kolumny liczbowej * ``sum(col) -> value``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Oparte na kryteria pobiera łącznej sumy kolumny liczbowej. Warunek może być oparte na dowolną kolumnę * ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales) ``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera agregacji Suma odrębne wartości kolumny liczbowej * ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Oparte na kryteria pobiera łącznej sumy kolumny liczbowej. Warunek może być oparte na dowolną kolumnę * ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales) ``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Pobiera liczbę agregacji wartości. Jeśli określono opcjonalny kolumny, ignoruje ona wartości NULL w liczbie * ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Oparte na pobiera kryteria zagregowana liczba wartości. Jeśli określono kolumn opcjonalnych, ignoruje ona wartości NULL w liczbie * ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Pobiera łączna liczba unikatowych wartości właściwości zestawu kolumn * ``countDistinct(custId, custName) -> 60``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera średnią wartości kolumny * ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Pobiera kryteria — na podstawie średniej wartości kolumny * ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pobiera średniej wartości kolumny. Takie same jak Średni * ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Oparte na pobiera kryteria średniej wartości kolumny. Takie same jak avgIf * ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Pobiera minimalną wartość kolumny * ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na podstawie kryteriów, pobiera minimalną wartość kolumny * ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Pobiera maksymalną wartość kolumny * ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na podstawie kryteriów, pobiera maksymalną wartość kolumny * ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera odchylenie standardowe kolumny * ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera odchylenie standardowe kolumny * ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera odchylenie standardowe populacji kolumny * ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera odchylenie standardowe populacji kolumny * ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera odchylenie standardowe próbki kolumny * ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera odchylenie standardowe próbki kolumny * ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera wariancję kolumny * ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera wariancję kolumny * ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera wariancję populacji kolumny * ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera wariancję populacji kolumny * ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera nieobciążonej wariancję kolumny * ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera nieobciążonej wariancję kolumny * ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Pobiera populacji kowariancję dwóch kolumn * ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera kowariancję populacji dwie kolumny * ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Pobiera próbki kowariancję dwóch kolumn * ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera przykładową kowariancję dwóch kolumn * ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera kurtoza kolumny * ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera kurtoza kolumny * ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Pobiera skośność kolumny * ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryteriów, pobiera skośność kolumny * ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Pobiera pierwszą wartość grupę kolumn. Jeśli drugi ignoreNulls parametr zostanie pominięty, zakłada się, wartość false * ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Pobiera ostatnią wartość grupę kolumn. Jeśli drugi ignoreNulls parametr zostanie pominięty, zakłada się, wartość false * ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Pobiera wartość pierwszych wierszy n parametr oceniane przed bieżącego wiersza. Drugi parametr jest liczba wierszy Wstecz. Wartość domyślna to jeden. Jeśli nie są dowolną liczbę wierszy, które zostanie zwrócona wartość null, jeśli nie określono wartości domyślnej * ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Pobiera wartość pierwszych wierszy n parametr oceniane po bieżącego wiersza. Drugi parametr jest liczba wierszy do wyszukiwania do przodu. Wartość domyślna to jeden. Jeśli nie są dowolną liczbę wierszy, które zostanie zwrócona wartość null, jeśli nie określono wartości domyślnej * ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
Funkcja CumeDist oblicza pozycja wartości względem wszystkich wartości w partycji. Wynik jest liczba wierszy, poprzedni lub równa bieżącego wiersza w kolejności partycji podzielona przez łączną liczbę wierszy w oknie partycji. Wartości tie w kolejności, ocenia się na tym samym położeniu.
* ``cumeDist() -> 1``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Funkcja NTile dzieli wierszy dla każdej partycji okna do `n` zasobników z zakresu od 1 do co najwyżej `n`. Wartości będą się różnić, przez co najwyżej 1. Jeśli liczba wierszy w partycji nie równomiernie podzielić na liczbę przedziałów, wartości resztę to rozproszona jednego na przedział, rozpoczynając od pierwszego pakietu. Funkcja NTile jest szczególnie przydatne podczas obliczania tertiles, Kwartyle, deciles i inne typowe statystyki podsumowujące. Funkcja oblicza dwie zmienne podczas inicjowania: Rozmiar zasobnika regularnych i liczbę przedziałów, które będą miały jeden dodatkowy wiersz dodawanych do niego (w przypadku wierszy równomiernie nie pasuje do liczby zasobników); obie zmienne są oparte na rozmiar bieżącej partycji. Podczas obliczania funkcji przechowuje informacje o bieżący numer wiersza, bieżący numer przedziału i numer wiersza, w którym zasobniku zmieni (bucketThreshold). Gdy osiągnie numer bieżącego wiersza zasobnika próg, zasobnika wartość zwiększa się o jeden, a progu jest zwiększana o rozmiaru przedziału (plus jeden dodatkowy, jeśli bieżące zasobnika są dopełniane).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Oblicza pozycję wartości w grupy wartości. Wynik jest jedną oraz liczbę wierszy w poprzednim lub równa bieżącego wiersza w kolejności partycji. Wartości spowoduje przerwy w sekwencji. Ranga działa, nawet wtedy, gdy nie są sortowane dane i szuka zmiany w wartościach * ``rank(salesQtr, salesAmt) -> 1``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Oblicza pozycję wartości w grupy wartości. Wynik jest jedną oraz liczbę wierszy w poprzednim lub równa bieżącego wiersza w kolejności partycji. Wartości nie przyniesie przerwy w sekwencji. Ranga gęstą działa nawet wtedy, gdy dane nie są sortowane i szuka zmiany w wartościach * ``denseRank(salesQtr, salesAmt) -> 1``
*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
Przypisuje kolejny wiersz numerowanie wierszy w oknie, począwszy od 1 * ``rowNumber() -> 1``

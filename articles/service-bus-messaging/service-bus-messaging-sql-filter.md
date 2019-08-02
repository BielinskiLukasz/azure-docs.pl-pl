---
title: Odwołanie do składni Azure Service Bus sqlfilter | Microsoft Docs
description: Szczegóły dotyczące gramatyki xmlfilter.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: e490c7c24ed38e2988c1f097b09b508746f08178
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "60591802"
---
# <a name="sqlfilter-syntax"></a>Składnia elementu SQLFilter

Obiekt *sqlfilter* jest wystąpieniem [klasy sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)i reprezentuje wyrażenie filtru oparte na języku SQL, które jest oceniane względem [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Element sqlfilter obsługuje podzestaw standardu SQL-92.  
  
 W tym temacie przedstawiono szczegółowe informacje na temat gramatyki xmlfilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumenty  
  
-   `<scope>`jest opcjonalnym ciągiem wskazującym zakres `<property_name>`. Prawidłowe wartości to `sys` lub `user`. Wartość wskazuje zakres systemowy, `<property_name>` gdzie jest publiczną nazwą właściwości [klasy BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `sys` `user`wskazuje zakres użytkownika, `<property_name>` gdzie jest kluczem słownika [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user`zakres jest zakresem domyślnym, `<scope>` Jeśli nie jest określony.  
  
## <a name="remarks"></a>Uwagi

Próba uzyskania dostępu do nieistniejącej właściwości systemu jest błędem, podczas gdy próba uzyskania dostępu do nieistniejącej właściwości użytkownika nie jest błędem. Zamiast tego nieistniejąca Właściwość użytkownika jest obliczana wewnętrznie jako nieznana wartość. Nieznana wartość jest traktowana specjalnie podczas obliczania operatora.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  

 `<regular_identifier>`jest ciągiem przedstawionym przez następujące wyrażenie regularne:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Ta Gramatyka oznacza dowolny ciąg, który rozpoczyna się od litery, po której następuje jeden lub więcej podkreśleń/liter/cyfr.  
  
`[:IsLetter:]`oznacza dowolny znak Unicode, który jest kategoryzowany jako litera Unicode. `System.Char.IsLetter(c)`zwraca `true` wartość `c` , jeśli jest literą Unicode.  
  
`[:IsDigit:]`oznacza dowolny znak Unicode, który jest kategoryzowany jako cyfra dziesiętna. `System.Char.IsDigit(c)`zwraca `true` wartość `c` , jeśli jest cyfrą Unicode.  
  
A `<regular_identifier>` nie może być zastrzeżonym słowem kluczowym.  
  
`<delimited_identifier>`jest dowolnym ciągiem, który jest ujęty w nawiasy kwadratowe ([]). Prawy nawias kwadratowy jest reprezentowany jako dwa prawy nawias kwadratowy. Poniżej przedstawiono przykłady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>`to dowolny ciąg, który jest ujęty w znaki podwójnego cudzysłowu. Podwójny cudzysłów w identyfikatorze jest reprezentowany jako dwa podwójne cudzysłowy. Nie zaleca się używania identyfikatorów w cudzysłowie, ponieważ można je łatwo pomylić z stałą typu String. Jeśli to możliwe, należy użyć oddzielonego identyfikatora. Oto przykład `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>znaczne  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
`<pattern>`musi być wyrażeniem, które jest oceniane jako ciąg. Jest używany jako wzorzec dla operatora LIKE.      Może zawierać następujące symbole wieloznaczne:  
  
-   `%`:  Dowolny ciąg składający się z zero lub więcej znaków.  
  
-   `_`: Dowolny pojedynczy znak.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi  

`<escape_char>`musi być wyrażeniem, które jest oceniane jako ciąg o długości 1. Jest używany jako znak ucieczki dla operatora LIKE.  
  
 Na przykład `property LIKE 'ABC\%' ESCAPE '\'` pasuje `ABC%` do ciągu, który rozpoczyna się `ABC`od.  
  
## <a name="constant"></a>Stałe  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>`jest ciągiem liczb, które nie są ujęte w cudzysłów i nie zawierają punktów dziesiętnych. Wartości są przechowywane jako `System.Int64` wewnętrznie i zgodne z tym samym zakresem.  
  
     Są to przykłady długich stałych:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`jest ciągiem liczb, które nie są ujęte w cudzysłów i zawierają separator dziesiętny. Wartości są przechowywane jako `System.Double` wewnętrznie i zgodne z tym samym zakresem/dokładnością.  
  
     W przyszłych wersjach ten numer może być przechowywany w innym typie danych do obsługi dokładnej semantyki liczb, dlatego nie należy polegać na tym, że jest `System.Double` `<decimal_constant>`to typ danych bazowych.  
  
     Poniżej przedstawiono przykłady stałych dziesiętnych:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`to liczba zapisywana w notacji wykładniczej. Wartości są przechowywane jako `System.Double` wewnętrznie i zgodne z tym samym zakresem/dokładnością. Poniżej przedstawiono przykłady przybliżonych stałych liczbowych:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Uwagi  

Stałe logiczne są reprezentowane przez słowa kluczowe **true** lub **false**. Wartości są przechowywane jako `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Uwagi  

Stałe ciągów są ujęte w znaki pojedynczego cudzysłowu i zawierają wszystkie prawidłowe znaki Unicode. Pojedynczy cudzysłów osadzony w stałej ciągu jest reprezentowany jako dwa znaki pojedynczego cudzysłowu.  
  
## <a name="function"></a>funkcja  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Uwagi
  
Funkcja zwraca obiekt **System. GUID** wygenerowany przez `System.Guid.NewGuid()` metodę. `newid()`  
  
Funkcja zwraca wartość właściwości, `name`do której odwołuje się. `property(name)` `name` Wartość może być dowolnym prawidłowym wyrażeniem zwracającym wartość ciągu.  
  
## <a name="considerations"></a>Zagadnienia do rozważenia
  
Rozważ użycie następujących [](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) semantyki sqlfilter:  
  
-   Nazwy właściwości nie uwzględniają wielkości liter.  
  
-   Operatory stosują C# niejawną semantykę konwersji wszędzie tam, gdzie to możliwe.  
  
-   Właściwości systemu są właściwościami publicznymi ujawnianymi w wystąpieniach [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .  
  
    Weź pod uwagę `IS [NOT] NULL` następujące semantyke:  
  
    -   `property IS NULL`jest oceniane tak `true` , jakby właściwość nie istnieje lub wartość właściwości jest. `null`  
  
### <a name="property-evaluation-semantics"></a>Semantyka oceny właściwości  
  
- Próba oszacowania nieistniejącej właściwości systemowej zgłasza wyjątek Filtruexception. [](/dotnet/api/microsoft.servicebus.messaging.filterexception)  
  
- Właściwość, która nie istnieje, jest obliczana wewnętrznie jako **nieznana**.  
  
  Nieznana Ocena w operatorach arytmetycznych:  
  
- W przypadku operatorów binarnych, jeśli po lewej i/lub prawej stronie operandów jest oceniane jako **nieznany**, wynik jest **nieznany**.  
  
- W przypadku operatorów jednoargumentowych, jeśli operand jest oceniany jako **nieznany**, wynik jest **nieznany**.  
  
  Nieznana Ocena w binarnych operatorach porównania:  
  
- Jeśli po lewej i/lub prawej stronie operandów jest oceniane jako **nieznany**, wynik jest **nieznany**.  
  
  Nieznana Ocena `[NOT] LIKE`w:  
  
- Jeśli którykolwiek operand jest oceniany jako **nieznany**, wynik jest **nieznany**.  
  
  Nieznana Ocena `[NOT] IN`w:  
  
- Jeśli argument operacji po lewej stronie jest oceniany jako **nieznany**, wynik jest **nieznany**.  
  
  Nieznana Ocena w operatorze **i** :  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Nieznana Ocena w operatorze **lub** :  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semantyka powiązania operatora
  
-   Operatory `>`porównania C# , takie jak `>=`, `<=` ,,`=` , i postępują według tej samej semantyki co powiązanie operatora w promocjach typu danych i konwersje niejawne. `<` `!=`  
  
-   Operatory `+` `-`arytmetyczne C# , takie jak, `/` `%` ,, i stosują tę samą semantykę jako powiązanie operatora w promocjach typu danych i konwersje niejawne. `*`

## <a name="next-steps"></a>Kolejne kroki

- [Klasa sqlfilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Klasa sqlfilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Klasa SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)

---
title: Odwołanie do składni Azure Service Bus sqlfilter | Microsoft Docs
description: Ten artykuł zawiera szczegółowe informacje na temat gramatyki xmlfilter. Element sqlfilter obsługuje podzestaw standardu SQL-92.
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
ms.openlocfilehash: d5a8e165fcee23c5feecd5935983dd77d3ec6c30
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759667"
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
  
-   `<scope>` jest opcjonalnym ciągiem wskazującym zakres `<property_name>`. Prawidłowe wartości to `sys` lub `user`. Wartość `sys` wskazuje zakres systemu, gdzie `<property_name>` jest publiczną nazwą właściwości [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` wskazuje zakres użytkownika, gdzie `<property_name>` jest kluczem słownika [klasy BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . zakres `user` jest domyślnym zakresem, jeśli nie określono `<scope>`.  
  
## <a name="remarks"></a>Uwagi

Próba uzyskania dostępu do nieistniejącej właściwości systemu jest błędem, podczas gdy próba uzyskania dostępu do nieistniejącej właściwości użytkownika nie jest błędem. Zamiast tego nieistniejąca Właściwość użytkownika jest obliczana wewnętrznie jako nieznana wartość. Nieznana wartość jest traktowana specjalnie podczas obliczania operatora.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenty  

 `<regular_identifier>` jest ciągiem przedstawionym przez następujące wyrażenie regularne:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Ta Gramatyka oznacza dowolny ciąg, który rozpoczyna się od litery, po której następuje jeden lub więcej podkreśleń/liter/cyfr.  
  
`[:IsLetter:]` oznacza dowolny znak Unicode, który jest kategoryzowany jako litera Unicode. `System.Char.IsLetter(c)` zwraca `true`, jeśli `c` jest literą Unicode.  
  
`[:IsDigit:]` oznacza dowolny znak Unicode, który jest kategoryzowany jako cyfra dziesiętna. `System.Char.IsDigit(c)` zwraca `true`, jeśli `c` jest cyfrą Unicode.  
  
`<regular_identifier>` nie może być zastrzeżonym słowem kluczowym.  
  
`<delimited_identifier>` to dowolny ciąg, który jest ujęty w nawiasy kwadratowe ([]). Prawy nawias kwadratowy jest reprezentowany jako dwa prawy nawias kwadratowy. Poniżej przedstawiono przykłady `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` to dowolny ciąg, który jest ujęty w znaki podwójnego cudzysłowu. Podwójny cudzysłów w identyfikatorze jest reprezentowany jako dwa podwójne cudzysłowy. Nie zaleca się używania identyfikatorów w cudzysłowie, ponieważ można je łatwo pomylić z stałą typu String. Jeśli to możliwe, należy użyć oddzielonego identyfikatora. Poniżej przedstawiono przykład `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>znaczne  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi
  
`<pattern>` musi być wyrażeniem, które jest oceniane jako ciąg. Jest używany jako wzorzec dla operatora LIKE.      Może zawierać następujące symbole wieloznaczne:  
  
-   `%`: dowolny ciąg składający się z zero lub więcej znaków.  
  
-   `_`: dowolny pojedynczy znak.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Uwagi  

`<escape_char>` musi być wyrażeniem, które jest oceniane jako ciąg o długości 1. Jest używany jako znak ucieczki dla operatora LIKE.  
  
 Na przykład `property LIKE 'ABC\%' ESCAPE '\'` dopasowuje `ABC%` zamiast ciągu rozpoczynającego się od `ABC`.  
  
## <a name="constant"></a>Stałe  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenty  
  
-   `<integer_constant>` jest ciągiem liczb, które nie są ujęte w cudzysłów i nie zawierają punktów dziesiętnych. Wartości są przechowywane jako `System.Int64` wewnętrznie i zgodne z tym samym zakresem.  
  
     Są to przykłady długich stałych:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` jest ciągiem liczb, które nie są ujęte w cudzysłów i zawierają separator dziesiętny. Wartości są przechowywane jako `System.Double` wewnętrznie i są zgodne z tym samym zakresem/dokładnością.  
  
     W przyszłej wersji ten numer może być przechowywany w innym typie danych w celu obsługi dokładnej semantyki liczb, dlatego nie należy polegać na tym, że podstawowy typ danych jest `System.Double` dla `<decimal_constant>`.  
  
     Poniżej przedstawiono przykłady stałych dziesiętnych:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` to liczba zapisywana w notacji wykładniczej. Wartości są przechowywane jako `System.Double` wewnętrznie i są zgodne z tym samym zakresem/dokładnością. Poniżej przedstawiono przykłady przybliżonych stałych liczbowych:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Uwagi  

Stałe logiczne są reprezentowane przez słowa kluczowe **true** lub **false**. Wartości są przechowywane jako `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Uwagi  

Stałe ciągów są ujęte w znaki pojedynczego cudzysłowu i zawierają wszystkie prawidłowe znaki Unicode. Pojedynczy cudzysłów osadzony w stałej ciągu jest reprezentowany jako dwa znaki pojedynczego cudzysłowu.  
  
## <a name="function"></a>funkcyjn  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Uwagi
  
Funkcja `newid()` zwraca element **System. GUID** wygenerowany przez metodę `System.Guid.NewGuid()`.  
  
Funkcja `property(name)` zwraca wartość właściwości, do której odwołuje się `name`. Wartość `name` może być dowolnym prawidłowym wyrażeniem zwracającym wartość ciągu.  
  
## <a name="considerations"></a>Zagadnienia do rozważenia
  
Rozważ użycie następujących semantyki [sqlfilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) :  
  
-   Nazwy właściwości nie uwzględniają wielkości liter.  
  
-   Operatory stosują C# niejawną semantykę konwersji wszędzie tam, gdzie to możliwe.  
  
-   Właściwości systemu są właściwościami publicznymi ujawnianymi w wystąpieniach [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .  
  
    Rozważmy następujące `IS [NOT] NULL` semantyki:  
  
    -   `property IS NULL` jest oceniane jako `true`, jeśli właściwość nie istnieje lub wartość właściwości jest `null`.  
  
### <a name="property-evaluation-semantics"></a>Semantyka oceny właściwości  
  
- Próba oszacowania nieistniejącej właściwości systemowej zgłasza wyjątek [filtruexception](/dotnet/api/microsoft.servicebus.messaging.filterexception) .  
  
- Właściwość, która nie istnieje, jest obliczana wewnętrznie jako **nieznana**.  
  
  Nieznana Ocena w operatorach arytmetycznych:  
  
- W przypadku operatorów binarnych, jeśli po lewej i/lub prawej stronie operandów jest oceniane jako **nieznany**, wynik jest **nieznany**.  
  
- W przypadku operatorów jednoargumentowych, jeśli operand jest oceniany jako **nieznany**, wynik jest **nieznany**.  
  
  Nieznana Ocena w binarnych operatorach porównania:  
  
- Jeśli po lewej i/lub prawej stronie operandów jest oceniane jako **nieznany**, wynik jest **nieznany**.  
  
  Nieznana Ocena w `[NOT] LIKE`:  
  
- Jeśli którykolwiek operand jest oceniany jako **nieznany**, wynik jest **nieznany**.  
  
  Nieznana Ocena w `[NOT] IN`:  
  
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
  
-   Operatory porównania, takie jak `>`, `>=`, `<`, `<=`, `!=`i `=`, są zgodne z tą samą semantyką jak powiązanie C# operatora w promocjach typu danych i konwersje niejawne.  
  
-   Operatory arytmetyczne, takie jak `+`, `-`, `*`, `/`i `%`, są zgodne z tą samą C# semantyką jak powiązanie operatora w promocjach typu danych i konwersje niejawne.

## <a name="next-steps"></a>Następne kroki

- [Klasa sqlfilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Klasa sqlfilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Klasa SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)

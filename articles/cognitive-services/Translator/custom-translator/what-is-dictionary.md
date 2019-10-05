---
title: Co to jest słownik? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Słownik jest dokumentem wyrównanym, który określa listę fraz lub zdań (i ich tłumaczenia), które są zawsze potrzebne do tłumaczenia usługi Microsoft Translator w taki sam sposób. Słowniki są czasami nazywane również bazami Glossaries lub terminami.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a4aac8afb7974be402ee98bb65c920133d4c118f
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947701"
---
# <a name="what-is-a-dictionary"></a>Co to jest słownik?

Słownik jest wyrównanym parę dokumentów, które określają listę fraz lub zdań i odpowiadające im tłumaczenia. Użyj słownika w ramach szkolenia, jeśli chcesz, aby program Microsoft Translator zawsze przetłumaczy wszystkie wystąpienia frazy źródłowej lub zdania przy użyciu tłumaczenia podanego w słowniku. Słowniki są czasami nazywane glossariesami lub terminami. Słownik można traktować jako "Kopiuj i Zamień" dla wszystkich wystawianych terminów. Ponadto usługa translatora niestandardowego firmy Microsoft kompiluje i korzysta z własnych słowników ogólnego przeznaczenia, aby zwiększyć jakość tłumaczenia. Niemniej jednak słownik dostarczony przez klienta jest poprzedni i będzie przeszukiwany w pierwszej kolejności w poszukiwaniu słów lub zdań.

Słowniki działają tylko w przypadku projektów w parach języka, które mają w pełni obsługiwany model sieci Microsoft General neuronowych Network. [Zapoznaj się z pełną listą języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Słownik fraz
Po dołączeniu słownika wyrazów w ramach szkolenia modelu wszystkie wymienione wyrazy lub frazy są tłumaczone w określony sposób. Pozostałe zdanie jest tłumaczone w zwykły sposób. Możesz użyć słownika frazy, aby określić frazy, które nie powinny być tłumaczone przez podanie tej samej nieprzetłumaczonej frazy w pliku źródłowym i docelowym w słowniku.

## <a name="sentence-dictionary"></a>Słownik zdań
Słownik zdań umożliwia określenie dokładnego tłumaczenia docelowego dla zdania źródłowego. Aby można było dopasować słownik zdań, całe przesłane zdanie musi być zgodne z wpisem słownika źródłowego.  Jeśli tylko część zdania jest zgodna, wpis nie będzie zgodny.  Po wykryciu dopasowania zostanie zwrócony wpis docelowy słownika zdań.

## <a name="dictionary-only-trainings"></a>Szkolenia dotyczące tylko słownika
Możesz nauczyć model przy użyciu tylko danych słownika. W tym celu zaznacz tylko dokument słownika (lub wiele dokumentów słownika), które chcesz dołączyć, i naciśnij pozycję Utwórz model. Ponieważ jest to szkolenie tylko do słownika, nie jest wymagana minimalna liczba zdań szkoleniowych. Model zwykle kończy szkolenia znacznie szybciej niż w przypadku standardowego szkolenia.  Modele powstające będą używać modeli bazowych firmy Microsoft do tłumaczenia z dodaniem dodanych słowników.  Nie otrzymasz raportu testowego.

>[!Note]
>W przypadku translatora niestandardowego nie są wyrównania plików słowników, dlatego ważne jest, aby w dokumentach słownika była równa Liczba zwrotów źródłowych i docelowych, a także dokładnie wyrównane.

## <a name="recommendations"></a>Mając

- Słowniki nie stanowią zamiennika szkolenia modelu przy użyciu danych szkoleniowych. Zaleca się ich uniknięcie i umożliwienie systemowi uczenia się od danych szkoleniowych. Jednak gdy zdania lub rzeczowniki złożone muszą być renderowane jako-is, należy użyć słownika.
- Słownik wyrazów powinien być oszczędnie używany. Należy więc pamiętać, że gdy fraza w zdaniu zostanie zastąpiona, kontekst w tym zdaniu zostanie utracony lub ograniczony do przetłumaczenia reszty zdania. Wynikiem tego jest to, że podczas gdy fraza lub wyraz w zdaniu zostanie przetłumaczy zgodnie z podanym słownikiem, ogólna jakość tłumaczenia zdania często się odnosi.
- Słownik fraz dobrze sprawdza się w przypadku niezłożonej rzeczowników, takich jak nazwy produktów ("Microsoft SQL Server"), odpowiednie nazwy ("miasto Hamburg") lub funkcje produktu ("tabela przestawna"). Nie działa równie dobrze w przypadku czasowników lub przymiotników, ponieważ są zwykle wysoce oddzielone w źródle lub w języku docelowym. Najlepsze praktyki polegają na uniknięciu wpisów słownika zwrotów dla niczego, ale rzeczowników złożonych.
- W przypadku korzystania z słownika, wielkie litery i interpunkcja są ważne. Wpisy słownika będą pasować tylko do wyrazów i fraz, które mają taką samą wielką literę i znaki interpunkcyjne jak wpis znaleziony w słowniku. Twoje tłumaczenia będą odzwierciedlać wielkie litery i znaki interpunkcyjne podane w stronie docelowej pliku słownika. Załóżmy na przykład, że został przeszkolony model angielski do hiszpański, przy użyciu słownika, który określił "Hello" w pliku źródłowym do tłumaczenia na "Buenos Dias" w pliku docelowym. Po zażądaniu tłumaczenia zdania zawierającego "Hello" system przeszuka najpierw słownik i znajdzie dopasowanie ("Witaj") i zwróci "Buenos Dias" w końcowym tłumaczeniu.
- Jeśli słowo pojawia się więcej niż raz w pliku słownika, system zawsze będzie używać ostatniego dostarczonego wpisu. W związku z tym słownik nie powinien zawierać wielu tłumaczeń tego samego wyrazu.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [wytycznych dotyczących formatów dokumentów](document-formats-naming-convention.md).

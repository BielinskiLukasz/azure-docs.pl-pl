---
title: Parowanie i wyrównywanie zdań — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: W trakcie wykonywania szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Translator niestandardowy uzyskuje tłumaczenie jednego zdania jednocześnie, odczytując zdanie, tłumaczenie tego zdania. Następnie wyrównuje słowa i frazy w tych dwóch zdaniach.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: adbc21c3e963a98a8482de0c26bf5e257f43013e
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675451"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Parowanie i wyrównanie zdań w dokumentach równoległych

W trakcie szkolenia zdania obecne w dokumentach równoległych są sparowane lub wyrównane. Translator niestandardowy zgłasza liczbę zdań, które było w stanie sparować jako wyrównane zdania w każdym z zestawów danych.

## <a name="pairing-and-alignment-process"></a>Proces parowania i wyrównywania

Translator niestandardowy uzyskuje translację zdań z jednego zdania w danym momencie. Odczytuje zdanie z lokalizacji źródłowej, a następnie tłumaczenie tego zdania z celu. Następnie wyrównuje słowa i frazy w tych dwóch zdaniach. Ten proces umożliwia działowi IT utworzenie mapy wyrazów i fraz w jednym zdaniu do równoważnych słów i fraz w tłumaczeniu tego zdania. Wyrównania próbuje upewnić się, że system pociąga za sobą tłumaczenie.

## <a name="pre-aligned-documents"></a>Wstępnie wyrównane dokumenty

Jeśli wiesz, że masz dokumenty równoległe, możesz zastąpić wyrównanie zdania, dostarczając wstępnie wyrównane pliki tekstowe. Można wyodrębnić wszystkie zdania z dokumentów do pliku tekstowego, zorganizować jedno zdanie na wiersz i przekazać je za pomocą rozszerzenia `.align`. Rozszerzenie `.align` informuje niestandardową funkcję translatora, że powinna pominąć wyrównanie zdania.

Aby uzyskać najlepsze wyniki, należy się upewnić, że w plikach znajduje się jedno zdanie na wiersz. Nie mają znaków nowego wiersza w obrębie zdania, ponieważ spowoduje to słabą wyrównania.

## <a name="suggested-minimum-number-of-sentences"></a>Sugerowana minimalna liczba zdań

Aby szkolenie powiodło się, w poniższej tabeli przedstawiono minimalną liczbę zdań wymaganych w każdym typie dokumentu. To ograniczenie jest środkiem bezpieczeństwa, aby upewnić się, że zdania równoległe zawierają wystarczającą liczbę unikatowych słownictwa, aby pomyślnie szkolić model tłumaczenia. Ogólne wytyczne mają więcej niż równoległe zdania z jakością tłumaczenia ludzkiego powinny generować wyższe modele jakości.

| Typ dokumentu   | Sugerowana minimalna liczba zdań | Maksymalna liczba zdań |
|------------|--------------------------------------------|--------------------------------|
| Szkolenie   | 10 000                                     | Brak górnego limitu                 |
| BIÓR     | 5000                                      | 2 500                          |
| Testowanie    | 5000                                      | 2 500                          |
| Słownik | 0                                          | Brak górnego limitu                 |

> [!NOTE]
> - Szkolenie nie zostanie uruchomione i zakończy się niepowodzeniem, jeśli nie spełniono minimalnej liczby zdań w 10 000 dla szkolenia. 
> - Dostrajanie i testowanie są opcjonalne. Jeśli ich nie podasz, system usunie odpowiedni odsetek z szkolenia do użycia w celu weryfikacji i testowania. 
> - Możesz nauczyć model przy użyciu tylko danych słownika. Zapoznaj się z informacjami o [słowniku](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [słownika](what-is-dictionary.md) w translatoru niestandardowym.

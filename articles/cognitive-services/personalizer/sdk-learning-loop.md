---
title: 'Szybki Start: Tworzenie i używanie pętli szkoleniowej z zestawem SDK — Personalizacja'
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia bazy wiedzy i zarządzania nią przy użyciu zestawu SDK klienta.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: e09476bc084465cf08087a3200d8b7d663b0685e
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122908"
---
# <a name="quickstart-personalizer-client-library"></a>Szybki Start: Biblioteka kliencka personalizacji

Wyświetl spersonalizowaną zawartość w tym przewodniku szybki start dla języka Python za pomocą usługi personalizacji.

Rozpocznij pracę z biblioteką klienta personalizacji. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

 * Ranga API — wybiera najlepszy element z elementów zawartości, na podstawie informacji w czasie rzeczywistym dostarczanych na temat zawartości i kontekstu.
 * Interfejs API nagradzania — możesz określić wynik nagrody w zależności od potrzeb firmy, a następnie wysłać go do personalizacji przy użyciu tego interfejsu API. Ten wynik może być pojedynczą wartością, taką jak 1 dla dobra, i 0 dla uszkodzonych lub algorytmem tworzonym w zależności od potrzeb firmy.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

::: zone pivot="programming-language-nodejs"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
>[Jak działa Personalizacja](how-personalizer-works.md)

* [Co to jest Personalizacja?](what-is-personalizer.md)
* [Gdzie można używać personalizacji?](where-can-you-use-personalizer.md)
* [Rozwiązywanie problemów](troubleshooting.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).

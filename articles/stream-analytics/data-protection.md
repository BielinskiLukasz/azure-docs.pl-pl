---
title: Ochrona danych w Azure Stream Analytics
description: W tym artykule wyjaśniono, jak szyfrować dane prywatne używane przez zadanie Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 34cbe5f23728c1fcff0555256575cc34b2b6a869
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079852"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Ochrona danych w Azure Stream Analytics 

Azure Stream Analytics to w pełni zarządzana platforma jako usługa, która umożliwia tworzenie potoków analizy w czasie rzeczywistym. Wszystkie duże funkcje, takie jak inicjowanie obsługi klastrów, skalowanie węzłów do użycia i zarządzanie wewnętrznymi punktami kontrolnymi, są zarządzane w tle.

## <a name="encrypt-your-data"></a>Szyfrowanie danych

Stream Analytics automatycznie korzysta z najlepszych w swojej klasie standardów szyfrowania w celu zaszyfrowania i zabezpieczenia danych. Możesz po prostu zaufać Stream Analytics, aby bezpiecznie przechowywać wszystkie dane, dzięki czemu nie musisz martwić się o zarządzanie infrastrukturą.

Jeśli chcesz używać kluczy zarządzanych przez klienta (CMK) do szyfrowania danych, możesz użyć własnego konta magazynu (ogólnego przeznaczenia w wersji 1 lub v2) do przechowywania wszystkich prywatnych zasobów danych, które są wymagane przez środowisko uruchomieniowe Stream Analytics. Twoje konto magazynu może być zaszyfrowane zgodnie z potrzebami. Żaden z Twoich prywatnych zasobów danych nie jest trwale przechowywany przez infrastrukturę Stream Analytics. 

To ustawienie należy skonfigurować w momencie Stream Analytics tworzenia zadania i nie można go modyfikować w cyklu życia zadania. Nie zaleca się modyfikowania ani usuwania magazynu używanego przez Stream Analytics. Usunięcie konta magazynu spowoduje trwałe usunięcie wszystkich prywatnych zasobów danych, co spowoduje niepowodzenie zadania. 

Nie można zaktualizować ani obrócić kluczy do konta magazynu przy użyciu portalu Stream Analytics. Klucze można aktualizować za pomocą interfejsów API REST.


## <a name="configure-storage-account-for-private-data"></a>Skonfiguruj konto magazynu dla prywatnych danych 

Wykonaj poniższe kroki, aby skonfigurować konto magazynu dla prywatnych zasobów danych. Ta konfiguracja została wykonana z zadania Stream Analytics, a nie z konta magazynu.

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**. 

1. Wybierz pozycję **analiza** > **Stream Analytics zadania** z listy wyników. 

1. Wypełnij stronę Stream Analytics zadania, aby uzyskać niezbędne szczegóły, takie jak nazwa, region i skala. 

1. Zaznacz pole wyboru informujące o *zabezpieczeniu wszystkich prywatnych zasobów danych wymaganych przez to zadanie na koncie magazynu*.

1. Wybierz konto magazynu z subskrypcji. Należy pamiętać, że tego ustawienia nie można modyfikować w całym cyklu życia zadania. 

   ![Ustawienia konta prywatnego magazynu danych](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Prywatne zasoby danych, które są przechowywane

Wszystkie prywatne dane, które są wymagane do utrwalenia przez Stream Analytics są przechowywane na koncie magazynu. Przykłady prywatnych zasobów danych obejmują: 

* Zapytania, które zostały utworzone i powiązane z nimi konfiguracje  

* Funkcje zdefiniowane przez użytkownika 

* Wyniki próbkowania danych z wejść 

* Punkty kontrolne potrzebne przez środowisko uruchomieniowe Stream Analytics

* Migawki danych referencyjnych 

Są również przechowywane szczegóły połączenia zasobów, które są używane przez zadanie Stream Analytics. Zaszyfruj konto magazynu, aby zabezpieczyć wszystkie Twoje dane. 

Aby dowiedzieć się, jak zapewnić zgodność z wymaganiami dotyczącymi zgodności w dowolnych sektorach lub środowiskach objętych regulacją, możesz przeczytać więcej na temat [ofert zgodności firmy Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="next-steps"></a>Następne kroki

* [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md)
* [Informacje o danych wejściowych dla Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Pojęcia dotyczące punktów kontrolnych i powtarzania w zadaniach Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Używanie danych referencyjnych do wyszukiwania w Stream Analytics](stream-analytics-use-reference-data.md)

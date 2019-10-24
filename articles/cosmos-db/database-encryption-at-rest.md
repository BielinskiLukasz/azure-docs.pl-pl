---
title: Szyfrowanie w spoczynku w Azure Cosmos DB
description: Dowiedz się, jak Azure Cosmos DB zapewnia szyfrowanie danych w czasie spoczynku i sposób ich implementacji.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: a9e89336973b0b13544c5bc0bccec41652c6952e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755103"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Szyfrowanie danych w Azure Cosmos DB 

Szyfrowanie w spoczynku to fraza, która często odnosi się do szyfrowania danych na nietrwałych urządzeniach magazynujących, takich jak dyski półprzewodnikowe (dysków SSD) i dyski twarde (HDD). Cosmos DB przechowuje podstawowe bazy danych w dysków SSD. Jego załączniki i kopie zapasowe są przechowywane w usłudze Azure Blob Storage, która jest zazwyczaj tworzona przy użyciu HDD. Dzięki wykorzystaniu szyfrowania dla Cosmos DB wszystkie bazy danych, załączniki multimedialne i kopie zapasowe są szyfrowane. Dane są teraz szyfrowane w trakcie przesyłania (za pośrednictwem sieci) i w spoczynku (w magazynie nietrwałym), co zapewnia kompleksowe szyfrowanie.

Cosmos DB usługa PaaS jest bardzo łatwa w użyciu. Ponieważ wszystkie dane użytkownika przechowywane w Cosmos DB są szyfrowane w spoczynku i w transporcie, nie trzeba podejmować żadnych działań. Innym sposobem na umieszczenie tego ustawienia jest to, że szyfrowanie w stanie spoczynku jest domyślnie włączone. Brak kontrolek do wyłączenia lub włączenia. Azure Cosmos DB używa szyfrowania AES-256 we wszystkich regionach, w których konto jest uruchomione. Udostępniamy tę funkcję, gdy będziemy nadal sprostać naszym [umowy slaom dostępności i wydajności](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementacja szyfrowania w stanie spoczynku dla Azure Cosmos DB

Szyfrowanie w spoczynku jest implementowane przy użyciu wielu technologii zabezpieczeń, w tym bezpiecznych systemów magazynowania kluczy, szyfrowanych sieci i kryptograficznych interfejsów API. Systemy, które odszyfrowują i przetwarzają dane, muszą komunikować się z systemami zarządzanymi kluczami. Na diagramie przedstawiono sposób przechowywania zaszyfrowanych danych i zarządzania kluczami. 

![Diagram projektu](./media/database-encryption-at-rest/design-diagram.png)

Podstawowy przepływ żądania użytkownika jest następujący:
- Konto bazy danych użytkownika jest gotowe, a klucze magazynu są pobierane za pośrednictwem żądania do dostawcy zasobów usługi zarządzania.
- Użytkownik tworzy połączenie do Cosmos DB za pośrednictwem protokołu HTTPS/Secure transport. (Zestawy SDK deabstrakcyjne szczegóły).
- Użytkownik wysyła dokument JSON, który będzie przechowywany za pośrednictwem wcześniej utworzonego bezpiecznego połączenia.
- Dokument JSON jest indeksowany, chyba że użytkownik wyłączył indeksowanie.
- Zarówno dokument JSON, jak i dane indeksu są zapisywane w bezpiecznym magazynie.
- Okresowo dane są odczytywane z bezpiecznego magazynu i tworzone przy użyciu kopii zapasowej w zaszyfrowanym magazynie obiektów blob platformy Azure.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: ile więcej kosztuje usługa Azure Storage, jeśli szyfrowanie usługi Storage jest włączony?
Odp.: nie ma dodatkowych kosztów.

### <a name="q-who-manages-the-encryption-keys"></a>P: kto zarządza kluczami szyfrowania?
Odp.: klucze są zarządzane przez firmę Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: jak często są obracane klucze szyfrowania?
Odp.: Firma Microsoft ma zestaw wewnętrznych wytycznych dotyczących rotacji kluczy szyfrowania, który Cosmos DB poniżej. Określone wskazówki nie są publikowane. Firma Microsoft publikuje [cykl projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/default.aspx), który jest traktowany jako podzbiór wskazówek wewnętrznych i ma przydatne najlepsze rozwiązania dla deweloperów.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Czy mogę użyć własnych kluczy szyfrowania?
Odp.: Cosmos DB jest usługą PaaS, a firma Microsoft pracowała nad tym, aby usługa była łatwa do użycia. Zauważyliśmy, że to pytanie jest często pytane jako pytanie serwera proxy w celu spełnienia wymagań dotyczących zgodności, takich jak PCI-DSS. W ramach tworzenia tej funkcji współpracowały z audytorami zgodności, aby upewnić się, że klienci, którzy korzystają z Cosmos DB spełniają wymagania, bez konieczności zarządzania kluczami.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: w jakich regionach włączono szyfrowanie?
Odp.: wszystkie regiony Azure Cosmos DB mają włączone szyfrowanie dla wszystkich danych użytkownika.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: czy szyfrowanie ma wpływ na opóźnienia wydajności i umowy SLA przepływności?
Odp.: nie ma żadnego wpływu lub zmiany wydajności umowy SLA teraz, że szyfrowanie w spoczynku jest włączone dla wszystkich istniejących i nowych kont. Więcej informacji można znaleźć na stronie [SLA dla Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) , aby zobaczyć najnowsze gwarancje.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: czy emulator lokalny obsługuje szyfrowanie w spoczynku?
Odp.: emulator jest autonomicznym narzędziem deweloperskim/testowym i nie korzysta z usług zarządzania kluczami, których używa usługa zarządzana Cosmos DB. Naszym zaleceniem jest włączenie funkcji BitLocker na dyskach, na których przechowywane są poufne dane testów emulatora. [Emulator obsługuje Zmienianie domyślnego katalogu danych](local-emulator.md) , a także przy użyciu dobrze znanej lokalizacji.

## <a name="next-steps"></a>Następne kroki

Omówienie zabezpieczeń Cosmos DB i najnowszych ulepszeń można znaleźć w temacie [zabezpieczenia usługi Azure Cosmos Database](database-security.md).
Aby uzyskać więcej informacji o certyfikatach firmy Microsoft, zobacz [Centrum zaufania Azure](https://azure.microsoft.com/support/trust-center/).

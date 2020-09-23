---
title: Skalowanie grupy serwerów — Citus — Azure Database for PostgreSQL
description: Dostosowywanie pamięci grupy serwerów, dysku i zasobów procesora CPU w celu zwiększenia obciążenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 5b1a5da688b162c85d2be8580e29dc6ee9db6d40
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906470"
---
# <a name="server-group-size"></a>Rozmiar grupy serwerów

Opcja wdrożenia Citus) używa współpracujących serwerów baz danych, aby zrównoleglanie wykonywanie zapytań i przechowywać więcej danych. Grupa serwerów "size" odnosi się zarówno do liczby serwerów, jak i zasobów sprzętowych każdego z nich.

## <a name="picking-initial-size"></a>Pobieranie początkowego rozmiaru

Rozmiar grupy serwerów, w sensie liczby węzłów i ich pojemności sprzętowej, można łatwo zmienić ([patrz poniżej](#scale-a-hyperscale-citus-server-group)). Jednak nadal trzeba wybrać rozmiar początkowy nowej grupy serwerów. Poniżej przedstawiono niektóre wskazówki dotyczące rozsądnego wyboru.

### <a name="multi-tenant-saas-use-case"></a>SaaS użycia wielu dzierżawców

W przypadku migracji do Citus z istniejącego wystąpienia bazy danych z jednym węzłem PostgreSQL zaleca się wybranie klastra, w którym liczba rdzeni wirtualnych procesów roboczych i pamięć RAM łącznie jest równa pierwotnemu wystąpieniu. W takich scenariuszach pojawiły się następujące udoskonalenia wydajności, ponieważ fragmentowania zwiększa wykorzystanie zasobów, co pozwala na mniejsze indeksy itd.

Liczba rdzeni wirtualnych wymaganych przez węzeł koordynatora zależy od istniejącego obciążenia (przepływność zapisu/odczytu). Węzeł koordynatora nie wymaga tak dużej ilości pamięci RAM jako węzłów procesu roboczego, ale alokacja pamięci RAM jest określana na podstawie liczby rdzeń wirtualny (zgodnie z opisem w oknie [Opcje konfiguracji w ramach skalowania](concepts-hyperscale-configuration-options.md)), więc licznik rdzeń wirtualny jest zasadniczo rzeczywistą decyzją.

### <a name="real-time-analytics-use-case"></a>Przypadek użycia analizy w czasie rzeczywistym

Łącznie rdzeni wirtualnych: gdy praca danych mieści się w pamięci RAM, można spodziewać się liniowe zwiększenie wydajności na potrzeby skalowania (Citus) proporcjonalnie do liczby rdzeni roboczych. Aby określić odpowiednią liczbę rdzeni wirtualnych dla potrzeb, należy wziąć pod uwagę bieżące opóźnienie dla zapytań w bazie danych z jednym węzłem i wymagane opóźnienie w ramach funkcji (Citus). Podziel bieżące opóźnienie przez żądane opóźnienie i zaokrąglij wynik.

Pamięć RAM procesu roboczego: najlepszym rozwiązaniem jest zapewnienie wystarczającej ilości pamięci, aby większość zestawu roboczego mieściła się w pamięci. Typ zapytań, które są używane przez aplikację, wpływa na wymagania dotyczące pamięci. Można uruchomić wyjaśnienie analizowanie zapytania, aby określić, ile pamięci wymaga. Należy pamiętać, że rdzeni wirtualnych i pamięć RAM są skalowane ze sobą zgodnie z opisem w artykule [Opcje konfiguracji w ramach skalowania](concepts-hyperscale-configuration-options.md) .

## <a name="scale-a-hyperscale-citus-server-group"></a>Skalowanie grupy serwerów ze skalą (Citus)

Azure Database for PostgreSQL-Citus) oferuje skalowanie samoobsługowe umożliwiające zwiększenie obciążenia. Azure Portal ułatwia dodawanie nowych węzłów procesu roboczego i zwiększanie rdzeni wirtualnych istniejących węzłów. Dodawanie węzłów nie powoduje przestoju, a nawet przeniesienie fragmentów do nowych węzłów (o nazwie [fragmentu Rebalancing](#rebalance-shards)) odbywa się bez przerywania zapytań.

### <a name="add-worker-nodes"></a>Dodaj węzły procesu roboczego

Aby dodać węzły, przejdź do karty **obliczenia + magazyn** w grupie serwerów Citus.  Przeciągnięcie suwaka dla **liczby węzłów roboczych** powoduje zmianę wartości.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Suwaki zasobów":::

Kliknij przycisk **Zapisz** , aby zmiana wartości zaczęła obowiązywać.

> [!NOTE]
> Po zwiększeniu i zapisaniu liczba węzłów procesu roboczego nie może być obniżona przy użyciu suwaka.

#### <a name="rebalance-shards"></a>Ponowne równoważenie fragmentów

Aby skorzystać z nowo dodanych węzłów, należy ponownie zrównoważyć rozproszoną tabelę [fragmentów](concepts-hyperscale-distributed-data.md#shards), co oznacza przeniesienie niektórych fragmentów z istniejących węzłów do nowych. Najpierw sprawdź, czy nowi pracownicy pomyślnie ukończyli Inicjowanie obsługi administracyjnej. Następnie należy uruchomić moduł równoważenia fragmentu, łącząc się z węzłem koordynatora klastra z PSQL i uruchamiając następujące polecenie:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards`Funkcja ponownie równoważy wszystkie tabele w grupie wspólnej [lokalizacji](concepts-hyperscale-colocation.md) tabeli o nazwie w jej argumencie. W ten sposób nie trzeba wywoływać funkcji dla każdej tabeli rozproszonej, po prostu wywołaj ją na reprezentatywnej tabeli z każdej grupy wspólnej lokalizacji.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Zwiększ lub Zmniejsz rdzeni wirtualnych na węzłach

> [!NOTE]
> Ta funkcja jest obecnie w wersji zapoznawczej. Aby zażądać zmiany w rdzeni wirtualnych dla węzłów w grupie serwerów, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Oprócz dodawania nowych węzłów można zwiększyć możliwości istniejących węzłów. Dostosowanie pojemności obliczeniowej w górę i w dół może być przydatne w przypadku eksperymentów dotyczących wydajności, a także krótko-lub długoterminowych zmian w zakresie ruchu.

Aby zmienić rdzeni wirtualnych dla wszystkich węzłów procesu roboczego, Dostosuj suwak **rdzeni wirtualnych** w obszarze **Konfiguracja (na węzeł procesu roboczego)**. Rdzeni wirtualnych węzła koordynatora można dostosowywać niezależnie. Dostosuj suwak **rdzeni wirtualnych** w obszarze  **Konfiguracja (węzeł koordynatora)**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.

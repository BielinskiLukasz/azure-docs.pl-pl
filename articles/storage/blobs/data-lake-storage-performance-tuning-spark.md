---
title: 'Dostrajanie wydajności: Spark, & HDInsight Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Wskazówki dotyczące dostrajania wydajności Azure Data Lake Storage Gen2 Spark
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b28765c9ac4fa664b84c456c31ee10e0e9e19003
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465934"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: Spark, & usługi HDInsight Azure Data Lake Storage Gen2

Podczas dostrajania wydajności na platformie Spark należy wziąć pod uwagę liczbę aplikacji, które będą działać w klastrze.  Domyślnie można uruchamiać 4 aplikacje jednocześnie w klastrze HDI (Uwaga: ustawienie domyślne może ulec zmianie).  Możesz zdecydować się na użycie mniejszej liczby aplikacji, aby można było zastąpić ustawienia domyślne i używać więcej klastrów dla tych aplikacji.  

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Upewnij się, że Pulpit zdalny dla klastra są włączone.
* **Uruchamianie klastra Spark na Data Lake Storage Gen2**.  Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight Spark do analizowania danych w Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Wskazówki dotyczące dostrajania wydajności na Data Lake Storage Gen2**.  Ogólne pojęcia dotyczące wydajności znajdują się w temacie [Data Lake Storage Gen2 wskazówki dotyczące dostrajania wydajności](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Parametry

W przypadku uruchamiania zadań platformy Spark poniżej przedstawiono najważniejsze ustawienia, które można dostrajać w celu zwiększenia wydajności Data Lake Storage Gen2:

* **NUM-wykonawcze** — liczba współbieżnych zadań, które można wykonać.

* Moduł **wykonawczy pamięci** — ilość pamięci przydzieloną do każdego wykonawcy.

* Program **wykonujący rdzenie** — liczbę rdzeni przypisanych do każdego wykonawcy.                     

**NUM-wykonawcy** Program NUM-wykonawczy ustawi maksymalną liczbę zadań, które mogą być uruchamiane równolegle.  Rzeczywista liczba zadań, które mogą być uruchamiane równolegle, jest ograniczona przez zasoby pamięci i procesora CPU dostępne w klastrze.

**Wykonawca pamięci** Jest to ilość pamięci, która jest przydzielna do każdego wykonawcy.  Pamięć wymagana dla każdego wykonawcy zależy od zadania.  W przypadku złożonych operacji pamięć musi być wyższa.  W przypadku prostych operacji, takich jak Odczyt i zapis, wymagania dotyczące pamięci będą mniejsze.  Ilość pamięci dla każdego wykonawcy można wyświetlić w Ambari.  W Ambari przejdź do platformy Spark i Wyświetl kartę konfiguracje.  

Program **wykonujący rdzenie** Ustawia liczbę rdzeni używanych dla wykonawcy, który określa liczbę równoległych wątków, które mogą być uruchamiane na wykonawcę.  Na przykład jeśli program wykonujący rdzenie = 2, każdy wykonawca może uruchomić 2 równoległe zadania w programie wykonującym.  Wymagające rdzeni wykonawcze będą zależne od zadania.  Duże zadania we/wy nie wymagają dużej ilości pamięci na zadanie, więc każdy wykonawca może obsłużyć więcej zadań równoległych.

Domyślnie dwie wirtualne rdzenie PRZĘDZy są definiowane dla każdego fizycznego rdzenia podczas uruchamiania platformy Spark w usłudze HDInsight.  Ta liczba zapewnia dobry balans współbieżności i liczby przełączeń kontekstu z wielu wątków.  

## <a name="guidance"></a>Wskazówki

Podczas uruchamiania obciążeń analitycznych platformy Spark w celu pracy z danymi w Data Lake Storage Gen2 zalecamy użycie najnowszej wersji usługi HDInsight w celu uzyskania najlepszej wydajności z Data Lake Storage Gen2. Gdy zadanie ma większą liczbę operacji we/wy, można skonfigurować pewne parametry w celu zwiększenia wydajności.  Data Lake Storage Gen2 to wysoce skalowalna platforma magazynu, która może obsługiwać wysoką przepływność.  Jeśli zadanie polega głównie na odczycie lub zapisie, zwiększenie współbieżności dla operacji we/wy do i z Data Lake Storage Gen2 może zwiększyć wydajność.

Istnieje kilka ogólnych sposobów zwiększania współbieżności zadań intensywnie korzystających z operacji we/wy.

**Krok 1. określenie, ile aplikacji jest uruchomionych w klastrze** — należy wiedzieć, ile aplikacji jest uruchomionych w klastrze, w tym dla bieżącego.  Wartości domyślne dla poszczególnych ustawień platformy Spark zakładają, że istnieją 4 aplikacje jednocześnie uruchomione.  W związku z tym dla każdej aplikacji będzie dostępny tylko 25% klastra.  Aby uzyskać lepszą wydajność, można zastąpić wartości domyślne, zmieniając liczbę modułów wykonujących.  

**Krok 2. Ustawianie pamięci programu wykonującego** — w pierwszej kolejności, w której ma zostać ustawiony moduł wykonawczy pamięci.  Pamięć będzie zależna od zadania, które ma zostać uruchomione.  Współbieżność można zwiększyć, przydzielając mniejszą ilość pamięci na wykonawcę.  Jeśli podczas uruchamiania zadania widzisz wyjątki dotyczące braku pamięci, należy zwiększyć wartość tego parametru.  Alternatywą jest uzyskanie większej ilości pamięci przy użyciu klastra o większej ilości pamięci lub zwiększenie rozmiaru klastra.  Większa ilość pamięci umożliwi użycie większej liczby modułów wykonujących, co oznacza większą współbieżność.

**Krok 3. Ustawianie rdzeni wykonawców** — w przypadku obciążeń intensywnie korzystających z operacji we/wy, które nie mają złożonych działań, warto zacząć od dużej liczby rdzeni wykonawców, aby zwiększyć liczbę zadań równoległych na wykonawcę.  Ustawienie "wykonawca-rdzenie na 4" jest dobrym uruchomieniem.   

    executor-cores = 4
Zwiększenie liczby rdzeni programu wykonującego zapewnia większą równoległość, dzięki czemu można eksperymentować z różnymi rdzeniami wykonawcy.  W przypadku zadań, które mają bardziej skomplikowane operacje, należy zmniejszyć liczbę rdzeni na wykonawcę.  Jeśli liczba rdzeni programu wykonującego jest większa niż 4, wówczas wyrzucanie elementów bezużytecznych może stać się niewydajne i spadek wydajności.

**Krok 4. Określanie ilości pamięci przędzy w klastrze** — te informacje są dostępne w Ambari.  Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje.  W tym oknie zostanie wyświetlona pamięć PRZĘDZy.  
Pamiętaj, że w oknie można także zobaczyć domyślny rozmiar kontenera PRZĘDZy.  Rozmiar kontenera PRZĘDZy jest taki sam jak pamięć na parametr wykonawcy.

    Total YARN memory = nodes * YARN memory per node
**Krok 5. Obliczanie NUM-wykonawców**

**Obliczanie ograniczenia pamięci** — parametr NUM-wykonawczy jest ograniczany przez pamięć lub przez procesor CPU.  Ograniczenie pamięci zależy od ilości dostępnej pamięci PRZĘDZy dla aplikacji.  Należy pobrać łączną ilość pamięci PRZĘDZy i podzielić ją przez program wykonujący pamięć.  Ograniczenie musi być w nieskalowanej liczbie aplikacji, aby można było podzielić je na liczbę aplikacji.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Oblicz ograniczenie procesora CPU** — ograniczenie procesora CPU jest obliczane jako łączne rdzenie wirtualne podzielone przez liczbę rdzeni na wykonawcę.  Każdy rdzeń fizyczny ma 2 rdzenie wirtualne.  Podobnie jak w przypadku ograniczenia pamięci, musimy podzielić na liczbę aplikacji.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Ustaw NUM-wykonawcze** — parametr NUM-wykonawcs jest określany przez pobranie minimalnej wartości ograniczenia pamięci i ograniczenia procesora CPU. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Ustawienie większej liczby programów wykonujących liczbę NUM-wykonawców nie musi zwiększyć wydajności.  Należy wziąć pod uwagę, że dodanie większej liczby modułów wykonujących spowoduje dodanie dodatkowych obciążeń dla każdego dodatkowego wykonawcy, co może spowodować spadek wydajności.  Liczba modułów wykonujących jest ograniczona przez zasoby klastra.    

## <a name="example-calculation"></a>Przykładowe obliczenie

Załóżmy, że obecnie klaster składa się z 8 D4v2 węzłów, w których jest uruchomionych 2 aplikacji, w tym tych, które mają być uruchamiane.  

**Krok 1. określenie, ile aplikacji jest uruchomionych w klastrze** — masz 2 aplikacje w klastrze, w tym te, które chcesz uruchomić.  

**Krok 2. Ustawianie pamięci programu wykonującego** — w tym przykładzie określimy, że 6 GB pamięci podręcznej jest wystarczająca dla zadania intensywnie korzystających z operacji we/wy.  

    executor-memory = 6GB
**Krok 3. Ustawianie rdzeni wykonawcy** — ponieważ jest to zadanie intensywnie korzystające z operacji we/wy, można ustawić liczbę rdzeni dla każdego wykonawcy na 4.  Ustawienie rdzeni na wykonawcę do większej niż 4 może spowodować problemy z wyrzucaniem elementów bezużytecznych.  

    executor-cores = 4
**Krok 4. określenie ilości pamięci przędzy w klastrze** — Ambari, aby dowiedzieć się, że każdy D4V2 ma 25 GB pamięci przędzenia.  Ponieważ istnieją 8 węzłów, dostępna pamięć PRZĘDZy jest mnożona przez 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Krok 5. Obliczanie liczby programów wykonujących liczbę-wykonawcze** — parametr NUM-wykonawczy jest określany przez przejęcie minimalnej wartości ograniczenia pamięci i ograniczenia procesora CPU podzielone przez liczbę aplikacji uruchomionych na platformie Spark.    

**Ograniczenie pamięci** — ograniczenie pamięci jest obliczane jako łączna ilość pamięci przędzy podzielona przez pamięć na wykonawcę.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Obliczanie ograniczenia procesora CPU** — ograniczenie procesora CPU jest obliczane jako łączna liczba rdzeni przędzy podzielona przez liczbę rdzeni na wykonawcę.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Ustaw NUM-wykonawcze**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    


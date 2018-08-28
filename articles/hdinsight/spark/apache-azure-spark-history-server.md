---
title: Użyj rozszerzonej serwer historii platformy Spark na debugowanie i diagnozowanie aplikacji platformy Spark — Azure HDInsight
description: Użyj rozszerzonej serwer historii platformy Spark na debugowanie i diagnozowanie aplikacji platformy Spark — Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2018
ms.openlocfilehash: b514f23f2e8a43f99fd5bf5c3afb5ed625ad4472
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046579"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Użyj rozszerzonej serwer historii platformy Spark, aby debugowanie i diagnozowanie aplikacji Spark

Ten artykuł zawiera wskazówki dotyczące sposobu używania rozszerzony serwer historii platformy Spark do debugowania i diagnozowania ukończone i uruchamianie aplikacji platformy Spark. Rozszerzenie zawiera obecnie karty danych i grafu. Na karcie dane użytkowników można sprawdzić dane wejściowe i wyjściowe zadań platformy Spark. Na karcie Wykres użytkowników można sprawdzić przepływu danych oraz powtarzania Graf zadania.

## <a name="open-the-spark-history-server"></a>Otwórz serwer historii platformy Spark

Serwer historii platformy Spark jest interfejs użytkownika sieci web dla aplikacji platformy Spark ukończone i uruchomione. 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>Aby otworzyć Interfejs sieci Web uzyskiwania informacji na temat serwer historii platformy Spark z witryny Azure portal

1. Z [witryny Azure portal](https://portal.azure.com/), otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz [listy i wyświetlaniu klastrów](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Z **szybkich łączy**, kliknij przycisk **pulpit nawigacyjny klastra**, a następnie kliknij przycisk **serwer historii platformy Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra Spark. 

    ![Platforma Spark jest serwer historii](./media/apache-azure-spark-history-server/launch-history-server.png "Spark serwer historii")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>Aby otworzyć Interfejs sieci Web serwer historii platformy Spark według adresu URL
Otwórz Zamień serwer historii platformy Spark, przechodząc pod następujący adres URL <ClusterName> nazwą klastra Spark klienta.

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

Serwer historii platformy Spark w sieci web UI wygląda następująco:

![Serwer historii platformy HDInsight Spark](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>Otwórz kartę danych z serwer historii platformy Spark
Wybierz identyfikator zadania, a następnie kliknij przycisk **danych** w menu Narzędzia, aby uzyskać widok danych.

+ Sprawdź **dane wejściowe**, **dane wyjściowe**, i **operacje tabeli** , wybierając oddzielnie na kartach.

    ![Dane kart](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ Kopiuj wszystkie wiersze, klikając przycisk **kopiowania**.

    ![Kopiowanie danych](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ Zapisz wszystkie dane jako plik CSV, klikając przycisk **csv**.

    ![Dane, Zapisz](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ Wyszukaj, wpisując słowa kluczowe w polu **wyszukiwania**, wynikiem wyszukiwania będą wyświetlane natychmiast.

    ![Wyszukiwanie danych](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ Kliknij nagłówek kolumny, aby posortować tabelę, kliknij znak plus, aby rozwinąć wiersz, aby wyświetlić więcej szczegółów lub kliknij znak minus, aby zwinąć wiersz.

    ![Tabela danych](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ Pobierz jeden plik, klikając przycisk **częściowe pobieranie** umieść po prawej stronie, a następnie wybrany plik zostanie pobrany do lokalnego lub jeśli plik nie istnieje, zostanie otwarty nową kartę, aby wyświetlić komunikaty o błędach.

    ![Wiersz pobierania danych](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopiuj pełną ścieżkę lub ścieżki względnej, wybierając **Kopiuj pełną ścieżkę**, **Kopiuj ścieżkę względną** rozszerza się w menu Pobierz. W przypadku plików magazynu usługi azure data lake **Otwórz w Eksploratorze usługi Azure Storage** spowoduje uruchomienie Eksploratora usługi Azure Storage i Znajdź folder podczas logowania.

    ![Ścieżka do kopiowania danych](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Kliknij numer pod tabelą, aby przejść strony kiedy zbyt wiele wierszy do wyświetlenia na jednej stronie. 

    ![Strona danych](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ Umieść kursor na znak zapytania obok dane, aby pokazać etykietkę narzędzia, lub kliknij znak zapytania, aby uzyskać więcej informacji.

    ![Dane uzyskać więcej informacji](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ Prześlij opinię z problemami, klikając **przesłanie nam opinii**.

    ![Wykres opinii](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>Otwórz kartę Wykres z serwer historii platformy Spark
Wybierz identyfikator zadania, a następnie kliknij przycisk **wykres** w menu Narzędzia, aby uzyskać widok wykresu zadania.

+ Sprawdź Przegląd zadania, Graf zadania wygenerowany. 

+ Domyślnie, spowoduje wyświetlenie wszystkich zadań i może być filtrowane według **identyfikator zadania**.

    ![Identyfikator zadania programu Graph](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ Domyślnie **postępu** jest zaznaczone, użytkownik można sprawdzić przepływu danych, wybierając **odczytu/Written** na liście rozwijanej **wyświetlania**.

    ![Wyświetl wykres](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    Wyświetlanie węzła grafu kolor, który pokazuje cieplnej.

    ![Wykres mapy cieplnej](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ Odtwarzanie zadania, klikając **odtwarzania** przycisk i zatrzymać w dowolnym momencie, klikając przycisk Zatrzymaj. Wyświetlanie zadań są oznaczone kolorem do wyświetlania stanu różnych podczas odtwarzania:

    + Zielony dla powiodło się: zadanie została pomyślnie zakończona.
    + Pomarańczowy na potrzeby podjęta próba: wystąpienia zadania, które nie powiodło się, ale nie wpływają na ostateczny wynik zadania. Te zadania ma zduplikowany lub spróbuj ponownie wystąpienia, które może powieść się później.
    + Czerwony, nie powiodło się: zadanie zakończyło się niepowodzeniem.
    + Niebieski do uruchomienia: zadanie jest uruchomione.
    + Białe, aby pominięte lub Oczekiwanie: zadanie oczekuje na uruchomienie lub etapu została pominięta.

    ![przykład koloru wykresu, uruchomione](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![przykład koloru wykresu, nie powiodło się](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > Odtwarzanie dla każdego zadania jest dozwolone. Gdy zadanie nie ma żadnych stanu lub nie zostało ukończone, odtwarzanie nie jest obsługiwane.


+ Myszy Przewija powiększenia w pionie i poziomie wykresu zadania, lub kliknąć przycisk **Dopasuj do** celu dopasowania go do ekranu.
 
    ![Powiększenie wykresu, aby dopasować](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Umieść kursor w węźle wykres, aby zobaczyć, że etykietki narzędzia w przypadku zadań zakończonych niepowodzeniem, a następnie kliknij etap, aby otworzyć stronę etapu.

    ![etykietki narzędzi wykresu](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Węzłem grafu zadania zostaną wyświetlone następujące informacje, każdy z etapów:
    + IDENTYFIKATOR.
    + Nazwy lub opisu.
    + Liczba wszystkich zadań.
    + Dane odczytane: Suma rozmiar danych wejściowych i shuffle rozmiar odczytu.
    + Zapisywanie danych: Suma rozmiar danych wyjściowych i shuffle rozmiar zapisu.
    + Czas wykonywania: czas między przy pierwszej próbie czas rozpoczęcia i czas ukończenia ostatniej próby.
    + Liczba wierszy: Suma rekordów danych wejściowych, wyjściowych rekordów mieszania odczytu rekordów i mieszania rekordów zapisu.
    + Postęp.

    > [!NOTE]
    > Domyślnie węzłem grafu zadania spowoduje wyświetlenie informacji z ostatniej próby, każdy z etapów (z wyjątkiem czas wykonywania etapu), ale podczas odtwarzania wykresu węźle są wyświetlane informacje z kolejnymi próbami.

    > [!NOTE]
    > Rozmiar danych odczytu i zapisu, używamy 1 MB = 1000 KB = 1000 * 1000 bajtów.

+ Prześlij opinię z problemami, klikając **przesłanie nam opinii**.

    ![Wykres opinii](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>Często zadawane pytania

### <a name="1-revert-to-community-version"></a>1. Przywróć wersję community

Aby powrócić do wersji community, wykonaj następujące czynności:

1. Otwórz klaster w Ambari. Kliknij przycisk **Spark2** w panelu po lewej stronie.
2. Kliknij przycisk **Configs** kartę.
3. Rozwiń grupę **niestandardowe spark2 — domyślne**.
4. Kliknij przycisk **Dodaj właściwość**, Dodaj **spark.ui.enhancement.enabled=false**, Zapisz.
5. Ustawia właściwość **false** teraz.
6. Kliknij przycisk **Zapisz** Aby zapisać konfigurację.

    ![Wyłącza funkcję](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. Kliknij przycisk **Spark2** w lewym panelu w obszarze **Podsumowanie** kliknij pozycję **serwer historii Spark2**.

    ![ponowne uruchomienie komputera serwer1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. Uruchom ponownie serwer historii, klikając **ponowne uruchomienie** z **serwer historii Spark2**.

    ![Uruchom ponownie serwer2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Odśwież serwer historii platformy Spark w internetowym interfejsie użytkownika, będzie można przywrócić do wersji community.

### <a name="2-upload-history-server-event"></a>2. Przekazywanie zdarzeń serwera historii

Jeśli napotkasz błąd serwera historii postępuj zgodnie z instrukcjami, aby zapewnić zdarzenia:
1. Pobierz zdarzenia, klikając **Pobierz** w historii serwera internetowego interfejsu użytkownika.

    ![Pobieranie zdarzenia](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Kliknij przycisk **przesłanie nam opinii** z karty danych/wykresu.

    ![Wykres opinii](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Podaj tytuł i opis błędu, przeciągnij plik zip do pola edycji, a następnie kliknij przycisk **Prześlij nowy problem**.

    ![problem z pliku](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. Uaktualnij plik jar w scenariuszu poprawki

Do uaktualnienia przy użyciu poprawek, należy użyć poniższego skryptu, która spowoduje uaktualnienie enhancement.jar* platformy spark.

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**Użycie**: 

`upgrade_spark_enhancement.sh https://${jar_path}`

**Przykład**:

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**Aby użyć pliku bash z witryny Azure portal**

1. Uruchom [witryny Azure Portal](https://ms.portal.azure.com)i wybierz klaster.
2. Kliknij przycisk **akcji skryptu**, następnie **Prześlij nowe**. Wykonaj **Prześlij akcję skryptu** formularza, a następnie kliknij przycisk **Utwórz** przycisku.
    
    + **Typ skryptu**: Wybierz **niestandardowe**.
    + **Nazwa**: Określ nazwę skryptu.
    + **Identyfikator URI skryptu powłoki systemowej**: Przekaż plik programu bash do klastra prywatnego, a następnie skopiuj adres URL, w tym miejscu. Można również użyć podanego identyfikatora URI.
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + Sprawdzanie **Head** i **procesu roboczego**.
    + **Parametry**: Ustaw następujące parametry użycia powłoki bash.

    ![przekazanie dzienników lub uaktualnienia poprawki](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>Znany problem

1.  Obecnie usługa działa tylko dla klastra Spark 2.3.

2.  Dane wejściowe i wyjściowe przy użyciu RDD nie będzie widoczna na karcie danych.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie zasobami klastra Spark na HDInsight](apache-spark-resource-manager.md)
* [Konfigurowanie ustawień platformy Spark](apache-spark-settings.md)


## <a name="contact-us"></a>Skontaktuj się z nami

Jeśli macie jakieś uwagi lub występują inne problemy, korzystając z tego narzędzia, Wyślij wiadomość e-mail na ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)).

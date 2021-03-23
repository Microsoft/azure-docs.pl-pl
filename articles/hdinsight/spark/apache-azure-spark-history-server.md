---
title: Używanie rozszerzonych funkcji na serwerze historii Apache Spark do debugowania aplikacji — Azure HDInsight
description: Użyj rozszerzonych funkcji na serwerze historii Apache Spark, aby debugować i diagnozować aplikacje Spark — Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: c6645bc605dbd60d331ac0de002c36384b2bbbc4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864758"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Użyj rozszerzonych funkcji serwera historii Apache Spark, aby debugować i diagnozować aplikacje Spark

W tym artykule pokazano, jak za pomocą rozszerzonych funkcji serwera historii Apache Spark debugować i diagnozować ukończone lub działające aplikacje platformy Spark. Rozszerzenie zawiera kartę **dane** , kartę **Graf** i kartę **Diagnostyka** . Na karcie **dane** można sprawdzić dane wejściowe i wyjściowe zadania platformy Spark. Na karcie **Graph** można sprawdzić przepływ danych i ponownie odtworzyć wykres zadań. Na karcie **Diagnostyka** można odwołać się do funkcji **odchylenia danych**, **pochylania czasu i wykonywania** programu **wykonującego testy** .

## <a name="get-access-to-the-spark-history-server"></a>Uzyskaj dostęp do serwera historii platformy Spark

Serwer historii platformy Spark jest interfejsem użytkownika sieci Web dla ukończonych i uruchomionych aplikacji platformy Spark. Można go otworzyć z poziomu Azure Portal lub z adresu URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark z poziomu Azure Portal

1. W [Azure Portal](https://portal.azure.com/)Otwórz klaster Spark. Aby uzyskać więcej informacji, zobacz Wyświetlanie [i wyświetlanie klastrów](../hdinsight-administer-use-portal-linux.md#showClusters).
2. W obszarze **pulpity nawigacyjne klastra** wybierz pozycję  **serwer historii platformy Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra Spark.

    :::image type="content" source="./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png " alt-text="Uruchom serwer historii platformy Spark z poziomu Azure Portal." border="true"::: Azure Portal ". Border = "true":::

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Otwórz interfejs użytkownika sieci Web serwera historii platformy Spark według adresu URL

Otwórz serwer historii platformy Spark, przechodząc do `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` lokalizacji, gdzie **ClusterName** jest nazwą klastra Spark.

Interfejs użytkownika sieci Web serwera historii platformy Spark może wyglądać podobnie do tego obrazu:

:::image type="content" source="./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png" alt-text="Na stronie serwer historii platformy Spark." border="true":::

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Korzystanie z karty dane na serwerze historii platformy Spark

Wybierz identyfikator zadania, a następnie wybierz pozycję **dane** w menu Narzędzia, aby wyświetlić widok dane.

+ Przejrzyj **dane wejściowe**, **wyjściowe** i **operacje tabeli** , wybierając poszczególne karty.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-tabs.png" alt-text="Karty danych na stronie aplikacji platformy Spark." border="true":::

+ Skopiuj wszystkie wiersze, wybierając przycisk **Kopiuj** .

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-copy.png" alt-text="Skopiuj dane na stronie aplikacji platformy Spark." border="true":::

+ Zapisz wszystkie dane jako. Plik CSV, wybierając przycisk **CSV** .

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-save.png" alt-text="Zapisz dane jako. Plik CSV na stronie danych aplikacji platformy Spark." border="true":::

+ Przeszukaj dane, wprowadzając słowa kluczowe w polu **wyszukiwania** . Wyniki wyszukiwania będą wyświetlane natychmiast.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-search.png" alt-text="Wyszukaj dane na stronie danych aplikacji platformy Spark." border="true":::

+ Wybierz nagłówek kolumny, aby posortować tabelę. Wybierz znak plus, aby rozwinąć wiersz, aby wyświetlić więcej szczegółów. Wybierz znak minus, aby zwinąć wiersz.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-table.png" alt-text="Tabela danych na stronie aplikacji platformy Spark." border="true":::

+ Pobierz pojedynczy plik, wybierając przycisk **Pobierz części** po prawej stronie. Wybrany plik zostanie pobrany lokalnie. Jeśli plik już nie istnieje, spowoduje to otwarcie nowej karty w celu wyświetlenia komunikatów o błędach.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-download-row.png" alt-text="Wiersz pobierania danych na stronie aplikacji platformy Spark." border="true":::

+ Skopiuj pełną ścieżkę lub ścieżkę względną, wybierając opcję **Kopiuj pełną ścieżkę** lub **Kopiuj ścieżkę względną** , która została rozwinięta z menu Pobierz. W przypadku plików Azure Data Lake Storage wybierz pozycję **Otwórz w Eksplorator usługi Azure Storage** , aby uruchomić Eksplorator usługi Azure Storage i zlokalizować folder po zalogowaniu.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-copy-path.png" alt-text="Kopiuj pełną ścieżkę i skopiuj opcje ścieżki względnej na stronie dane aplikacji platformy Spark." border="true":::

+ Jeśli masz zbyt wiele wierszy do wyświetlenia na jednej stronie, wybierz numery stron u dołu tabeli, aby przejść.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-data-page.png" alt-text="Numery stron na stronie danych aplikacji platformy Spark." border="true":::

+ Aby uzyskać więcej informacji, umieść kursor nad lub zaznacz znak zapytania obok pozycji **dane dla aplikacji Spark** , aby wyświetlić etykietkę narzędzia.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-data-more-info.png" alt-text="Uzyskaj więcej informacji na stronie aplikacji platformy Spark." border="true":::

+  Aby wysłać opinię o problemach, wybierz pozycję Prześlij **nam swoją opinię**.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="Prześlij opinię na temat danych na stronie aplikacji platformy Spark." border="true":::

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Korzystanie z karty graf na serwerze historii platformy Spark

+ Wybierz identyfikator zadania, a następnie wybierz pozycję **Graph** w menu Narzędzia, aby wyświetlić wykres zadania. Domyślnie na wykresie zostaną wyświetlone wszystkie zadania. Filtrowanie wyników przy użyciu menu rozwijanego **Identyfikator zadania** .

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png" alt-text="Menu rozwijane identyfikator zadania na stronie grafu zadania & aplikacji platformy Spark." border="true":::

+ **Postęp** jest wybierany domyślnie. Sprawdź przepływ danych, wybierając z menu rozwijanego **ekran** opcję **Odczyt** lub **zapis** .

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-display.png" alt-text="Sprawdź przepływ danych na stronie grafu zadania & aplikacji platformy Spark." border="true":::

+ Kolor tła każdego zadania odpowiada mapie cieplnej.

   :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png" alt-text="Mapa cieplna na stronie grafu zadania & aplikacji platformy Spark." border="true":::


    |Kolor |Opis |
    |---|---|
    |Green (Zielony)|Zadanie zostało ukończone pomyślnie.|
    |Orange|Zadanie nie powiodło się, ale nie ma wpływu na końcowy wynik zadania. Te zadania mają zduplikowane lub ponawiane wystąpienia, które mogą się powieść później.|
    |Blue (Niebieski)|Zadanie jest uruchomione.|
    |Biały|Zadanie oczekuje na uruchomienie lub etap został pominięty.|
    |Red (Czerwony)|Zadanie nie powiodło się.|

     :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-running.png" alt-text="Uruchamianie zadania na stronie grafu zadania & aplikacji platformy Spark." border="true":::

     Pominięte etapy są wyświetlane w kolorze białym.
    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png" alt-text="Zadanie pominięte na stronie grafu zadania & aplikacji platformy Spark." border="true":::

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png" alt-text="Zadanie zakończone niepowodzeniem na stronie grafu zadania & aplikacji platformy Spark." border="true":::

     > [!NOTE]  
     > Odtwarzanie jest dostępne dla ukończonych zadań. Wybierz przycisk **odtwarzania** , aby odtworzyć zadanie ponownie. Zatrzymaj zadanie w dowolnym momencie, wybierając przycisk Zatrzymaj. Gdy zadanie zostanie odtworzone ponownie, każde zadanie wyświetli jego stan według koloru. Odtwarzanie nie jest obsługiwane dla nieukończonych zadań.

+ Przewiń, aby powiększyć lub pomniejszyć wykres zadania, lub wybierz pozycję **Powiększ, aby dopasować** go do ekranu.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png" alt-text="Wybierz pozycję Powiększ, aby zmieścić na stronie grafu zadania & aplikacji platformy Spark." border="true":::

+ Gdy zadania kończą się niepowodzeniem, umieść kursor nad węzłem grafu, aby wyświetlić etykietkę narzędzia, a następnie wybierz etap, aby otworzyć go na nowej stronie.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png" alt-text="Wyświetl etykietkę narzędzia na stronie grafu zadania & aplikacji platformy Spark." border="true":::

+ Na stronie grafu zadania & aplikacji platformy Spark na etapach zostaną wyświetlone etykietki narzędzi i małe ikony, jeśli zadania spełnią następujące warunki:
  + Pochylenie danych: rozmiar odczytu danych > średni rozmiar odczytanych danych wszystkich zadań w tym etapie * 2 *i* rozmiar odczytu danych > 10 MB.
  + Pochylenie czasu: czas wykonywania > średni czas wykonywania wszystkich zadań w tym etapie * 2 *i* czas wykonywania > 2 min.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png" alt-text="Ikona zadania skośnego na stronie grafu zadania & aplikacji platformy Spark." border="true":::

+ W węźle grafu zadania zostaną wyświetlone następujące informacje dotyczące poszczególnych etapów:
  + ID (Identyfikator)
  + Nazwa lub opis
  + Łączny numer zadania
  + Odczytane dane: suma rozmiaru danych wejściowych i rozmiaru losowego odczytu
  + Zapis danych: suma rozmiaru wyjściowego i rozmiaru losowego zapisu
  + Czas wykonywania: czas między godziną rozpoczęcia pierwszej próby i czas ukończenia ostatniej próby
  + Liczba wierszy: suma rekordów wejściowych, rekordów wyjściowych, losowego odczytywania rekordów i losowego zapisu rekordów
  + Postęp

    > [!NOTE]  
    > Domyślnie węzeł grafu zadania będzie wyświetlał informacje z ostatniej próby każdego etapu (z wyjątkiem czasu wykonywania etapu). Jednak podczas odtwarzania węzeł grafu zadania będzie wyświetlał informacje o każdej próbie.

    > [!NOTE]  
    > W przypadku rozmiarów zapisu i odczytu danych używamy 1 MB = 1000 KB = 1000 * 1000 bajtów.

+ Prześlij opinię o problemach, wybierając pozycję Prześlij **nam swoją opinię**.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="Opcja opinii na stronie grafu zadania & aplikacji platformy Spark." border="true":::

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Korzystanie z karty Diagnostyka na serwerze historii platformy Spark

Wybierz identyfikator zadania, a następnie w menu Narzędzia wybierz pozycję **Diagnostyka** , aby wyświetlić widok Diagnostyka zadań. Karta **Diagnostyka** obejmuje **przechylenie danych**, **pochylenie czasu** i **analizę użycia programu wykonującego**.

+ Sprawdź **pochylenie danych**, **pochylenie czasu** i **analizę użycia programu wykonującego** , wybierając odpowiednio karty.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png" alt-text="Karta przechylenie danych na karcie Diagnostyka." border="true":::

### <a name="data-skew"></a>Pochylenie danych

Wybierz kartę **pochylanie danych** . Odpowiednie skośne zadania są wyświetlane na podstawie określonych parametrów.

#### <a name="specify-parameters"></a>Określ parametry

W sekcji **Określanie parametrów** są wyświetlane parametry, które są używane do wykrywania pochylenia danych. Reguła domyślna to: odczyt danych zadania jest dłuższy niż trzy razy średniego odczytania danych zadania, a odczytane dane zadania są większe niż 10 MB. Jeśli chcesz zdefiniować własną regułę dla zadań skośnych, możesz wybrać parametry. Sekcje **skośne** i **ukośne wykresu** zostaną odpowiednio zaktualizowane.

#### <a name="skewed-stage"></a>Skośny etap

Sekcja **skośny etap** przedstawia etapy, które mają skośne zadania spełniające określone kryteria. Jeśli na etapie występuje więcej niż jedno zadanie skośne, sekcja **skośny etap** wyświetla tylko zadanie najbardziej skośne (czyli największe dane do pochylenia danych).

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png" alt-text="Większy widok karty pochylanie danych na karcie Diagnostyka." border="true":::

##### <a name="skew-chart"></a>Wykres skośny

Po wybraniu wiersza w tabeli **etapów** skośności **Wykres skośny** Wyświetla więcej szczegółów dystrybucji zadań na podstawie czasu odczytu i wykonywania danych. Skośne zadania są oznaczone kolorem czerwonym, a normalne zadania są oznaczone kolorem niebieskim. Ze względu na wydajność Wykres wyświetla do 100 przykładowych zadań. Szczegóły zadania są wyświetlane w prawym dolnym panelu.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png" alt-text="Wykres skośny dla etapu 10 w interfejsie użytkownika Spark." border="true":::

### <a name="time-skew"></a>Pochylenie czasu

Karta **przechylenie czasu** przedstawia zadania skośne w oparciu o czas wykonywania zadania.

#### <a name="specify-parameters"></a>Określ parametry

W sekcji **Określanie parametrów** są wyświetlane parametry, które są używane do wykrywania pochylenia czasu. Reguła domyślna to: czas wykonywania zadania jest dłuższy niż trzy razy średni czas wykonywania, a czas wykonywania zadania jest większy niż 30 sekund. Można zmienić parametry w zależności od potrzeb. Na wykresie skośny **stopień** i **skośny** są wyświetlane odpowiednie etapy i zadania, podobnie jak na karcie **pochylanie danych** .

Po wybraniu opcji **przechylenie czasu** filtrowany wynik zostanie wyświetlony w sekcji **pochylone etap** zgodnie z parametrami ustawionymi w sekcji **Określanie parametrów** . Po wybraniu jednego elementu w sekcji **skośny etap** odpowiadający mu wykres zostanie sporządzony w trzeciej sekcji, a szczegóły zadania zostaną wyświetlone w prawym dolnym panelu.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png" alt-text="Karta przechylenie czasu na karcie Diagnostyka." border="true":::

### <a name="executor-usage-analysis-graphs"></a>Wykresy analizy użycia dla wykonawców

**Wykres użycia programu wykonującego** przedstawia rzeczywistą alokację wykonawców i stan uruchomienia zadania.  

Po wybraniu **analizy użycia programu wykonującego**, cztery różne krzywe dotyczące użycia programu wykonującego są przygotowane: **przydzielono wykonawcy**, **uruchomione wykonawcy**, **wykonawcze bezczynne** i **maksymalną liczbę wystąpień programu wykonującego**. Wszystkie zdarzenia dodane przez program **wykonujący** lub **usuniętego wykonawcy** spowodują zwiększenie lub zmniejszenie przyznanych wykonawców. Możesz sprawdzić **oś czasu zdarzeń** na karcie **zadania** , aby uzyskać więcej porównań.

:::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png" alt-text="Karta Analiza użycia programu wykonującego na karcie Diagnostyka." border="true":::

Wybierz ikonę koloru, aby wybrać lub usunąć zaznaczenie odpowiedniej zawartości we wszystkich wersjach roboczych.

 :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png" alt-text="Wybierz wykres na karcie Analiza użycia programu wykonującego." border="true":::

## <a name="faq"></a>Często zadawane pytania

### <a name="how-do-i-revert-to-the-community-version"></a>Jak mogę powrócić do wersji społecznościowej?

Aby powrócić do wersji społeczności, wykonaj następujące czynności.

1. Otwórz klaster w Ambari.
1. Przejdź do **Spark2**  >  **configs**.
1. Wybierz pozycję **niestandardowe spark2 — ustawienia domyślne**.
1. Wybierz pozycję **Dodaj właściwość.**...
1. Dodaj element **Spark. UI. ulepszenie. Enabled = false**, a następnie zapisz go.
1. Właściwość ma teraz **wartość false** .
1. Wybierz pozycję **Zapisz** , aby zapisać konfigurację.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-turn-off.png" alt-text="Wyłącz funkcję w programie Apache Ambari." border="true":::

1. Wybierz pozycję **Spark2** w lewym panelu. Następnie na karcie **Podsumowanie** wybierz pozycję **serwer historii Spark2**.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-restart1.png" alt-text="Widok podsumowania w programie Apache Ambari." border="true":::

1. Aby ponownie uruchomić serwer historii platformy Spark, wybierz **przycisk** Uruchom z prawej strony **serwera historii Spark2**, a następnie wybierz pozycję **restart** z menu rozwijanego.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-restart2.png" alt-text="Uruchom ponownie serwer historii platformy Spark w programie Apache Ambari." border="true":::  

1. Odśwież interfejs użytkownika sieci Web serwera historii platformy Spark. Spowoduje to przywrócenie wersji społeczności.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Jak mogę przekazać zdarzenia serwera historii platformy Spark w celu zgłoszenia go jako problemu?

Jeśli wystąpi błąd na serwerze historii platformy Spark, wykonaj następujące kroki, aby zgłosić zdarzenie.

1. Pobierz zdarzenie, wybierając pozycję **Pobierz** w interfejsie użytkownika sieci Web serwera historii platformy Spark.

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-download-event.png" alt-text="Pobierz zdarzenie w interfejsie użytkownika serwera historii platformy Spark." border="true":::

2. Wybierz pozycję **Prześlij nam swoją opinię** na stronie **grafu zadania & aplikacji platformy Spark** .

    :::image type="content" source="./media/apache-azure-spark-history-server/sparkui-graph-feedback.png" alt-text="Prześlij opinię na stronie grafu zadania & aplikacji platformy Spark" border="true":::

3. Podaj tytuł i opis błędu. Następnie przeciągnij plik. zip do pola edycji i wybierz pozycję **Prześlij nowy problem**.

    :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-file-issue.png" alt-text="Przekaż i Prześlij nowy problem." border="true":::

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Jak mogę uaktualnić plik JAR w scenariuszu poprawek?

Jeśli chcesz uaktualnić przy użyciu poprawki, użyj następującego skryptu, który zostanie uaktualniony `spark-enhancement.jar*` .

**upgrade_spark_enhancement. sh**:

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

#### <a name="usage"></a>Użycie

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Przykład

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Użyj pliku bash z Azure Portal

1. Uruchom [Azure Portal](https://ms.portal.azure.com)a następnie wybierz klaster.
2. Ukończ [akcję skryptu](../hdinsight-hadoop-customize-cluster-linux.md) z poniższymi parametrami.

    |Właściwość |Wartość |
    |---|---|
    |Typ skryptu|-Niestandardowe|
    |Nazwa|UpgradeJar|
    |Identyfikator URI skryptu bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Typy węzłów|Kierownik, proces roboczy|
    |Parametry|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     :::image type="content" source="./media/apache-azure-spark-history-server/apache-spark-upload1.png" alt-text="Azure Portal akcji przesyłania skryptu" border="true":::

## <a name="known-issues"></a>Znane problemy

+ Obecnie serwer historii Spark działa tylko dla platformy Spark 2,3 i 2,4.

+ Dane wejściowe i wyjściowe, które używają RDD, nie będą wyświetlane na karcie **dane** .

## <a name="next-steps"></a>Następne kroki

+ [Zarządzanie zasobami klastra Apache Spark w usłudze HDInsight](apache-spark-resource-manager.md)
+ [Konfigurowanie ustawień platformy Apache Spark](apache-spark-settings.md)

## <a name="suggestions"></a>Sugestie

Jeśli podczas korzystania z tego narzędzia masz jakiekolwiek Opinie lub problemy, Wyślij wiadomość e-mail na adres ( [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) ).

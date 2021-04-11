---
title: Monitorowanie wydajności klastra — Azure HDInsight
description: Monitorowanie kondycji i wydajności klastrów Apache Hadoop w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 5e6bec71c44d7fbcf2841e087af9887d99c94e6f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867580"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitorowanie wydajności klastra w usłudze Azure HDInsight

Monitorowanie kondycji i wydajności klastra usługi HDInsight jest niezbędne do utrzymania optymalnej wydajności i wykorzystania zasobów. Monitorowanie pomaga również wykrywać i rozwiązywać problemy z konfiguracją klastra oraz błędy związane z kodem użytkownika.

W poniższych sekcjach opisano, jak monitorować i optymalizować obciążenie klastrów, Apache Hadoop kolejki PRZĘDZy i wykrywać problemy związane z ograniczaniem magazynu.

## <a name="monitor-cluster-load"></a>Monitorowanie obciążenia klastra

Klastry Hadoop mogą zapewnić optymalną wydajność, gdy obciążenie klastra jest równomiernie dystrybuowane we wszystkich węzłach. Dzięki temu zadania przetwarzania mają być uruchamiane bez ograniczenia ilości pamięci RAM, procesora CPU ani zasobów dyskowych w poszczególnych węzłach.

Aby uzyskać ogólne omówienie węzłów klastra i ich ładowania, zaloguj się do [interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md), a następnie wybierz kartę **hosty** . Hosty są wyświetlane na podstawie ich w pełni kwalifikowanych nazw domen. Stan operacyjny każdego hosta jest pokazywany przez kolorowy wskaźnik kondycji:

| Kolor | Opis |
| --- | --- |
| Red (Czerwony) | Co najmniej jeden główny składnik na hoście nie działa. Umieść kursor w celu wyświetlenia etykietki narzędzia, która wyświetla listę składników, których to dotyczy. |
| Orange | Co najmniej jeden składnik pomocniczy na hoście nie działa. Umieść kursor w celu wyświetlenia etykietki narzędzia, która wyświetla listę składników, których to dotyczy. |
| Yellow | Serwer Ambari nie otrzymał pulsu od hosta przez więcej niż 3 minuty. |
| Green (Zielony) | Normalny stan działania. |

Zobaczysz również kolumny przedstawiające liczbę rdzeni i ilość pamięci RAM dla każdego hosta, a także użycie dysku i średnie obciążenie.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png" alt-text="Karta Apache Ambari hosts — Omówienie":::

Wybierz dowolny z nazw hostów, aby zapoznać się ze szczegółowymi informacjami na temat składników uruchomionych na tym hoście oraz ich metryk. Metryki są wyświetlane jako można wybrać oś czasu użycia procesora CPU, obciążenia, użycie dysku, użycie pamięci, użycie sieci i liczbę procesów.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png" alt-text="Omówienie szczegółów hosta Apache Ambari":::

Aby uzyskać szczegółowe informacje na temat ustawiania alertów i wyświetlania metryk, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md) .

## <a name="yarn-queue-configuration"></a>Konfiguracja kolejki PRZĘDZy

Usługa Hadoop oferuje różne usługi działające na platformie rozproszonej. PRZĘDZa (inna niż inny Negocjuj zasób) koordynuje te usługi i przydziela zasoby klastra, aby zapewnić równomierne rozłożenie obciążenia w klastrze.

PRZĘDZa dzieli dwie odpowiedzialności za JobTracker, zarządzanie zasobami i planowanie/monitorowanie zadań w dwóch demonach: globalna Menedżer zasobów i dla aplikacji ApplicationMaster (AM).

Menedżer zasobów jest *czystym harmonogramem* i wyłącznie rozstrzyga dostępne zasoby między wszystkimi konkurującymi aplikacjami. Menedżer zasobów zapewnia, że wszystkie zasoby są zawsze używane, Optymalizacja pod kątem różnych stałych, takich jak umowy SLA, gwarancje wydajności i tak dalej. ApplicationMaster negocjuje zasoby z Menedżer zasobów i współpracuje z węzłów w celu wykonywania i monitorowania kontenerów oraz ich zużycia zasobów.

Gdy wiele dzierżawców współużytkuje duży klaster, istnieje konkurs dla zasobów klastra. CapacityScheduler to podłączany harmonogram, który ułatwia udostępnianie zasobów przez kolejkowanie żądań. CapacityScheduler obsługuje również *hierarchiczne kolejki* , aby zapewnić, że zasoby są współużytkowane przez kolejki podrzędne organizacji, zanim inne kolejki aplikacji będą mogły korzystać z bezpłatnych zasobów.

PRZĘDZa umożliwia przydzielanie zasobów do tych kolejek i pokazuje, czy są przypisane wszystkie dostępne zasoby. Aby wyświetlić informacje o kolejkach, zaloguj się do interfejsu użytkownika sieci Web Ambari, a następnie wybierz pozycję **Menedżer kolejki przędzy** w górnym menu.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png" alt-text="Menedżer kolejki PRZĘDZy w usłudze Apache Ambari":::

Na stronie Menedżer kolejki PRZĘDZy zostanie wyświetlona lista kolejek z lewej strony wraz z wartością procentową pojemności przypisanej do każdego z nich.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png" alt-text="Strona szczegółów Menedżera kolejki PRZĘDZy":::

Aby zapoznać się z bardziej szczegółowymi informacjami o kolejkach, na pulpicie nawigacyjnym Ambari wybierz usługę **przędzy** z listy po lewej stronie. Następnie w menu rozwijanym **szybkie linki** wybierz pozycję **Menedżer zasobów interfejs użytkownika** pod aktywnym węzłem.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png" alt-text="Menedżer zasobów linki menu interfejsu użytkownika":::

W interfejsie użytkownika Menedżer zasobów wybierz pozycję **harmonogram** z menu po lewej stronie. Zostanie wyświetlona lista kolejek pod *kolejkami aplikacji*. W tym miejscu możesz zobaczyć pojemność użytą dla każdej z kolejek, jak również są dystrybuowane zadania między nimi oraz czy wszystkie zadania są ograniczone do zasobów.

:::image type="content" source="./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png" alt-text="Menu interfejsu użytkownika Menedżer zasobów Apache HAdoop":::

## <a name="storage-throttling"></a>Ograniczanie przepustowości magazynu

Na poziomie magazynu mogą wystąpić wąskie gardła wydajności klastra. Ten typ wąskich gardeł najczęściej z powodu *blokowania* operacji wejścia/wyjścia (IO), które są wykonywane, gdy uruchomione zadania wysyłają więcej operacji we/wy niż może obsłużyć usługa magazynu. Ten blok umożliwia utworzenie kolejki żądań we/wy oczekujących na przetworzenie do momentu przetworzenia bieżącego systemu IOs. Bloki są spowodowane *ograniczeniami magazynu*, które nie jest limitem fizycznym, ale raczej limitem narzuconym przez usługę magazynu przez umowę dotyczącą poziomu usług (SLA). Ten limit gwarantuje, że żaden pojedynczy klient lub dzierżawca nie może monopolize usługi. Umowa SLA ogranicza liczbę operacji we/wy na sekundę (IOPS) dla usługi Azure Storage — Aby uzyskać szczegółowe informacje, zobacz [elementy docelowe skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../storage/common/scalability-targets-standard-account.md).

Jeśli używasz usługi Azure Storage, aby uzyskać informacje na temat monitorowania problemów związanych z magazynem, w tym ograniczania przepustowości, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md).

Jeśli magazyn zapasowy klastra jest Azure Data Lake Storage (ADLS), ograniczenie jest najprawdopodobniej spowodowane limitami przepustowości. Ograniczanie w tym przypadku może być identyfikowane przez obserwowanie błędów ograniczania w dziennikach zadań. Aby uzyskać ADLS, zobacz sekcję ograniczanie przepustowości dla odpowiedniej usługi w następujących artykułach:

* [Wskazówki dotyczące dostrajania wydajności Apache Hive w usłudze HDInsight i Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Wskazówki dotyczące dostrajania wydajności dla MapReduce w usłudze HDInsight i Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Wskazówki dotyczące dostrajania wydajności Apache Storm w usłudze HDInsight i Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Rozwiązywanie problemów z powolnymi węzłami

W niektórych przypadkach może wystąpić spowolnienie z powodu małej ilości miejsca na dysku w klastrze. Zbadaj następujące kroki:

1. Użyj [polecenia SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z każdym węzłem.

1. Sprawdź użycie dysku, uruchamiając jedno z następujących poleceń:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Przejrzyj dane wyjściowe i sprawdź obecność dużych plików w `mnt` folderze lub innych folderach. Zwykle `usercache` foldery, i `appcache` (mnt/Resource/Hadoop/usercache/Hive/appcache/) zawierają duże pliki.

1. W przypadku dużych plików, bieżące zadanie powoduje wzrost rozmiaru pliku lub zakończyło się niepowodzeniem poprzednie zadanie mogło zostać wniesione do tego problemu. Aby sprawdzić, czy to zachowanie jest spowodowane przez bieżące zadanie, uruchom następujące polecenie: 

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Jeśli to polecenie wskazuje określone zadanie, możesz przerwać zadanie przy użyciu polecenia podobnego do następującego:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Zamień na `application_id` Identyfikator aplikacji. Jeśli nie określono konkretnych zadań, przejdź do następnego kroku.

1. Po zakończeniu powyższego polecenia lub jeśli nie określono konkretnych zadań, usuń duże pliki, które zostały zidentyfikowane przez uruchomienie polecenia podobnego do następującego:

    ```bash
    rm -rf filecache usercache
    ```

Aby uzyskać więcej informacji dotyczących problemów z miejscem na dysku, zobacz [miejsce na dysku](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Jeśli masz duże pliki, które chcesz zachować, ale mają one wpływ na problem z małą ilością wolnego miejsca na dysku, musisz skalować klaster usługi HDInsight i ponownie uruchomić usługę. Po wykonaniu tej procedury i poczekaniu przez kilka minut można zauważyć, że magazyn zostanie zwolniony i zostanie przywrócona zwykła wydajność węzła.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o rozwiązywaniu problemów i monitorowaniu klastrów, odwiedź następujące linki:

* [Analizowanie dzienników usługi HDInsight](./hdinsight-troubleshoot-guide.md)
* [Debugowanie aplikacji przy użyciu dzienników Apache Hadoop PRZĘDZy](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Włączanie zrzutów sterty dla usług Apache Hadoop w usłudze HDInsight opartej na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
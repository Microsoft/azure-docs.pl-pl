---
title: Rozwiązywanie problemów z klastrem Apache Spark w usłudze Azure HDInsight
description: Dowiedz się więcej o problemach związanych z klastrami Apache Spark w usłudze Azure HDInsight i sposobach ich obejścia.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: c92b55d3ac7f4476b7b74d25b40150a74c6ea1cf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930154"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Znane problemy dotyczące klastra Apache Spark w usłudze HDInsight

Ten dokument śledzi wszystkie znane problemy dotyczące publicznej wersji zapoznawczej usługi HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Przeciek usługi Livy wycieka sesji interaktywnej
Po ponownym uruchomieniu programu [Apache usługi Livy](https://livy.incubator.apache.org/) (z platformy [Apache Ambari](https://ambari.apache.org/) lub z powodu ponownego uruchomienia maszyny wirtualnej węzła głównego 0) przy jednoczesnym utrzymywaniu aktywnej sesji zostanie ujawniona nieoczekiwana sesja zadania interaktywnego. W związku z tym nowe zadania mogą być zablokowane w stanie zaakceptowanym.

**Środki zaradcze**

Aby obejść ten problem, wykonaj czynności opisane w poniższej procedurze:

1. SSH do węzła głównego. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Uruchom następujące polecenie, aby znaleźć identyfikatory aplikacji zadań interaktywnych rozpoczętych za pomocą usługi Livy.

   ```bash
   yarn application –list
   ```

    Domyślne nazwy zadań zostaną usługi Livy, jeśli zadania zostały uruchomione z sesją interaktywną usługi Livy bez określonych jawnych nazw. W przypadku sesji usługi Livy uruchomionej przez [Jupyter Notebook](https://jupyter.org/)nazwa zadania rozpoczyna się od `remotesparkmagics_*` .

3. Uruchom następujące polecenie, aby skasować te zadania.

   ```bash
   yarn application –kill <Application ID>
   ```

Rozpoczęto wykonywanie nowych zadań.

## <a name="spark-history-server-not-started"></a>Serwer historii platformy Spark nie został uruchomiony
Serwer historii platformy Spark nie jest uruchamiany automatycznie po utworzeniu klastra.  

**Środki zaradcze**

Ręcznie uruchom serwer historii z Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problem z uprawnieniami w katalogu dzienników platformy Spark
hdiuser pobiera następujący błąd podczas przesyłania zadania przy użyciu usługi Spark-Submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

I nie zapisano dziennika sterownika.

**Środki zaradcze**

1. Dodaj hdiuser do grupy usługi Hadoop.
2. Udostępnienie 777 uprawnień na/var/log/Spark po utworzeniu klastra.
3. Zaktualizuj lokalizację dziennika platformy Spark za pomocą Ambari jako katalog z uprawnieniami 777.  
4. Uruchom platformę Spark — Prześlij jako sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Łącznik Spark-Phoenix nie jest obsługiwany

Klastry usługi HDInsight Spark nie obsługują łącznika Spark-Phoenix.

**Środki zaradcze**

Zamiast tego należy użyć łącznika Spark-HBase. Aby uzyskać instrukcje, zobacz [jak używać łącznika Spark-HBase](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemy związane z notesami Jupyter

Poniżej przedstawiono znane problemy związane z notesami Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notesy zawierające znaki inne niż ASCII w nazwach plików

Nie należy używać znaków innych niż ASCII w nazwach plików Jupyter Notebook. Jeśli próbujesz przekazać plik za pomocą interfejsu użytkownika Jupyter, który ma nazwę pliku innego niż ASCII, kończy się niepowodzeniem bez żadnego komunikatu o błędzie. Jupyter nie zezwala na przekazywanie pliku, ale nie generuje widocznego błędu ani.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Wystąpił błąd podczas ładowania notesów o większych rozmiarach

Podczas ładowania notesów o większej wielkości może pojawić się komunikat o błędzie **`Error loading notebook`** .  

**Środki zaradcze**

Jeśli zostanie wyświetlony ten błąd, nie oznacza to, że dane są uszkodzone lub utracone.  Twoje notesy nadal znajdują się na dysku w systemie i można do niego uzyskać dostęp za pomocą protokołu `/var/lib/jupyter` SSH. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia z klastrem przy użyciu protokołu SSH można skopiować notesy z klastra na maszynę lokalną (za pomocą usługi SCP lub WinSCP) jako kopię zapasową, aby zapobiec utracie ważnych danych w notesie. Można następnie tunel SSH do węzła głównego na porcie 8001, aby uzyskać dostęp do Jupyter bez przechodzenia przez bramę.  Stamtąd możesz wyczyścić dane wyjściowe notesu i zapisać go ponownie, aby zminimalizować rozmiar notesu.

Aby zapobiec wystąpieniu tego błędu w przyszłości, musisz przestrzegać pewnych najlepszych rozwiązań:

* Ważne jest, aby zachować mały rozmiar notesu. Wszystkie dane wyjściowe z zadań platformy Spark, które są wysyłane z powrotem do Jupyter, są utrwalane w notesie.  Najlepszym rozwiązaniem jest Jupyter ogólnie, aby uniknąć uruchamiania `.collect()` w dużych RDD lub ramkach danych, a zamiast tego, jeśli chcesz uzyskać wgląd w zawartość RDD, Rozważ uruchomienie `.take()` lub, `.sample()` Aby dane wyjściowe nie były zbyt duże.
* Ponadto podczas zapisywania notesu Wyczyść wszystkie komórki wyjściowe, aby zmniejszyć rozmiar.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Początkowe uruchamianie notesu trwa dłużej niż oczekiwano

Pierwsza instrukcja Code w Jupyter Notebook przy użyciu Magic Spark może trwać dłużej niż minutę.  

**Znaleźć**

Dzieje się tak, ponieważ po uruchomieniu pierwszej komórki kodu. W tle spowoduje to zainicjowanie konfiguracji sesji i kontekstów platformy Spark, SQL i Hive. Po ustawieniu tych kontekstów Pierwsza instrukcja jest uruchamiana i daje wrażenie, że ukończenie instrukcji trwało długo.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter Notebook przekroczenia limitu czasu podczas tworzenia sesji

Gdy klaster Spark nie jest zasobem, jądra Spark i PySpark w Jupyter Notebook spowodują przekroczenie limitu czasu podczas próby utworzenia sesji.

**Środki zaradcze**

1. Zwolnij trochę zasobów w klastrze Spark, wykonując następujące:

   * Zatrzymywanie innych notesów platformy Spark przez przejście do menu Zamknij i zatrzymane lub kliknięcie przycisku Zamknij w Eksploratorze notesu.
   * Zatrzymywanie innych aplikacji platformy Spark z PRZĘDZy.

2. Uruchom ponownie Notes, który próbujesz uruchomić. Aby można było utworzyć sesję, należy teraz udostępnić wystarczającą ilość zasobów.

## <a name="see-also"></a>Zobacz także

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki narzędzi HDInsight do IntelliJ pomysł, aby debugować aplikacje Apache Spark zdalnie](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla Jupyter Notebook w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)

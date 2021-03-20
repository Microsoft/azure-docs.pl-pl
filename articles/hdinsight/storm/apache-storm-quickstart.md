---
title: 'Szybki Start: Tworzenie i zarządzanie topologią Apache Storm — Azure HDInsight'
description: W samouczku szybki start dowiesz się, jak utworzyć i monitorować topologię Apache Storm w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.custom: mvc
ms.openlocfilehash: fe7b1cdc96ae95ac361183342ea491432c3ab4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928851"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Szybki Start: Tworzenie i monitorowanie topologii Apache Storm w usłudze Azure HDInsight

Apache Storm to skalowalny, odporny na błędy, rozproszony system obliczeniowy działający w czasie rzeczywistym do przetwarzania strumieni danych. Dzięki platformie Storm w usłudze Azure HDInsight można utworzyć oparty na chmurze klaster Storm do wykonywania analizy danych big data w czasie rzeczywistym.

W tym przewodniku szybki start użyto przykładu z projektu Apache [burza-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) do tworzenia i monitorowania topologii Apache Storm w istniejącym klastrze Apache Storm.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Storm w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **burza** dla **typu klastra**.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Tworzenie topologii

1. Nawiąż połączenie z klastrem burzy. Edytuj poniższe polecenie, zastępując je `CLUSTERNAME` nazwą klastra burzy, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Przykład **WORDCOUNT** jest dołączany do klastra usługi HDInsight w lokalizacji `/usr/hdp/current/storm-client/contrib/storm-starter/` . Topologia generuje losowe zdania i zlicza, ile razy występują słowa. Użyj następującego polecenia, aby uruchomić topologię **WORDCOUNT** w klastrze:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Monitorowanie topologii

Burza udostępnia interfejs sieci Web do pracy z uruchomionymi topologiami i znajduje się w klastrze usługi HDInsight.

Wykonaj następujące kroki, aby monitorować topologię za pomocą interfejsu użytkownika platformy Storm:

1. Aby wyświetlić interfejs użytkownika platformy Storm, otwórz w przeglądarce internetowej adres `https://CLUSTERNAME.azurehdinsight.net/stormui`. Zastąp ciąg `CLUSTERNAME` nazwą klastra.

2. W obszarze **Podsumowanie topologii** wybierz wpis **WORDCOUNT** w kolumnie **Nazwa** . Zostaną wyświetlone informacje o topologii.

    ![Pulpit nawigacyjny platformy Storm z informacjami o topologii przykładu z projektu Storm Starter o nazwie WordCount.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    Nowa strona zawiera następujące informacje:

    |Właściwość | Opis |
    |---|---|
    |Statystyka topologii|Podstawowe informacje o wydajności topologii, zorganizowane w okna czasu. Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.|
    |Elementy Spout|Podstawowe informacje na temat elementy Spout, w tym ostatni błąd zwrócony przez każdy elementu Spout.|
    |Elementy Bolt|Podstawowe informacje na temat piorunów.|
    |Konfiguracja topologii|Szczegółowe informacje o konfiguracji topologii.|
    |Activate|Wznawia przetwarzanie zdezaktywowanej topologii.|
    |Dezaktywuj|Wstrzymuje uruchomioną topologię.|
    |Ponownego równoważenia|Dostosowuje równoległość topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Pozwoli to dostosować równoległość topologii w celu kompensacji zwiększonej/zmniejszonej liczby węzłów w klastrze. Aby uzyskać więcej informacji, zobacz [Omówienie równoległości topologii Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Killbit|Kończy topologię burzy po określonym limicie czasu.|

3. Na tej stronie wybierz pozycję z sekcji **Spouts** (Elementy spout) lub **Bolts** (Elementy bolt). Zostaną wyświetlone informacje o wybranym składniku.

    ![Pulpit nawigacyjny Storm z informacjami o wybranych składnikach.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Na nowej stronie są wyświetlane następujące informacje:

    |Właściwość | Opis |
    |---|---|
    |Statystyka elementu Spout/błyskawicy|Podstawowe informacje o wydajności składników zorganizowane w okna czasu. Wybór określonego okna czasowego zmienia przedział czasu dla informacji wyświetlanych w innych sekcjach strony.|
    |Statystyki wejściowe (tylko Piorun)|Informacje o składnikach, które generują dane używane przez pioruna.|
    |Statystyka wyjściowa|Informacje o danych emitowanych przez ten piorun.|
    |Wykonawców|Informacje o wystąpieniach tego składnika.|
    |błędy|Błędy wytwarzane przez ten składnik.|

4. Wyświetlając szczegółowe informacje o elemencie spout lub bolt, wybierz pozycję w kolumnie **Port** w sekcji **Executors** (Wykonawcy), aby wyświetlić szczegóły dotyczące określonego wystąpienia składnika.

```output
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
```

W tym przykładzie wyraz **seven** wystąpił 1493957 razy. Tyle razy został on napotkany od momentu uruchomienia tej topologii.

## <a name="stop-the-topology"></a>Zatrzymywanie topologii

Wróć do strony **Topology summary** (Podsumowanie topologii) dla topologii WordCount, a następnie naciśnij przycisk **Kill** (Kasuj) w obszarze **Topology actions** (Akcje topologii). Po wyświetleniu monitu wprowadź 10 jako liczbę sekund oczekiwania przed zatrzymaniem topologii. Po upłynięciu limitu czasu topologia nie będzie już wyświetlana w obszarze **interfejsu użytkownika platformy Storm** pulpitu nawigacyjnego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto przykładu z projektu Apache [burza-Starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) do tworzenia i monitorowania topologii Apache Storm w istniejącym klastrze Apache Storm. Przejdź do następnego artykułu, aby poznać podstawy zarządzania i monitorowania topologii Apache Storm.

> [!div class="nextstepaction"]
>[Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie nimi](./apache-storm-deploy-monitor-topology-linux.md)
---
title: Interaktywne środowisko PySpark z narzędziami usługi Azure HDInsight
description: Dowiedz się, jak używać narzędzi Azure HDInsight Tools for Visual Studio Code do tworzenia i przesyłania zapytań i skryptów.
keywords: VScode,Narzędzia usługi Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interaktywna gałąź,Interaktywna kwerenda
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 2a725f3c5c9e1428079807b5b76dbe72d416a9c7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393672"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Konfigurowanie interaktywnego środowiska PySpark dla programu Visual Studio Code

Poniższe kroki pokazują, jak skonfigurować środowisko interaktywne PySpark w programie VS Code.

Używamy polecenia **python/pip** do tworzenia środowiska wirtualnego na ścieżce głównej. Jeśli chcesz użyć innej wersji, musisz ręcznie zmienić domyślną wersję polecenia **python/pip.** Więcej szczegółów można znaleźć w [aktualizacji- alternatywy](https://linux.die.net/man/8/update-alternatives).

1. Zainstaluj [Pythona](https://www.python.org/downloads/) i [pip](https://pip.pypa.io/en/stable/installing/).

   + Zainstaluj Pythona z [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Zainstaluj pip [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) z (jeśli nie jest zainstalowany z instalacji Pythona).
   + Sprawdź, czy Python i pip są pomyślnie zainstalowane przy użyciu następujących poleceń. (opcjonalnie)

        ![Sprawdź polecenie wersji programu Python pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Zaleca się ręczne zainstalowanie języka Python zamiast domyślnej wersji systemu macOS.

2. Zainstaluj **virtualenv,** uruchamiając polecenie poniżej.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Inne pakiety

Jeśli natkniesz się na komunikat o błędzie, zainstaluj wymagane pakiety, uruchamiając następujące polecenia:

   ![Zainstaluj pakiet libkrb5 dla pythona](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Uruchom ponownie program VS Code, a następnie wróć do edytora skryptów z systemem **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Następne kroki

### <a name="demo"></a>Demonstracja

* HDInsight dla kodu VS: [Wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Korzystanie z narzędzia Azure HDInsight dla kodu programu Visual Studio](hdinsight-for-vscode.md)
* [Użyj zestawu narzędzi Azure toolkit for IntelliJ, aby tworzyć i przesyłać aplikacje Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark za pośrednictwem SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Użyj zestawu narzędzi Azure toolkit dla intellij do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Tworzenie aplikacji Apache Spark za pomocą narzędzi HDInsight Tools w usłudze Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualize Apache Hive data with Microsoft Power BI in Azure HDInsight (Wizualizowanie danych programu Apache Hive przy użyciu usługi Microsoft Power BI w usłudze Azure HDInsight)](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Uruchamianie zapytań usługi Apache Hive w usłudze Azure HDInsight za pomocą rozwiązania Apache Zeppelin](./interactive-query/hdinsight-connect-hive-zeppelin.md)

---
title: Środowisko interaktywne PySpark przy użyciu narzędzi usługi Azure HDInsight
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty za pomocą narzędzi usługi Azure HDInsight dla Visual Studio Code.
keywords: Programu vscode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: d9a3356ea18ccf4660d05b3fade9d9e6d6cbb5ee
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131344"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Skonfiguruj środowisko interaktywne PySpark dla Visual Studio Code

Poniższe kroki pokazują, jak skonfigurować środowisko interaktywne PySpark w VS Code.

Używamy polecenia **Python/PIP** do kompilowania środowiska wirtualnego w ścieżce domowej. Jeśli chcesz użyć innej wersji, musisz ręcznie zmienić domyślną wersję polecenia **Python/PIP** . Więcej szczegółów można znaleźć w temacie [Update-alternatywy](https://linux.die.net/man/8/update-alternatives).

1. Zainstaluj język [Python](https://www.python.org/downloads/) i narzędzie [PIP](https://pip.pypa.io/en/stable/installing/).

   * Zainstaluj Język Python [https://www.python.org/downloads/](https://www.python.org/downloads/)z programu.
   * Zainstaluj program PIP [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) from (jeśli nie jest zainstalowany w instalacji języka Python).
   * Sprawdź, czy środowisko Python i PIP zostały pomyślnie zainstalowane, używając następujących poleceń. (opcjonalnie)

        ![Sprawdzanie polecenia pip Version języka Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Zalecane jest ręczne zainstalowanie języka Python zamiast używania domyślnej wersji programu macOS.

2. Zainstaluj **virtualenv** , uruchamiając poniższe polecenie.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Inne pakiety

Jeśli zostanie wyświetlony komunikat o błędzie, Zainstaluj wymagane pakiety, uruchamiając następujące polecenia:

   ![Zainstaluj pakiet libkrb5 dla języka Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Uruchom ponownie VS Code, a następnie wróć do edytora skryptów, w którym działa usługa **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Następne kroki

### <a name="demo"></a>Demonstracja

* HDInsight dla VS Code: [wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Użyj narzędzia Azure HDInsight dla Visual Studio Code](hdinsight-for-vscode.md)
* [Używanie Azure Toolkit for IntelliJ do tworzenia i przesyłania Apache Spark aplikacji Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)

---
title: Środowisko interaktywne PySpark przy użyciu narzędzi usługi Azure HDInsight
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty za pomocą narzędzi usługi Azure HDInsight dla Visual Studio Code.
keywords: Programu vscode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 14f0c7177953e43b0c58b4f5432d0c08c5f03f45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876618"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Skonfiguruj środowisko interaktywne PySpark dla Visual Studio Code

Poniższe kroki pokazują, jak skonfigurować środowisko interaktywne PySpark w programu vscode. Ten krok dotyczy tylko użytkowników innych niż Windows.

Używamy polecenia **Python/PIP** do kompilowania środowiska wirtualnego w ścieżce domowej. Jeśli chcesz użyć innej wersji, musisz ręcznie zmienić domyślną wersję polecenia **Python/PIP** . Więcej szczegółów można znaleźć w temacie [Update-alternatywy](https://linux.die.net/man/8/update-alternatives).

1. Zainstaluj język [Python](https://www.python.org/downloads/) i narzędzie [PIP](https://pip.pypa.io/en/stable/installing/).

   * Zainstaluj Język Python z programu [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * Zainstaluj program PIP from [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (jeśli nie jest zainstalowany w instalacji języka Python).
   * Sprawdź, czy środowisko Python i PIP zostały pomyślnie zainstalowane, używając następujących poleceń. (opcjonalnie)

        ![Sprawdzanie polecenia pip Version języka Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Zalecane jest ręczne zainstalowanie języka Python zamiast używania domyślnej wersji programu macOS.

2. Zainstaluj **virtualenv** , uruchamiając poniższe polecenie.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Inne pakiety

W systemie Linux, Jeśli zobaczysz poniższy komunikat o błędzie, Zainstaluj wymagane pakiety, uruchamiając poniższe dwa polecenia.

   ![Zainstaluj pakiet libkrb5 dla języka Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Uruchom ponownie program programu vscode, a następnie wróć do edytora programu vscode i uruchom polecenie **Spark: PySPark Interactive** .

## <a name="next-steps"></a>Następne kroki

### <a name="demo"></a>Demonstracja

* HDInsight dla VS Code: [wideo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Użyj narzędzia Azure HDInsight dla Visual Studio Code](hdinsight-for-vscode.md)
* [Używanie Azure Toolkit for IntelliJ do tworzenia i przesyłania Apache Spark aplikacji Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)

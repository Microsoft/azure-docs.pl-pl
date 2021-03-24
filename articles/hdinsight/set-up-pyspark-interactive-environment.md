---
title: Środowisko interaktywne PySpark przy użyciu narzędzi usługi Azure HDInsight
description: Dowiedz się, jak tworzyć i przesyłać zapytania i skrypty za pomocą narzędzi usługi Azure HDInsight dla Visual Studio Code.
keywords: Programu vscode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: a6247116cdf579691e48883231f57712da36c4ad
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864571"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Skonfiguruj środowisko interaktywne PySpark dla Visual Studio Code

Poniższe kroki pokazują, jak skonfigurować środowisko interaktywne PySpark w programu vscode. Ten krok dotyczy tylko użytkowników innych niż Windows.

Używamy polecenia **Python/PIP** do kompilowania środowiska wirtualnego w ścieżce domowej. Jeśli chcesz użyć innej wersji, musisz ręcznie zmienić domyślną wersję polecenia **Python/PIP** . Więcej szczegółów można znaleźć w temacie [Update-alternatywy](https://linux.die.net/man/8/update-alternatives).

1. Zainstaluj język [Python](https://www.python.org/downloads/) i narzędzie [PIP](https://pip.pypa.io/en/stable/installing/).

   * Zainstaluj Język Python z programu [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * Zainstaluj program PIP from [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (jeśli nie jest zainstalowany w instalacji języka Python).
   * Opcjonalnie Sprawdź, czy środowisko Python i PIP zostały pomyślnie zainstalowane przy użyciu poleceń `python --version` i `pip --version` , odpowiednio. 

     > [!NOTE]
     > Zalecane jest ręczne zainstalowanie języka Python zamiast używania domyślnej wersji programu macOS.

2. Zainstaluj **virtualenv** , uruchamiając poniższe polecenie.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Inne pakiety

W systemie Linux, Jeśli zobaczysz poniższy komunikat o błędzie, Zainstaluj wymagane pakiety, uruchamiając poniższe dwa polecenia.

   :::image type="content" source="./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png" alt-text="Zainstaluj pakiet libkrb5 dla języka Python" border="true":::

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

---
title: Instalowanie Jupyter lokalnie i nawiązywanie połączenia z platformą Spark w usłudze Azure HDInsight
description: Dowiedz się, jak zainstalować Jupyter Notebook lokalnie na komputerze i połączyć go z klastrem Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 16cb8a9c2a951c9f60640248ef74757d1e5ee200
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518927"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalowanie Jupyter Notebook na komputerze i nawiązywanie połączenia z Apache Spark w usłudze HDInsight

W tym artykule dowiesz się, jak zainstalować Jupyter Notebook przy użyciu niestandardowych PySpark (dla języka Python) i Apache Spark jądra (dla Scala) przy użyciu usługi Spark Magic. Następnie można połączyć Notes z klastrem usługi HDInsight.

Istnieją cztery kluczowe kroki związane z instalowaniem Jupyter i nawiązywanie połączenia z usługą Apache Spark w usłudze HDInsight.

* Skonfiguruj klaster Spark.
* Zainstaluj Jupyter Notebook.
* Zainstaluj jądra PySpark i Spark przy użyciu Magic Spark.
* Skonfiguruj magiczną platformę Spark, aby uzyskać dostęp do klastra Spark w usłudze HDInsight.

Aby uzyskać więcej informacji na temat niestandardowych jądra i programu Spark Magic, zobacz [jądra dostępne dla notesów Jupyter przy użyciu klastrów Apache Spark Linux w usłudze HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md). Lokalny Notes nawiązuje połączenie z klastrem usługi HDInsight.

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalowanie Jupyter Notebook na komputerze

Zainstaluj środowisko Python przed zainstalowaniem notesów Jupyter. W ramach [dystrybucji Anaconda](https://www.anaconda.com/download/) zostanie zainstalowany zarówno Język Python, jak i Jupyter Notebook.

Pobierz [Instalatora Anaconda](https://www.anaconda.com/download/) na platformę i uruchom Instalatora. Podczas uruchamiania Kreatora instalacji upewnij się, że wybrano opcję dodania Anaconda do zmiennej PATH.  Zobacz również [Instalowanie Jupyter przy użyciu Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Zainstaluj Magic Spark

1. Wprowadź jedno z poniższych poleceń, aby zainstalować program Spark Magic. Zobacz również [dokumentację sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Wersja klastra | Polecenie instalacji |
    |---|---|
    |v 3.6 i v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. Upewnij `ipywidgets` się, że program jest prawidłowo zainstalowany, uruchamiając następujące polecenie:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>Instalowanie PySpark i jądra platformy Spark

1. Określ `sparkmagic` , gdzie jest zainstalowana, wprowadzając następujące polecenie:

    ```cmd
    pip show sparkmagic
    ```

    Następnie zmień katalog roboczy na **lokalizację** identyfikowaną za pomocą powyższego polecenia.

1. W nowym katalogu roboczym wprowadź co najmniej jedno z poniższych poleceń, aby zainstalować odpowiednie jądra:

    |Jądro | Polecenie |
    |---|---|
    |platforma Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Opcjonalny. Wprowadź poniższe polecenie, aby włączyć rozszerzenie serwera:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Konfigurowanie programu Spark Magic do nawiązywania połączenia z klastrem usługi HDInsight Spark

W tej sekcji skonfigurujesz program Spark, który został wcześniej zainstalowany do łączenia się z klastrem Apache Spark.

1. Uruchom powłokę języka Python za pomocą następującego polecenia:

    ```cmd
    python
    ```

2. Informacje o konfiguracji Jupyter są zwykle przechowywane w katalogu macierzystym użytkowników. Wprowadź następujące polecenie, aby zidentyfikować katalog macierzysty, i Utwórz folder o nazwie **\. sparkmagic**.  Pełna ścieżka zostanie wypełniona.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. W folderze `.sparkmagic` Utwórz plik o nazwie **config.jsna** i Dodaj do niego Poniższy fragment kodu JSON.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Wprowadź następujące zmiany do pliku:

    |Wartość szablonu | Nowa wartość |
    |---|---|
    |UŻ|Logowanie klastra, wartość domyślna to `admin` .|
    |CLUSTERDNSNAME|Nazwa klastra|
    |{BASE64ENCODEDPASSWORD}|Zakodowane w formacie base64 hasło do rzeczywistego hasła.  Hasło Base64 można wygenerować pod adresem [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) .|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Kontynuuj, jeśli jest używany `sparkmagic 0.12.7` (klastry v 3.5 i 3.6).  W przypadku używania `sparkmagic 0.2.3` (klastrów v 3.4) Zastąp ciąg opcją `"should_heartbeat": true` .|

    Możesz zobaczyć pełny przykładowy plik w [przykładowym config.jsna](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Pulsy są wysyłane w celu zapewnienia braku przecieku sesji. Gdy komputer przejdzie w tryb uśpienia lub jest wyłączony, puls nie zostanie wysłany, co spowodowało wyczyszczenie sesji. W przypadku klastrów w wersji 3.4, jeśli chcesz wyłączyć to zachowanie, można ustawić konfigurację usługi Livy `livy.server.interactive.heartbeat.timeout` `0` z poziomu interfejsu użytkownika Ambari. Jeśli w przypadku klastrów 3.5 nie zostanie ustawiona Powyższa konfiguracja 3,5, sesja nie zostanie usunięta.

5. Uruchom Jupyter. Użyj następującego polecenia w wierszu polecenia.

    ```cmd
    jupyter notebook
    ```

6. Sprawdź, czy możesz użyć Magic Spark dostępnego z jądrami. Wykonaj poniższe czynności.

    a. Utwórz nowy notes. W prawym górnym rogu wybierz pozycję **Nowy**. Powinien zostać wyświetlony domyślny jądro **Python 2** lub **Python 3** i zainstalowane jądra. Rzeczywiste wartości mogą się różnić w zależności od opcji instalacji.  Wybierz pozycję **PySpark**.

    ![Dostępne jądra w Jupyter Notebook](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jądra w Jupyter Notebook")

    > [!IMPORTANT]  
    > Po wybraniu pozycji **Nowy** Przejrzyj powłokę pod kątem błędów.  Jeśli zobaczysz błąd, `TypeError: __init__() got an unexpected keyword argument 'io_loop'` może wystąpić znany problem z niektórymi wersjami programu Tornado.  Jeśli tak, Zatrzymaj jądro, a następnie Obniż poziom instalacji Tornado przy użyciu następującego polecenia: `pip install tornado==4.5.3` .

    b. Uruchom Poniższy fragment kodu.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Jeśli można pomyślnie pobrać dane wyjściowe, zostanie przetestowane połączenie z klastrem usługi HDInsight.

    Jeśli chcesz zaktualizować konfigurację notesu w celu nawiązania połączenia z innym klastrem, zaktualizuj config.jsprzy użyciu nowego zestawu wartości, jak pokazano w kroku 3 powyżej.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Dlaczego należy zainstalować program Jupyter na komputerze?

Przyczyny instalacji usługi Jupyter na komputerze, a następnie połączenia jej z klastrem Apache Spark w usłudze HDInsight:

* Zapewnia możliwość lokalnego tworzenia notesów, testowania aplikacji w uruchomionym klastrze, a następnie przekazywania notesów do klastra. Aby przekazać Notes do klastra, można przekazać je przy użyciu Jupyter Notebook z systemem lub w klastrze lub zapisać je `/HdiNotebooks` w folderze na koncie magazynu skojarzonym z klastrem. Aby uzyskać więcej informacji o tym, jak notesy są przechowywane w klastrze, zobacz [gdzie są przechowywane notesy Jupyter](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Dostępne lokalnie notesy umożliwiają łączenie się z różnymi klastrami platformy Spark na podstawie wymagań aplikacji.
* Za pomocą usługi GitHub można zaimplementować system kontroli źródła i mieć kontrolę wersji dla notesów. Możesz również mieć środowisko współpracy, w którym wielu użytkowników może pracować z tym samym notesem.
* Można korzystać z notesów lokalnie bez potrzeby klastra. Do testowania notesów jest potrzebny tylko klaster, a nie ręczne zarządzanie notesami lub środowiskiem programistycznym.
* Skonfigurowanie lokalnego środowiska deweloperskiego może być łatwiejsze niż skonfigurowanie instalacji Jupyter w klastrze.  Można korzystać ze wszystkich programów zainstalowanych lokalnie, bez konfigurowania co najmniej jednego klastra zdalnego.

> [!WARNING]  
> Po zainstalowaniu Jupyter na komputerze lokalnym, wielu użytkowników może uruchamiać ten sam Notes w tym samym klastrze Spark w tym samym czasie. W takiej sytuacji tworzone są wiele sesji usługi Livy. Jeśli napotkasz problem i chcesz debugować ten program, będzie to złożone zadanie umożliwiające śledzenie, która sesja usługi Livy należy do którego użytkownika.  

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Jądra dla Jupyter Notebook na Apache Spark](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter w Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)

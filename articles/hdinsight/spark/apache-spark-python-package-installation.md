---
title: Akcja skryptu dla pakietów języka Python z programem Jupyter w usłudze Azure HDInsight
description: Instrukcje krok po kroku dotyczące konfigurowania notesów Jupyter dostępnych z klastrami usługi HDInsight Spark w celu korzystania z zewnętrznych pakietów języka Python.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: dc1da641ba628cef92250549c1c6b6482cf18b51
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547337"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Bezpieczne zarządzanie środowiskiem Python w usłudze Azure HDInsight za pomocą akcji skryptu

Usługa HDInsight ma dwie wbudowane instalacje języka Python w klastrze Spark, Anaconda Python 2,7 i Python 3,5. Klienci mogą wymagać dostosowania środowiska Python, takiego jak instalowanie zewnętrznych pakietów języka Python. Poniżej przedstawiono najlepsze rozwiązanie bezpiecznego zarządzania środowiskami języka Python dla klastrów Apache Spark w usłudze HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md). Jeśli nie masz jeszcze klastra Spark w usłudze HDInsight, możesz uruchamiać akcje skryptu podczas tworzenia klastra. Zapoznaj się z dokumentacją dotyczącą [korzystania z niestandardowych akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Obsługa oprogramowania Open Source używanego w klastrach usługi HDInsight

Usługa Microsoft Azure HDInsight używa środowiska dla technologii typu "open source" utworzonych wokół Apache Hadoop. Microsoft Azure zapewnia ogólny poziom wsparcia dla technologii typu open source. Aby uzyskać więcej informacji, zobacz [witrynę sieci Web pomocy technicznej dla systemu Azure](https://azure.microsoft.com/support/faq/). Usługa HDInsight zapewnia dodatkowy poziom wsparcia dla wbudowanych składników programu.

Istnieją dwa typy składników typu "open source", które są dostępne w usłudze HDInsight:

|Składnik |Opis |
|---|---|
|Wbudowane|Te składniki są wstępnie zainstalowane w klastrach usługi HDInsight i zapewniają podstawowe funkcje klastra. Na przykład, Apache Hadoop Menedżer zasobów PRZĘDZenia, Apache Hive języka zapytań (HiveQL) i Biblioteka Mahout należy do tej kategorii. Pełna lista składników klastra jest dostępna w temacie [co nowego w wersjach Apache Hadoop klastra dostarczonych przez usługi HDInsight](../hdinsight-component-versioning.md).|
|Niestandardowy|Użytkownik może zainstalować lub używać w obciążeniu dowolnego składnika dostępnego w społeczności lub utworzyć go w ramach obciążenia.|

> [!IMPORTANT]
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane. Pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, która ułatwia dalsze Rozwiązywanie problemu. Pomoc techniczna firmy Microsoft może być w stanie rozwiązać ten problem lub zażądać dostępnych kanałów dla technologii open source, w których znajduje się Szczegółowa wiedza dotycząca tej technologii. Na przykład istnieje wiele witryn społeczności, których można użyć, takich jak: [Microsoft Q&stronie pytania dotyczącej usługi HDInsight](/answers/topics/azure-hdinsight.html) `https://stackoverflow.com` . Również projekty Apache mają witryny projektu `https://apache.org` .

## <a name="understand-default-python-installation"></a>Opis domyślnej instalacji języka Python

Klaster usługi HDInsight Spark jest tworzony z instalacją Anaconda. W klastrze są zainstalowane dwie instalacje języka Python, Anaconda Python 2,7 i Python 3,5. W poniższej tabeli przedstawiono domyślne ustawienia języka Python dla platformy Spark, usługi Livy i Jupyter.

|Ustawienie |Python 2,7|Python 3,5|
|----|----|----|
|Ścieżka|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Wersja platformy Spark|Domyślnie ustawiona na 2,7|Może zmienić konfigurację na 3,5|
|Wersja usługi Livy|Domyślnie ustawiona na 2,7|Może zmienić konfigurację na 3,5|
|Jupyter|Jądro PySpark|Jądro PySpark3|

## <a name="safely-install-external-python-packages"></a>Bezpieczne Instalowanie zewnętrznych pakietów języka Python

Klaster usługi HDInsight zależy od wbudowanego środowiska języka Python, zarówno Python 2,7, jak i Python 3,5. Bezpośrednie Instalowanie pakietów niestandardowych w tych domyślnych środowiskach wbudowanych może spowodować nieoczekiwane zmiany wersji biblioteki. I Przerwij klaster. Aby bezpiecznie instalować niestandardowe zewnętrzne pakiety języka Python dla aplikacji platformy Spark, wykonaj poniższe czynności.

1. Utwórz środowisko wirtualne języka Python za pomocą Conda. Środowisko wirtualne zapewnia izolowane miejsce dla projektów bez przerywania pracy. Podczas tworzenia środowiska wirtualnego języka Python można określić wersję języka Python, która ma być używana. Nadal musisz utworzyć środowisko wirtualne, mimo że chcesz użyć języka Python 2,7 i 3,5. Jest to wymagane, aby upewnić się, że środowisko domyślne klastra nie zostanie zerwane. Uruchom akcje skryptu w klastrze dla wszystkich węzłów z poniższym skryptem, aby utworzyć środowisko wirtualne języka Python.

    -   `--prefix` Określa ścieżkę, w której przebywa środowisko wirtualne Conda. Istnieje kilka konfiguracji, które należy zmienić na podstawie określonej tutaj ścieżki. W tym przykładzie używamy py35new, ponieważ klaster ma już istniejące środowisko wirtualne o nazwie py35.
    -   `python=` Określa wersję języka Python dla środowiska wirtualnego. W tym przykładzie używamy wersji 3,5, która jest taka sama jak w przypadku klastra skompilowanego w jednym z nich. Do utworzenia środowiska wirtualnego można także użyć innych wersji języka Python.
    -   `anaconda` Określa package_spec do instalowania pakietów Anaconda w środowisku wirtualnym.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. W razie konieczności Zainstaluj zewnętrzne pakiety języka Python w utworzonym środowisku wirtualnym. Uruchom akcje skryptu w klastrze dla wszystkich węzłów z poniższym skryptem, aby zainstalować zewnętrzne pakiety języka Python. Musisz mieć tutaj uprawnienia sudo, aby zapisywać pliki w folderze środowiska wirtualnego.

    Aby uzyskać pełną listę dostępnych pakietów, Wyszukaj w [indeksie pakietu](https://pypi.python.org/pypi) . Możesz również uzyskać listę dostępnych pakietów z innych źródeł. Można na przykład zainstalować pakiety udostępniane za poorednictwem [Conda-fałszerstwa](https://conda-forge.org/feedstocks/).

    Użyj poniższego polecenia, aby zainstalować bibliotekę z najnowszą wersją:

    - Użyj kanału Conda:

        -   `seaborn` to nazwa pakietu, który chcesz zainstalować.
        -   `-n py35new` Określ nazwę środowiska wirtualnego, która właśnie została utworzona. Należy koniecznie zmienić nazwę odpowiadającą tworzeniu środowiska wirtualnego.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Lub użyj repozytorium PyPi, aby zmienić `seaborn` i `py35new` odpowiednio:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Użyj poniższego polecenia, aby zainstalować bibliotekę z określoną wersją:

    - Użyj kanału Conda:

        -   `numpy=1.16.1` to nazwa pakietu i wersja, które chcesz zainstalować.
        -   `-n py35new` Określ nazwę środowiska wirtualnego, która właśnie została utworzona. Należy koniecznie zmienić nazwę odpowiadającą tworzeniu środowiska wirtualnego.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Lub użyj repozytorium PyPi, aby zmienić `numpy==1.16.1` i `py35new` odpowiednio:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    Jeśli nie znasz nazwy środowiska wirtualnego, możesz przeprowadzić protokół SSH do węzła głównego klastra i uruchomić polecenie, `/usr/bin/anaconda/bin/conda info -e` Aby wyświetlić wszystkie środowiska wirtualne.

3. Zmień konfigurację platformy Spark i usługi Livy, a następnie wskaż utworzone środowisko wirtualne.

    1. Otwórz interfejs użytkownika Ambari, przejdź do strony Spark2 na karcie konfiguracje.

        ![Zmień konfigurację platformy Spark i usługi Livy na Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Rozwiń pozycję Advanced livy2-ENV, Dodaj poniższe instrukcje na dole. Jeśli środowisko wirtualne zostało zainstalowane z innym prefiksem, należy odpowiednio zmienić ścieżkę.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Zmień konfigurację usługi Livy w Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Rozwiń pozycję Advanced spark2-ENV, Zastąp istniejącą instrukcję Export PYSPARK_PYTHON w dolnej części. Jeśli środowisko wirtualne zostało zainstalowane z innym prefiksem, należy odpowiednio zmienić ścieżkę.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Zmień konfigurację platformy Spark za poorednictwem Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Zapisz zmiany i ponownie uruchom usługi, których to dotyczy. Te zmiany wymagają ponownego uruchomienia usługi Spark2. Interfejs użytkownika Ambari wyświetli monit o wymagane przypomnienie ponownego uruchomienia, kliknij przycisk Uruchom ponownie, aby ponownie uruchomić wszystkie usługi, których to dotyczy.

        ![Ponowne uruchamianie usług](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. Jeśli chcesz użyć nowego utworzonego środowiska wirtualnego w Jupyter. Zmień konfiguracje Jupyter i uruchom ponownie Jupyter. Uruchom akcje skryptu na wszystkich węzłach nagłówka z instrukcją poniżej, aby wskazać Jupyter nowe środowisko wirtualne. Należy zmienić ścieżkę na prefiks określony dla środowiska wirtualnego. Po uruchomieniu tej akcji skryptu należy ponownie uruchomić usługę Jupyter za pomocą interfejsu użytkownika Ambari w celu udostępnienia tej zmiany.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Możesz dwukrotnie potwierdzić środowisko Python w Jupyter Notebook, uruchamiając Poniższy kod:

    ![Sprawdź wersję języka Python w Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Znany problem

Istnieje znana usterka dotycząca wersji Anaconda `4.7.11` , `4.7.12` i `4.8.0` . Jeśli zobaczysz, że akcje skryptu przestaną odpowiadać na `"Collecting package metadata (repodata.json): ...working..."` i zakończą się niepowodzeniem `"Python script has been killed due to timeout after waiting 3600 secs"` . Możesz pobrać [ten skrypt](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) i uruchomić go jako akcje skryptu na wszystkich węzłach, aby rozwiązać ten problem.

Aby sprawdzić wersję programu Anaconda, można przeprowadzić protokół SSH do węzła nagłówka klastra i uruchomić polecenie `/usr/bin/anaconda/bin/conda --v` .

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Pakiety zewnętrzne z notesami Jupyter w Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
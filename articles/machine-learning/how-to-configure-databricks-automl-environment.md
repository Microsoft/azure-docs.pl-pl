---
title: Tworzenie za pomocą AutoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować środowisko programistyczne w Azure Machine Learning i Azure Databricks. Korzystanie z zestawów SDK usługi Azure ML dla kostek dataAutoMLs i datakostks z usługą.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 9bf167f5be9aeb65f0d7c1d69e6687589ebea8a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102498884"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Skonfiguruj środowisko deweloperskie za pomocą Azure Databricks i AutoML w Azure Machine Learning 

Dowiedz się, jak skonfigurować środowisko programistyczne w Azure Machine Learning, które używa Azure Databricks i zautomatyzowanej ML.

Azure Databricks doskonale nadaje się do uruchamiania przepływów pracy uczenia maszynowego dużej skali na skalowalnej platformie Apache Spark w chmurze platformy Azure. Udostępnia środowisko współpracy oparte na notesach z procesorem CPU lub klastrem obliczeniowym opartym na procesorach GPU.

Aby uzyskać informacje o innych środowiskach deweloperskich uczenia maszynowego, zobacz [Konfigurowanie środowiska deweloperskiego języka Python](how-to-configure-environment.md).


## <a name="prerequisite"></a>Wymaganie wstępne

Azure Machine Learning obszar roboczy. Jeśli go nie masz, możesz utworzyć obszar roboczy Azure Machine Learning za pomocą szablonów [Azure Portal](how-to-manage-workspace.md), [interfejsu wiersza polecenia platformy Azure](how-to-manage-workspace-cli.md#create-a-workspace)i [Azure Resource Manager](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks z Azure Machine Learning i AutoML

Azure Databricks integruje się z funkcjami Azure Machine Learning i AutoML. 

Możesz użyć Azure Databricks:

+ Aby nauczyć model przy użyciu platformy Spark MLlib i wdrożyć model w ACI/AKS.
+ Dzięki [automatycznym funkcjom uczenia maszynowego](concept-automated-ml.md) przy użyciu zestawu Azure ml SDK.
+ Jako obiekt docelowy obliczeń z [potoku Azure Machine Learning](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Konfigurowanie klastra datakostki

Utwórz [klaster datakostki](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Niektóre ustawienia są stosowane tylko w przypadku instalowania zestawu SDK w celu automatycznego uczenia maszynowego w kostkach.

**Utworzenie klastra trwa kilka minut.**

Użyj następujących ustawień:

| Ustawienie |Dotyczy| Wartość |
|----|---|---|
| Nazwa klastra |zawsze| yourclustername |
| Wersja Databricks Runtime |zawsze| Środowisko uruchomieniowe 7,1 (Scala 2,21, Spark 3.0.0) — nie ML|
| Wersja języka Python |zawsze| 3 |
| Typ procesu roboczego <br>(określa maksymalną liczbę współbieżnych iteracji) |Zautomatyzowane uczenie maszynowe<br>jedyn| Preferowana maszyna wirtualna zoptymalizowana pod kątem pamięci |
| Ochotnik |zawsze| 2 lub nowszy |
| Włącz Skalowanie automatyczne |Zautomatyzowane uczenie maszynowe<br>jedyn| Usuń zaznaczenie |

Przed kontynuowaniem Zaczekaj, aż klaster zostanie uruchomiony.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Dodawanie zestawu SDK usługi Azure ML do kostek datacegły

Po uruchomieniu klastra [Utwórz bibliotekę](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , aby dołączyć odpowiedni pakiet Azure Machine Learning SDK do klastra. 

Aby użyć zautomatyzowanej ML, przejdź do [dodania zestawu Azure ml SDK z AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Kliknij prawym przyciskiem myszy folder bieżący obszar roboczy, w którym chcesz przechowywać bibliotekę. Wybierz pozycję **Utwórz**  >  **bibliotekę**.
    
    > [!TIP]
    > Jeśli masz starą wersję zestawu SDK, usuń ją z zainstalowanych bibliotek klastra i Przenieś do kosza. Zainstaluj nową wersję zestawu SDK i ponownie uruchom klaster. Jeśli po ponownym uruchomieniu wystąpi problem, odłącz i ponownie Dołącz klaster.

1. Wybierz następującą opcję (nie są obsługiwane żadne inne instalacje zestawu SDK)

   |&nbsp;Dodatki do pakietu SDK &nbsp;|Element źródłowy|&nbsp;Nazwa PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Dla kostek datakostki| Przekaż Język Python lub PyPI | Azure-SDK [datakostki]|

   > [!WARNING]
   > Nie można zainstalować żadnych innych rozszerzeń zestawu SDK. Wybierz tylko opcję [ `databricks` ].

   * Nie wybieraj opcji **Dołącz automatycznie do wszystkich klastrów**.
   * Wybierz pozycję  **Dołącz** obok nazwy klastra.

1. Monitoruj błędy do momentu zmiany stanu **dołączone**, co może potrwać kilka minut.  Jeśli ten krok zakończy się niepowodzeniem:

   Spróbuj ponownie uruchomić klaster przez:
   1. W lewym okienku wybierz pozycję **klastry**.
   1. W tabeli wybierz nazwę klastra.
   1. Na karcie **biblioteki** wybierz pozycję **Uruchom ponownie**.

   Pomyślna instalacja wygląda następująco: 

  ![Zestaw Azure Machine Learning SDK dla datakostki](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Dodawanie zestawu Azure ML SDK z AutoML do datakostki
Jeśli klaster został utworzony przy użyciu Databricks Runtime 7,1 lub nowszego (*nie* ml), uruchom następujące polecenie w pierwszej komórce notesu, aby zainstalować zestaw SDK AML.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
W przypadku Databricks Runtime 7,0 i niższych Zainstaluj zestaw SDK Azure Machine Learning przy użyciu [skryptu init](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>Ustawienia konfiguracji AutoML

W konfiguracji AutoML podczas korzystania z Azure Databricks dodać następujące parametry:

- ```max_concurrent_iterations``` jest oparty na liczbie węzłów procesu roboczego w klastrze.
- ```spark_context=sc``` jest oparty na domyślnym kontekście Spark.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Notesy ML, które współpracują z Azure Databricks

Wypróbuj:
+ Chociaż dostępnych jest wiele przykładowych notesów, **tylko [te przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) współpracują z Azure Databricks.**

+ Zaimportuj te próbki bezpośrednio z obszaru roboczego. Patrz poniżej: ![ Wybierz pozycję Importuj ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ panel importowania](./media/how-to-configure-environment/azure-db-import.png)

+ Dowiedz się [, jak tworzyć potoki z kostkami w ramach obliczeń szkoleniowych](./how-to-create-machine-learning-pipelines.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* W obszarze **datakostki można anulować automatyczne uruchamianie uczenia maszynowego**: w przypadku korzystania z funkcji automatycznego uczenia maszynowego na Azure Databricks, aby anulować uruchomienie i rozpocząć nowe uruchomienie eksperymentu, uruchom ponownie klaster Azure Databricks.

* **Datakosteks >10 iteracji dla automatycznej uczenia maszynowego**: w oknie ustawienia automatycznej uczenia maszynowego, jeśli masz więcej niż 10 iteracji, ustaw wartość `show_output` `False` podczas przesyłania przebiegu.

* **Widżet datakosteks dla zestawu sdk Azure Machine Learning i automatycznej uczenia maszynowego**: widżet zestawu Azure Machine Learning SDK nie jest obsługiwany w notesie datacegły, ponieważ notesy nie mogą analizować widżetów html. Widżet można wyświetlić w portalu przy użyciu tego kodu w języku Python w komórce notesu Azure Databricks:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

* **Błąd podczas instalowania pakietów**

    Azure Machine Learning Instalacja zestawu SDK kończy się niepowodzeniem na Azure Databricks po zainstalowaniu większej liczby pakietów. Niektóre pakiety, takie jak `psutil` , mogą spowodować konflikty. Aby uniknąć błędów instalacji, należy zainstalować pakiety przez zamarzanie wersji biblioteki. Ten problem jest związany z kostkami, a nie z zestawem SDK Azure Machine Learning. Ten problem może również wystąpić z innymi bibliotekami. Przykład:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Alternatywnie można użyć skryptów init w przypadku, gdy występują problemy z instalacją w języku Python. Takie podejście nie jest oficjalnie obsługiwane. Aby uzyskać więcej informacji, zobacz [skrypty init z zakresem klastra](/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts).

* **Błąd importu: nie można zaimportować `Timedelta` nazwy `pandas._libs.tslibs` z**: Jeśli podczas korzystania z automatycznego uczenia maszynowego zostanie wyświetlony ten błąd, Uruchom dwa następujące wiersze w notesie:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Błąd importowania: Brak modułu o nazwie "Pandas. Core. indexes"**: Jeśli ten błąd wystąpi podczas korzystania z zautomatyzowanej uczenia maszynowego:

    1. Uruchom to polecenie, aby zainstalować dwa pakiety w klastrze Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Odłącz i ponownie Dołącz klaster do notesu.
    
    Jeśli te kroki nie rozwiążą problemu, spróbuj ponownie uruchomić klaster.

* **FailToSendFeather**: Jeśli podczas `FailToSendFeather` odczytywania danych w klastrze Azure Databricks wystąpi błąd, zapoznaj się z następującymi rozwiązaniami:
    
    * Uaktualnij `azureml-sdk[automl]` pakiet do najnowszej wersji.
    * Dodaj `azureml-dataprep` wersję 1.1.8 lub nowszą.
    * Dodaj `pyarrow` wersję 0,11 lub nowszą.
  

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning z zestawem danych mnist ręcznie.
- Zapoznaj się z informacjami w [Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro).
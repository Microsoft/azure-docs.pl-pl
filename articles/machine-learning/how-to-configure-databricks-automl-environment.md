---
title: Tworzenie za pomocą autoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować środowisko programistyczne w Azure Machine Learning i Azure Databricks. Korzystanie z zestawów SDK usługi Azure ML dla kostek dataautoMLs i datakostks z usługą.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef8ee7718aabb443fda6cd7b276ee53472261913
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424351"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Skonfiguruj środowisko deweloperskie za pomocą Azure Databricks i autoML w Azure Machine Learning 

Dowiedz się, jak skonfigurować środowisko programistyczne w Azure Machine Learning, które używa Azure Databricks i zautomatyzowanej ML.

Azure Databricks doskonale nadaje się do uruchamiania przepływów pracy uczenia maszynowego dużej skali na skalowalnej platformie Apache Spark w chmurze platformy Azure. Udostępnia środowisko współpracy oparte na notesach z procesorem CPU lub klastrem obliczeniowym opartym na procesorach GPU.

Aby uzyskać informacje o innych środowiskach deweloperskich uczenia maszynowego, zobacz [Konfigurowanie środowiska deweloperskiego języka Python](how-to-configure-environment.md).


## <a name="prerequisite"></a>Wymaganie wstępne

Azure Machine Learning obszar roboczy. Jeśli go nie masz, możesz utworzyć obszar roboczy Azure Machine Learning za pomocą szablonów [Azure Portal](how-to-manage-workspace.md), [interfejsu wiersza polecenia platformy Azure](how-to-manage-workspace-cli.md#create-a-workspace)i [Azure Resource Manager](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks z Azure Machine Learning i autoML

Azure Databricks integruje się z funkcjami Azure Machine Learning i autoML. 

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

1. Monitoruj błędy do momentu zmiany stanu **dołączone** , co może potrwać kilka minut.  Jeśli ten krok zakończy się niepowodzeniem:

   Spróbuj ponownie uruchomić klaster przez:
   1. W lewym okienku wybierz pozycję **klastry**.
   1. W tabeli wybierz nazwę klastra.
   1. Na karcie **biblioteki** wybierz pozycję **Uruchom ponownie**.

   Pomyślna instalacja wygląda następująco: 

  ![Zestaw Azure Machine Learning SDK dla datakostki](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Dodawanie zestawu Azure ML SDK z AutoML do datakostki
Jeśli klaster został utworzony przy użyciu Databricks Runtime 7,1 lub nowszego ( *nie* ml), uruchom następujące polecenie w pierwszej komórce notesu, aby zainstalować zestaw SDK AML.

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

+ Dowiedz się [, jak tworzyć potoki z kostkami w ramach obliczeń szkoleniowych](how-to-create-your-first-pipeline.md).

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning z zestawem danych mnist ręcznie.
- Zapoznaj się z informacjami w [Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

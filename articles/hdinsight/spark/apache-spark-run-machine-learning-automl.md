---
title: Uruchamianie Azure Machine Learning obciążeń na Apache Spark w usłudze HDInsight
description: Dowiedz się, jak uruchamiać obciążenia Azure Machine Learning przy użyciu automatycznego uczenia maszynowego (AutoML) na Apache Spark w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 4087341a9a96ae56c00972f886ce3cc8891750a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929740"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Uruchamianie obciążeń Azure Machine Learning przy użyciu automatycznego uczenia maszynowego na Apache Spark w usłudze HDInsight

Azure Machine Learning upraszcza i przyspiesza kompilowanie, uczenie i wdrażanie modeli uczenia maszynowego. W przypadku automatycznego uczenia maszynowego (AutoML) zaczynasz od danych szkoleniowych, które mają zdefiniowaną funkcję docelową, a następnie iterować przez kombinację wybranych algorytmów i funkcji, aby automatycznie wybierać najlepszy model dla danych na podstawie ocen szkoleniowych. Usługa HDInsight umożliwia klientom obsługę klastrów przy użyciu setek węzłów. AutoML uruchomione na platformie Spark w klastrze usługi HDInsight umożliwia użytkownikom wykorzystanie pojemności obliczeniowej w tych węzłach do uruchamiania zadań szkoleniowych w sposób skalowalny w poziomie, a jednocześnie uruchamianie wielu zadań szkoleniowych. Pozwala to użytkownikom na uruchamianie eksperymentów AutoML podczas udostępniania obliczeń przy użyciu innych obciążeń związanych z danymi Big Data.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalowanie Azure Machine Learning w klastrze usługi HDInsight

Ogólne samouczki dotyczące automatycznego uczenia maszynowego znajdują się w [samouczku: korzystanie z zautomatyzowanej uczenia maszynowego do kompilowania modelu regresji](../../machine-learning/tutorial-auto-train-models.md).
Wszystkie nowe klastry HDInsight-Spark są preinstalowane z użyciem AzureML-AutoML SDK.

> [!Note]
> Pakiety Azure Machine Learning są instalowane w środowisku python3 Conda. Zainstalowaną Jupyter Notebook należy uruchomić przy użyciu jądra PySpark3.

Notesy Zeppelin umożliwiają również korzystanie z AutoML.

> [!Note]
> Zeppelin ma [znany problem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) , w którym PySpark3 nie wybiera właściwej wersji języka Python. Użyj udokumentowanego obejścia.

## <a name="authentication-for-workspace"></a>Uwierzytelnianie dla obszaru roboczego

Tworzenie obszaru roboczego i przesyłanie eksperymentu wymagają tokenu uwierzytelniania. Ten token może być wygenerowany przy użyciu [aplikacji usługi Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). [Użytkownik usługi Azure AD](/azure/python/python-sdk-azure-authenticate) może również użyć do wygenerowania wymaganego tokenu uwierzytelniania, jeśli na koncie nie jest włączone uwierzytelnianie wieloskładnikowe.  

Poniższy fragment kodu tworzy token uwierzytelniania przy użyciu **aplikacji usługi Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Poniższy fragment kodu tworzy token uwierzytelniania przy użyciu użytkownika usługi **Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Ładowanie zestawu danych

Automatyczne Uczenie maszynowe w usłudze Spark używa **przepływów** danych, które są oceniane przez opóźnieniem, niezmienne operacje na dane.  Przepływu danych może załadować zestaw danych z obiektu BLOB z dostępem do odczytu publicznego lub z adresu URL obiektu BLOB z tokenem SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Możesz również zarejestrować magazyn danych z obszarem roboczym przy użyciu jednorazowej rejestracji.

## <a name="experiment-submission"></a>Przesyłanie eksperymentu

W [zautomatyzowanej konfiguracji usługi Machine Learning](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)Właściwość `spark_context` powinna być ustawiona, aby pakiet był uruchamiany w trybie rozproszonym. Właściwość `concurrent_iterations` , która jest maksymalną liczbą iteracji wykonywanych równolegle, powinna być ustawiona na liczbę mniejszą niż rdzenie wykonawcze dla aplikacji Spark.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat motywacji za automatyczne Uczenie maszynowe, zobacz [modele wydań przy użyciu zautomatyzowanej nauki maszyn firmy Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Aby uzyskać więcej informacji na temat korzystania z funkcji zautomatyzowanej platformy Azure ML, zobacz [nowe automatyczne Uczenie maszynowe w Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML projekt z Microsoft Research](https://www.microsoft.com/research/project/automl/)

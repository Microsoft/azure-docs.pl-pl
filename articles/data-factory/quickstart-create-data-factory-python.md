---
title: 'Szybki Start: Tworzenie Azure Data Factory przy użyciu języka Python'
description: Użyj fabryki danych do kopiowania danych z jednej lokalizacji w usłudze Azure Blob Storage do innej lokalizacji.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2018
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: e511b8b5505f1fa8713d81d0ce959df32ef28503
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013330"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Szybki start: Tworzenie fabryki danych i potoku przy użyciu języka Python

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Bieżąca wersja](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku szybki start utworzysz fabrykę danych przy użyciu języka Python. Potok w tej fabryce danych kopiuje dane z jednego folderu do innego folderu w usłudze Azure Blob Storage.

Azure Data Factory to oparta na chmurze usługa integracji danych, która umożliwia tworzenie przepływów pracy opartych na danych na potrzeby organizowania i automatyzowania przenoszenia i przekształcania danych. Za pomocą Azure Data Factory można tworzyć i planować przepływy pracy oparte na danych, nazywane potokami.

Potoki mogą pozyskiwanie danych z różnych magazynów danych. Potoki przetwarzają lub przekształcają dane przy użyciu usług obliczeniowych, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics i Azure Machine Learning. Potoki publikują dane wyjściowe w magazynach danych, takich jak Azure Synapse Analytics (dawniej SQL Data Warehouse) dla aplikacji analizy biznesowej (BI).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Konto usługi Azure Storage](../storage/common/storage-account-create.md).

* [Eksplorator usługi Azure Storage](https://storageexplorer.com/) (opcjonalnie).

* [Aplikacja w Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Należy pamiętać o następujących wartościach, które mają być używane w kolejnych krokach: **Identyfikator aplikacji**, **klucz uwierzytelniania** i **Identyfikator dzierżawy**. Przypisz aplikację do roli **współautor** , wykonując instrukcje opisane w tym samym artykule.

## <a name="create-and-upload-an-input-file"></a>Tworzenie i przekazywanie pliku wejściowego

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **input.txt**.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Użyj narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/), aby utworzyć kontener **adfv2tutorial** i folder **input** w kontenerze. Następnie przekaż plik **input.txt** do folderu **input**.

## <a name="install-the-python-package"></a>Instalacja pakietu języka Python

1. Otwórz terminal lub wiersz polecenia z uprawnieniami administratora. 
2. Najpierw zainstaluj pakiet języka Python dla zasobów zarządzania platformy Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Aby zainstalować pakiet języka Python dla usługi Data Factory, uruchom następujące polecenie:

    ```python
    pip install azure-mgmt-datafactory
    ```

    [Zestaw SDK języka Python dla usługi Data Factory](https://github.com/Azure/azure-sdk-for-python) obsługuje język Python w wersji 2.7, 3.3, 3.4, 3.5, 3.6 i 3.7.

## <a name="create-a-data-factory-client"></a>Tworzenie klienta fabryki danych

1. Utwórz plik o nazwie **datafactory.py**. Dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Dodaj następujące funkcje, które drukują informacje.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Dodaj do metody **Main** poniższy kod, który tworzy wystąpienie klasy DataFactoryManagementClient. Ten obiekt jest używany do tworzenia fabryki danych, połączonej usługi, zestawów danych i potoku. Umożliwia on również monitorowanie szczegółów uruchomienia potoku. Ustaw zmienną **subscription_id** na identyfikator swojej subskrypcji platformy Azure. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<Specify your Azure Subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = 'ADFTutorialResourceGroup'

        # The data factory name. It must be globally unique.
        df_name = '<Specify a name for the data factory. It must be globally unique>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ServicePrincipalCredentials(client_id='<Active Directory application/client ID>', secret='<client secret>', tenant='<Active Directory tenant ID>')
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'eastus'}
        df_params = {'location':'eastus'}
    ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Dodaj do metody **Main** poniższy kod, który tworzy **fabrykę danych**. Jeśli grupa zasobów już istnieje, dodaj komentarz do pierwszej instrukcji `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Tworzenie usługi połączonej

Dodaj do metody **Main** poniższy kod, który tworzy **połączoną usługę Azure Storage**.

Połączone usługi tworzy się w fabryce danych w celu połączenia magazynów danych i usług obliczeniowych z fabryką danych. W tym samouczku Szybki start musisz utworzyć tylko jedną połączoną usługę Azure Storage zarówno jako źródło kopii, jak i ujście magazynu o nazwie „AzureStorageLinkedService” w przykładzie. Zastąp wartości `<storageaccountname>` i `<storageaccountkey>` nazwą i kluczem konta usługi Azure Storage.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Tworzenie zestawów danych

W tej sekcji utworzysz zestaw danych źródła i ujścia.

### <a name="create-a-dataset-for-source-azure-blob"></a>Tworzenie zestawu danych źródłowego obiektu blob platformy Azure

Dodaj do metody Main poniższy kod, który tworzy zestaw danych obiektu blob platformy Azure. Aby uzyskać informacje o właściwościach zestawu danych obiektu blob platformy Azure, zobacz artykuł dotyczący [łącznika obiektu blob platformy Azure](connector-azure-blob-storage.md#dataset-properties).

Należy zdefiniować zestaw danych reprezentujący źródło danych w obiekcie blob platformy Azure. Ten zestaw danych obiektu blob odwołuje się do połączonej usługi Azure Storage utworzonej w poprzednim kroku.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(linked_service_name=ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Tworzenie zestawu danych obiektu blob platformy Azure ujścia

Dodaj do metody Main poniższy kod, który tworzy zestaw danych obiektu blob platformy Azure. Aby uzyskać informacje o właściwościach zestawu danych obiektu blob platformy Azure, zobacz artykuł dotyczący [łącznika obiektu blob platformy Azure](connector-azure-blob-storage.md#dataset-properties).

Należy zdefiniować zestaw danych reprezentujący źródło danych w obiekcie blob platformy Azure. Ten zestaw danych obiektu blob odwołuje się do połączonej usługi Azure Storage utworzonej w poprzednim kroku.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Dodaj do metody **Main** poniższy kod, który tworzy **potok z działaniem kopiowania**.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

Dodaj do metody **Main** poniższy kod, który **wyzwala uruchomienie potoku**.

```python
    #Create a pipeline run.
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Monitorowanie uruchomienia potoku

Aby monitorować uruchomienie potoku, dodaj do metody **Main** następujący kod:

```python
    #Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Teraz dodaj poniższą instrukcję w celu wywołania metody **Main** po uruchomieniu programu:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Pełny skrypt

Oto pełny kod języka Python:

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time


def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)
    print("\n")


def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n")


def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(
            activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(
            activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(
            activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<your Azure subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<Azure resource group name>'

    # The data factory name. It must be globally unique.
    df_name = '<Your data factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ServicePrincipalCredentials(
        client_id='<Active Directory client ID>', secret='<client secret>', tenant='<tenant ID>')
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location': 'eastus'}
    df_params = {'location': 'eastus'}

    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # Specify the name and key of your Azure Storage account
    storage_string = SecureString(
        value='DefaultEndpointsProtocol=https;AccountName=<storage account name>;AccountKey=<storage account key>')

    ls_azure_storage = AzureStorageLinkedService(
        connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(
        rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob = AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie zweryfikuj wykonywanie potoku.

Konsola wypisuje postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i uruchomienia potoku. Poczekaj na wyświetlenie szczegółów uruchomienia działania kopiowania z rozmiarem odczytanych/zapisanych danych. Następnie użyj narzędzi takich jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) , aby sprawdzić, czy obiekty blob zostały skopiowane do "outputBlobPath" z "inputBlobPath", jak określono w zmiennych.

Oto przykładowe dane wyjściowe:

```json
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć fabrykę danych, dodaj następujący kod do programu:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Następne kroki

Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. Zapoznaj się z [samouczkami](tutorial-copy-data-dot-net.md), aby dowiedzieć się więcej o korzystaniu z usługi Data Factory w dalszych scenariuszach.

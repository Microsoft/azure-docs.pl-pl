---
title: Zmień klucze dostępu do konta magazynu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zmienić klucze dostępu dla konta usługi Azure Storage używanego w obszarze roboczym. Azure Machine Learning używa konta usługi Azure Storage do przechowywania danych i modeli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 787f7727e18b7055055ed2b764ddce5be20f5afc
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503440"
---
# <a name="regenerate-storage-account-access-keys"></a>Ponowne generowanie kluczy dostępu do konta magazynu


Dowiedz się, jak zmienić klucze dostępu dla kont usługi Azure Storage używanych przez Azure Machine Learning. Azure Machine Learning mogą używać kont magazynu do przechowywania danych lub modeli szkoleniowych.

Ze względów bezpieczeństwa może zajść potrzeba zmiany kluczy dostępu dla konta usługi Azure Storage. Po ponownym wygenerowaniu klucza dostępu Azure Machine Learning należy zaktualizować, aby użyć nowego klucza. Azure Machine Learning może korzystać z konta magazynu zarówno dla magazynu modelowego, jak i magazynu danych.

> [!IMPORTANT]

> Poświadczenia zarejestrowane w magazynach danych są zapisywane w Azure Key Vault skojarzonych z obszarem roboczym. Jeśli dla Key Vault włączono opcję [nietrwałego usuwania](../key-vault/general/soft-delete-overview.md) , ten artykuł zawiera instrukcje dotyczące aktualizowania poświadczeń. Jeśli wyrejestrujesz magazyn danych i spróbujesz ponownie zarejestrować go pod tą samą nazwą, ta akcja zakończy się niepowodzeniem. Zobacz [Włączanie usuwania nietrwałego dla istniejącego magazynu kluczy,]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault) aby dowiedzieć się, jak włączyć usuwanie nietrwałe w tym scenariuszu.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .

* [Zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install).

* [Rozszerzenie interfejsu wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Fragmenty kodu w tym dokumencie zostały przetestowane z wersją 1.0.83 zestawu Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Co należy zaktualizować

Konta magazynu mogą być używane przez obszar roboczy Azure Machine Learning (przechowywanie dzienników, modeli, migawek itp.) oraz magazynu danych. Proces aktualizowania obszaru roboczego to pojedyncze polecenie interfejsu wiersza polecenia platformy Azure, które można wykonać po zaktualizowaniu klucza magazynu. Proces aktualizowania magazynów danych jest większy i wymaga odnajdywania elementów, które są obecnie używane w ramach konta magazynu, a następnie ich ponownego rejestrowania.

> [!IMPORTANT]
> Zaktualizuj obszar roboczy przy użyciu interfejsu wiersza polecenia platformy Azure i magazynów danych przy użyciu języka Python w tym samym czasie. Aktualizowanie tylko jednej lub drugiej nie jest wystarczające i może spowodować błędy do momentu zaktualizowania obu tych wartości.

Aby odnaleźć konta magazynu, które są używane przez magazyny danych, użyj następującego kodu:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Ten kod wyszukuje wszystkie zarejestrowane magazyny danych korzystające z usługi Azure Storage i wyświetla następujące informacje:

* Nazwa magazynu danych: Nazwa magazynów danych, w ramach którego zarejestrowano konto magazynu.
* Nazwa konta magazynu: nazwa konta usługi Azure Storage.
* Kontener: kontener na koncie magazynu, który jest używany przez tę rejestrację.

Wskazuje również, czy magazyn danych jest przeznaczony dla obiektów blob platformy Azure, czy udziału plików platformy Azure, ponieważ istnieją różne metody ponownego rejestrowania każdego typu magazynu danych.

Jeśli istnieje wpis dla konta magazynu, w którym planujesz ponownie wygenerować klucze dostępu, Zapisz nazwę magazynu danych, nazwę konta magazynu i nazwę kontenera.

## <a name="update-the-access-key"></a>Aktualizowanie klucza dostępu

Aby zaktualizować Azure Machine Learning, aby użyć nowego klucza, wykonaj następujące czynności:

> [!IMPORTANT]
> Wykonaj wszystkie kroki, zaktualizuj oba obszary robocze przy użyciu interfejsu wiersza polecenia i magazynów danych przy użyciu języka Python. Aktualizacja tylko jednego lub drugiego może spowodować błędy do momentu zaktualizowania obu tych metod.

1. Wygeneruj ponownie klucz. Aby uzyskać informacje na temat ponownego generowania klucza dostępu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md). Zapisz nowy klucz.

1. Obszar roboczy Azure Machine Learning automatycznie zsynchronizuje nowy klucz i zacznie korzystać z niego po godzinie. Aby wymusić natychmiastowe zsynchronizowanie obszaru roboczego z nowym kluczem, wykonaj następujące czynności:

    1. Aby zalogować się do subskrypcji platformy Azure, która zawiera obszar roboczy, za pomocą następującego polecenia platformy Azure:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Aby zaktualizować obszar roboczy pod kątem używania nowego klucza, użyj następującego polecenia. Zastąp ciąg `myworkspace` nazwą obszaru roboczego Azure Machine Learning i Zamień na `myresourcegroup` nazwę grupy zasobów platformy Azure, która zawiera obszar roboczy.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        To polecenie automatycznie synchronizuje nowe klucze dla konta usługi Azure Storage używanego w obszarze roboczym.

1. Można ponownie zarejestrować magazyny danych korzystające z konta magazynu za pośrednictwem zestawu SDK lub [Azure Machine Learning Studio](https://ml.azure.com).
    1. **Aby ponownie zarejestrować magazyny danych za pomocą zestawu SDK języka Python**, użyj wartości z sekcji [co należy zaktualizować](#whattoupdate) , a klucz z kroku 1 z poniższym kodem. 
    
        Ponieważ `overwrite=True` został określony, ten kod zastępuje istniejącą rejestrację i aktualizuje ją w celu użycia nowego klucza.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **Aby ponownie zarejestrować magazyny danych za pośrednictwem programu Studio**, wybierz pozycję **magazyny** danych z okienka po lewej stronie w programie Studio. 
        1. Wybierz magazyn danych, który chcesz zaktualizować.
        1. Wybierz przycisk **Aktualizuj poświadczenia** w lewym górnym rogu. 
        1. Użyj nowego klucza dostępu z kroku 1, aby wypełnić formularz, a następnie kliknij przycisk **Zapisz**.
        
            Jeśli aktualizujesz poświadczenia dla **domyślnego magazynu** danych, wykonaj ten krok i powtórz krok 2b, aby ponownie zsynchronizować nowy klucz z domyślnym magazynem danych obszaru roboczego. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rejestrowania magazynów danych, zobacz [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) odwołanie do klasy.
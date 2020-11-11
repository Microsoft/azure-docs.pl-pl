---
title: Uwierzytelniaj dane wyjściowe obiektów blob z zarządzaną tożsamością Azure Stream Analytics
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania Azure Stream Analytics w danych wyjściowych usługi Azure Blob Storage.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/11/2020
ms.openlocfilehash: f14ded89ad294abbfaf9861e5f4caf17dd82fb98
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490716"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Uwierzytelnianie zadania Azure Stream Analytics za pomocą tożsamości zarządzanej na platformie Azure Blob Storage dane wyjściowe

[Uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla danych wyjściowych w usłudze Azure Blob storage zapewnia Stream Analytics zadania bezpośredniego dostępu do konta magazynu zamiast używać parametrów połączenia. Oprócz ulepszonych zabezpieczeń ta funkcja umożliwia również zapisywanie danych na koncie magazynu w Virtual Network (VNET) na platformie Azure.

W tym artykule pokazano, jak włączyć zarządzaną tożsamość dla danych wyjściowych obiektu BLOB Stream Analytics zadania za pomocą Azure Portal i wdrożenia Azure Resource Manager.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Utwórz zadanie Stream Analytics przy użyciu Azure Portal

1. Utwórz nowe zadanie Stream Analytics lub Otwórz istniejące zadanie w Azure Portal. Na pasku menu znajdującym się po lewej stronie ekranu wybierz pozycję **zarządzana tożsamość** znajdującą się w obszarze **Konfiguruj**. Upewnij się, że wybrano opcję Użyj tożsamości zarządzanej przypisanej do systemu, a następnie kliknij przycisk **Zapisz** u dołu ekranu.

   ![Konfigurowanie tożsamości zarządzanej Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. W oknie właściwości danych wyjściowych ujścia danych wyjściowych usługi Azure Blob Storage wybierz listę rozwijaną tryb uwierzytelniania i wybierz pozycję **zarządzana tożsamość**. Aby uzyskać informacje dotyczące innych właściwości danych wyjściowych, zobacz informacje o [wyjściach z Azure Stream Analytics](./stream-analytics-define-outputs.md). Po zakończeniu kliknij pozycję **Zapisz**.

   ![Konfigurowanie danych wyjściowych usługi Azure Blob Storage](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Po utworzeniu zadania zapoznaj się z sekcją [udziel Stream Analytics dostęp do konta magazynu](#give-the-stream-analytics-job-access-to-your-storage-account) w tym artykule.

## <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

Za pomocą Azure Resource Manager można w pełni zautomatyzować wdrożenie zadania Stream Analytics. Szablony Menedżer zasobów można wdrożyć przy użyciu Azure PowerShell lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/). W poniższych przykładach użyto interfejsu wiersza polecenia platformy Azure.


1. Zasób **Microsoft. StreamAnalytics/streamingjobs** można utworzyć przy użyciu tożsamości zarządzanej, dołączając następującą właściwość w sekcji resource szablonu Menedżer zasobów:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ta właściwość nakazuje Azure Resource Manager tworzenia tożsamości dla zadania Stream Analytics i zarządzania nią. Poniżej znajduje się przykładowy szablon Menedżer zasobów, który wdraża zadanie Stream Analytics z włączoną tożsamością zarządzaną oraz ujścia danych wyjściowych obiektu BLOB, które korzysta z tożsamości zarządzanej:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Powyższe zadanie można wdrożyć do **przykładowej** grupy zasobów przy użyciu poniższego polecenia interfejsu CLI platformy Azure:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po utworzeniu zadania można użyć Azure Resource Manager, aby pobrać pełną definicję zadania.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Powyższe polecenie zwróci odpowiedź podobną do poniższej:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Zanotuj **principalId** z definicji zadania, która identyfikuje tożsamość zarządzaną zadania w ramach Azure Active Directory i zostanie użyta w następnym kroku, aby udzielić Stream Analytics dostęp do konta magazynu.

3. Po utworzeniu zadania zapoznaj się z sekcją [udziel Stream Analytics dostęp do konta magazynu](#give-the-stream-analytics-job-access-to-your-storage-account) w tym artykule.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Przyznaj Stream Analytics dostęp do konta magazynu

Istnieją dwa poziomy dostępu, które można wybrać, aby wykonać zadanie Stream Analytics:

1. **Dostęp na poziomie kontenera:** ta opcja daje zadanie dostępu do określonego istniejącego kontenera.
2. **Dostęp na poziomie konta:** ta opcja zapewnia ogólny dostęp do konta magazynu, w tym możliwość tworzenia nowych kontenerów.

Jeśli nie potrzebujesz zadania tworzenia kontenerów w Twoim imieniu, należy wybrać opcję **dostęp do poziomu kontenera** , ponieważ ta opcja przyznaje zadanie minimalny wymagany poziom dostępu. Poniższe opcje są wyjaśnione poniżej dla Azure Portal i wiersza polecenia.

### <a name="grant-access-via-the-azure-portal"></a>Udzielanie dostępu za pośrednictwem Azure Portal

#### <a name="container-level-access"></a>Dostęp na poziomie kontenera

1. Przejdź do okienka konfiguracja kontenera w ramach konta magazynu.

2. Wybierz pozycję **Access Control (IAM)** po lewej stronie.

3. W sekcji "Dodawanie przypisania roli" kliknij pozycję **Dodaj**.

4. W okienku przypisanie roli:

    1. Ustaw **rolę** na "Współautor danych obiektu blob magazynu"
    2. Upewnij się, że pole wyboru **Przypisz dostęp do** jest ustawione na wartość "użytkownik, Grupa lub nazwa główna usługi" usługi Azure AD.
    3. Wpisz nazwę zadania Stream Analytics w polu wyszukiwania.
    4. Wybierz zadanie Stream Analytics i kliknij przycisk **Zapisz**.

   ![Przyznaj dostęp do kontenera](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Dostęp na poziomie konta

1. Przejdź do konta magazynu.

2. Wybierz pozycję **Access Control (IAM)** po lewej stronie.

3. W sekcji "Dodawanie przypisania roli" kliknij pozycję **Dodaj**.

4. W okienku przypisanie roli:

    1. Ustaw **rolę** na "Współautor danych obiektu blob magazynu"
    2. Upewnij się, że pole wyboru **Przypisz dostęp do** jest ustawione na wartość "użytkownik, Grupa lub nazwa główna usługi" usługi Azure AD.
    3. Wpisz nazwę zadania Stream Analytics w polu wyszukiwania.
    4. Wybierz zadanie Stream Analytics i kliknij przycisk **Zapisz**.

   ![Przyznaj dostęp do konta](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Udzielanie dostępu za pośrednictwem wiersza polecenia

#### <a name="container-level-access"></a>Dostęp na poziomie kontenera

Aby udzielić dostępu do określonego kontenera, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Dostęp na poziomie konta

Aby udzielić dostępu do całego konta, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Włącz dostęp do sieci wirtualnej

Podczas konfigurowania **zapór i sieci wirtualnych** konta magazynu można opcjonalnie zezwolić na ruch sieciowy z innych zaufanych usług firmy Microsoft. Gdy Stream Analytics uwierzytelnia się przy użyciu tożsamości zarządzanej, zapewnia potwierdzenie, że żądanie pochodzi z zaufanej usługi. Poniżej znajdują się instrukcje włączania tego wyjątku dostępu do sieci wirtualnej.

1.    Przejdź do okienka "zapory i sieci wirtualne" w okienku Konfiguracja konta magazynu.
2.    Upewnij się, że opcja "Zezwalaj na dostęp do tego konta magazynu zaufanych usług firmy Microsoft" jest włączona.
3.    Jeśli ją włączono, kliknij przycisk **Zapisz**.

   ![Włącz dostęp do sieci wirtualnej](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>Usuwanie tożsamości zarządzanej

Tożsamość zarządzana utworzona dla zadania Stream Analytics jest usuwana tylko po usunięciu zadania. Nie można usunąć tożsamości zarządzanej bez usuwania zadania. Jeśli nie chcesz już używać tożsamości zarządzanej, możesz zmienić metodę uwierzytelniania dla danych wyjściowych. Tożsamość zarządzana będzie nadal istnieć do momentu usunięcia zadania i zostanie użyta, jeśli zdecydujesz się ponownie użyć uwierzytelniania tożsamości zarządzanej.

## <a name="limitations"></a>Ograniczenia
Poniżej znajdują się bieżące ograniczenia tej funkcji:

1. Klasyczne konta usługi Azure Storage.

2. Konta platformy Azure bez Azure Active Directory.

3. Dostęp z wieloma dzierżawcami nie jest obsługiwany. Nazwa główna usługi utworzona dla danego zadania Stream Analytics musi znajdować się w tej samej dzierżawie Azure Active Directory, w której zadanie zostało utworzone i nie można jej używać z zasobem znajdującym się w innej dzierżawie Azure Active Directory.

4. [Tożsamość przypisana przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md) nie jest obsługiwana. Oznacza to, że użytkownik nie może wprowadzić własnej nazwy głównej usługi, która będzie używana przez ich Stream Analytics zadanie. Nazwa główna usługi musi być generowana przez Azure Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Poznanie danych wyjściowych z Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics partycjonowanie niestandardowego obiektu BLOB](./stream-analytics-custom-path-patterns-blob-storage-output.md)
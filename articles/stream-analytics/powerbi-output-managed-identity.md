---
title: Użyj tożsamości zarządzanej do uwierzytelnienia zadania Azure Stream Analytics do Power BI danych wyjściowych
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania Azure Stream Analytics do Power BI danych wyjściowych.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 3/10/2020
ms.openlocfilehash: 7c1ddbbbd8198cf769e89cfa824de370184a992c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589688"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Za pomocą tożsamości zarządzanej można uwierzytelniać zadanie Azure Stream Analytics w Power BI

[Uwierzytelnianie tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla danych wyjściowych do Power BI daje Stream Analytics zadania bezpośredniego dostępu do obszaru roboczego w ramach konta Power BI. Ta funkcja umożliwia wdrażanie Stream Analytics zadań w pełni zautomatyzowanej, ponieważ nie jest już wymagane, aby użytkownik mógł interakcyjnie zalogować się do Power BI za pośrednictwem Azure Portal. Ponadto, długotrwałe zadania, które zapisują w Power BI są teraz lepiej obsługiwane, ponieważ nie trzeba okresowo ponownie autoryzować zadania.

W tym artykule opisano sposób włączania zarządzanej tożsamości dla Power BI danych wyjściowych zadania Stream Analytics za pomocą Azure Portal oraz wdrożenia Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z tej funkcji wymagane są następujące elementy:

- Konto Power BI z [licencją Pro](/power-bi/service-admin-purchasing-power-bi-pro).

- Uaktualniony obszar roboczy w ramach konta Power BI. Aby uzyskać więcej informacji, zobacz [anons Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) tej funkcji.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Tworzenie zadania Stream Analytics przy użyciu Azure Portal

1. Utwórz nowe zadanie Stream Analytics lub Otwórz istniejące zadanie w Azure Portal. Na pasku menu znajdującym się po lewej stronie ekranu wybierz pozycję **zarządzana tożsamość** znajdującą się w obszarze **Konfiguruj**. Upewnij się, że wybrano opcję Użyj tożsamości zarządzanej przypisanej do systemu, a następnie wybierz przycisk **Zapisz** u dołu ekranu.

   ![Konfigurowanie tożsamości zarządzanej Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Przed skonfigurowaniem danych wyjściowych Udziel Stream Analytics dostęp do obszaru roboczego Power BI, postępując zgodnie z instrukcjami podanymi w sekcji [zapewnianie zadań Stream Analytics dostęp do obszaru roboczego Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) w tym artykule.

3. Przejdź do sekcji dane **wyjściowe** zadania analitycznego strumienia, wybierz pozycję **+ Dodaj**, a następnie wybierz pozycję **Power BI**. Następnie wybierz przycisk **Autoryzuj** i zaloguj się przy użyciu konta Power BI.

   ![Autoryzuj przy użyciu konta Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Po autoryzowaniu lista rozwijana będzie zawierać wszystkie obszary robocze, do których masz dostęp. Wybierz obszar roboczy, który został autoryzowany w poprzednim kroku. Następnie wybierz opcję **tożsamość zarządzana** jako "tryb uwierzytelniania". Na koniec wybierz przycisk **Zapisz** .

   ![Konfigurowanie danych wyjściowych Power BI z tożsamością zarządzaną](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

Azure Resource Manager pozwala w pełni zautomatyzować wdrożenie zadania Stream Analytics. Szablony Menedżer zasobów można wdrożyć przy użyciu Azure PowerShell lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/). W poniższych przykładach użyto interfejsu wiersza polecenia platformy Azure.


1. Zasób **Microsoft. StreamAnalytics/streamingjobs** można utworzyć przy użyciu tożsamości zarządzanej, dołączając następującą właściwość w sekcji resource szablonu Menedżer zasobów:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Ta właściwość nakazuje Azure Resource Manager tworzenia tożsamości dla zadania Stream Analytics i zarządzania nią. Poniżej znajduje się przykładowy szablon Menedżer zasobów, który wdraża zadanie Stream Analytics z włączoną tożsamością zarządzaną oraz Power BIego ujścia danych wyjściowych, który używa tożsamości zarządzanej:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    Wdróż powyższe zadanie w grupie zasobów **przykład** , korzystając z poniższego polecenia platformy Azure:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Po utworzeniu zadania użyj Azure Resource Manager, aby pobrać pełną definicję zadania.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Powyższe polecenie zwróci odpowiedź podobną do poniższej:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Jeśli planujesz użyć interfejsu API REST Power BI, aby dodać zadanie Stream Analytics do obszaru roboczego Power BI, zanotuj zwracaną wartość "principalId".

3. Teraz, gdy zadanie zostanie utworzone, przejdź do sekcji [udziel Stream Analytics dostęp do obszaru roboczego Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) w tym artykule.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Przyznaj Stream Analytics dostęp do obszaru roboczego Power BI

Teraz, gdy zadanie Stream Analytics zostało utworzone, można uzyskać dostęp do Power BI obszaru roboczego. Po uzyskaniu dostępu do zadania poczekaj kilka minut na propagację tożsamości.

### <a name="use-the-power-bi-ui"></a>Korzystanie z Power BI interfejsu użytkownika

   > [!Note]
   > Aby dodać zadanie Stream Analytics do obszaru roboczego Power BI za pomocą interfejsu użytkownika, należy również włączyć dostęp jednostki usługi w **ustawieniach dewelopera** w portalu administratora Power BI. Aby uzyskać więcej informacji, zobacz Wprowadzenie do jednostki [usługi](/power-bi/developer/embed-service-principal) .

1. Przejdź do ustawień dostępu obszaru roboczego. Zobacz ten artykuł, aby uzyskać więcej szczegółów: [nadaj dostęp do obszaru roboczego](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Wpisz nazwę zadania Stream Analytics w polu tekstowym i wybierz opcję **współautor** jako poziom dostępu.

3. Wybierz pozycję **Dodaj** i zamknij okienko.

   ![Dodawanie zadania Stream Analytics do Power BI obszaru roboczego](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Korzystanie z Power BI poleceń cmdlet programu PowerShell

1. Zainstaluj Power BI `MicrosoftPowerBIMgmt` polecenia cmdlet programu PowerShell.

   > [!Important]
   > Upewnij się, że używasz wersji 1.0.821 lub nowszej poleceń cmdlet.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Zaloguj się do Power BI.

```powershell
Login-PowerBI
```

3. Dodaj zadanie Stream Analytics jako współautor do obszaru roboczego.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Korzystanie z interfejsu API REST Power BI

Zadanie Stream Analytics można również dodać jako współautor do obszaru roboczego za pomocą interfejsu API REST "Dodaj użytkownika grupy" bezpośrednio. Pełną dokumentację tego interfejsu API można znaleźć tutaj: [grupy — Dodaj użytkownika grupy](/rest/api/power-bi/groups/addgroupuser).

**Przykładowe żądanie**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Treść żądania
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="remove-managed-identity"></a>Usuwanie tożsamości zarządzanej

Tożsamość zarządzana utworzona dla zadania Stream Analytics jest usuwana tylko po usunięciu zadania. Nie można usunąć tożsamości zarządzanej bez usuwania zadania. Jeśli nie chcesz już używać tożsamości zarządzanej, możesz zmienić metodę uwierzytelniania dla danych wyjściowych. Tożsamość zarządzana będzie nadal istnieć do momentu usunięcia zadania i zostanie użyta, jeśli zdecydujesz się ponownie użyć uwierzytelniania tożsamości zarządzanej.

## <a name="limitations"></a>Ograniczenia
Poniżej przedstawiono ograniczenia tej funkcji:

- Klasyczne obszary robocze Power BI nie są obsługiwane.

- Konta platformy Azure bez Azure Active Directory.

- Dostęp z wieloma dzierżawcami nie jest obsługiwany. Nazwa główna usługi utworzona dla danego zadania Stream Analytics musi znajdować się w tej samej dzierżawie Azure Active Directory, w której zadanie zostało utworzone i nie można jej używać z zasobem znajdującym się w innej dzierżawie Azure Active Directory.

- [Tożsamość przypisana przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md) nie jest obsługiwana. Oznacza to, że nie można wprowadzić własnej nazwy głównej usługi, która będzie używana przez ich Stream Analytics zadania. Nazwa główna usługi musi być generowana przez Azure Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Power BI integrację pulpitu nawigacyjnego z Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Poznanie danych wyjściowych z Azure Stream Analytics](./stream-analytics-define-outputs.md)

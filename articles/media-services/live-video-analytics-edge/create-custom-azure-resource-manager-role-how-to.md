---
title: Tworzenie niestandardowej Azure Resource Manager usługi i przypisywanie jej do jednostki usługi — Azure
description: Ten artykuł zawiera wskazówki dotyczące tworzenia niestandardowej roli Azure Resource Manager i przypisywania jej do jednostki usługi Live Video Analytics na IoT Edge interfejsie wiersza polecenia platformy Azure.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 6c33f6703522fc0b28237e22c16c96587467df40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788515"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Tworzenie niestandardowej Azure Resource Manager usługi i przypisywanie jej do jednostki usługi

Live Video Analytics w IoT Edge modułu musi mieć aktywne konto Azure Media Services, aby działało prawidłowo. Relacja między modułem Live Video Analytics on IoT Edge a kontem usługi Azure Media Service jest ustanowiona za pośrednictwem zestawu właściwości bliźniaczej reprezentacji modułu. Jedną z tych właściwości [](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) bliźniaczej reprezentacji jest jednostka usługi, która umożliwia wystąpieniu modułu komunikowanie się z kontem usługi i wyzwalanie niezbędnych operacji Media Services danych. Aby zminimalizować potencjalne niewłaściwe użycie i/lub przypadkowe narażenie danych z urządzenia brzegowego, ta jednostka usługi powinna mieć najmniejsze uprawnienia.

W tym artykule przedstawiono kroki tworzenia niestandardowej roli Azure Resource Manager za pomocą Azure Cloud Shell, która następnie jest używana do tworzenia jednostki usługi.

## <a name="prerequisites"></a>Wymagania wstępne  

Wymagania wstępne dotyczące tego artykułu są następujące:

* Subskrypcja platformy Azure z subskrypcją właściciela.
* Aplikacja Azure Active Directory uprawnieniami do tworzenia aplikacji i przypisywania jednostki usługi do roli.

Najłatwiejszym sposobem sprawdzenia, czy Twoje konto ma odpowiednie uprawnienia, jest skorzystanie z portalu. Zobacz [Sprawdzanie wymaganego uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Omówienie  

Przejmiemy kroki tworzenia roli niestandardowej i łączenia jej z jednostką usługi w następującej kolejności:

1. Utwórz konto usługi Media Service, jeśli jeszcze go nie masz.
1. Tworzenie jednostki usługi.
1. Utwórz niestandardową rolę Azure Resource Manager z ograniczonymi uprawnieniami.
1. "Ogranicz" uprawnienia jednostki usługi przy użyciu utworzonej roli niestandardowej.
1. Uruchom prosty test, aby sprawdzić, czy można pomyślnie ograniczyć jednostkę usługi.
1. Przechwyć parametry, które będą używane w manifestach IoT Edge wdrożenia.

### <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services  

Jeśli nie masz konta usługi Media Service, skorzystaj z poniższych kroków, aby je utworzyć.

1. Przejdź do [strony Cloud Shell](https://shell.azure.com/).
1. Wybierz pozycję "Bash" jako środowisko z listy rozwijanej po lewej stronie okna powłoki

    ![Przechwytywanie ekranu pokazuje powłokę Bash wybraną w oknie powłoki.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Ustaw subskrypcję platformy Azure jako konto domyślne przy użyciu następującego szablonu polecenia:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Utwórz [grupę zasobów](/cli/azure/group#az_group_create) i [konto magazynu.](/cli/azure/storage/account#az_storage_account_create)
1. Teraz utwórz konto usługi Azure Media Service przy użyciu następującego szablonu polecenia w Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Tworzenie jednostki usługi  

Teraz utworzymy nową jednostkę usługi i połączymy ją z Kontem usługi Media Service.

Bez żadnych parametrów uwierzytelniania uwierzytelnianie oparte na hasłach jest używane z losowym hasłem dla jednostki usługi. W Cloud Shell użyj następującego szablonu polecenia:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

To polecenie generuje odpowiedź w ten sposób:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. Dane wyjściowe jednostki usługi z uwierzytelnianiem za pomocą hasła obejmują klucz hasła, który w tym przypadku jest parametrem "AadSecret". 

    Upewnij się, że skopiowano tę wartość — nie można jej pobrać. Jeśli zapomnisz hasła, [zresetuj poświadczenia jednostki usługi](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. Elementy appId i tenant key są wyświetlane w danych wyjściowych odpowiednio jako "AadClientId" i "AadTenantId". Są one używane w uwierzytelnianiu jednostki usługi. Zanotuj ich wartości, ale można je pobrać w dowolnym momencie za pomocą [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list).

### <a name="create-a-custom-role-definition"></a>Tworzenie niestandardowej definicji roli  

Aby utworzyć rolę niestandardową, wykonaj następujące kroki:

1. Utwórz plik JSON definicji roli w systemie lokalnym i zapisz następujący tekst w pliku. 
    1. Zastąp < yourSubscriptionId> identyfikatorem subskrypcji platformy Azure
    1. Jedyne akcje dozwolone dla tej roli to:
        * listContainerSas — ułatwia uzyskiwanie listy adresów URL kontenerów magazynu modułów z sygnaturami dostępu współdzielonego (SAS) w celu przekazywania i pobierania zawartości zasobów.
        * Pisanie zasobów — pomaga w tworzeniu lub aktualizowaniu dowolnego zasobu w module
        * listEdgePolicies — zawiera listę zasad, które są stosowane do urządzenia brzegowego  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Po utworzeniu uruchom następujący szablon polecenia, aby utworzyć nową definicję roli w subskrypcji:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Po pomyślnym wykonaniu polecenia zobaczysz następujące dane wyjściowe:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Tworzenie przypisania roli  

Aby dodać przypisanie roli, potrzebny jest identyfikator objectId jednostki usługi, do której chcesz przypisać właśnie utworzoną rolę niestandardową.

Użyj następującego polecenia w Cloud Shell, aby uzyskać identyfikator objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` Można go pobrać z danych wyjściowych kroku [Tworzenie jednostki](#create-service-principal) usługi.

Powyższe polecenie spowoduje wydrukowanie objectId jednostki usługi. 

```
“objectId” : “<yourObjectId>”,
```

Użyj [szablonu polecenia az role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby połączyć rolę niestandardową z jednostką usługi:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametry:

|Parametry|Opis| 
|---|---|
|--role |Nazwa lub identyfikator roli niestandardowej. W naszym przypadku: "Użytkownik LVAEdge".|
|--assignee-object-id|Identyfikator obiektu jednostki usługi, która będzie przez Ciebie używania.|

Wynik będzie wyglądać tak:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Potwierdzanie, że przypisanie roli miało miejsce

Aby potwierdzić, że jednostkę usługi powiązyliśmy teraz z utworzoną przez nas rolą niestandardową, uruchom następujące polecenie:

```
az role assignment list  --assignee < objectId>
```

Wynik powinien wyglądać tak:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Poszukaj wartości "roleDefinitionName" i sprawdź, czy jej wartość jest ustawiona na "LVAEdge User". 

To potwierdza, że powiązyliśmy niestandardową rolę użytkownika z jednostką usługi używaną dla naszej aplikacji.

### <a name="test-the-service-principal-access-control"></a>Testowanie kontroli dostępu jednostki usługi

1. Zaloguj się przy użyciu jednostki usługi. W tym celu będziemy potrzebować 3 informacji dla jednostki usługi Azure Active Directory, aby udzielić nam odpowiedniego tokenu dostępu, który można uzyskać z danych wyjściowych kroku [Tworzenie jednostki](#create-service-principal) usługi:
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Teraz spróbujmy się zalogować przy użyciu poniższego szablonu polecenia:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Teraz sprawdźmy, czy identyfikator logowania jest ograniczony do jednostki usługi z rolą "Użytkownik LVAEdge", próbując utworzyć grupę zasobów, aby upewnić się, że kończy się niepowodzeniem. Uruchom następujące polecenie w Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    To polecenie powinno się nie powieść i będzie wyglądać tak:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Następne kroki  

Zwróć uwagę na następujące wartości z tego artykułu. Te wartości będą wymagane do skonfigurowania właściwości bliźniaczej reprezentacji obiektu Live Video Analytics module IoT Edge module. Zobacz [Schemat JSON bliźniaczej reprezentacji modułu.](module-twin-configuration-schema.md)

| Zmienna z tego artykułu|Nazwa właściwości bliźniaczej reprezentacji Live Video Analytics na IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|

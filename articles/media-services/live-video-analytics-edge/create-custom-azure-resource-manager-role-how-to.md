---
title: Utwórz niestandardową rolę Azure Resource Manager i przypisz ją do jednostki usługi — Azure
description: Ten artykuł zawiera wskazówki dotyczące tworzenia niestandardowej roli Azure Resource Manager i przypisywania jej do jednostki usługi na potrzeby analizy filmów wideo na żywo na IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 80974c111dd451314635d06334766322bc68e437
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210448"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Utwórz niestandardową rolę Azure Resource Manager i przypisz ją do nazwy głównej usługi

Analiza wideo na żywo w wystąpieniu modułu IoT Edge wymaga aktywnego konta Azure Media Services do prawidłowego działania. Relacja między dynamicznym analizowaniem filmów wideo w module IoT Edge i kontem usługi Azure Media Service została ustanowiona za pośrednictwem zestawu właściwości sznurka modułu. Jedną z tych właściwości sznurów jest nazwa [główna usługi](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) , która umożliwia wystąpienie modułu komunikowanie się z i wyzwalanie niezbędnych operacji na koncie Media Services. Aby zminimalizować potencjalne niewłaściwe i/lub przypadkowe narażenie na dane z urządzenia brzegowego, ta jednostka usługi powinna mieć najmniejszą liczbę uprawnień.

W tym artykule przedstawiono procedurę tworzenia niestandardowej roli Azure Resource Manager z Azure Cloud Shell, która jest używana do tworzenia nazwy głównej usługi.

## <a name="prerequisites"></a>Wymagania wstępne  

Wymagania wstępne dotyczące tego artykułu są następujące:

* Subskrypcja platformy Azure z subskrypcją właściciela.
* Azure Active Directory z uprawnieniami do tworzenia aplikacji i przypisywania jednostki usługi do roli.

Najłatwiejszym sposobem sprawdzenia, czy Twoje konto ma odpowiednie uprawnienia, jest skorzystanie z portalu. Zobacz [Sprawdzanie wymaganego uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Omówienie  

Przejdziemy do instrukcji, aby utworzyć rolę niestandardową i połączyć ją z jednostką usługi w następującej kolejności:

1. Utwórz konto usługi multimediów, jeśli jeszcze go nie masz.
1. Tworzenie jednostki usługi.
1. Utwórz niestandardową rolę Azure Resource Manager z ograniczonymi uprawnieniami.
1. "Ogranicz" uprawnienia nazwy głównej usługi przy użyciu utworzonej roli niestandardowej.
1. Uruchom prosty test, aby sprawdzić, czy można pomyślnie ograniczyć nazwę główną usługi.
1. Przechwyć parametry, które będą używane w manifestach wdrażania IoT Edge.

### <a name="create-a-media-services-account"></a>Tworzenie konta usługi Media Services  

Jeśli nie masz konta usługi multimediów, wykonaj następujące kroki, aby go utworzyć.

1. Przejdź do [Cloud Shell](https://shell.azure.com/).
1. Wybierz pozycję "bash" jako środowisko na liście rozwijanej po lewej stronie okna powłoki

    ![Capturs ekranu wyświetla bash wybrane z okna powłoki.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Ustaw subskrypcję platformy Azure jako konto domyślne przy użyciu następującego szablonu polecenia:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Utwórz [grupę zasobów](/cli/azure/group#az-group-create) i [konto magazynu](/cli/azure/storage/account#az-storage-account-create).
1. Teraz Utwórz konto usługi Azure Media Service przy użyciu następującego szablonu polecenia w Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Tworzenie jednostki usługi  

Teraz utworzymy nową nazwę główną usługi i połączysz ją z kontem usługi Media.

Bez żadnych parametrów uwierzytelniania uwierzytelnianie oparte na hasłach jest używane z losowym hasłem dla nazwy głównej usługi. W Cloud Shell Użyj następującego szablonu polecenia:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

To polecenie generuje odpowiedź w następujący sposób:

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
1. Dane wyjściowe dla jednostki usługi z uwierzytelnianiem przy użyciu hasła zawierają klucz hasła, który w tym przypadku jest parametrem "AadSecret". 

    Upewnij się, że skopiujesz tę wartość — nie można jej pobrać. Jeśli zapomnisz hasła, [Zresetuj poświadczenia nazwy głównej usługi](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. Identyfikator appId i klucz dzierżawy są wyświetlane odpowiednio w danych wyjściowych jako "AadClientId" i "AadTenantId". Są one używane w uwierzytelnianiu nazwy głównej usługi. Zapisz ich wartości, ale można je pobrać w dowolnym momencie za pomocą [AZ AD Sp list](/cli/azure/ad/sp#az-ad-sp-list).

### <a name="create-a-custom-role-definition"></a>Utwórz niestandardową definicję roli  

Aby utworzyć rolę niestandardową, należy wykonać następujące czynności:

1. Utwórz plik JSON definicji roli w systemie lokalnym i Zapisz następujący tekst w pliku. 
    1. Zastąp < yourSubscriptionId> IDENTYFIKATORem subskrypcji platformy Azure
    1. Jedyne akcje dozwolone dla tej roli to:
        * listContainerSas — umożliwia wyświetlanie listy modułów adresów URL kontenera magazynu z sygnaturami dostępu współdzielonego (SAS) do przekazywania i pobierania zawartości zasobów.
        * Zasoby zapisu — ułatwiają tworzenie lub aktualizowanie dowolnego elementu zawartości modułu.
        * listEdgePolicies — wyświetla listę zasad, które są stosowane do urządzenia brzegowego  
        
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
          
1. Po utworzeniu należy uruchomić następujący szablon polecenia, aby utworzyć nową definicję roli w ramach subskrypcji:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Po pomyślnym wykonaniu polecenia zostaną wyświetlone następujące dane wyjściowe:
    
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

### <a name="create-role-assignment"></a>Utwórz przypisanie roli  

Aby dodać przypisanie roli, wymagany jest identyfikator objectId jednostki usługi, do której chcesz przypisać utworzoną rolę niestandardową.

Użyj następującego polecenia w Cloud Shell, aby uzyskać identyfikator objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` można pobrać z danych wyjściowych kroku [Create Service Principal](#create-service-principal) .

Powyższe polecenie spowoduje wydrukowanie identyfikatora obiektu nazwy głównej usługi. 

```
“objectId” : “<yourObjectId>”,
```

Użyj [polecenia AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create) , aby połączyć rolę niestandardową z jednostką usługi:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametry:

|Parametry|Opis| 
|---|---|
|--rola |Nazwa lub identyfikator roli niestandardowej. W naszym przypadku: "LVAEdge User".|
|--cesjonariusz-Object-ID|Identyfikator obiektu jednostki usługi, który będzie używany.|

Wynik będzie wyglądać następująco:

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

### <a name="confirm-that-role-assignment-happened"></a>Potwierdź, że przypisanie roli zaszło

Aby upewnić się, że jednostka usługi jest teraz połączona z utworzoną rolą niestandardową, uruchom następujące polecenie:

```
az role assignment list  --assignee < objectId>
```

Wynik powinien wyglądać następująco:

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
 
Poszukaj elementu "roleDefinitionName" i sprawdź, czy jego wartość jest równa "LVAEdge User". 

Jest to potwierdzenie, że niestandardowa rola użytkownika jest połączona z jednostką usługi używaną dla naszej aplikacji.

### <a name="test-the-service-principal-access-control"></a>Testowanie kontroli dostępu jednostki usługi

1. Zaloguj się przy użyciu nazwy głównej usługi. W takim przypadku będziemy potrzebować 3 fragmentów informacji dla Azure Active Directory, aby udzielić nam odpowiedniego tokenu dostępu, który możemy pobrać z danych wyjściowych etapu [tworzenia głównej usługi](#create-service-principal) :
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Teraz program umożliwia próbę zalogowania się przy użyciu poniższego szablonu polecenia:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Teraz zobaczmy, czy logowanie jest ograniczone do jednostki usługi z rolą "LVAEdge User", próbując utworzyć grupę zasobów, aby upewnić się, że jej nie można wykonać. Uruchom następujące polecenie w Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    To polecenie powinno zakończyć się niepowodzeniem i wyglądać następująco:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Następne kroki  

Zwróć uwagę na następujące wartości z tego artykułu. Te wartości będą wymagane do skonfigurowania właściwości bliźniaczych analizy wideo na żywo w module IoT Edge, zobacz [schemat kodu JSON dla modułu](module-twin-configuration-schema.md).

| Zmienna z tego artykułu|Nazwa właściwości przędzy dla analizy wideo na żywo na IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|

---
title: Informacje o definicjach ról platformy Azure — Azure RBAC
description: Dowiedz się więcej na temat definicji ról platformy Azure w kontroli dostępu opartej na rolach (Azure RBAC) na potrzeby precyzyjnego zarządzania dostępem do zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/18/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f6ae9ff27e773c36626812387b1284d660cbf39d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602470"
---
# <a name="understand-azure-role-definitions"></a>Informacje o definicjach ról platformy Azure

Jeśli próbujesz zrozumieć, jak działa rola platformy Azure, lub jeśli tworzysz własną [rolę niestandardową platformy Azure](custom-roles.md), warto zrozumieć, w jaki sposób role są definiowane. W tym artykule opisano szczegóły definicji ról i przedstawiono kilka przykładów.

## <a name="role-definition"></a>Definicja roli

*Definicja roli* to kolekcja uprawnień. Jest to czasami nazywane tylko *rolą*. Definicja roli określa dozwolone operacje, na przykład odczyt, zapis, czy usuwanie. Można również wyświetlić listę operacji, które są wykluczone z dozwolonych operacji lub operacji związanych z danymi źródłowymi.

Poniżej przedstawiono przykład właściwości w definicji roli, które są wyświetlane przy użyciu Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Poniżej przedstawiono przykład właściwości w definicji roli, które są wyświetlane przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

W poniższej tabeli opisano znaczenie właściwości roli.

| Właściwość | Opis |
| --- | --- |
| `Name`</br>`roleName` | Nazwa wyświetlana roli. |
| `Id`</br>`name` | Unikatowy identyfikator roli. |
| `IsCustom`</br>`roleType` | Wskazuje, czy jest to rola niestandardowa. Ustaw na `true` lub `CustomRole` dla ról niestandardowych. Ustaw dla `false` `BuiltInRole` ról wbudowanych lub. |
| `Description`</br>`description` | Opis roli. |
| `Actions`</br>`actions` | Tablica ciągów, która określa operacje zarządzania, które mogą być wykonywane przez rolę. |
| `NotActions`</br>`notActions` | Tablica ciągów, która określa operacje zarządzania, które są wykluczone z dozwolonej wartości `Actions` . |
| `DataActions`</br>`dataActions` | Tablica ciągów, która określa operacje na danych, które mogą być wykonywane na danych w tym obiekcie. |
| `NotDataActions`</br>`notDataActions` | Tablica ciągów, która określa operacje na danych, które są wykluczone z dozwolonej wartości `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | Tablica ciągów, która określa zakresy, które rola jest dostępna do przypisania. |

### <a name="operations-format"></a>Format operacji

Operacje są określone za pomocą ciągów, które mają następujący format:

- `{Company}.{ProviderName}/{resourceType}/{action}`

`{action}`Część ciągu operacji określa typ operacji, które można wykonać dla typu zasobu. Na przykład następujące podciągi będą widoczne w `{action}` :

| Podciąg akcji    | Opis         |
| ------------------- | ------------------- |
| `*` | Symbol wieloznaczny daje dostęp do wszystkich operacji, które pasują do ciągu. |
| `read` | Włącza operacje odczytu (GET). |
| `write` | Włącza operacje zapisu (PUT lub PATCH). |
| `action` | Włącza niestandardowe operacje, takie jak ponowne uruchamianie maszyn wirtualnych (POST). |
| `delete` | Włącza operacje usuwania (usuwania). |

### <a name="role-definition-example"></a>Przykład definicji roli

Oto definicja roli [współautor](built-in-roles.md#contributor) , która jest wyświetlana w Azure PowerShell i interfejs wiersza polecenia platformy Azure. Symbol wieloznaczny (`*`) w obszarze `Actions` oznacza, że podmiot zabezpieczeń przypisany do tej roli może wykonywać wszystkie akcje, czyli może zarządzać wszystkim. Dotyczy to również akcji, które zostaną zdefiniowane, gdy do platformy Azure zostaną dodane nowe typy zasobów. Operacje w obszarze `NotActions` są odejmowane od zestawu operacji w obszarze `Actions`. W przypadku roli [współautor](built-in-roles.md#contributor) program `NotActions` usuwa możliwość zarządzania dostępem do zasobów, a także do zarządzania przypisaniami Azure Blueprint.

Rola współautor wyświetlana w Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rola współautor wyświetlana w interfejsie wiersza polecenia platformy Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Zarządzanie i operacje na danych

Kontrola dostępu oparta na rolach dla operacji zarządzania jest określona we `Actions` `NotActions` właściwościach i definicji roli. Poniżej przedstawiono kilka przykładów operacji zarządzania na platformie Azure:

- Zarządzanie dostępem do konta magazynu
- Tworzenie, aktualizowanie lub usuwanie kontenera obiektów BLOB
- Usuwanie grupy zasobów i wszystkich jej zasobów

Dostęp do zarządzania nie jest dziedziczony do danych, pod warunkiem, że metoda uwierzytelniania kontenera jest ustawiona na "konto użytkownika usługi Azure AD", a nie "klucz dostępu". Ta separacja zapobiega nieograniczonemu dostępowi do danych w rolach z symbolami wieloznacznymi ( `*` ). Na przykład jeśli użytkownik ma rolę [czytelnika](built-in-roles.md#reader) w ramach subskrypcji, może wyświetlić konto magazynu, ale domyślnie nie może wyświetlić danych źródłowych.

Wcześniej kontrola dostępu oparta na rolach nie była używana do wykonywania operacji na danych. Autoryzacja dla operacji na danych, które są różne dla różnych dostawców zasobów. Ten sam model autoryzacji kontroli dostępu oparty na rolach używany na potrzeby operacji zarządzania został rozszerzony do operacji na danych.

Aby obsługiwać operacje na danych, nowe właściwości danych zostały dodane do definicji roli. Operacje na danych są definiowane za pomocą właściwości `DataActions` i `NotDataActions`. Po dodaniu tych właściwości danych jest zachowywane rozdzielenie między zarządzaniem i danymi. Zapobiega to nieoczekiwanemu uzyskiwaniu dostępu do danych za pośrednictwem bieżących przypisań ról z symbolami wieloznacznymi (`*`). Poniżej przedstawiono niektóre operacje na danych, określane za pomocą właściwości `DataActions` i `NotDataActions`:

- Odczyt listy obiektów blob w kontenerze
- Zapis obiektu blob magazynu w kontenerze
- Usuwanie komunikatu z kolejki

Oto definicja roli [czytnika danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-reader) , która obejmuje operacje we `Actions` `DataActions` właściwościach i. Ta rola umożliwia odczytywanie kontenera obiektów blob, a także bazowe dane obiektów BLOB.

Rola czytnika danych obiektów blob magazynu, która jest wyświetlana w Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rola czytnika danych obiektów blob magazynu, która jest wyświetlana w interfejsie wiersza polecenia platformy Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Do właściwości `DataActions` i `NotDataActions` można dodawać tylko operacje na danych. Dostawcy zasobów identyfikują, które operacje są operacjami danych, ustawiając `isDataAction` Właściwość na `true` . Aby wyświetlić listę operacji `isDataAction` , gdzie is `true` , zobacz [operacje dostawcy zasobów](resource-provider-operations.md). Role, które nie mają operacji na danych, nie muszą mieć `DataActions` ani `NotDataActions` właściwości w ramach definicji roli.

Autoryzacja wszystkich wywołań interfejsu API operacji zarządzania jest obsługiwana przez Azure Resource Manager. Autoryzacja wywołań interfejsu API operacji danych jest obsługiwana przez dostawcę zasobów lub Azure Resource Manager.

### <a name="data-operations-example"></a>Przykład operacji na danych

Aby lepiej zrozumieć, jak działają operacje zarządzania i danych, rozważmy konkretny przykład. Alicja przypisała rolę [właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Robert ma przypisaną rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) w zakresie konta magazynu. Na poniższym diagramie przedstawiono ten przykład.

![Kontrola dostępu oparta na rolach została rozszerzona w celu obsługi operacji zarządzania i danych](./media/role-definitions/rbac-management-data.png)

Rola [właściciela](built-in-roles.md#owner) dla Alicja i rola [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) dla Roberta mają następujące akcje:

Właściciel

&nbsp;&nbsp;&nbsp;&nbsp;Wykonane<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Współautor danych obiektu blob usługi Storage

&nbsp;&nbsp;&nbsp;&nbsp;Wykonane<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Akcje dataactions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ponieważ Alicja ma akcję symbol wieloznaczny ( `*` ) w zakresie subskrypcji, ich uprawnienia dziedziczą, aby umożliwić im wykonywanie wszystkich akcji zarządzania. Alicja może odczytywać, zapisywać i usuwać kontenery. Jednak Alicja nie może wykonywać operacji na danych bez podejmowania dodatkowych kroków. Na przykład domyślnie Alicja nie może odczytać obiektów BLOB w kontenerze. Aby odczytać obiekty blob, Alicja musiałaby pobrać klucze dostępu do magazynu i korzystać z nich w celu uzyskania dostępu do obiektów BLOB.

Uprawnienia Roberta są ograniczone tylko do `Actions` `DataActions` określonych w roli [współautor danych obiektu blob magazynu](built-in-roles.md#storage-blob-data-contributor) . W oparciu o rolę Robert może wykonywać operacje związane z zarządzaniem i danymi. Na przykład Robert może odczytywać, zapisywać i usuwać kontenery na określonym koncie magazynu, a także odczytywać, zapisywać i usuwać obiekty blob.

Aby uzyskać więcej informacji na temat zarządzania i zabezpieczeń płaszczyzny danych dla magazynu, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Jakie narzędzia są obsługiwane przy użyciu ról platformy Azure dla operacji na danych?

Aby wyświetlać operacje na danych i korzystać z nich, należy dysponować prawidłowymi wersjami narzędzi lub zestawów SDK:

| Narzędzie  | Wersja  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 lub nowszy |
| [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) | 2.0.30 lub nowszy |
| [Platforma Azure dla platformy .NET](/dotnet/azure/) | 2.8.0 — wersja zapoznawcza lub nowsza |
| [Zestaw Azure SDK dla języka Go](/azure/go/azure-sdk-go-install) | 15.0.0 lub nowszy |
| [Platforma Azure dla języka Java](/java/azure/) | 1.9.0 lub nowszy |
| [Platforma Azure dla języka Python](/azure/python/) | 0.40.0 lub nowszy |
| [Zestaw Azure SDK dla środowiska Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 lub nowszy |

Aby wyświetlić operacje na danych w interfejsie API REST i korzystać z nich, należy ustawić parametr **API-Version** w następującej wersji lub nowszej:

- 2018-07-01

## <a name="actions"></a>Akcje

`Actions`Uprawnienie określa operacje zarządzania, które mogą być wykonywane przez rolę. Jest to kolekcja ciągów operacji, która identyfikuje zabezpieczone operacje dostawców zasobów platformy Azure. Poniżej przedstawiono kilka przykładów operacji zarządzania, które mogą być używane w programie `Actions` .

> [!div class="mx-tableFixed"]
> | Ciąg operacji    | Opis         |
> | ------------------- | ------------------- |
> | `*/read` | Przyznaje dostęp do operacji odczytu dla wszystkich typów zasobów wszystkich dostawców zasobów platformy Azure.|
> | `Microsoft.Compute/*` | Przyznaje dostęp do wszystkich operacji dla wszystkich typów zasobów w dostawcy zasobów Microsoft. COMPUTE.|
> | `Microsoft.Network/*/read` | Przyznaje dostęp do operacji odczytu dla wszystkich typów zasobów w dostawcy zasobów Microsoft. Network.|
> | `Microsoft.Compute/virtualMachines/*` | Przyznaje dostęp do wszystkich operacji maszyn wirtualnych i jego podrzędnych typów zasobów.|
> | `microsoft.web/sites/restart/Action` | Przyznaje dostęp do ponownego uruchomienia aplikacji sieci Web.|

## <a name="notactions"></a>NotActions

`NotActions`Uprawnienie określa operacje zarządzania, które są odejmowane lub wykluczone z dozwolonego `Actions` , który ma symbol wieloznaczny ( `*` ). Użyj `NotActions` uprawnienia, jeśli zestaw operacji, które chcesz zezwolić, jest łatwiejszy do zdefiniowania przez odjęcie od `Actions` , który ma symbol wieloznaczny ( `*` ). Dostęp udzielony przez rolę (czynne uprawnienia) jest obliczany przez odjęcie `NotActions` operacji od `Actions` operacji.

`Actions - NotActions = Effective management permissions`

W poniższej tabeli przedstawiono dwa przykłady czynnych uprawnień dla operacji wieloznacznej [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement) :

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Efektywne uprawnienia zarządzania |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *brak* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, która wyklucza operację w programie `NotActions` , i ma przypisaną drugą rolę, która przyznaje dostęp do tej samej operacji, użytkownik może wykonać tę operację. `NotActions` nie jest regułą odmowy — jest to po prostu wygodny sposób tworzenia zestawu dozwolonych operacji, gdy wymagane jest wykluczenie określonych operacji.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>Różnice między oddziałami i Odmów przypisań

`NotActions` i Odmów przypisania nie są takie same i służą do różnych celów. `NotActions` jest wygodnym sposobem odejmowania określonych akcji z wieloznacznej `*` operacji ().

Odmowa przypisań uniemożliwia użytkownikom wykonywanie określonych akcji nawet wtedy, gdy przypisanie roli udzieli im dostępu. Aby uzyskać więcej informacji, zobacz [Omówienie przypisań Odmów na platformie Azure](deny-assignments.md).

## <a name="dataactions"></a>Akcje dataactions

`DataActions`Uprawnienie określa operacje na danych, które mogą być wykonywane na danych w tym obiekcie. Na przykład jeśli użytkownik odczytał dostęp do danych obiektów BLOB do konta magazynu, może odczytać obiekty blob w ramach tego konta magazynu. Poniżej przedstawiono kilka przykładów operacji na danych, które mogą być używane w programie `DataActions` .

> [!div class="mx-tableFixed"]
> | Ciąg operacji    | Opis         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Zwraca obiekt BLOB lub listę obiektów BLOB. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Zwraca wynik zapisania obiektu BLOB. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Zwraca komunikat. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Zwraca komunikat lub wynik zapisywania lub usuwania wiadomości. |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions`Uprawnienie określa operacje na danych, które są odejmowane lub wykluczone z dozwolonego `DataActions` , który ma symbol wieloznaczny ( `*` ). Użyj `NotDataActions` uprawnienia, jeśli zestaw operacji, które chcesz zezwolić, jest łatwiejszy do zdefiniowania przez odjęcie od `DataActions` , który ma symbol wieloznaczny ( `*` ). Dostęp udzielony przez rolę (czynne uprawnienia) jest obliczany przez odjęcie `NotDataActions` operacji od `DataActions` operacji. Każdy dostawca zasobów udostępnia swój odpowiedni zestaw interfejsów API do realizacji operacji na danych.

`DataActions - NotDataActions = Effective data permissions`

W poniższej tabeli przedstawiono dwa przykłady czynnych uprawnień dla operacji symboli wieloznacznych [Microsoft. Storage](resource-provider-operations.md#microsoftstorage) :

> [!div class="mx-tableFixed"]
> | Akcje dataactions | NotDataActions | Obowiązujące uprawnienia do danych |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *brak* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, która wyklucza operację danych w programie `NotDataActions` , i przypisuje drugą rolę, która przyznaje dostęp do tej samej operacji danych, użytkownik może wykonać tę operację. `NotDataActions` nie jest regułą odmowy — jest to po prostu wygodny sposób tworzenia zestawu dozwolonych operacji na danych, gdy wymagane jest wykluczenie określonych operacji na danych.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes`Właściwość określa zakresy (grupy zarządzania, subskrypcje lub grupy zasobów), które mają dostępną definicję roli. Rolę można przypisać do przypisania tylko do grup zarządzania, subskrypcji lub grup zasobów, które go wymagają. Należy użyć co najmniej jednej grupy zarządzania, subskrypcji lub grupy zasobów.

Wbudowane role mają `AssignableScopes` ustawiony zakres główny ( `"/"` ). Zakres główny wskazuje, że rola jest dostępna do przypisania we wszystkich zakresach. Przykłady prawidłowych zakresów do przypisania to:

> [!div class="mx-tableFixed"]
> | Rola jest dostępna do przypisania | Przykład |
> |----------|---------|
> | Jedna subskrypcja | `"/subscriptions/{subscriptionId1}"` |
> | Dwie subskrypcje | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Grupa zasobów sieciowych | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Jedna grupa zarządzania | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Grupa zarządzania i subskrypcja | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Wszystkie zakresy (dotyczy tylko ról wbudowanych) | `"/"` |

Informacje o `AssignableScopes` rolach niestandardowych można znaleźć w temacie [role niestandardowe platformy Azure](custom-roles.md).

## <a name="next-steps"></a>Następne kroki

* [Wbudowane role platformy Azure](built-in-roles.md)
* [Role niestandardowe platformy Azure](custom-roles.md)
* [Operacje dostawcy zasobów platformy Azure](resource-provider-operations.md)

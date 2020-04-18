---
title: Opis definicji ról w zasobach RBAC dla platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o definicjach ról w kontroli dostępu opartej na rolach (RBAC) do precyzyjnego zarządzania dostępem zasobów platformy Azure.
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
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 777ea7cc29679a3819e94d39913f167ea1cb3453
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641377"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Opis definicji ról dla zasobów platformy Azure

Jeśli próbujesz zrozumieć, jak działa rola lub jeśli tworzysz własną [rolę niestandardową dla zasobów platformy Azure,](custom-roles.md)warto zrozumieć, jak zdefiniowane są role. W tym artykule opisano szczegóły definicji ról i zawiera kilka przykładów.

## <a name="role-definition"></a>Definicja roli

*Definicja roli* to zbiór uprawnień. Czasami jest nazywana po prostu *rolą*. Definicja roli określa dozwolone operacje, na przykład odczyt, zapis, czy usuwanie. Może ona również zawierać listę operacji, których nie można wykonywać, lub operacji związanych z danymi źródłowymi. Definicja roli ma następujące właściwości:

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

| Właściwość | Opis |
| --- | --- |
| `Name` | Wyświetlana nazwa roli. |
| `Id` | Unikatowy identyfikator roli. |
| `IsCustom` | Wskazuje, czy jest to rola niestandardowa. Ustaw `true` dla ról niestandardowych. |
| `Description` | Opis roli. |
| `Actions` | Tablica ciągów, która określa operacje zarządzania, które rola pozwala na wykonanie. |
| `NotActions` | Tablica ciągów określających operacje zarządzania, które są `Actions`wykluczone z dozwolonej . |
| `DataActions` | Tablica ciągów, która określa operacje danych, które rola pozwala na wykonanie danych w tym obiekcie. |
| `NotDataActions` | Tablica ciągów określających operacje danych, które są `DataActions`wykluczone z dozwolonej . |
| `AssignableScopes` | Tablica ciągów, która określa zakresy, które rola jest dostępna dla przypisania. |

### <a name="operations-format"></a>Format operacji

Operacje są określane za pomocą ciągów, które mają następujący format:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Część `{action}` ciągu operacji określa typ operacji, które można wykonać na typie zasobu. Na przykład `{action}`w:

| Podciąg akcji    | Opis         |
| ------------------- | ------------------- |
| `*` | Symbol wieloznaczny udziela dostępu do wszystkich operacji, które pasują do ciągu. |
| `read` | Włącza operacje odczytu (GET). |
| `write` | Włącza operacje zapisu (PUT lub PATCH). |
| `action` | Włącza operacje niestandardowe, takie jak ponowne uruchomienie maszyn wirtualnych (POST). |
| `delete` | Włącza operacje usuwania (DELETE). |

### <a name="role-definition-example"></a>Przykład definicji roli

Oto definicja roli [współautora](built-in-roles.md#contributor) w formacie JSON. Symbol wieloznaczny (`*`) w obszarze `Actions` oznacza, że podmiot zabezpieczeń przypisany do tej roli może wykonywać wszystkie akcje, czyli może zarządzać wszystkim. Dotyczy to również akcji, które zostaną zdefiniowane, gdy do platformy Azure zostaną dodane nowe typy zasobów. Operacje w obszarze `NotActions` są odejmowane od zestawu operacji w obszarze `Actions`. W przypadku roli [Współautor](built-in-roles.md#contributor) zawartość właściwości `NotActions` uniemożliwia tej roli zarządzanie dostępem do zasobów oraz przypisywanie dostępu do zasobów.

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
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Zarządzanie i operacje na danych

Kontrola dostępu oparta na rolach `Actions` dla `NotActions` operacji zarządzania jest określona w i właściwości definicji roli. Oto kilka przykładów operacji zarządzania na platformie Azure:

- Zarządzanie dostępem do konta magazynu
- Tworzenie, aktualizowanie lub usuwanie kontenera obiektów blob
- Usuwanie grupy zasobów i wszystkich jej zasobów

Dostęp do zarządzania nie jest dziedziczony do danych, pod warunkiem, że metoda uwierzytelniania kontenera jest ustawiona na "Konto użytkownika usługi Azure AD", a nie "Klucz dostępu". To oddzielenie uniemożliwia role z`*`symbolami wieloznaczymi ( ) z nieograniczonym dostępem do danych. Na przykład jeśli użytkownik ma rolę [czytelnika](built-in-roles.md#reader) w subskrypcji, a następnie można wyświetlić konto magazynu, ale domyślnie nie można wyświetlić danych źródłowych.

Wcześniej kontrola dostępu oparta na rolach nie była używana do operacji na danych. Autoryzacja dla operacji danych różniła się w zależności od dostawców zasobów. Ten sam oparty na rolach model autoryzacji kontroli dostępu, używany w operacjach zarządzania, został rozszerzony na operacje na danych.

Do obsługi operacji danych dodano nowe właściwości danych do definicji roli. Operacje na danych są definiowane za pomocą właściwości `DataActions` i `NotDataActions`. Dodając te właściwości danych, zachowywana jest separacja między zarządzaniem a danymi. Zapobiega to nieoczekiwanemu uzyskiwaniu dostępu do danych za pośrednictwem bieżących przypisań ról z symbolami wieloznacznymi (`*`). Poniżej przedstawiono niektóre operacje na danych, określane za pomocą właściwości `DataActions` i `NotDataActions`:

- Odczyt listy obiektów blob w kontenerze
- Zapis obiektu blob magazynu w kontenerze
- Usuwanie komunikatu z kolejki

Oto definicja roli [czytnika danych obiektów blob magazynu,](built-in-roles.md#storage-blob-data-reader) która obejmuje operacje zarówno we właściwościach, jak `Actions` i `DataActions` właściwościach. Ta rola umożliwia odczytanie kontenera obiektów blob, a także podstawowych danych obiektów blob.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
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

Do właściwości `DataActions` i `NotDataActions` można dodawać tylko operacje na danych. Dostawcy zasobów identyfikują operacje, które są `isDataAction` operacjami danych, ustawiając właściwość na `true`. Aby wyświetlić listę operacji `isDataAction` `true`tam, gdzie jest , zobacz [Operacje dostawcy zasobów](resource-provider-operations.md). Role, które nie mają operacji danych `DataActions` nie `NotDataActions` są wymagane do i właściwości w definicji roli.

Autoryzacja dla wszystkich wywołań interfejsu API operacji zarządzania jest obsługiwana przez usługę Azure Resource Manager. Autoryzacja wywołania interfejsu API operacji danych jest obsługiwana przez dostawcę zasobów lub usługę Azure Resource Manager.

### <a name="data-operations-example"></a>Przykład operacji na danych

Aby lepiej zrozumieć, jak działają operacje zarządzania i danych, rozważmy konkretny przykład. Alicja została przypisana do roli [Właściciel](built-in-roles.md#owner) w zakresie subskrypcji. Robertowi przypisano rolę [współautora danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) w zakresie konta magazynu. Na poniższym diagramie przedstawiono ten przykład.

![Kontrola dostępu oparta na rolach została rozszerzona na obsługę zarówno zarządzania, jak i operacji na danych](./media/role-definitions/rbac-management-data.png)

Rola [Właściciela](built-in-roles.md#owner) dla Alicji i roli [współautora danych obiektu blob magazynu](built-in-roles.md#storage-blob-data-contributor) dla Roberta mają następujące akcje:

Właściciel

&nbsp;&nbsp;&nbsp;&nbsp;Działania<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Współautor danych obiektów blob magazynu

&nbsp;&nbsp;&nbsp;&nbsp;Działania<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataAkcje<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ponieważ Alicja ma`*`akcję symboli wieloznacznych ( ) w zakresie subskrypcji, ich uprawnienia dziedziczą w dół, aby umożliwić im wykonywanie wszystkich akcji zarządzania. Alicja może odczytywać, zapisywać i usuwać kontenery. Jednak Alicja nie może wykonywać operacji danych bez podejmowania dodatkowych kroków. Na przykład domyślnie Alicja nie może odczytać obiektów blob wewnątrz kontenera. Aby odczytać obiekty BLOB, Alicja musiałaby pobrać klucze dostępu do magazynu i użyć ich do uzyskania dostępu do obiektów blob.

Uprawnienia Roberta są ograniczone tylko `Actions` do `DataActions` i określone w [roli współautora danych obiektu blob magazynu.](built-in-roles.md#storage-blob-data-contributor) Na podstawie roli, Bob może wykonywać zarówno zarządzanie i operacje danych. Na przykład Robert może odczytywać, pisać i usuwać kontenery na określonym koncie magazynu, a także odczytywać, zapisywać i usuwać obiekty blob.

Aby uzyskać więcej informacji na temat zarządzania i zabezpieczeń płaszczyzny danych do magazynowania, zobacz [przewodnik po zabezpieczeniach usługi Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jakie narzędzia obsługują korzystanie z RBAC do operacji na danych?

Aby przeglądać operacje danych i pracować z nią, musisz mieć poprawne wersje narzędzi lub zestawów SDK:

| Narzędzie  | Wersja  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 lub nowsze |
| [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) | 2.0.30 lub nowsze |
| [Platforma Azure dla platformy .NET](/dotnet/azure/) | 2.8.0-podgląd lub nowszy |
| [Zestaw Azure SDK dla języka Go](/azure/go/azure-sdk-go-install) | 15.0.0 lub nowsze |
| [Platforma Azure dla języka Java](/java/azure/) | 1.9.0 lub nowsze |
| [Platforma Azure dla języka Python](/azure/python/) | 0.40.0 lub nowsze |
| [Zestaw Azure SDK dla środowiska Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 lub nowsze |

Aby wyświetlić i używać operacji danych w interfejsie API REST, należy ustawić parametr **wersji interfejsu API** na następującą wersję lub nowszą:

- 2018-07-01

## <a name="actions"></a>Akcje

Uprawnienie `Actions` określa operacje zarządzania, które rola umożliwia wykonanie. Jest to zbiór ciągów operacji, które identyfikują zabezpieczalne operacje dostawców zasobów platformy Azure. Oto kilka przykładów operacji zarządzania, `Actions`które mogą być używane w programie .

> [!div class="mx-tableFixed"]
> | Ciąg operacji    | Opis         |
> | ------------------- | ------------------- |
> | `*/read` | Udziela dostępu do operacji odczytu dla wszystkich typów zasobów wszystkich dostawców zasobów platformy Azure.|
> | `Microsoft.Compute/*` | Udziela dostępu do wszystkich operacji dla wszystkich typów zasobów w dostawcy zasobów Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Udziela dostępu do operacji odczytu dla wszystkich typów zasobów w dostawcy zasobów microsoft.network.|
> | `Microsoft.Compute/virtualMachines/*` | Udziela dostępu do wszystkich operacji maszyn wirtualnych i ich podrzędnych typów zasobów.|
> | `microsoft.web/sites/restart/Action` | Udziela dostępu do ponownego uruchomienia aplikacji sieci web.|

## <a name="notactions"></a>NotActions

Uprawnienie `NotActions` określa operacje zarządzania, które są `Actions`wykluczone z dozwolonej . Użyj `NotActions` uprawnienia, jeśli zestaw operacji, które chcesz zezwolić, jest łatwiej zdefiniowany przez wykluczenie operacji z ograniczeniami. Dostęp przyznany przez rolę (uprawnienia skuteczne) jest obliczany przez `NotActions` odjęcie operacji od `Actions` operacji.

> [!NOTE]
> Jeśli użytkownikowi jest przypisana rola, która `NotActions`wyklucza operację w programie i jest przypisana do drugiej roli, która udziela dostępu do tej samej operacji, użytkownik może wykonać tę operację. `NotActions`nie jest regułą zaprzeczania — jest to po prostu wygodny sposób tworzenia zestawu dozwolonych operacji, gdy należy wykluczyć określone operacje.
>

## <a name="dataactions"></a>DataAkcje

Uprawnienie `DataActions` określa operacje danych, które rola umożliwia wykonanie danych w tym obiekcie. Na przykład jeśli użytkownik odczytuje dostęp danych obiektów blob do konta magazynu, następnie mogą odczytać obiekty blob w ramach tego konta magazynu. Oto kilka przykładów operacji danych, które `DataActions`mogą być używane w programie .

> [!div class="mx-tableFixed"]
> | Ciąg operacji    | Opis         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Zwraca obiekt blob lub listę obiektów blob. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Zwraca wynik zapisu obiektu blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Zwraca wiadomość. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Zwraca wiadomość lub wynik zapisu lub usunięcia wiadomości. |

## <a name="notdataactions"></a>NotDataACtions (NotDataActions)

Uprawnienie `NotDataActions` określa operacje danych, które są `DataActions`wykluczone z dozwolonej . Dostęp przyznany przez rolę (uprawnienia skuteczne) jest obliczany przez `NotDataActions` odjęcie operacji od `DataActions` operacji. Każdy dostawca zasobów udostępnia odpowiedni zestaw interfejsów API do realizacji operacji danych.

> [!NOTE]
> Jeśli użytkownikowi przypisano rolę, która wyklucza operację `NotDataActions`danych w programie i jest przypisana do drugiej roli, która udziela dostępu do tej samej operacji danych, użytkownik może wykonać tę operację danych. `NotDataActions`nie jest regułą zaprzeczania — jest to po prostu wygodny sposób tworzenia zestawu dozwolonych operacji danych, gdy należy wykluczyć określone operacje danych.
>

## <a name="assignablescopes"></a>AssignableS

Właściwość `AssignableScopes` określa zakresy (grupy zarządzania, subskrypcje lub grupy zasobów), które mają tę definicję roli dostępne. Rolę można udostępnić do przypisania tylko w grupach zarządzania, subskrypcjach lub grupach zasobów, które jej wymagają. Należy użyć co najmniej jednej grupy zarządzania, subskrypcji lub grupy zasobów.

Wbudowane role `AssignableScopes` zostały ustawione na`"/"`zakres główny ( ). Zakres główny wskazuje, że rola jest dostępna dla przypisania we wszystkich zakresach. Przykłady prawidłowych zakresów podlegania cesji:

> [!div class="mx-tableFixed"]
> | Rola jest dostępna do przypisania | Przykład |
> |----------|---------|
> | Jedna subskrypcja | `"/subscriptions/{subscriptionId1}"` |
> | Dwie subskrypcje | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Grupa zasobów sieciowych | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Jedna grupa zarządzania | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Grupa zarządzania i subskrypcja | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Wszystkie zakresy (dotyczy tylko wbudowanych ról) | `"/"` |

Aby uzyskać `AssignableScopes` informacje dotyczące ról niestandardowych, zobacz [Role niestandardowe dla zasobów platformy Azure](custom-roles.md).

## <a name="next-steps"></a>Następne kroki

* [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
* [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
* [Operacje dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md)

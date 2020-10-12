---
title: IdentitySelector — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. ManagedIdentity. IdentitySelector dla Azure Portal. Służy do przypisywania tożsamości zarządzanych do zasobu.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063345"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft. ManagedIdentity. IdentitySelector — element interfejsu użytkownika

Kontrolka służąca do przypisywania [tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md) dla zasobu we wdrożeniu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Kontrolka składa się z następujących elementów:

![Microsoft. ManagedIdentity. IdentitySelector — pierwszy krok](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Gdy użytkownik wybierze pozycję **Dodaj**, zostanie otwarty poniższy formularz. Użytkownik może wybrać co najmniej jedną tożsamość przypisaną do użytkownika dla zasobu.

![Microsoft. ManagedIdentity. IdentitySelector — drugi krok](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

Wybrane tożsamości są wyświetlane w tabeli. Użytkownik może dodawać lub usuwać elementy z tej tabeli.

![Microsoft. ManagedIdentity. IdentitySelector — trzeci krok](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Uwagi

- Użyj **defaultValue.systemAssignedIdentity** , aby ustawić początkową wartość kontrolki opcji tożsamości przypisanej do systemu. Wartość domyślna to **off**. Dozwolone są następujące wartości:
  - **On** — skojarzona z systemem tożsamość jest przypisana do zasobu.
  - **Wyłączone** — tożsamość przypisana do systemu nie jest przypisana do zasobu.
  - **Tylko** on— skojarzona z systemem tożsamość jest przypisywana do zasobu. Użytkownicy nie mogą edytować tej wartości podczas wdrażania.
  - **OffOnly** — tożsamość przypisana do systemu nie jest przypisana do zasobu. Użytkownicy nie mogą edytować tej wartości podczas wdrażania.

- Jeśli **opcja Options. hideSystemAssignedIdentity** ma **wartość true**, nie jest wyświetlany interfejs użytkownika służący do konfigurowania tożsamości przypisanej do systemu. Wartość domyślna tej opcji to **false**.
- Jeśli **opcja Options. hideUserAssignedIdentity** ma **wartość true**, interfejs użytkownika służący do konfigurowania tożsamości przypisanej przez użytkownika nie jest wyświetlany. Zasób nie ma przypisanej tożsamości przypisanej do użytkownika. Wartość domyślna tej opcji to **false**.

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
- Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
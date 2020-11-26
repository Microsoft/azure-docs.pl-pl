---
title: ServicePrincipalSelector — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. ServicePrincipalSelector dla Azure Portal. Udostępnia kontrolkę, aby wybrać aplikację i pole tekstowe do wprowadzania hasła lub odcisku palca certyfikatu.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: tomfitz
ms.openlocfilehash: 2fdbbad467d8c762db485fc7935e9cef78313fd0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184454"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft. Common. ServicePrincipalSelector — element interfejsu użytkownika

Kontrolka, która umożliwia użytkownikom wybranie istniejącej jednostki [usługi](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) lub zarejestrowanie nowej aplikacji. Po wybraniu opcji **Utwórz nową** wykonaj kroki w celu zarejestrowania nowej aplikacji. Po wybraniu istniejącej aplikacji kontrolka zawiera pole tekstowe służące do wprowadzania hasła lub odcisku palca certyfikatu.

## <a name="ui-samples"></a>Przykłady interfejsu użytkownika

Możesz użyć domyślnej aplikacji, utworzyć nową aplikację lub użyć istniejącej aplikacji.

### <a name="use-default-application-or-create-new"></a>Użyj domyślnej aplikacji lub Utwórz nową

Domyślny widok jest określany na podstawie wartości we `defaultValue` właściwości, a **Typ jednostki usługi** jest ustawiony na **Utwórz nowy**. Jeśli `principalId` Właściwość zawiera prawidłowy unikatowy identyfikator globalny (GUID), formant wyszukuje dla aplikacji `objectId` . Wartość domyślna jest stosowana, jeśli użytkownik nie wybierze zaznaczenia z formantu.

Jeśli chcesz zarejestrować nową aplikację, wybierz pozycję **Zmień wybór** i zostanie wyświetlone okno dialogowe **zarejestruj aplikację** . Wprowadź **nazwę**, **Typ obsługiwanego konta** i wybierz przycisk **zarejestruj** .

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-default.png" alt-text="Widok początkowy Microsoft. Common. ServicePrincipalSelector.":::

Po zarejestrowaniu nowej aplikacji należy użyć **typu uwierzytelniania** w celu wprowadzenia hasła lub odcisku palca certyfikatu.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-authenticate.png" alt-text="Uwierzytelnianie Microsoft. Common. ServicePrincipalSelector.":::

### <a name="use-existing-application"></a>Użyj istniejącej aplikacji

Aby użyć istniejącej aplikacji, wybierz **pozycję Wybierz istniejące** , a następnie wybierz pozycję **Utwórz zaznaczenie**. Użyj okna dialogowego **Wybierz aplikację** , aby wyszukać nazwę aplikacji. Z wyników wybierz aplikację, a następnie przycisk **Wybierz** . Po wybraniu aplikacji formant Wyświetla **Typ uwierzytelniania** , aby wprowadzić hasło lub odcisk palca certyfikatu.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-existing.png" alt-text="Microsoft. Common. ServicePrincipalSelector wybierz istniejącą aplikację.":::

## <a name="schema"></a>Schemat

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Uwagi

- Wymagane właściwości są następujące:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Określa wartość domyślną `principalId` i `name` .

- Właściwości opcjonalne są następujące:
  - `toolTip`: Dołącza etykietkę narzędzia `infoBalloon` do każdej etykiety.
  - `visible`: Ukryj lub Wyświetl formant.
  - `options`: Określa, czy opcja odcisku palca certyfikatu powinna być dostępna.
  - `constraints`: Ograniczenia wyrażenia regularnego do sprawdzania poprawności hasła.

## <a name="example"></a>Przykład

Poniżej znajduje się przykład `Microsoft.Common.ServicePrincipalSelector` formantu. `defaultValue`Właściwość ustawia `principalId` `<default guid>` jako symbol zastępczy domyślny identyfikator GUID identyfikatora aplikacji.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Przykładowe dane wyjściowe

`appId`To identyfikator rejestracji aplikacji, która została wybrana lub utworzona. `objectId`Jest tablicą identyfikatorów obiektów dla jednostek usługi skonfigurowanych dla wybranej rejestracji aplikacji.

Gdy nie wybrano żadnego z formantów, `newOrExisting` wartość właściwości jest **Nowa**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

Po wybraniu **nowej** lub istniejącej aplikacji z kontrolki `newOrExisting` wartość właściwości jest **istniejąca**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
- Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
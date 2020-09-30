---
title: ServicePrincipalSelector — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. ServicePrincipalSelector dla Azure Portal. Zawiera listę rozwijaną umożliwiającą wybranie identyfikatora aplikacji i pola tekstowego do wprowadzania hasła lub odcisku palca certyfikatu.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576000"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Microsoft. Common. ServicePrincipalSelector — element interfejsu użytkownika

Kontrolka, która umożliwia użytkownikom wybranie istniejącej jednostki usługi lub zarejestrowanie nowej. Po wybraniu pozycji **Utwórz nową**wykonaj kroki, aby zarejestrować nową aplikację. Po wybraniu istniejącej aplikacji kontrolka zawiera pole tekstowe służące do wprowadzania hasła lub odcisku palca certyfikatu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Domyślny widok jest określany przez wartości `defaultValue` właściwości. Jeśli `principalId` Właściwość zawiera prawidłowy unikatowy identyfikator globalny (GUID), formant wyszukuje identyfikator obiektu aplikacji. Wartość domyślna jest stosowana, jeśli użytkownik nie wybierze zaznaczenia z listy rozwijanej.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Widok początkowy Microsoft. Common. ServicePrincipalSelector":::

Po wybraniu opcji **Utwórz nowy** lub istniejący identyfikator aplikacji z listy rozwijanej zostanie wyświetlony **Typ uwierzytelniania** , który umożliwia wprowadzanie hasła lub odcisku palca certyfikatu w polu tekstowym.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Widok początkowy Microsoft. Common. ServicePrincipalSelector":::

## <a name="schema"></a>Schemat

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
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

- Wymagane właściwości to:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Określa wartość domyślną `principalId` i `name` .

- Opcjonalne właściwości to:
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
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
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

`appId`To identyfikator rejestracji aplikacji, która została wybrana lub utworzona. `objectId`Jest tablicą obiektów objectid dla jednostek usługi skonfigurowanych dla wybranej rejestracji aplikacji.

Gdy nie wybrano żadnych opcji z listy rozwijanej, `newOrExisting` wartość właściwości jest **Nowa**:

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

Podczas **tworzenia nowego** lub istniejącego identyfikatora aplikacji z listy rozwijanej `newOrExisting` wartość właściwości jest **istniejąca**:

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

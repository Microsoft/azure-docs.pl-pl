---
title: Artefakt CreateUiDefinition
description: Pokazuje, jak utworzyć artefakt createUiDefinition dla aplikacji zarządzanej platformy Azure. Plik ma nazwę createUiDefinition.jsna.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651490"
---
# <a name="reference-user-interface-elements-artifact"></a>Odwołanie: artefakt elementów interfejsu użytkownika

Ten artykuł jest odwołaniem do *createUiDefinition.jsna* artefaktie w Azure Managed Applications. Aby uzyskać więcej informacji na temat tworzenia elementów interfejsu użytkownika, zobacz [Tworzenie elementów interfejsu użytkownika](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Elementy interfejsu użytkownika

Poniższy kod JSON przedstawia przykład *createUiDefinition.jsw* pliku dla Azure Managed Applications:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie aplikacji zarządzanej z niestandardowymi akcjami i zasobami](tutorial-create-managed-app-with-custom-provider.md)
- [Odwołanie: artefakt szablonu wdrożenia](reference-main-template-artifact.md)
- [Reference: artefakt definicji widoku](reference-view-definition-artifact.md)
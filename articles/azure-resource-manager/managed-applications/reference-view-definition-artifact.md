---
title: Pokaż odwołanie artefaktu definicji
description: Zawiera przykład artefaktu definicji widoku dla Azure Managed Applications. Nazwa pliku jest viewDefinition.js.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651204"
---
# <a name="reference-view-definition-artifact"></a>Reference: artefakt definicji widoku

Ten artykuł jest odwołaniem do *viewDefinition.jsna* artefaktie w Azure Managed Applications. Aby uzyskać więcej informacji na temat konfiguracji widoków tworzenia, zobacz [artefakt definicji widoku](concepts-view-definition.md).

## <a name="view-definition"></a>Definicja widoku

Poniższy kod JSON przedstawia przykład *viewDefinition.jsw* pliku dla Azure Managed Applications:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie aplikacji zarządzanej z niestandardowymi akcjami i zasobami](tutorial-create-managed-app-with-custom-provider.md)
- [Odwołanie: artefakt elementów interfejsu użytkownika](reference-createuidefinition-artifact.md)
- [Odwołanie: artefakt szablonu wdrożenia](reference-main-template-artifact.md)
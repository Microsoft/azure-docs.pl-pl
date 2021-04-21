---
title: 'Samouczek: tworzenie roli niestandardowej platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure — RBAC platformy Azure'
description: W tym samouczku rozpoczynamy tworzenie roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure i kontroli dostępu na podstawie ról (RBAC) platformy Azure.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3743697d58d0f5b167b123df59bc5638aa60489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771681"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>Samouczek: tworzenie roli niestandardowej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli wbudowane [role platformy Azure](built-in-roles.md) nie spełniają określonych potrzeb organizacji, możesz utworzyć własne role niestandardowe. W tym samouczku utworzysz rolę niestandardową o nazwie Reader Support Tickets przy użyciu interfejsu wiersza polecenia platformy Azure. Rola niestandardowa umożliwi użytkownikowi przeglądanie całej zawartości płaszczyzny zarządzania subskrypcją, a także otwieranie biletów pomocy technicznej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie roli niestandardowej
> * Wyświetlanie ról niestandardowych
> * Aktualizacja roli niestandardowej
> * Usuwanie roli niestandardowej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Zaloguj się do interfejsu [wiersza polecenia platformy Azure.](/cli/azure/authenticate-azure-cli)

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Najprostszym sposobem na utworzenie roli niestandardowej jest rozpoczęcie od szablonu JSON, dodanie własnych zmian, a następnie utworzenie nowej roli.

1. Przejrzyj listę operacji dla [dostawcy zasobów Microsoft.Support](resource-provider-operations.md#microsoftsupport). Dobrze jest znać operacje, które są dostępne do tworzenia uprawnień.

    | Operacja | Opis |
    | --- | --- |
    | Microsoft.Support/register/action | Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej |
    | Microsoft.Support/supportTickets/read | Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i komunikacji) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji. |
    | Microsoft.Support/supportTickets/write | Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć bilet pomocy technicznej dotyczący problemów technicznych, rozliczeń, limitów przydziału lub problemów związanych z zarządzaniem subskrypcją. Można także zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej. |
    
1. Utwórz nowy plik o nazwie **ReaderSupportRole.json**.

1. Otwórz plik ReaderSupportRole.json w edytorze i dodaj następujący kod JSON.

    Aby uzyskać informacje o różnych właściwościach, zobacz [Role niestandardowe platformy Azure.](custom-roles.md)

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Dodaj następujące operacje do właściwości `Actions`. Te akcje umożliwią użytkownikowi przeglądanie całej zawartości subskrypcji i tworzenie biletów pomocy technicznej.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. Pobierz identyfikator subskrypcji za pomocą polecenia [az account list](/cli/azure/account#az_account_list).

    ```azurecli
    az account list --output table
    ```

1. We właściwości `AssignableScopes` zastąp element `{subscriptionId1}` identyfikatorem subskrypcji.

    Musisz jawnie dodać identyfikatory subskrypcji, ponieważ w przeciwnym razie nie będzie można zaimportować roli do subskrypcji.

1. Zmień właściwości `Name` i `Description` na „Reader Support Tickets” i „View everything in the subscription and also open support tickets”.

    Zawartość pliku JSON powinna wyglądać następująco:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Aby utworzyć nową rolę niestandardową, użyj polecenia [az role definition create](/cli/azure/role/definition#az_role_definition_create) i określ plik definicji roli JSON.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    Nowa rola niestandardowa jest teraz dostępna i można ją przypisywać do użytkowników, grup lub jednostek usług tak samo jak role wbudowane.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

- Aby wyświetlić listę wszystkich ról niestandardowych, użyj polecenia [az role definition list](/cli/azure/role/definition#az_role_definition_list) z parametrem `--custom-role-only`.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    Rolę niestandardową można również sprawdzić w witrynie Azure Portal.

    ![zrzut ekranu przedstawiający rolę niestandardową zaimportowaną do witryny Azure Portal](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, zaktualizuj plik JSON, a następnie zaktualizuj rolę niestandardową.

1. Otwórz plik ReaderSupportRole.json.

1. W elemencie `Actions` dodaj operację do tworzenia wdrożeń grupy zasobów i zarządzania nimi `"Microsoft.Resources/deployments/*"`. Pamiętaj o dodaniu przecinka po poprzedniej operacji.

    Zaktualizowany plik JSON powinien wyglądać następująco:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Aby zaktualizować rolę niestandardową, użyj polecenia [az role definition update](/cli/azure/role/definition#az_role_definition_update) i określ zaktualizowany plik JSON.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

- Użyj polecenia [az role definition delete](/cli/azure/role/definition#az_role_definition_delete) i określ nazwę lub identyfikator roli w celu usunięcia roli niestandardowej.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](custom-roles-cli.md)

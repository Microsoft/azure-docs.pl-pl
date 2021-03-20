---
title: Przenoszenie aplikacji do innego regionu
description: Dowiedz się, jak przenosić App Service zasoby z jednego regionu do innego.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86524858"
---
# <a name="move-an-app-service-resource-to-another-region"></a>Przenoszenie zasobu App Service do innego regionu

W tym artykule opisano sposób przenoszenia zasobów App Service do innego regionu platformy Azure. Możesz przenieść zasoby do innego regionu z kilku powodów. Na przykład w celu skorzystania z nowego regionu platformy Azure, wdrożenia funkcji lub usług dostępnych tylko w określonych regionach, spełnienia wymagań wewnętrznych zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności.

Zasoby App Service są specyficzne dla regionu i nie można ich przenosić między regionami. Należy utworzyć kopię istniejących zasobów App Service w regionie docelowym, a następnie przenieść zawartość do nowej aplikacji. Jeśli Twoja aplikacja źródłowa używa domeny niestandardowej, możesz [ją zmigrować do nowej aplikacji w regionie docelowym](manage-custom-dns-migrate-domain.md) po zakończeniu.

Aby ułatwić kopiowanie aplikacji, można [sklonować poszczególne App Service aplikacji](app-service-web-app-cloning.md) do planu App Service w innym regionie, ale ma on [ograniczenia](app-service-web-app-cloning.md#current-restrictions), w szczególności nie obsługuje aplikacji systemu Linux.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że aplikacja App Service znajduje się w regionie świadczenia usługi Azure, z którego chcesz przenieść.
- Upewnij się, że region docelowy obsługuje App Service i wszystkie powiązane usługi, których zasobów chcesz przenieść.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Przygotowywanie

Zidentyfikuj wszystkie zasoby App Service, które są obecnie używane. Na przykład:

- Aplikacje usługi App Service
- [Plany usługi App Service](overview-hosting-plans.md)
- [Miejsca wdrożenia](deploy-staging-slots.md)
- [Domeny niestandardowe zakupione na platformie Azure](manage-custom-dns-buy-domain.md)
- [Certyfikaty SSL](configure-ssl-certificate.md)
- [Integracja z usługą Azure Virtual Network](web-sites-integrate-with-vnet.md)
- [Połączenia hybrydowe](app-service-hybrid-connections.md).
- [Tożsamości zarządzane](overview-managed-identity.md)
- [Ustawienia kopii zapasowej](manage-backup.md)

Niektóre zasoby, takie jak zaimportowane certyfikaty lub połączenia hybrydowe, zawierają integrację z innymi usługami platformy Azure. Aby uzyskać informacje na temat przenoszenia tych zasobów między regionami, zapoznaj się z dokumentacją odpowiednich usług.

## <a name="move"></a>Move

1. [Utwórz kopię zapasową aplikacji źródłowej](manage-backup.md).
1. [Utwórz aplikację w nowym planie App Service w regionie docelowym](app-service-plan-manage.md#create-an-app-service-plan).
2. [Przywracanie kopii zapasowej w aplikacji docelowej](web-sites-restore.md)
2. Jeśli używasz domeny niestandardowej, [Powiąż ją zapobiegawczo z aplikacją docelową](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) `awverify.` i [Włącz domenę w aplikacji docelowej](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Skonfiguruj wszystkie inne elementy w aplikacji docelowej tak samo, jak w przypadku aplikacji źródłowej, a następnie sprawdź konfigurację.
4. Gdy domena niestandardowa ma wskazywać aplikację docelową, należy ponownie [zamapować nazwę domeny](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Usuń aplikację źródłową i plan App Service. [Plan App Service w warstwie nieodpłatnej przenosi opłatę, nawet jeśli w niej nie działa żadna aplikacja.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Następne kroki

[Azure App Service klonowania aplikacji przy użyciu programu PowerShell](app-service-web-app-cloning.md)
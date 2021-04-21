---
title: Wdrażanie aplikacji za pomocą szablonów
description: Znajdź wskazówki dotyczące tworzenia szablonów Azure Resource Manager do aprowizowania i wdrażania App Service aplikacji.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ed6edeadfb1c6f73cc10771d4a5328e7bddb3642
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835168"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Wskazówki dotyczące wdrażania aplikacji internetowych przy użyciu Azure Resource Manager szablonów

Ten artykuł zawiera zalecenia dotyczące tworzenia szablonów Azure Resource Manager wdrażania Azure App Service rozwiązań. Te zalecenia mogą pomóc uniknąć typowych problemów.

## <a name="define-dependencies"></a>Definiowanie zależności

Definiowanie zależności dla aplikacji internetowych wymaga zrozumienia sposobu interakcji zasobów w aplikacji internetowej. Jeśli określisz zależności w nieprawidłowej kolejności, możesz spowodować błędy wdrażania lub utworzyć warunek wyścigu, który wsadzi wdrożenie.

> [!WARNING]
> Jeśli szablon zawiera rozszerzenie witryny MSDeploy, należy ustawić wszystkie zasoby konfiguracji jako zależne od zasobu MSDeploy. Zmiany konfiguracji powodują asynchroniczne ponowne uruchomienie lokacji. Dzięki temu, że zasoby konfiguracji zależą od programu MSDeploy, program MSDeploy zostanie uruchomiony przed ponownym uruchomieniem lokacji. Bez tych zależności lokacja może zostać ponownie uruchomiona podczas procesu wdrażania programu MSDeploy. Przykładowy szablon można znaleźć na stronie WordPress Template with Web Deploy Dependency (Szablon [WordPress z Web Deploy zależnością).](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)

Na poniższej ilustracji przedstawiono kolejność zależności dla różnych App Service zasobów:

![Zależności aplikacji internetowej](media/web-sites-rm-template-guidance/web-dependencies.png)

Zasoby są wdrażane w następującej kolejności:

**Warstwa 1**
* App Service planu.
* Wszelkie inne powiązane zasoby, takie jak bazy danych lub konta magazynu.

**Warstwa 2**
* Aplikacja internetowa — zależy od App Service planu.
* Usługa Azure Application Insights, które jest docelowe dla farmy serwerów — zależy App Service planu.

**Warstwa 3**
* Kontrola źródła — zależy od aplikacji internetowej.
* Rozszerzenie witryny MSDeploy — zależy od aplikacji internetowej.
* Usługa Azure Application Insights, które jest docelowe dla aplikacji internetowej — zależy od aplikacji internetowej.

**Warstwa 4**
* App Service certyfikatu — zależy od kontroli źródła lub programu MSDeploy, jeśli którykolwiek z nich jest obecny. W przeciwnym razie zależy to od aplikacji internetowej.
* Ustawienia konfiguracji (parametry połączenia, web.config, ustawienia aplikacji) — zależy od kontroli źródła lub programu MSDeploy, jeśli istnieje. W przeciwnym razie zależy to od aplikacji internetowej.

**Warstwa 5**
* Powiązania nazw hostów — zależy od certyfikatu, jeśli jest obecny. W przeciwnym razie zależy to od zasobu wyższego poziomu.
* Rozszerzenia lokacji — zależy od ustawień konfiguracji, jeśli są obecne. W przeciwnym razie zależy to od zasobu wyższego poziomu.

Zazwyczaj rozwiązanie obejmuje tylko niektóre z tych zasobów i warstw. W przypadku brakujących warstw zamapuj niższe zasoby na następną wyższą warstwę.

W poniższym przykładzie przedstawiono część szablonu. Wartość konfiguracji parametrów połączenia zależy od rozszerzenia MSDeploy. Rozszerzenie MSDeploy zależy od aplikacji internetowej i bazy danych. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Aby uzyskać gotowy do uruchomienia przykład, który korzysta z powyższego kodu, zobacz [Template: Build a simple Umbraco Web App (Szablon:](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)tworzenie prostej aplikacji internetowej Umbraco).

## <a name="find-information-about-msdeploy-errors"></a>Znajdowanie informacji o błędach programu MSDeploy

Jeśli szablon Resource Manager korzysta z programu MSDeploy, komunikaty o błędach wdrożenia mogą być trudne do zrozumienia. Aby uzyskać więcej informacji po nieudanym wdrożeniu, spróbuj wykonać następujące czynności:

1. Przejdź do konsoli [Kudu lokacji.](https://github.com/projectkudu/kudu/wiki/Kudu-console)
2. Przejdź do folderu D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Poszukaj plików appManagerStatus.xml i appManagerLog.xml plików. Pierwszy plik rejestruje stan. Drugi plik rejestruje informacje o błędzie. Jeśli błąd nie jest dla Ciebie oczywisty, możesz go dołączyć, gdy prosisz o pomoc na [forum](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Wybieranie unikatowej nazwy aplikacji internetowej

Nazwa aplikacji internetowej musi być globalnie unikatowa. Możesz użyć konwencji nazewnictwa, która prawdopodobnie będzie unikatowa, lub użyć funkcji [uniqueString,](../azure-resource-manager/templates/template-functions-string.md#uniquestring) aby pomóc w wygenerowaniu unikatowej nazwy.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Wdrażanie certyfikatu aplikacji internetowej z Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli szablon zawiera zasób [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) dla powiązania protokołu TLS/SSL, a certyfikat jest przechowywany w witrynie Key Vault, upewnij się, że tożsamość App Service może uzyskać dostęp do certyfikatu.

Na globalnej platformie Azure App Service service principal ma identyfikator **abfa0a7c-a6b6-4736-8310-5855508787cd.** Aby udzielić dostępu do Key Vault dla App Service jednostki usługi, użyj:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

W Azure Government jednostki usługi App Service ma identyfikator **6a02c803-zdumiony-4136-b4c3-5a6f318b4714.** Użyj tego identyfikatora w poprzednim przykładzie.

W swoim Key Vault wybierz **pozycję Certyfikaty i** **wygeneruj/zaimportuj,** aby przekazać certyfikat.

![Importowanie certyfikatu](media/web-sites-rm-template-guidance/import-certificate.png)

W szablonie podaj nazwę certyfikatu dla `keyVaultSecretName` .

Aby uzyskać przykładowy szablon, zobacz Deploy a Web App certificate from Key Vault secret and use it for creating SSL binding (Wdrażanie certyfikatu aplikacji internetowej z Key Vault tajnym i używanie go [do tworzenia powiązania SSL.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać samouczek dotyczący wdrażania aplikacji internetowych za pomocą szablonu, zobacz Przewidywalny aprowizowanie i wdrażanie [mikrousług na platformie Azure.](deploy-complex-application-predictably.md)
* Aby dowiedzieć się więcej o składni JSON i właściwościach typów zasobów w szablonach, [zobacz Azure Resource Manager informacje o szablonie](/azure/templates/).

---
title: Wdrażanie aplikacji przy użyciu szablonów
description: Poznaj wskazówki dotyczące tworzenia Azure Resource Manager szablonów w celu aprowizacji i wdrażania aplikacji App Service.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961622"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Wskazówki dotyczące wdrażania aplikacji sieci Web za pomocą szablonów Azure Resource Manager

W tym artykule przedstawiono zalecenia dotyczące tworzenia Azure Resource Manager szablonów do wdrażania rozwiązań Azure App Service. Te zalecenia mogą pomóc uniknąć typowych problemów.

## <a name="define-dependencies"></a>Definiowanie zależności

Definiowanie zależności dla aplikacji sieci Web wymaga poznania sposobu działania zasobów w aplikacji internetowej. W przypadku określenia zależności w niepoprawnej kolejności mogą wystąpić błędy wdrażania lub utworzyć sytuację wyścigu, która blokuje wdrożenie.

> [!WARNING]
> Jeśli w szablonie dołączysz rozszerzenie witryny MSDeploy, należy ustawić wszystkie zasoby konfiguracji zależne od zasobu MSDeploy. Zmiany w konfiguracji powodują, że lokacja jest uruchamiana asynchronicznie. Przez udostępnienie zasobów konfiguracji zależnych od MSDeploy upewnij się, że program MSDeploy zakończy działanie przed ponownym uruchomieniem lokacji. Bez tych zależności lokacja może zostać ponownie uruchomiona podczas procesu wdrażania programu MSDeploy. Aby zapoznać się z przykładowym szablonem, zobacz [szablon WordPress z zależnością Web Deploy](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Na poniższej ilustracji przedstawiono kolejność zależności dla różnych App Service zasobów:

![Zależności aplikacji sieci Web](media/web-sites-rm-template-guidance/web-dependencies.png)

Zasoby są wdrażane w następującej kolejności:

**Warstwa 1**
* App Service plan.
* Wszystkie inne powiązane zasoby, takie jak bazy danych lub konta magazynu.

**Warstwa 2**
* Aplikacja sieci Web — zależy od planu App Service.
* Wystąpienie usługi Azure Application Insights, które jest przeznaczone dla farmy serwerów — zależy od planu App Service.

**Warstwa 3**
* Kontrola źródła — zależy od aplikacji sieci Web.
* Rozszerzenie witryny MSDeploy — zależy od aplikacji sieci Web.
* Wystąpienie usługi Azure Application Insights, które jest przeznaczone dla aplikacji sieci Web — zależy od aplikacji sieci Web.

**Warstwa 4**
* Certyfikat App Service — zależy od kontroli źródła lub MSDeploy, jeśli jest obecny. W przeciwnym razie zależy od aplikacji sieci Web.
* Ustawienia konfiguracji (parametry połączenia, wartości web.config, ustawienia aplikacji) — są zależne od kontroli źródła lub MSDeploy, jeśli są obecne. W przeciwnym razie zależy od aplikacji sieci Web.

**Warstwa 5**
* Powiązania nazw hostów — są zależne od certyfikatu, jeśli jest obecny. W przeciwnym razie zależy od zasobu wyższego poziomu.
* Rozszerzenia lokacji — zależy od ustawień konfiguracji, jeśli są obecne. W przeciwnym razie zależy od zasobu wyższego poziomu.

Zazwyczaj rozwiązanie obejmuje tylko niektóre z tych zasobów i warstw. W przypadku brakujących warstw Mapuj mniejsze zasoby na następną wyższą warstwę.

Poniższy przykład przedstawia część szablonu. Wartość konfiguracji parametrów połączenia zależy od rozszerzenia MSDeploy. Rozszerzenie MSDeploy zależy od aplikacji sieci Web i bazy danych. 

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

Aby uzyskać gotowy do uruchomienia przykład, który używa powyższego kodu, zobacz [szablon: tworzenie prostej aplikacji sieci Web Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Znajdowanie informacji o błędach MSDeploy

Jeśli szablon Menedżer zasobów używa MSDeploy, komunikaty o błędach wdrożenia mogą być trudne do zrozumienia. Aby uzyskać więcej informacji po nieudanym wdrożeniu, spróbuj wykonać następujące czynności:

1. Przejdź do [konsoli kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console)lokacji.
2. Przejdź do folderu pod adresem D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Wyszukaj pliki appManagerStatus.xml i appManagerLog.xml. Pierwszy plik rejestruje stan. Drugi plik rejestruje informacje o błędzie. Jeśli błąd nie jest oczywisty, możesz dołączyć go, gdy zostanie wyświetlony monit o pomoc na [forum](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Wybierz unikatową nazwę aplikacji sieci Web

Nazwa aplikacji sieci Web musi być globalnie unikatowa. Można użyć konwencji nazewnictwa, która może być unikatowa lub można użyć [funkcji uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) , aby ułatwić generowanie unikatowej nazwy.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Wdróż certyfikat aplikacji sieci Web na podstawie Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli szablon zawiera zasób [Microsoft. Web/Certificates](/azure/templates/microsoft.web/certificates) dla powiązania TLS/SSL, a certyfikat jest przechowywany w Key Vault, należy się upewnić, że App Service tożsamość może uzyskać dostęp do certyfikatu.

Na platformie Azure globalna jednostka usługi App Service ma identyfikator **abfa0a7c-a6b6-4736-8310-5855508787cd**. Aby udzielić dostępu do Key Vault dla jednostki usługi App Service, użyj:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

W Azure Government jednostka usługi App Service ma identyfikator **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Użyj tego identyfikatora w poprzednim przykładzie.

W Key Vault wybierz pozycję **Certyfikaty** i **Wygeneruj/Importuj** , aby przekazać certyfikat.

![Importowanie certyfikatu](media/web-sites-rm-template-guidance/import-certificate.png)

W szablonie Podaj nazwę certyfikatu `keyVaultSecretName` .

Aby zapoznać się z przykładowym szablonem, zobacz [Wdrażanie certyfikatu aplikacji sieci Web z poziomu wpisu tajnego Key Vault i używanie go do tworzenia powiązania SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z samouczkiem dotyczącym wdrażania aplikacji sieci Web za pomocą szablonu, zobacz [udostępnianie i wdrażanie mikrousług na platformie Azure](deploy-complex-application-predictably.md).
* Aby dowiedzieć się więcej o składni i właściwościach JSON dla typów zasobów w szablonach, zobacz [Azure Resource Manager dokumentacja szablonu](/azure/templates/).
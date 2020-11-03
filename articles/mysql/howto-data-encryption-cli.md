---
title: Szyfrowanie danych — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: Dowiedz się, jak skonfigurować szyfrowanie danych Azure Database for MySQL i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07d2e9fa98c24695a119c651539d4003ecd8524a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242096"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Szyfrowanie danych dla Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak skonfigurować szyfrowanie danych dla Azure Database for MySQL i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
* Utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta. Włącz również ochronę przed czyszczeniem i usuwanie nietrwałe w magazynie kluczy.

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true -enable-purge-protection true
  ```

* W utworzonym Azure Key Vault Utwórz klucz, który będzie używany do szyfrowania danych Azure Database for MySQL.

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* Aby można było użyć istniejącego magazynu kluczy, musi on mieć następujące właściwości, aby użyć go jako klucza zarządzanego przez klienta:

  * [Usuwanie nietrwałe](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Przeczyść chronione](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty do użycia jako klucz zarządzany przez klienta:
  * Brak daty wygaśnięcia
  * Niewyłączone
  * Wykonywanie operacji **Get** , **zawijania** i **odpakowania**

## <a name="set-the-right-permissions-for-key-operations"></a>Ustaw odpowiednie uprawnienia dla operacji Key

1. Istnieją dwa sposoby uzyskania tożsamości zarządzanej dla Azure Database for MySQL.

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Utwórz nowy serwer Azure Database for MySQL z tożsamością zarządzaną.

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Zaktualizuj istniejący serwer Azure Database for MySQL, aby uzyskać zarządzaną tożsamość.

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. Ustaw **uprawnienia klucza** ( **pobieranie** , **Zawijanie** , **odpakowywanie) dla** **podmiotu zabezpieczeń** , który jest nazwą serwera MySQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ustaw szyfrowanie danych dla Azure Database for MySQL

1. Włącz szyfrowanie danych dla Azure Database for MySQL przy użyciu klucza utworzonego w Azure Key Vault.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Adres URL klucza:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Korzystanie z szyfrowania danych dla serwerów przywracania lub repliki

Gdy Azure Database for MySQL jest szyfrowany przy użyciu klucza zarządzanego przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera zostanie również zaszyfrowana. Tę nową kopię można wykonać za pomocą operacji w trybie lokalnym lub z możliwością przywracania geograficznego albo za pomocą operacji repliki (lokalnej/obejmującej wiele regionów). W przypadku szyfrowanego serwera MySQL można wykonać następujące czynności, aby utworzyć zaszyfrowany przywrócony serwer.

### <a name="creating-a-restoredreplica-server"></a>Tworzenie serwera przywróconego/repliki

* [Tworzenie serwera przywracania](howto-restore-server-cli.md) 
* [Tworzenie serwera repliki odczytu](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Po przywróceniu serwera ponownie Zweryfikuj szyfrowanie danych na przywróconym serwerze

*   Przypisywanie tożsamości dla serwera repliki
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Pobierz istniejący klucz, który ma być używany dla serwera przywróconego/repliki

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Ustawianie zasad dla nowej tożsamości serwera przywrócenia/repliki
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Ponowne Weryfikowanie przywróconego serwera/repliki przy użyciu klucza szyfrowania

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Dodatkowa możliwość dla klucza używanego dla Azure Database for MySQL

### <a name="get-the-key-used"></a>Pobierz klucz używany

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

Adres URL klucza: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Wyświetlenie używanego klucza

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Usuń używany klucz

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Używanie szablonu Azure Resource Manager do włączania szyfrowania danych

Oprócz Azure Portal można również włączyć szyfrowanie danych na serwerze Azure Database for MySQL przy użyciu szablonów Azure Resource Manager dla nowych i istniejących serwerów.

### <a name="for-a-new-server"></a>Dla nowego serwera

Użyj jednego z wstępnie utworzonych szablonów Azure Resource Manager, aby zainicjować obsługę serwera z włączonym szyfrowaniem danych: [przykład z szyfrowaniem danych](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Ten Azure Resource Manager szablonu służy do tworzenia serwera Azure Database for MySQL i korzystania z **magazynu** **kluczy oraz klucza** , który został przesłany jako parametry, aby umożliwić szyfrowanie danych na serwerze.

### <a name="for-an-existing-server"></a>Dla istniejącego serwera

Ponadto można użyć szablonów Azure Resource Manager, aby włączyć szyfrowanie danych na istniejących serwerach Azure Database for MySQL.

* Przekaż identyfikator zasobu klucza Azure Key Vault, który został wcześniej skopiowany we `Uri` właściwości obiektu właściwości.

* Użyj *2020-01-01-Preview* jako wersji interfejsu API.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for MySQL szyfrowanie danych za pomocą klucza zarządzanego przez klienta](concepts-data-encryption-mysql.md).

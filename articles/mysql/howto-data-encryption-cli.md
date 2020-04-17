---
title: Szyfrowanie danych — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database for MySQL
description: Dowiedz się, jak skonfigurować szyfrowanie danych dla usługi Azure Database dla aplikacji MySQL i zarządzać nimi przy użyciu interfejsu wiersza polecenia platformy Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 37f6f0dc9c1221207273110252bff445d2e1245b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459104"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Szyfrowanie danych dla usługi Azure Database dla mysql przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak skonfigurować szyfrowanie danych dla usługi Azure Database dla aplikacji MySQL za pomocą interfejsu wiersza polecenia platformy Azure i zarządzać nimi.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem w tej subskrypcji.
* Utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta. Włącz również ochronę przed przeczyszczaniem i usuwanie nietrwałe w magazynie kluczy.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* W utworzonej usługi Azure Key Vault utwórz klucz, który będzie używany do szyfrowania danych usługi Azure Database for MySQL.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Aby użyć istniejącego magazynu kluczy, musi mieć następujące właściwości, aby użyć go jako klucza zarządzanego przez klienta:
  * [Usuwanie nietrwałe](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Czyszczenie chronione](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty, aby użyć go jako klucza zarządzanego przez klienta:
  * Brak daty ważności
  * Nie wyłączono
  * Wykonywanie **operacji get,** **wrap,** **unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Ustawianie odpowiednich uprawnień dla operacji kluczy

1. Istnieją dwa sposoby uzyskiwania tożsamości zarządzanej dla usługi Azure Database dla MySQL.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Utwórz nową usługę Azure Database dla serwera MySQL z tożsamością zarządzaną.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Zaktualizuj istniejącą usługę Azure Database for MySQL server, aby uzyskać tożsamość zarządzaną.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. Ustaw **uprawnienia klucza** **(Pobierz**, **Zawijaj,** **Rozpakuj)** dla **głównego zobowiązanego**, który jest nazwą serwera MySQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ustawianie szyfrowania danych dla usługi Azure Database dla mysql

1. Włącz szyfrowanie danych dla bazy danych platformy Azure dla MySQL przy użyciu klucza utworzonego w usłudze Azure Key Vault.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Kluczowy adres URL:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Używanie szyfrowania danych do przywracania lub repliki serwerów

Po zaszyfrowywanie usługi Azure Database for MySQL przy pomocą klucza zarządzanego klienta przechowywanego w usłudze Key Vault każda nowo utworzona kopia serwera jest również szyfrowana. Tę nową kopię można wykonać za pomocą operacji lokalnego lub przywracania geograficznego lub za pośrednictwem operacji repliki (lokalnej/międzyregionowej). Tak więc dla zaszyfrowanego serwera MySQL, można użyć następujących kroków, aby utworzyć zaszyfrowany serwer przywrócony.

### <a name="creating-a-restoredreplica-server"></a>Tworzenie przywróconego/repliki serwera

  *  [Tworzenie serwera przywracania](howto-restore-server-cli.md) 
  *  [Tworzenie serwera replik odczytu](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Po przywróceniu serwera ponownie zdeterminuj szyfrowanie danych przywróconego serwera

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Dodatkowa możliwość klucza używanego w usłudze Azure Database for MySQL

### <a name="get-the-key-used"></a>Zastosuj klucz

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Wyświetl listę użytego klucza

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Upuść używany klucz

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Włączanie szyfrowania danych za pomocą szablonu usługi Azure Resource Manager

Oprócz witryny Azure portal można również włączyć szyfrowanie danych na serwerze usługi Azure Database dla mysql przy użyciu szablonów usługi Azure Resource Manager dla nowych i istniejących serwerów.

### <a name="for-a-new-server"></a>Dla nowego serwera

Użyj jednego ze wstępnie utworzonych szablonów usługi Azure Resource Manager, aby aprowizować serwer z włączonym szyfrowaniem danych: [Przykład z szyfrowaniem danych](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Ten szablon usługi Azure Resource Manager tworzy usługę Azure Database dla serwera MySQL i używa **keyvault** i **key** przekazywane jako parametry, aby włączyć szyfrowanie danych na serwerze.

### <a name="for-an-existing-server"></a>Dla istniejącego serwera
Ponadto można użyć szablonów usługi Azure Resource Manager, aby włączyć szyfrowanie danych na istniejących serwerach usługi Azure Database dla serwerów MySQL.

* Przekaż identyfikator zasobu klucza usługi Azure Key Vault, `Uri` który został skopiowany wcześniej pod właściwością w obiekcie właściwości.

* Użyj *wersji interfejsu API 2020-01-01-preview.*

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

 Aby dowiedzieć się więcej o szyfrowaniu danych, zobacz [Usługa Azure Database for MySQL data encryption with customer-managed key](concepts-data-encryption-mysql.md).

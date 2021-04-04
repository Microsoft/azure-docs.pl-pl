---
title: Szyfrowanie danych — interfejs wiersza polecenia platformy Azure — dla Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować szyfrowanie danych dla Azure Database for PostgreSQL jednego serwera i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240617"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Szyfrowanie danych dla Azure Database for PostgreSQL jednego serwera przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak skonfigurować szyfrowanie danych i zarządzać nimi Azure Database for PostgreSQL jednym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
* Utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta. Włącz również ochronę przed czyszczeniem i usuwanie nietrwałe w magazynie kluczy.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* W utworzonym Azure Key Vault Utwórz klucz, który będzie używany do szyfrowania danych na Azure Database for PostgreSQL pojedynczym serwerze.

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
  * Wykonywanie operacji **Get**, **zawijania** i **odpakowania**

## <a name="set-the-right-permissions-for-key-operations"></a>Ustaw odpowiednie uprawnienia dla operacji Key

1. Istnieją dwa sposoby uzyskania tożsamości zarządzanej dla Azure Database for PostgreSQL jednego serwera.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Utwórz nowy serwer Azure Database for PostgreSQL z tożsamością zarządzaną.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Zaktualizuj istniejący serwer Azure Database for PostgreSQL, aby uzyskać zarządzaną tożsamość.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Ustaw **uprawnienia klucza** (**pobieranie**, **Zawijanie**, **odpakowywanie) dla** **podmiotu zabezpieczeń**, który jest nazwą serwera PostgreSQL jednego serwera.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ustawianie szyfrowania danych dla Azure Database for PostgreSQL pojedynczego serwera

1. Włącz szyfrowanie danych dla Azure Database for PostgreSQL pojedynczego serwera przy użyciu klucza utworzonego w Azure Key Vault.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    Adres URL klucza:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Korzystanie z szyfrowania danych dla serwerów przywracania lub repliki

Po zaszyfrowaniu pojedynczego serwera Azure Database for PostgreSQL z kluczem zarządzanym przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera zostanie również zaszyfrowana. Tę nową kopię można wykonać za pomocą operacji w trybie lokalnym lub z możliwością przywracania geograficznego albo za pomocą operacji repliki (lokalnej/obejmującej wiele regionów). W przypadku zaszyfrowanego serwera PostgreSQL Single Server można wykonać poniższe kroki, aby utworzyć zaszyfrowany przywrócony serwer.

### <a name="creating-a-restoredreplica-server"></a>Tworzenie serwera przywróconego/repliki

* [Tworzenie serwera przywracania](howto-restore-server-cli.md)
* [Tworzenie serwera repliki odczytu](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Po przywróceniu serwera ponownie Zweryfikuj szyfrowanie danych na przywróconym serwerze

*   Przypisywanie tożsamości dla serwera repliki
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Pobierz istniejący klucz, który ma być używany dla serwera przywróconego/repliki

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Ustawianie zasad dla nowej tożsamości serwera przywrócenia/repliki

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Ponowne Weryfikowanie przywróconego serwera/repliki przy użyciu klucza szyfrowania

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Dodatkowa możliwość dla klucza używanego dla Azure Database for PostgreSQL pojedynczego serwera

### <a name="get-the-key-used"></a>Pobierz klucz używany

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

Adres URL klucza: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Wyświetlenie używanego klucza

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Usuń używany klucz

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Używanie szablonu Azure Resource Manager do włączania szyfrowania danych

Oprócz Azure Portal można również włączyć szyfrowanie danych na Azure Database for PostgreSQL pojedynczym serwerze przy użyciu szablonów Azure Resource Manager dla nowego i istniejącego serwera.

### <a name="for-a-new-server"></a>Dla nowego serwera

Użyj jednego z wstępnie utworzonych szablonów Azure Resource Manager, aby zainicjować obsługę serwera z włączonym szyfrowaniem danych: [przykład z szyfrowaniem danych](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Ten Azure Resource Manager szablon tworzy Azure Database for PostgreSQL pojedynczy serwer i używa **magazynu** **kluczy i klucza** , który został przesłany jako parametry, aby umożliwić szyfrowanie danych na serwerze.

### <a name="for-an-existing-server"></a>Dla istniejącego serwera
Ponadto można użyć szablonów Azure Resource Manager, aby włączyć szyfrowanie danych na istniejących Azure Database for PostgreSQL pojedynczych serwerach.

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
      "type": "Microsoft.DBforPostgreSQL/servers",
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
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for PostgreSQL szyfrowanie danych na jednym serwerze z kluczem zarządzanym przez klienta](concepts-data-encryption-postgresql.md).

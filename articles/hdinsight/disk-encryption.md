---
title: Szyfrowanie dysków kluczy zarządzanych przez klienta dla usługi Azure HDInsight
description: W tym artykule opisano sposób używania własnego klucza szyfrowania z usługi Azure Key Vault do szyfrowania danych przechowywanych na dyskach zarządzanych w klastrach usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 732709dbcb5ebe54025a963379128f1a1e74183e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536305"
---
# <a name="customer-managed-key-disk-encryption"></a>Szyfrowanie dysków za pomocą klucza zarządzanego przez klienta

Usługa Azure HDInsight obsługuje szyfrowanie kluczy zarządzane przez klienta dla danych na dyskach zarządzanych i dyskach zasobów dołączonych do maszyn wirtualnych klastra HDInsight. Ta funkcja umożliwia korzystanie z usługi Azure Key Vault do zarządzania kluczami szyfrowania, które zabezpieczają dane w stanie spoczynku w klastrach HDInsight.

Wszystkie dyski zarządzane w usłudze HDInsight są chronione za pomocą szyfrowania usługi Azure Storage Service (SSE). Domyślnie dane na tych dyskach są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli włączysz klucze zarządzane przez klienta dla usługi HDInsight, podasz klucze szyfrowania dla usługi HDInsight do używania tych kluczy i zarządzania nimi przy użyciu usługi Azure Key Vault.

Ten dokument nie odnosi się do danych przechowywanych na koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage, zobacz [Szyfrowanie usługi Azure Storage dla danych w spoczynku](../storage/common/storage-service-encryption.md). Klastry mogą mieć jedno lub więcej dołączonych kont usługi Azure Storage, na których klucze szyfrowania mogą być również zarządzane przez firmę Microsoft lub zarządzane przez klienta, ale usługa szyfrowania jest inna.

## <a name="introduction"></a>Wprowadzenie

Szyfrowanie kluczy zarządzane przez klienta jest jednoetapowym procesem obsługiwanym podczas tworzenia klastra bez dodatkowych kosztów. Wszystko, co musisz zrobić, to zarejestrować usługę HDInsight jako tożsamość zarządzaną w usłudze Azure Key Vault i dodać klucz szyfrowania podczas tworzenia klastra.

Zarówno dysk zasobów, jak i dyski zarządzane w każdym węźle klastra są szyfrowane przy pomocą symetrycznego klucza szyfrowania danych (DEK). DEK jest chroniony przy użyciu klucza szyfrowania klucza (KEK) z magazynu kluczy. Procesy szyfrowania i odszyfrowywania są obsługiwane w całości przez usługę Azure HDInsight.

Jeśli zapora magazynu kluczy jest włączona w magazynie kluczy, w którym przechowywany jest klucz szyfrowania dysku, do konfiguracji zapory magazynu kluczy należy dodać regionalne adresy IP dostawcy zasobów USŁUGI HDInsight dla regionu, w którym zostanie wdrożony klaster. Jest to konieczne, ponieważ usługa HDInsight nie jest zaufaną usługą magazynu kluczy platformy Azure.

Za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure można bezpiecznie obracać klucze w magazynie kluczy. Gdy klucz się obraca, klaster HDInsight rozpoczyna korzystanie z nowego klucza w ciągu kilku minut. Włącz funkcje ochrony kluczy [usuwania](../key-vault/general/overview-soft-delete.md) nietrwałego, aby chronić przed scenariuszami ransomware i przypadkowym usunięciem. Magazyny kluczy bez tej funkcji ochrony nie są obsługiwane.

|Typ klastra |Dysk systemu operacyjnego (dysk zarządzany) |Dysk danych (dysk zarządzany) |Tymczasowy dysk danych (lokalny dysk SSD) |
|---|---|---|---|
|Kafka, HBase z przyspieszonym zapisem|[Szyfrowanie SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|Szyfrowanie SSE + opcjonalne szyfrowanie CMK|Opcjonalne szyfrowanie CMK|
|Wszystkie inne klastry (Spark, Interactive, Hadoop, HBase bez przyspieszonego zapisu)|Szyfrowanie SSE|Nie dotyczy|Opcjonalne szyfrowanie CMK|

## <a name="get-started-with-customer-managed-keys"></a>Wprowadzenie do kluczy zarządzanych przez klienta

Aby utworzyć klaster HDInsight z włączonym kluczem zarządzanym przez klienta, przejdziemy przez następujące kroki:

1. Tworzenie tożsamości zarządzanych dla zasobów platformy Azure
1. Tworzenie usługi Azure Key Vault
1. Utwórz klucz
1. Tworzenie zasad dostępu
1. Tworzenie klastra HDInsight z włączonym kluczem zarządzanym przez klienta
1. Obracanie klucza szyfrowania

## <a name="create-managed-identities-for-azure-resources"></a>Tworzenie tożsamości zarządzanych dla zasobów platformy Azure

Utwórz tożsamość zarządzaną przypisaną przez użytkownika do uwierzytelniania w magazynie kluczy.

Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) dla określonych kroków. Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [Tożsamości zarządzane w usłudze Azure HDInsight.](hdinsight-managed-identities.md) Pamiętaj, aby zapisać identyfikator zasobu tożsamości zarządzanej podczas dodawania go do zasad dostępu usługi Key Vault.

## <a name="create-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Tworzenie magazynu kluczy. Zobacz [Tworzenie usługi Azure Key Vault](../key-vault/secrets/quick-create-portal.md) dla określonych kroków.

Usługa HDInsight obsługuje tylko usługę Azure Key Vault. Jeśli masz własny magazyn kluczy, możesz zaimportować klucze do usługi Azure Key Vault. Należy pamiętać, że magazyn kluczy musi mieć włączone **usuwanie nietrwałe.** Aby uzyskać więcej informacji na temat importowania istniejących kluczy, odwiedź stronę [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Utwórz klucz

1. W nowym magazynie kluczy przejdź do pozycji**Klawisze** >  **ustawień** > **+ Generowanie/importowanie**.

    ![Generowanie nowego klucza w usłudze Azure Key Vault](./media/disk-encryption/create-new-key.png "Generowanie nowego klucza w usłudze Azure Key Vault")

1. Podaj nazwę, a następnie wybierz pozycję **Utwórz**. Obsługa domyślnego **typu klucza** **RSA**.

    ![generuje nazwę klucza](./media/disk-encryption/create-key.png "Generowanie nazwy klucza")

1. Po powrocie do strony **Klucze** wybierz utworzony klawisz.

    ![lista kluczy przechowalni kluczy](./media/disk-encryption/key-vault-key-list.png)

1. Wybierz wersję, aby otworzyć stronę **Wersja klucza.** Korzystając z własnego klucza do szyfrowania klastra HDInsight, należy podać klucz URI. Skopiuj **identyfikator klucza** i zapisz go gdzieś, dopóki nie będziesz gotowy do utworzenia klastra.

    ![pobierz identyfikator klucza](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Tworzenie zasad dostępu

1. W nowym magazynie kluczy przejdź do**pozycji Zasady** > dostępu **do ustawień** > **+ Dodaj zasady dostępu**.

    ![Tworzenie nowych zasad dostępu usługi Azure Key Vault](./media/disk-encryption/key-vault-access-policy.png)

1. Na stronie **Dodaj zasady dostępu** podaj następujące informacje:

    |Właściwość |Opis|
    |---|---|
    |Uprawnienia klucza|Wybierz **pozycję Pobierz**, **Rozpakuj klawisz**i **Zawiń klucz**.|
    |Tajne uprawnienia|Wybierz **pozycję Pobierz**, **Ustaw**i **Usuń**.|
    |Wybierz głównego zobowiązanego|Wybierz wcześniej utworzoną tożsamość zarządzaną przypisaną przez użytkownika.|

    ![Ustawianie zasady dostępu wybierz jednostkę zabezpieczeń dla usługi Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Wybierz pozycję **Dodaj**.

1. Wybierz pozycję **Zapisz**.

    ![Zapisywanie zasad dostępu usługi Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Tworzenie klastra z szyfrowaniem dysku klucza zarządzanego przez klienta

Teraz możesz przystąpić do tworzenia nowego klastra HDInsight. Klucz zarządzany przez klienta można zastosować tylko do nowych klastrów podczas tworzenia klastra. Szyfrowania nie można usunąć z klastrów kluczy zarządzanych przez klienta, a klucza zarządzanego przez klienta nie można dodać do istniejących klastrów.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia klastra podaj pełny **identyfikator klucza,** w tym wersję klucza. Na przykład `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Należy również przypisać tożsamość zarządzana do klastra i podać klucz URI.

![Tworzenie nowego klastra](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W poniższym przykładzie pokazano, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia nowego klastra Platformy Spark Apache z włączonym szyfrowaniem dysku. Aby uzyskać więcej informacji, zobacz [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>Używanie szablonów usługi Azure Resource Manager

W poniższym przykładzie pokazano, jak użyć szablonu usługi Azure Resource Manager do utworzenia nowego klastra Platformy Spark Apache z włączonym szyfrowaniem dysku. Aby uzyskać więcej informacji, zobacz [Co to są szablony ARM?](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview).

W tym przykładzie użyto programu PowerShell do wywołania szablonu.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Zawartość szablonu zarządzania `azuredeploy.json`zasobami, :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>Obracanie klucza szyfrowania

Mogą istnieć scenariusze, w których można zmienić klucze szyfrowania używane przez klaster HDInsight po jego utworzeniu. Można to łatwo za pośrednictwem portalu. W przypadku tej operacji klaster musi mieć dostęp zarówno do bieżącego klucza, jak i do zamierzonego nowego klucza, w przeciwnym razie operacja obracania klucza zakończy się niepowodzeniem.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby obrócić klucz, potrzebny jest identyfikator URI magazynu kluczy podstawowych. Po wykonaniu tej tej pracy przejdź do sekcji Właściwości klastra HDInsight w portalu i kliknij pozycję **Zmień klucz** w obszarze **Adres URL klucza szyfrowania dysku**. Wprowadź nowy klucz url i prześlij, aby obrócić klucz.

![obracanie klucza szyfrowania dysku](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W poniższym przykładzie pokazano, jak obrócić klucz szyfrowania dysku dla istniejącego klastra HDInsight. Aby uzyskać więcej informacji, zobacz [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Często zadawane pytania dotyczące szyfrowania kluczy zarządzanych przez klienta

**W jaki sposób klaster HDInsight uzyskuje dostęp do mojego magazynu kluczy?**

Usługa HDInsight uzyskuje dostęp do wystąpienia usługi Azure Key Vault przy użyciu tożsamości zarządzanej skojarzonej z klastrem HDInsight. Tę tożsamość zarządzaną można utworzyć przed utworzeniem klastra lub w jego trakcie. Należy również udzielić dostępu do tożsamości zarządzanej do magazynu kluczy, w którym klucz jest przechowywany.

**Czy ta funkcja jest dostępna dla wszystkich klastrów w programie HDInsight?**

Szyfrowanie kluczy zarządzane przez klienta jest dostępne dla wszystkich typów klastrów z wyjątkiem platformy Spark 2.1 i 2.2.

**Czy mogę używać wielu kluczy do szyfrowania różnych dysków lub folderów?**

Nie, wszystkie dyski zarządzane i dyski zasobów są szyfrowane przez ten sam klucz.

**Co się stanie, jeśli klaster utraci dostęp do magazynu kluczy lub klucza?**

Jeśli klaster utraci dostęp do klucza, ostrzeżenia będą wyświetlane w portalu Apache Ambari. W tym stanie operacja **Zmień klucz** zakończy się niepowodzeniem. Po przywróceniu dostępu do kluczy ostrzeżenia Ambari zniknę, a operacje takie jak obrót kluczy mogą być pomyślnie wykonywane.

![alert ambari dostępu do klawiszy](./media/disk-encryption/ambari-alert.png)

**Jak odzyskać klaster, jeśli klucze zostaną usunięte?**

Ponieważ obsługiwane są tylko klawisze włączone "Usuwanie nietrwałe", jeśli klucze zostaną odzyskane w magazynie kluczy, klaster powinien odzyskać dostęp do kluczy. Aby odzyskać klucz usługi Azure Key Vault, zobacz [Cofnij klucz AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) lub [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Jakie typy dysków są szyfrowane? Czy dyski systemu operacyjnego/dyski zasobów są również szyfrowane?**

Dyski zasobów i dyski danych/dysków zarządzanych są szyfrowane. Dyski systemu operacyjnego nie są szyfrowane.

**Jeśli klaster jest skalowany w górę, czy nowe węzły będą bezproblemowo obsługiwać klucze zarządzane przez klienta?**

Tak. Klaster potrzebuje dostępu do klucza w magazynie kluczy podczas skalowania w górę. Ten sam klucz jest używany do szyfrowania zarówno dysków zarządzanych, jak i dysków zasobów w klastrze.

**Czy klucze zarządzane przez klienta są dostępne w mojej lokalizacji?**

Klucze zarządzane przez klienta HDInsight są dostępne we wszystkich chmurach publicznych i chmurach krajowych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault](../key-vault/general/overview.md).
* [Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

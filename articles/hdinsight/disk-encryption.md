---
title: Podwójne szyfrowanie danych w spoczynku
titleSuffix: Azure HDInsight
description: W tym artykule opisano dwie warstwy szyfrowania dostępne dla danych w spoczynku Azure HDInsight klastrach.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 226516b1178f14789570b45b68cfdbf56f63bbd7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775155"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight podwójne szyfrowanie danych w spoczynku

W tym artykule omówiono metody szyfrowania danych w spoczynku Azure HDInsight klastrach. Szyfrowanie danych w spoczynku odnosi się do szyfrowania dysków zarządzanych (dysków danych, dysków systemu operacyjnego i dysków tymczasowych) dołączonych do maszyn wirtualnych klastra usługi HDInsight. 

Ten dokument nie dotyczy danych przechowywanych na koncie usługi Azure Storage. Klastry mogą mieć co najmniej jedno dołączone konto usługi Azure Storage, w którym klucze szyfrowania mogą być również zarządzane przez firmę Microsoft lub zarządzane przez klienta, ale usługa szyfrowania jest inna. Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage, zobacz [Szyfrowanie usługi Azure Storage dla danych magazynowych.](../storage/common/storage-service-encryption.md)

## <a name="introduction"></a>Wprowadzenie

Istnieją trzy główne role dysku zarządzanego na platformie Azure: dysk danych, dysk systemu operacyjnego i dysk tymczasowy. Aby uzyskać więcej informacji na temat różnych typów dysków zarządzanych, zobacz [Introduction to Azure managed disks (Wprowadzenie do dysków zarządzanych platformy Azure).](../virtual-machines/managed-disks-overview.md) 

HdInsight obsługuje wiele typów szyfrowania w dwóch różnych warstwach:

- Szyfrowanie po stronie serwera (SSE) — SSE jest wykonywane przez usługę magazynu. W umacie HDInsight szyfrowanie SSE jest używane do szyfrowania dysków systemu operacyjnego i dysków danych. Jest ona domyślnie włączona. SSE to usługa szyfrowania warstwy 1.
- Szyfrowanie na hoście przy użyciu klucza zarządzanego przez platformę — podobnie jak w przypadku szyfrowania SSE, ten typ szyfrowania jest wykonywany przez usługę magazynu. Jest ona jednak dostępna tylko dla dysków tymczasowych i nie jest domyślnie włączona. Szyfrowanie na hoście jest również usługą szyfrowania warstwy 1.
- Szyfrowanie danych w spoczynku przy użyciu klucza zarządzanego przez klienta — tego typu szyfrowania można używać na danych i dyskach tymczasowych. Nie jest ona domyślnie włączona i wymaga od klienta podania własnego klucza za pośrednictwem usługi Azure Key Vault. Szyfrowanie danych w spoczynku jest usługą szyfrowania warstwy 2.

Te typy zostały podsumowane w poniższej tabeli.

|Typ klastra |Dysk systemu operacyjnego (dysk zarządzany) |Dysk danych (dysk zarządzany) |Tymczasowy dysk danych (lokalny dysk SSD) |
|---|---|---|---|
|Platforma Kafka, baza danych HBase z przyspieszonym zapisem|Warstwa1: [domyślnie szyfrowanie SSE](../virtual-machines/managed-disks-overview.md#encryption)|Warstwa1: domyślnie [szyfrowanie SSE,](../virtual-machines/managed-disks-overview.md#encryption) Warstwa 2: Opcjonalne szyfrowanie danych spoczynku przy użyciu klucza cmk|Warstwa 1: opcjonalne szyfrowanie na hoście przy użyciu klucza zarządzania kluczami, warstwa 2: opcjonalne szyfrowanie danych spoczynku przy użyciu klucza cmk|
|Wszystkie inne klastry (Spark, Interactive, Hadoop, HBase bez przyspieszonych zapisu)|Warstwa1: [domyślnie szyfrowanie SSE](../virtual-machines/managed-disks-overview.md#encryption)|Nie dotyczy|Warstwa 1: opcjonalne szyfrowanie na hoście przy użyciu klucza zarządzania kluczami, warstwa 2: opcjonalne szyfrowanie danych spoczynku przy użyciu klucza cmk|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Szyfrowanie danych w spoczynku przy użyciu kluczy zarządzanych przez klienta

Szyfrowanie kluczy zarządzane przez klienta to proces jednoetapowy obsługiwany podczas tworzenia klastra bez dodatkowych kosztów. Wszystko, co należy zrobić, to autoryzować tożsamość zarządzaną za pomocą Azure Key Vault i dodać klucz szyfrowania podczas tworzenia klastra.

Zarówno dyski danych, jak i dyski tymczasowe w każdym węźle klastra są szyfrowane przy użyciu symetrycznego klucza szyfrowania danych (DEK). Klucz szyfrowania danych jest chroniony przy użyciu klucza szyfrowania klucza (KEK) z magazynu kluczy. Procesy szyfrowania i odszyfrowywania są obsługiwane w całości przez Azure HDInsight.

W przypadku dysków systemu operacyjnego dołączonych do maszyn wirtualnych klastra jest dostępna tylko jedna warstwa szyfrowania (PMK). Zaleca się, aby klienci unikali kopiowania poufnych danych na dyski systemu operacyjnego, jeśli w ich scenariuszach wymagane jest szyfrowanie klucza CMK.

Jeśli zapora magazynu kluczy jest włączona w magazynie kluczy, w którym jest przechowywany klucz szyfrowania dysków, do konfiguracji zapory magazynu kluczy należy dodać adresy IP regionalnego dostawcy zasobów usługi HDInsight dla regionu, w którym zostanie wdrożony klaster. Jest to konieczne, ponieważ usługa HDInsight nie jest zaufaną usługą azure key vault.

Aby bezpiecznie obracać klucze w magazynie kluczy, Azure Portal interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure. Gdy klucz się obraca, klaster usługi HDInsight rozpoczyna korzystanie z nowego klucza w ciągu kilku minut. Włącz funkcje ochrony [klucza usuwania nie](../key-vault/general/soft-delete-overview.md) softowego, aby chronić się przed scenariuszami oprogramowania wymuszającego okup i przypadkowym usunięciem. Magazyny kluczy bez tej funkcji ochrony nie są obsługiwane.

### <a name="get-started-with-customer-managed-keys"></a>Wprowadzenie do kluczy zarządzanych przez klienta

Aby utworzyć klaster usługi HDInsight z obsługą klucza zarządzanego przez klienta, należy wykonać następujące kroki:

1. Tworzenie tożsamości zarządzanych dla zasobów platformy Azure
1. Tworzenie Azure Key Vault
1. Tworzenie klucza
1. Tworzenie zasad dostępu
1. Tworzenie klastra usługi HDInsight z włączonym kluczem zarządzanym przez klienta
1. Obracanie klucza szyfrowania

Każdy krok został szczegółowo wyjaśniony w jednej z poniższych sekcji.

### <a name="create-managed-identities-for-azure-resources"></a>Tworzenie tożsamości zarządzanych dla zasobów platformy Azure

Utwórz tożsamość zarządzaną przypisaną przez użytkownika w celu uwierzytelnienia w Key Vault.

Zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika,](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) aby uzyskać szczegółowe instrukcje. Aby uzyskać więcej informacji na temat sposobu działania tożsamości zarządzanych w Azure HDInsight, zobacz [Tożsamości zarządzane](hdinsight-managed-identities.md)w Azure HDInsight . Pamiętaj, aby zapisać identyfikator zasobu tożsamości zarządzanej dla programu po dodaniu go do Key Vault dostępu.

### <a name="create-azure-key-vault"></a>Tworzenie Azure Key Vault

Utwórz magazyn kluczy. Zobacz [Tworzenie Azure Key Vault,](../key-vault/general/quick-create-portal.md) aby uzyskać szczegółowe instrukcje.

HdInsight obsługuje tylko Azure Key Vault. Jeśli masz własny magazyn kluczy, możesz zaimportować klucze do Azure Key Vault. Należy pamiętać, że magazyn kluczy musi mieć **włączoną funkcję usuwania nie softu.** Aby uzyskać więcej informacji na temat importowania istniejących kluczy, odwiedź stronę [About keys, secrets, and certificates (Informacje o kluczach, wpisach tajnych i certyfikatach).](../key-vault/general/about-keys-secrets-certificates.md)

### <a name="create-key"></a>Tworzenie klucza

1. W nowym magazynie kluczy przejdź do ustawień  >  **Klucze**  >  **+ Generuj/Importuj**.

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Generowanie nowego klucza w Azure Key Vault":::

1. Podaj nazwę, a następnie wybierz pozycję **Utwórz.** Zachowaj domyślny **typ klucza** **RSA.**

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="generuje nazwę klucza":::

1. Po powrocie do strony **Klucze** wybierz utworzony klucz.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="lista kluczy magazynu kluczy":::

1. Wybierz wersję, aby otworzyć **stronę Wersja** klucza. Jeśli używasz własnego klucza do szyfrowania klastra usługi HDInsight, musisz podać jego URI. Skopiuj identyfikator **klucza i** zapisz go w innym miejscu, aż wszystko będzie gotowe do utworzenia klastra.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="uzyskiwanie identyfikatora klucza":::

### <a name="create-access-policy"></a>Tworzenie zasad dostępu

1. W nowym magazynie kluczy przejdź do ustawień **Zasady** dostępu  >    >  **+ Dodaj zasady dostępu.**

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="Tworzenie nowych zasad Azure Key Vault dostępu":::

1. Na stronie **Dodawanie zasad dostępu** podaj następujące informacje:

    |Właściwość |Opis|
    |---|---|
    |Uprawnienia klucza|Wybierz **pozycję Pobierz,** **Odpakuj klucz** i **Opakuj klucz.**|
    |Uprawnienia wpisu tajnego|Wybierz **pozycję Pobierz,** **Ustaw** i **Usuń.**|
    |Wybieranie podmiotu zabezpieczeń|Wybierz utworzoną wcześniej tożsamość zarządzaną przypisaną przez użytkownika.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Ustawianie opcji Wybierz podmiot zabezpieczeń Azure Key Vault zasad dostępu":::

1. Wybierz pozycję **Dodaj**.

1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Zapisywanie Azure Key Vault dostępu":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Tworzenie klastra z szyfrowaniem dysków kluczem zarządzanym przez klienta

Teraz możesz utworzyć nowy klaster usługi HDInsight. Klucze zarządzane przez klienta można stosować tylko do nowych klastrów podczas tworzenia klastra. Szyfrowania nie można usunąć z klastrów kluczy zarządzanych przez klienta, a kluczy zarządzanych przez klienta nie można dodawać do istniejących klastrów.

Począwszy od wersji z listopada 2020 r., usługa HDInsight obsługuje tworzenie klastrów przy użyciu zarówno URI kluczy wersjonarowanych, jak i bez wersji. Jeśli utworzysz klaster przy użyciu klucza URI bez wersji, klaster usługi HDInsight spróbuje przeprowadzić automatyczną rotację kluczy po zaktualizowania klucza w Azure Key Vault. Jeśli utworzysz klaster z wersjonacyjną wersją klucza URI, musisz wykonać ręczną rotację kluczy, jak omówiono w artykule [Rotating the encryption key (Obracanie klucza szyfrowania).](#rotating-the-encryption-key)

W przypadku klastrów utworzonych przed wydaniem z listopada 2020 r. należy ręcznie wykonać rotację kluczy przy użyciu wersji URI klucza.

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia klastra można użyć klucza wersji lub klucza bez wersji w następujący sposób:

- **Wersja —** podczas tworzenia klastra podaj pełny **identyfikator klucza,** w tym wersję klucza. Na przykład `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **Bez wersji** — podczas tworzenia klastra podaj tylko **identyfikator klucza**. Na przykład `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

Należy również przypisać tożsamość zarządzaną do klastra.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="Tworzenie nowego klastra":::

#### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W poniższym przykładzie pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć nowy klaster Apache Spark z włączonym szyfrowaniem dysków. Aby uzyskać więcej informacji, zobacz [Azure CLI az hdinsight create](/cli/azure/hdinsight#az_hdinsight_create). Parametr `encryption-key-version` jest opcjonalny.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager

W poniższym przykładzie pokazano, jak za pomocą szablonu Azure Resource Manager utworzyć nowy klaster Apache Spark z włączonym szyfrowaniem dysków. Aby uzyskać więcej informacji, zobacz [Co to są szablony arm?](../azure-resource-manager/templates/overview.md). Właściwość szablonu usługi Resource Manager `diskEncryptionKeyVersion` jest opcjonalna.

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

Zawartość szablonu zarządzania `azuredeploy.json` zasobami:

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

### <a name="rotating-the-encryption-key"></a>Obracanie klucza szyfrowania

Klucze szyfrowania używane w uruchomionym klastrze można zmienić przy użyciu interfejsu wiersza Azure Portal platformy Azure. W przypadku tej operacji klaster musi mieć dostęp zarówno do bieżącego klucza, jak i do zamierzonego nowego klucza. W przeciwnym razie operacja rotacji klucza nie powiedzie się. W przypadku klastrów utworzonych po wydaniu z listopada 2020 r. można wybrać, czy nowy klucz ma mieć wersję, czy nie. W przypadku klastrów utworzonych przed wydaniem z listopada 2020 r. podczas rotacji klucza szyfrowania należy użyć klucza wersji.

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby obrócić klucz, potrzebujesz podstawowego klucza URI magazynu kluczy. Po zakończeniu przejdź do sekcji właściwości klastra usługi HDInsight w  portalu i kliknij pozycję Zmień klucz w obszarze **Adres URL klucza szyfrowania dysków.** Wprowadź nowy adres URL klucza i prześlij go, aby obrócić klucz.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="obracanie klucza szyfrowania dysku":::

#### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W poniższym przykładzie pokazano, jak obrócić klucz szyfrowania dysku dla istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [az hdinsight rotate-disk-encryption-key interfejsu wiersza](/cli/azure/hdinsight#az_hdinsight_rotate_disk_encryption_key)polecenia platformy Azure.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Często zadawane pytania dotyczące szyfrowania kluczy zarządzanych przez klienta

**Jak klaster usługi HDInsight uzyskuje dostęp do mojego magazynu kluczy?**

Usługa HDInsight uzyskuje dostęp do Azure Key Vault przy użyciu tożsamości zarządzanej skojarzeń z klastrem usługi HDInsight. Tę tożsamość zarządzaną można utworzyć przed lub podczas tworzenia klastra. Należy również udzielić tożsamości zarządzanej dostępu do magazynu kluczy, w którym jest przechowywany klucz.

**Czy ta funkcja jest dostępna dla wszystkich klastrów w umacie HDInsight?**

Szyfrowanie kluczy zarządzane przez klienta jest dostępne dla wszystkich typów klastrów z wyjątkiem platformy Spark 2.1 i 2.2.

**Czy mogę użyć wielu kluczy do szyfrowania różnych dysków lub folderów?**

Nie, wszystkie dyski zarządzane i dyski zasobów są szyfrowane za pomocą tego samego klucza.

**Co się stanie, jeśli klaster utraci dostęp do magazynu kluczy lub klucza?**

Jeśli klaster utraci dostęp do klucza, ostrzeżenia będą wyświetlane w portalu Apache Ambari. W tym stanie operacja **zmiany klucza** nie powiedzie się. Po przywróceniu dostępu do klucza ostrzeżenia systemu Ambari zostaną zmieściły się i można pomyślnie wykonać operacje, takie jak rotacja kluczy.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="alert Ambari dostępu do klucza":::

**Jak mogę odzyskać klaster, jeśli klucze zostaną usunięte?**

Ponieważ obsługiwane są tylko klucze włączone "usuwanie nie softu", jeśli klucze zostaną odzyskane w magazynie kluczy, klaster powinien odzyskać dostęp do kluczy. Aby odzyskać klucz Azure Key Vault, zobacz [Polecenie Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) lub [az-keyvault-key-recover.](/cli/azure/keyvault/key#az_keyvault_key_recover)


**Jeśli klaster jest skalowany w górę, czy nowe węzły będą bezproblemowo obsługiwać klucze zarządzane przez klienta?**

Tak. Klaster musi mieć dostęp do klucza w magazynie kluczy podczas skalowania w górę. Ten sam klucz jest używany do szyfrowania dysków zarządzanych i dysków zasobów w klastrze.

**Czy klucze zarządzane przez klienta są dostępne w mojej lokalizacji?**

Klucze zarządzane przez klienta usługi HDInsight są dostępne we wszystkich chmurach publicznych i chmurach krajowych.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Szyfrowanie na hoście przy użyciu kluczy zarządzanych przez platformę

### <a name="enable-in-the-azure-portal"></a>Włącz w Azure Portal

Szyfrowanie na hoście można włączyć podczas tworzenia klastra w Azure Portal.

> [!Note]
> Po włączeniu szyfrowania na hoście nie można dodawać aplikacji do klastra usługi HDInsight z poziomu witryny Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Włącz szyfrowanie na hoście.":::

Ta opcja umożliwia szyfrowanie [na hoście tymczasowych](../virtual-machines/disks-enable-host-based-encryption-portal.md) dysków danych maszyn wirtualnych usługi HDInsight przy użyciu klucza pmk. Szyfrowanie na hoście jest obsługiwane [tylko w przypadku niektórych jednostki SKU](../virtual-machines/disks-enable-host-based-encryption-portal.md) maszyn wirtualnych w ograniczonych regionach, a hdInsight obsługuje następującą [konfigurację węzłów i jednostki SKU](./hdinsight-supported-node-configuration.md).

Aby poznać właściwy rozmiar maszyny wirtualnej dla klastra usługi HDInsight, zobacz Wybieranie odpowiedniego rozmiaru maszyny wirtualnej [dla klastra Azure HDInsight usługi](hdinsight-selecting-vm-size.md). Domyślną sku maszyny wirtualnej dla węzła zookeeper po włączeniu szyfrowania na hoście będzie DS2V2.

### <a name="enable-using-powershell"></a>Włączanie przy użyciu programu PowerShell

Poniższy fragment kodu przedstawia sposób tworzenia nowego klastra klastra Azure HDInsight z włączonym szyfrowaniem na hoście przy użyciu programu PowerShell. Używa parametru , `-EncryptionAtHost $true` aby włączyć tę funkcję.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Włączanie przy użyciu interfejsu wiersza polecenia platformy Azure

Poniższy fragment kodu przedstawia sposób tworzenia nowego klastra klastra Azure HDInsight z włączonym szyfrowaniem na hoście przy użyciu interfejsu wiersza polecenia platformy Azure. Używa parametru , `--encryption-at-host true` aby włączyć tę funkcję.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na Azure Key Vault, zobacz [Co to jest Azure Key Vault](../key-vault/general/overview.md).
* [Omówienie zabezpieczeń przedsiębiorstwa w Azure HDInsight.](./domain-joined/hdinsight-security-overview.md)

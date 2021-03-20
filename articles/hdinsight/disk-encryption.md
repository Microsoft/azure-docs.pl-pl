---
title: Podwójne szyfrowanie danych magazynowanych
titleSuffix: Azure HDInsight
description: W tym artykule opisano dwie warstwy szyfrowania dostępne dla danych przechowywanych w klastrach usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 58b3d892ea24430a9d951a5a0230282f6c4fd584
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988617"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Podwójne szyfrowanie usługi Azure HDInsight dla danych magazynowanych

W tym artykule omówiono metody szyfrowania danych przechowywanych w klastrach usługi Azure HDInsight. Szyfrowanie danych w spoczynku odnosi się do szyfrowania na dyskach zarządzanych (dysków danych, dysków systemu operacyjnego i dysków tymczasowych) dołączonych do maszyn wirtualnych klastra usługi HDInsight. 

Ten dokument nie jest adresem danych przechowywanych na koncie usługi Azure Storage. Klastry mogą mieć co najmniej jedno dołączone konto usługi Azure Storage, na których klucze szyfrowania mogą być również zarządzane przez firmę Microsoft lub zarządzane przez klienta, ale usługa szyfrowania jest inna. Aby uzyskać więcej informacji na temat szyfrowania usługi Azure Storage, zobacz [szyfrowanie usługi Azure Storage w przypadku przechowywania danych](../storage/common/storage-service-encryption.md).

## <a name="introduction"></a>Wprowadzenie

Na platformie Azure istnieją trzy główne role dysków zarządzanych: dysk danych, dysk systemu operacyjnego i dysk tymczasowy. Aby uzyskać więcej informacji na temat różnych typów dysków zarządzanych, zobacz [wprowadzenie do usługi Azure Managed disks](../virtual-machines/managed-disks-overview.md). 

Usługa HDInsight obsługuje wiele typów szyfrowania w dwóch różnych warstwach:

- Szyfrowanie po stronie serwera (SSE) — funkcja SSE jest wykonywana przez usługę magazynu. W usłudze HDInsight funkcja SSE jest używana do szyfrowania dysków systemu operacyjnego i dysków danych. Jest on domyślnie włączony. SSE jest usługą szyfrowania warstwy 1.
- Szyfrowanie na hoście przy użyciu klucza zarządzanego przez platformę — podobnego do instrukcji SSE, ten typ szyfrowania jest wykonywany przez usługę magazynu. Jednak tylko w przypadku dysków tymczasowych i nie jest domyślnie włączona. Szyfrowanie na hoście to również usługa szyfrowania warstwy 1.
- Szyfrowanie w spoczynku przy użyciu klucza zarządzanego przez klienta — ten typ szyfrowania może być używany na dyskach danych i tymczasowych. Nie jest on domyślnie włączony i wymaga od klienta podania własnego klucza za pomocą usługi Azure Key magazyn. Szyfrowanie w spoczynku to usługa szyfrowania warstwy 2.

Te typy zostały podsumowane w poniższej tabeli.

|Typ klastra |Dysk systemu operacyjnego (dysk zarządzany) |Dysk danych (dysk zarządzany) |Dysk danych tymczasowych (lokalny dysk SSD) |
|---|---|---|---|
|Kafka, HBase z przyspieszeniem zapisu|Layer1: Domyślnie [szyfrowanie SSE](../virtual-machines/managed-disks-overview.md#encryption)|Layer1: [szyfrowanie SSE](../virtual-machines/managed-disks-overview.md#encryption) domyślnie, layer2: Opcjonalne szyfrowanie przy użyciu CMK|Layer1: Opcjonalne szyfrowanie na hoście przy użyciu klucza głównego parowania, layer2: Opcjonalne szyfrowanie w spoczynku przy użyciu CMK|
|Wszystkie inne klastry (Spark, Interactive, Hadoop, HBase bez przyspieszenia zapisu)|Layer1: Domyślnie [szyfrowanie SSE](../virtual-machines/managed-disks-overview.md#encryption)|Nie dotyczy|Layer1: Opcjonalne szyfrowanie na hoście przy użyciu klucza głównego parowania, layer2: Opcjonalne szyfrowanie w spoczynku przy użyciu CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Szyfrowanie w spoczynku przy użyciu kluczy zarządzanych przez klienta

Szyfrowanie klucza zarządzanego przez klienta to proces jednoetapowy obsługiwany podczas tworzenia klastra bez dodatkowych kosztów. Wszystko, co należy zrobić, jest autoryzowanie tożsamości zarządzanej przy użyciu Azure Key Vault i dodanie klucza szyfrowania podczas tworzenia klastra.

Zarówno dyski danych, jak i dyski tymczasowe w każdym węźle klastra są szyfrowane za pomocą klucza szyfrowania danych symetrycznych. Klucz szyfrowania danych jest chroniony przy użyciu klucza szyfrowanie klucza (KEK) z magazynu kluczy. Procesy szyfrowania i odszyfrowywania są obsługiwane całkowicie przez usługę Azure HDInsight.

W przypadku dysków systemu operacyjnego podłączonych do maszyn wirtualnych klastra dostępna jest tylko jedna warstwa szyfrowania (kluczy głównych parowania). Zaleca się, aby klienci unikali kopiowania poufnych danych na dyski systemu operacyjnego, jeśli dla ich scenariuszy wymagane jest szyfrowanie CMK.

Jeśli w magazynie kluczy, w którym jest przechowywany klucz szyfrowania dysku, jest włączona Zapora magazynu kluczy, adresy IP regionalnego dostawcy zasobów usługi HDInsight dla regionu, w którym zostanie wdrożony klaster, muszą zostać dodane do konfiguracji zapory magazynu kluczy. Jest to konieczne, ponieważ Usługa HDInsight nie jest zaufaną usługą magazynu kluczy platformy Azure.

Możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby bezpiecznie obrócić klucze w magazynie kluczy. Gdy klucz zostanie obrócony, klaster usługi HDInsight zaczyna używać nowego klucza w ciągu kilku minut. Włącz funkcje ochrony klucza [nietrwałego](../key-vault/general/soft-delete-overview.md) , aby chronić je przed scenariuszami oprogramowania wymuszającego okup i przypadkowym usunięciem. Magazyny kluczy bez tej funkcji ochrony nie są obsługiwane.

### <a name="get-started-with-customer-managed-keys"></a>Wprowadzenie do kluczy zarządzanych przez klienta

Aby utworzyć klaster usługi HDInsight z włączonym kluczem zarządzanym przez klienta, wykonaj następujące czynności:

1. Tworzenie tożsamości zarządzanych dla zasobów platformy Azure
1. Utwórz Azure Key Vault
1. Utwórz klucz
1. Utwórz zasady dostępu
1. Tworzenie klastra usługi HDInsight z włączonym kluczem zarządzanym przez klienta
1. Obracanie klucza szyfrowania

Każdy krok jest objaśniony w jednej z poniższych sekcji szczegółowo.

### <a name="create-managed-identities-for-azure-resources"></a>Tworzenie tożsamości zarządzanych dla zasobów platformy Azure

Utwórz tożsamość zarządzaną przypisaną przez użytkownika w celu uwierzytelnienia do Key Vault.

Szczegółowe instrukcje można znaleźć w temacie [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) . Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](hdinsight-managed-identities.md). Należy pamiętać o zapisaniu identyfikatora zasobu tożsamości zarządzanej dla programu podczas dodawania go do zasad dostępu Key Vault.

### <a name="create-azure-key-vault"></a>Utwórz Azure Key Vault

Utwórz magazyn kluczy. Aby zapoznać się z określonymi krokami, zobacz [tworzenie Azure Key Vault](../key-vault/general/quick-create-portal.md) .

Usługa HDInsight obsługuje tylko Azure Key Vault. Jeśli masz własny magazyn kluczy, możesz zaimportować klucze do Azure Key Vault. Należy pamiętać, że magazyn kluczy musi mieć włączone **usuwanie nietrwałe** . Aby uzyskać więcej informacji o importowaniu istniejących kluczy, odwiedź stronę [dotyczącą kluczy, wpisów tajnych i certyfikatów](../key-vault/general/about-keys-secrets-certificates.md).

### <a name="create-key"></a>Utwórz klucz

1. W nowym magazynie kluczy przejdź do pozycje **Ustawienia**  >  **klucze**  >  **+ Generuj/Importuj**.

    ![Generuj nowy klucz w Azure Key Vault](./media/disk-encryption/create-new-key.png "Generuj nowy klucz w Azure Key Vault")

1. Podaj nazwę, a następnie wybierz pozycję **Utwórz**. Zachowaj domyślny **Typ klucza** **RSA**.

    ![generuje nazwę klucza](./media/disk-encryption/create-key.png "Generuj nazwę klucza")

1. Po powrocie do strony **klucze** wybierz utworzony klucz.

    ![Lista kluczy magazynu kluczy](./media/disk-encryption/key-vault-key-list.png)

1. Wybierz wersję, aby otworzyć stronę **wersja klucza** . Jeśli używasz własnego klucza do szyfrowania klastra usługi HDInsight, musisz podać identyfikator URI klucza. Skopiuj **Identyfikator klucza** i Zapisz go w dowolnym miejscu do momentu, aż wszystko będzie gotowe do utworzenia klastra.

    ![Pobierz identyfikator klucza](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>Utwórz zasady dostępu

1. W nowym magazynie kluczy przejdź do **ustawień**  >  **zasady dostępu**  >  **i Dodaj zasady dostępu**.

    ![Utwórz nowe zasady dostępu Azure Key Vault](./media/disk-encryption/key-vault-access-policy.png)

1. Na stronie **Dodawanie zasad dostępu** podaj następujące informacje:

    |Właściwość |Opis|
    |---|---|
    |Uprawnienia klucza|Wybierz pozycję **Pobierz**, **Odpakuj klucz** i **Zawijaj klucz**.|
    |Uprawnienia wpisu tajnego|Wybierz pozycje **Get**, **Set** i **delete**.|
    |Wybierz podmiot zabezpieczeń|Wybierz utworzoną wcześniej tożsamość zarządzaną przypisaną przez użytkownika.|

    ![Ustaw pozycję Wybierz podmiot zabezpieczeń dla zasad dostępu Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Wybierz pozycję **Dodaj**.

1. Wybierz pozycję **Zapisz**.

    ![Zapisz zasady dostępu Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Tworzenie klastra przy użyciu szyfrowania dysku klucza zarządzanego przez klienta

Teraz możesz utworzyć nowy klaster usługi HDInsight. Klucze zarządzane przez klienta mogą być stosowane tylko do nowych klastrów podczas tworzenia klastra. Nie można usunąć szyfrowania z klastrów kluczy zarządzanych przez klienta, a klucze zarządzane przez klienta nie mogą być dodawane do istniejących klastrów.

Począwszy od wersji 2020 listopada Usługa HDInsight obsługuje tworzenie klastrów przy użyciu identyfikatorów URI kluczy z wersjami i bez znaku. Jeśli utworzysz klaster z identyfikatorem URI klucza bez wersji, klaster usługi HDInsight podejmie próbę przeprowadzenia kluczowego skalowania, gdy klucz zostanie zaktualizowany w Azure Key Vault. W przypadku utworzenia klastra z identyfikatorem URI klucza z podsystemem należy wykonać ręczną rotację kluczy, jak opisano w [obracaniu klucza szyfrowania](#rotating-the-encryption-key).

W przypadku klastrów utworzonych przed wydaniem listopad 2020 konieczne będzie ręczne przeprowadzenie rotacji przy użyciu identyfikatora URI klucza z wersją.

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia klastra można użyć klucza z podsystemem lub bezwartościowej wersji w następujący sposób:

- W **wersji** — podczas tworzenia klastra Podaj pełny **Identyfikator klucza**, w tym wersję klucza. Na przykład `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- Bezobsługowa **wersja** — podczas tworzenia klastra podaj tylko **Identyfikator klucza**. Na przykład `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

Należy również przypisać zarządzaną tożsamość do klastra.

![Utwórz nowy klaster](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

W poniższym przykładzie pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć nowy klaster Apache Spark z włączonym szyfrowaniem dysków. Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure AZ HDInsight Create](/cli/azure/hdinsight#az-hdinsight-create). Parametr `encryption-key-version` jest opcjonalny.

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

Poniższy przykład pokazuje, jak użyć szablonu Azure Resource Manager, aby utworzyć nowy klaster Apache Spark z włączonym szyfrowaniem dysków. Aby uzyskać więcej informacji, zobacz [co to są szablony ARM?](../azure-resource-manager/templates/overview.md). Właściwość szablonu Menedżera zasobów `diskEncryptionKeyVersion` jest opcjonalna.

Ten przykład używa programu PowerShell do wywoływania szablonu.

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

Zawartość szablonu zarządzania zasobami `azuredeploy.json` :

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

Klucze szyfrowania używane w uruchomionym klastrze można zmienić przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure. W przypadku tej operacji klaster musi mieć dostęp zarówno do bieżącego klucza, jak i do zamierzonego nowego klucza. w przeciwnym razie operacja zamiany klucza zakończy się niepowodzeniem. W przypadku klastrów utworzonych po wydaniu z listopada 2020 możesz wybrać, czy nowy klucz ma mieć wersję, czy nie. W przypadku klastrów utworzonych przed wydaniem listopad 2020 należy użyć klucza z podsystemem wersji podczas obracania klucza szyfrowania.

#### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby obrócić klucz, potrzebny jest podstawowy identyfikator URI magazynu kluczy. Po wykonaniu tej czynności przejdź do sekcji Właściwości klastra usługi HDInsight w portalu, a następnie kliknij pozycję **Zmień klucz** w obszarze **adres URL klucza szyfrowania dysku**. Wprowadź adres URL nowego klucza i prześlij go, aby obrócić klucz.

![Obróć klucz szyfrowania dysku](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Poniższy przykład pokazuje, jak obrócić klucz szyfrowania dysku dla istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [interfejs wiersza polecenia platformy Azure AZ HDInsight Obróć-Disk-Encryption-Key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key).

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

Usługa HDInsight uzyskuje dostęp do wystąpienia Azure Key Vault przy użyciu tożsamości zarządzanej skojarzonej z klastrem usługi HDInsight. Tę tożsamość zarządzaną można utworzyć przed lub podczas tworzenia klastra. Musisz również udzielić zarządzanej tożsamości dostępu do magazynu kluczy, w którym jest przechowywany klucz.

**Czy ta funkcja jest dostępna dla wszystkich klastrów w usłudze HDInsight?**

Szyfrowanie klucza zarządzanego przez klienta jest dostępne dla wszystkich typów klastrów z wyjątkiem Spark 2,1 i 2,2.

**Czy można użyć wielu kluczy do szyfrowania różnych dysków lub folderów?**

Nie, wszystkie dyski zarządzane i dyski zasobów są szyfrowane przy użyciu tego samego klucza.

**Co się stanie, Jeśli klaster utraci dostęp do magazynu kluczy lub klucza?**

Jeśli klaster utraci dostęp do klucza, ostrzeżenia będą wyświetlane w portalu Apache Ambari. W tym stanie operacja **zmiany klucza** zakończy się niepowodzeniem. Po przywróceniu dostępu do klucza Ambari ostrzeżenia i operacje, takie jak rotacja kluczy, mogą zostać wykonane pomyślnie.

![alert dotyczący Ambari dostępu do klucza](./media/disk-encryption/ambari-alert.png)

**Jak odzyskać klaster, jeśli klucze są usuwane?**

Ponieważ obsługiwane są tylko klucze z włączonym usuwaniem nietrwałego, jeśli klucze zostaną odzyskane w magazynie kluczy, klaster powinien odzyskać dostęp do kluczy. Aby odzyskać klucz Azure Key Vault, zobacz [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) lub [AZ-Key magazyn-klucz-Recover](/cli/azure/keyvault/key#az-keyvault-key-recover).


**Jeśli klaster jest skalowany w górę, nowe węzły będą bezproblemowo obsługiwać klucze zarządzane przez klienta?**

Tak. Klaster wymaga dostępu do klucza w magazynie kluczy podczas skalowania w górę. Ten sam klucz służy do szyfrowania zarówno dysków zarządzanych, jak i dysków zasobów w klastrze.

**Czy klucze zarządzane przez klienta są dostępne w mojej lokalizacji?**

Klucze zarządzane przez klienta usługi HDInsight są dostępne we wszystkich chmurach publicznych i w chmurach narodowych.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Szyfrowanie na hoście przy użyciu kluczy zarządzanych przez platformę

### <a name="enable-in-the-azure-portal"></a>Włącz w Azure Portal

Szyfrowanie na hoście można włączyć podczas tworzenia klastra w Azure Portal.

> [!Note]
> Gdy szyfrowanie na hoście jest włączone, nie można dodawać aplikacji do klastra usługi HDInsight z portalu Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Włącz szyfrowanie na hoście.":::

Ta opcja włącza [szyfrowanie na hoście](../virtual-machines/disks-enable-host-based-encryption-portal.md) dla maszyn wirtualnych usługi HDInsight tymczasowe dyski danych przy użyciu klucza głównego parowania. Szyfrowanie na hoście jest [obsługiwane tylko dla niektórych jednostek SKU maszyn wirtualnych w ograniczonej regionach](../virtual-machines/disks-enable-host-based-encryption-portal.md) , a Usługa HDInsight obsługuje [następujące konfiguracje węzłów i jednostki SKU](./hdinsight-supported-node-configuration.md).

Aby zrozumieć właściwy rozmiar maszyny wirtualnej dla klastra usługi HDInsight, zobacz [Wybieranie odpowiedniego rozmiaru maszyny wirtualnej dla klastra Azure HDInsight](hdinsight-selecting-vm-size.md). Domyślnym węzłem SKU maszyny wirtualnej dla węzła dozorcy, gdy włączone jest szyfrowanie na hoście, będzie DS2V2.

### <a name="enable-using-powershell"></a>Włączanie przy użyciu programu PowerShell

Poniższy fragment kodu przedstawia, jak utworzyć nowy klaster usługi Azure HDInsight z włączonym szyfrowaniem na hoście przy użyciu programu PowerShell. Używa parametru, `-EncryptionAtHost $true` Aby włączyć tę funkcję.

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

Poniższy fragment kodu pokazuje, jak utworzyć nowy klaster usługi Azure HDInsight z włączonym szyfrowaniem na hoście przy użyciu interfejsu wiersza polecenia platformy Azure. Używa parametru, `--encryption-at-host true` Aby włączyć tę funkcję.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault](../key-vault/general/overview.md).
* [Omówienie zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

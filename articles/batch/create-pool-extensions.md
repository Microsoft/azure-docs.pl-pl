---
title: Korzystanie z rozszerzeń przy użyciu pul wsadowych
description: Rozszerzenia to małe aplikacje, które ułatwiają konfigurowanie i konfigurację inicjowania obsługi administracyjnej w węzłach obliczeniowych wsadowych.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417446"
---
# <a name="use-extensions-with-batch-pools"></a>Korzystanie z rozszerzeń przy użyciu pul wsadowych

Rozszerzenia to małe aplikacje, które ułatwiają konfigurowanie i konfigurację inicjowania obsługi administracyjnej w węzłach obliczeniowych wsadowych. Można wybrać dowolne rozszerzenia, które są dozwolone przez Azure Batch i zainstalować je w węzłach obliczeniowych w miarę ich udostępniania. Po tym rozszerzeniu można wykonać zamierzoną operację.

Możesz sprawdzić stan na żywo używanych rozszerzeń i pobrać informacje, które zwracają w celu zapewnienia możliwości wykrywania, korekcji lub diagnostyki.

## <a name="prerequisites"></a>Wymagania wstępne

- Pule z rozszerzeniami muszą używać [konfiguracji maszyny wirtualnej](nodes-and-pools.md#virtual-machine-configuration).
- Typ rozszerzenia CustomScript jest zarezerwowany dla usługi Azure Batch i nie można go zastąpić.

### <a name="supported-extensions"></a>Obsługiwane rozszerzenia

Podczas tworzenia puli wsadowej można obecnie zainstalować następujące rozszerzenia. 

- Azure Key Vault rozszerzenie dla systemów [Linux](../virtual-machines/extensions/key-vault-linux.md) i [Windows](../virtual-machines/extensions/key-vault-windows.md)
- Rozszerzenie log Analytics i monitorowanie dla systemów [Linux](../virtual-machines/extensions/oms-linux.md) i [Windows](../virtual-machines/extensions/oms-windows.md)
- Pakiet zabezpieczeń platformy Azure

Możesz poprosić o pomoc techniczną dla dodatkowych wydawców i/lub typów rozszerzeń, otwierając żądanie pomocy technicznej.

## <a name="create-a-pool-with-extensions"></a>Tworzenie puli z rozszerzeniami

Poniższy przykład tworzy pulę wsadową węzłów systemu Linux, która używa rozszerzenia Azure Key Vault.

Identyfikator URI interfejsu API REST

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Treść żądania

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Pobieranie danych rozszerzenia z puli

Poniższy przykład pobiera dane z rozszerzenia Azure Key Vault.

Identyfikator URI interfejsu API REST

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Treść odpowiedzi

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat różnych sposobów [kopiowania aplikacji i danych do węzłów puli](batch-applications-to-pool-nodes.md).
- Dowiedz się więcej o pracy z [węzłami i pulami](nodes-and-pools.md).

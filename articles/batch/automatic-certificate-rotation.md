---
title: Włączanie automatycznego obrotu certyfikatów w puli partii
description: Można utworzyć pulę wsadową z zarządzaną tożsamością i certyfikatem, który zostanie automatycznie odnowiony.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962573"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Włączanie automatycznego obrotu certyfikatów w puli partii

 Można utworzyć pulę wsadową z certyfikatem, który zostanie automatycznie odnowiony. W tym celu należy utworzyć pulę przy użyciu [tożsamości zarządzanej przypisanej przez użytkownika](managed-identity-pools.md) , która będzie miała dostęp do certyfikatu w [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> Obsługa pul Azure Batch z tożsamościami zarządzanymi przez użytkownika jest obecnie w publicznej wersji zapoznawczej dla następujących regionów: zachodnie stany USA 2, Południowo-środkowe stany USA, wschód US, US Gov Arizona i US Gov Wirginia.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Tworzenie tożsamości przypisanej przez użytkownika

Najpierw [Utwórz tożsamość zarządzaną przypisaną przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) w tej samej dzierżawie, w której znajduje się konto w usłudze Batch. Ta tożsamość zarządzana nie musi znajdować się w tej samej grupie zasobów lub nawet w tej samej subskrypcji.

Zwróć uwagę na **Identyfikator klienta** tożsamości zarządzanej przypisanej przez użytkownika. Ta wartość będzie potrzebna później.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Zrzut ekranu przedstawiający identyfikator klienta zarządzanej tożsamości przypisanej przez użytkownika w Azure Portal.":::

## <a name="create-your-certificate"></a>Utwórz certyfikat

Następnie musisz utworzyć certyfikat i dodać go do Azure Key Vault. Jeśli magazyn kluczy nie został jeszcze utworzony, należy najpierw wykonać tę czynność. Aby uzyskać instrukcje, zobacz [Szybki Start: Ustawianie i pobieranie certyfikatu z Azure Key Vault przy użyciu Azure Portal](../key-vault/certificates/quick-create-portal.md).

Podczas tworzenia certyfikatu Pamiętaj, aby ustawić **Typ akcji okres istnienia** na automatyczne odnawianie, a następnie określ liczbę dni, po których certyfikat ma zostać odnowiony.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Zrzut ekranu przedstawiający ekran tworzenia certyfikatu w Azure Portal.":::

Po utworzeniu certyfikatu Zanotuj jego **Identyfikator tajny**. Ta wartość będzie potrzebna później.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Zrzut ekranu przedstawiający identyfikator tajny certyfikatu.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Dodawanie zasad dostępu w Azure Key Vault

W magazynie kluczy Przypisz zasady dostępu Key Vault, które umożliwiają tożsamości zarządzanej przez użytkownika dostęp do wpisów tajnych i certyfikatów. Aby uzyskać szczegółowe instrukcje, zobacz [przypisywanie zasad dostępu Key Vault przy użyciu Azure Portal](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Tworzenie puli wsadowej przy użyciu tożsamości zarządzanej przypisanej przez użytkownika

Utwórz pulę usługi Batch z zarządzaną tożsamością przy użyciu [biblioteki zarządzania programu .NET usługi Batch](/dotnet/api/overview/azure/batch#management-library). Aby uzyskać więcej informacji, zobacz [Konfigurowanie tożsamości zarządzanych w pulach usługi Batch](managed-identity-pools.md).

W poniższym przykładzie za pomocą interfejsu API REST zarządzania usługą Batch można utworzyć pulę. Pamiętaj, aby użyć **identyfikatora tajnego** certyfikatu dla `observedCertificates` i **identyfikatora klienta** zarządzanej tożsamości dla programu `msiClientId` , zastępując przykładowe dane poniżej.

Identyfikator URI interfejsu API REST

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Treść żądania

```json
{
    "name": "test2",
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
                        "name": "KVExtensions",
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
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Weryfikowanie certyfikatu

Aby upewnić się, że certyfikat został pomyślnie wdrożony, zaloguj się do węzła obliczeniowego. Wyświetlone dane wyjściowe powinny przypominać następujące dane:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Dowiedz się, jak używać [kluczy zarządzanych przez klienta z tożsamościami zarządzanymi przez użytkownika](batch-customer-managed-key.md).

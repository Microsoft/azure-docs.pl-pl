---
title: Konfigurowanie tożsamości zarządzanych w pulach wsadowym
description: Dowiedz się, jak włączyć zarządzane tożsamości przypisane przez użytkownika w pulach partii i jak używać zarządzanych tożsamości w węzłach.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: 7fab213ac1545c0bff9b74bc46504717b6038e8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950165"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Konfigurowanie tożsamości zarządzanych w pulach wsadowym

[Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) eliminują konieczność zarządzania poświadczeniami przez deweloperów, zapewniając tożsamość dla zasobu platformy Azure w usłudze Azure Active Directory (Azure AD) i korzystając z niej w celu uzyskania tokenów Azure Active Directory (Azure AD).

W tym temacie wyjaśniono, jak włączyć zarządzane tożsamości przypisane przez użytkownika w pulach wsadowym oraz jak używać tożsamości zarządzanych w węzłach.

> [!IMPORTANT]
> Obsługa pul Azure Batch z tożsamościami zarządzanymi przez użytkownika jest obecnie w publicznej wersji zapoznawczej dla następujących regionów: zachodnie stany USA 2, Południowo-środkowe stany USA, wschód US, US Gov Arizona i US Gov Wirginia.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Tworzenie tożsamości przypisanej przez użytkownika

Najpierw [Utwórz tożsamość zarządzaną przypisaną przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) w tej samej dzierżawie, w której znajduje się konto w usłudze Batch. Ta tożsamość zarządzana nie musi znajdować się w tej samej grupie zasobów lub nawet w tej samej subskrypcji.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Tworzenie puli wsadowej z tożsamościami zarządzanymi przez użytkownika

Po utworzeniu co najmniej jednej tożsamości zarządzanej przypisanej przez użytkownika można utworzyć pulę usługi Batch z tą tożsamością zarządzaną za pomocą [biblioteki zarządzania programu .NET usługi Batch](/dotnet/api/overview/azure/batch#management-library).

> [!IMPORTANT]
> Pule muszą być skonfigurowane przy użyciu [konfiguracji maszyny wirtualnej](nodes-and-pools.md#virtual-machine-configuration) , aby można było korzystać z zarządzanych tożsamości.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> Tworzenie pul z tożsamościami zarządzanymi nie jest obecnie obsługiwane w [bibliotece klienckiej programu .NET Batch](/dotnet/api/overview/azure/batch#client-library).

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Używanie tożsamości zarządzanych przypisanych przez użytkownika w węzłach wsadowych

Po utworzeniu pul zarządzane tożsamości przypisane przez użytkownika mogą uzyskiwać dostęp do węzłów puli za pośrednictwem Secure Shell (SSH) lub Pulpit zdalny (RDP). Możesz również skonfigurować zadania tak, aby tożsamości zarządzane mogły uzyskiwać bezpośredni dostęp do [zasobów platformy Azure, które obsługują zarządzane tożsamości](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

W węzłach usługi Batch można uzyskać tokeny zarządzanej tożsamości i używać ich do uwierzytelniania za pośrednictwem uwierzytelniania usługi Azure AD za pośrednictwem [instance Metadata Service platformy Azure](../virtual-machines/windows/instance-metadata-service.md).

W przypadku systemu Windows skrypt programu PowerShell umożliwiający uzyskanie tokenu dostępu do uwierzytelniania:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

W przypadku systemu Linux skrypt bash jest:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Aby uzyskać więcej informacji, zobacz [jak używać zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure w celu uzyskania tokenu dostępu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Dowiedz się, jak używać [kluczy zarządzanych przez klienta z tożsamościami zarządzanymi przez użytkownika](batch-customer-managed-key.md).
- Dowiedz się, jak [włączyć automatyczne obracanie certyfikatów w puli wsadowej](automatic-certificate-rotation.md).

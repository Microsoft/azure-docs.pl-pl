---
title: Konfigurowanie prywatnego punktu końcowego (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Użyj prywatnego linku platformy Azure, aby bezpiecznie uzyskać dostęp do obszaru roboczego Azure Machine Learning z sieci wirtualnej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: bdb7ba30d9fa2d0bd1eff9368d6e30e516b53895
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192725"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurowanie prywatnego linku platformy Azure dla obszaru roboczego Azure Machine Learning (wersja zapoznawcza)

W tym dokumencie dowiesz się, jak korzystać z prywatnego linku platformy Azure z obszarem roboczym Azure Machine Learning. 

> [!IMPORTANT]
> Korzystanie z prywatnego linku platformy Azure z obszarem roboczym Azure Machine Learning jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna tylko w regionach **Wschodnie stany USA** i **zachodnie stany USA 2** . Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Link prywatny platformy Azure umożliwia nawiązanie połączenia z obszarem roboczym przy użyciu prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej. Następnie można ograniczyć dostęp do obszaru roboczego tylko w przypadku prywatnych adresów IP. Link prywatny pomaga ograniczyć ryzyko związane z eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [prywatny link do platformy Azure](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Łącze prywatne platformy Azure nie wpływa na płaszczyznę kontroli platformy Azure (operacje zarządzania), takie jak usuwanie obszaru roboczego lub zarządzanie zasobami obliczeniowymi. Na przykład tworzenie, aktualizowanie lub usuwanie elementu docelowego obliczeń. Te operacje są wykonywane za pośrednictwem publicznej sieci Internet jako normalne.
>
> Azure Machine Learning wystąpienia obliczeniowe w wersji zapoznawczej nie są obsługiwane w obszarze roboczym, w którym włączono link prywatny.
>
> W przypadku korzystania z przeglądarki Mozilla Firefox mogą wystąpić problemy podczas próby uzyskania dostępu do prywatnego punktu końcowego dla obszaru roboczego. Ten problem może dotyczyć systemu DNS za pośrednictwem protokołu HTTPS w Mozilla. Zalecamy korzystanie z przeglądarki Google Chrome firmy Microsoft jako obejście problemu.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Tworzenie obszaru roboczego korzystającego z prywatnego punktu końcowego

> [!IMPORTANT]
> Obecnie obsługujemy tylko Włączanie prywatnego punktu końcowego podczas tworzenia nowego obszaru roboczego Azure Machine Learning.

Szablon w programie [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) może służyć do tworzenia obszaru roboczego z prywatnym punktem końcowym.

Aby uzyskać informacje na temat korzystania z tego szablonu, w tym prywatnych punktów końcowych, zobacz [Tworzenie obszaru roboczego dla Azure Machine Learning za pomocą szablonu Azure Resource Manager](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Używanie obszaru roboczego w prywatnym punkcie końcowym

Ponieważ komunikacja z obszarem roboczym jest dozwolona tylko w sieci wirtualnej, wszystkie środowiska deweloperskie korzystające z obszaru roboczego muszą należeć do sieci wirtualnej. Na przykład maszyna wirtualna w sieci wirtualnej.

> [!IMPORTANT]
> Aby uniknąć tymczasowego zakłócenia łączności, firma Microsoft zaleca opróżnianie pamięci podręcznej DNS na komputerach łączących się z obszarem roboczym po włączeniu linku prywatnego. 

Aby uzyskać informacje na temat usługi Azure Virtual Machines, zobacz [dokumentację Virtual Machines](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>Korzystanie z usługi Azure Storage

Aby zabezpieczyć konto usługi Azure Storage używane przez obszar roboczy, umieść je w sieci wirtualnej.

Aby uzyskać informacje dotyczące umieszczania konta magazynu w sieci wirtualnej, zobacz [Korzystanie z konta magazynu dla obszaru roboczego](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> Azure Machine Learning nie obsługuje korzystania z konta usługi Azure Storage z włączonym prywatnym linkiem.

## <a name="using-azure-key-vault"></a>Używanie Azure Key Vault

Aby zabezpieczyć Azure Key Vault używany przez obszar roboczy, możesz go umieścić w sieci wirtualnej lub włączyć dla niego link prywatny.

Aby uzyskać informacje dotyczące umieszczania magazynu kluczy w sieci wirtualnej, zobacz [Korzystanie z wystąpienia magazynu kluczy z obszarem roboczym](how-to-enable-virtual-network.md#key-vault-instance).

Aby uzyskać informacje na temat włączania prywatnego linku do magazynu kluczy, zobacz [integrowanie Key Vault z prywatnym łączem platformy Azure](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Korzystanie z usług Azure Kubernetes Services

Aby zabezpieczyć usługi Azure Kubernetes używane przez obszar roboczy, umieść je w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Korzystanie z usług Azure Kubernetes Services z Twoim obszarem roboczym](how-to-enable-virtual-network.md#aksvnet).

Azure Machine Learning teraz obsługuje korzystanie z usługi Azure Kubernetes, która ma włączone łącze prywatne.
Aby utworzyć prywatny klaster AKS, Skorzystaj z witryny docs [tutaj](https://docs.microsoft.com/azure/aks/private-clusters)

## <a name="azure-container-registry"></a>Azure Container Registry

Aby uzyskać informacje na temat zabezpieczania Azure Container Registry wewnątrz sieci wirtualnej, zobacz [Korzystanie z Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Jeśli używasz prywatnego linku do obszaru roboczego Azure Machine Learning i umieścisz Azure Container Registry dla obszaru roboczego w sieci wirtualnej, musisz również zastosować następujący szablon Azure Resource Manager. Ten szablon umożliwia obszarowi roboczemu komunikowanie się z ACR za pośrednictwem prywatnego linku.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zabezpieczania obszaru roboczego Azure Machine Learning, zobacz artykuł [zabezpieczenia przedsiębiorstwa](concept-enterprise-security.md) .
---
title: Skonfiguruj prywatny punkt końcowy
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
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828120"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Konfigurowanie prywatnego linku platformy Azure dla obszaru roboczego Azure Machine Learning

W tym dokumencie dowiesz się, jak korzystać z prywatnego linku platformy Azure z obszarem roboczym Azure Machine Learning. Aby uzyskać informacje na temat tworzenia sieci wirtualnej dla Azure Machine Learning, zobacz [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md)

Link prywatny platformy Azure umożliwia nawiązanie połączenia z obszarem roboczym przy użyciu prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej. Następnie można ograniczyć dostęp do obszaru roboczego tylko w przypadku prywatnych adresów IP. Link prywatny pomaga ograniczyć ryzyko związane z eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [prywatny link do platformy Azure](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Łącze prywatne platformy Azure nie wpływa na płaszczyznę kontroli platformy Azure (operacje zarządzania), takie jak usuwanie obszaru roboczego lub zarządzanie zasobami obliczeniowymi. Na przykład tworzenie, aktualizowanie lub usuwanie elementu docelowego obliczeń. Te operacje są wykonywane za pośrednictwem publicznej sieci Internet jako normalne. Operacje płaszczyzny danych, takie jak używanie Azure Machine Learning Studio, interfejsów API (w tym opublikowanych potoków) lub zestawu SDK korzystają z prywatnego punktu końcowego.
>
> W przypadku korzystania z przeglądarki Mozilla Firefox mogą wystąpić problemy podczas próby uzyskania dostępu do prywatnego punktu końcowego dla obszaru roboczego. Ten problem może dotyczyć systemu DNS za pośrednictwem protokołu HTTPS w Mozilla. Zalecamy korzystanie z przeglądarki Google Chrome firmy Microsoft jako obejście problemu.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli planujesz używanie obszaru roboczego z włączoną obsługą linku prywatnego z kluczem zarządzanym przez klienta, musisz zażądać tej funkcji przy użyciu biletu pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Zarządzanie i zwiększanie limitów przydziału](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Ograniczenia

Korzystanie z obszaru roboczego Azure Machine Learning z linkiem prywatnym nie jest dostępne w regionach Azure Government ani w regionach 21Vianet platformy Azure w Chinach.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Tworzenie obszaru roboczego korzystającego z prywatnego punktu końcowego

Użyj jednej z następujących metod, aby utworzyć obszar roboczy z prywatnym punktem końcowym:

> [!TIP]
> Szablon Azure Resource Manager może utworzyć nową sieć wirtualną w razie konieczności. Pozostałe metody wymagają istniejącej sieci wirtualnej.

# <a name="resource-manager-template"></a>[Szablon Menedżer zasobów](#tab/azure-resource-manager)

Szablon Azure Resource Manager w programie [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) zapewnia łatwy sposób tworzenia obszaru roboczego za pomocą prywatnego punktu końcowego i sieci wirtualnej.

Aby uzyskać informacje na temat korzystania z tego szablonu, w tym prywatnych punktów końcowych, zobacz [Tworzenie obszaru roboczego dla Azure Machine Learning za pomocą szablonu Azure Resource Manager](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Zestaw Azure Machine Learning Python SDK udostępnia klasę [PrivateEndpointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , która może być używana z elementem [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) w celu utworzenia obszaru roboczego z prywatnym punktem końcowym. Ta klasa wymaga istniejącej sieci wirtualnej.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md) udostępnia polecenie [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) . Następujące parametry dla tego polecenia mogą służyć do tworzenia obszaru roboczego z siecią prywatną, ale wymaga istniejącej sieci wirtualnej:

* `--pe-name`: Nazwa tworzonego prywatnego punktu końcowego.
* `--pe-auto-approval`: Czy połączenia prywatnego punktu końcowego z obszarem roboczym powinny być automatycznie zatwierdzane.
* `--pe-resource-group`: Grupa zasobów, w której ma zostać utworzony prywatny punkt końcowy. Musi być tą samą grupą, która zawiera sieć wirtualną.
* `--pe-vnet-name`: Istniejąca sieć wirtualna do utworzenia prywatnego punktu końcowego w programie.
* `--pe-subnet-name`: Nazwa podsieci, w której ma zostać utworzony prywatny punkt końcowy. Wartość domyślna to `default`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Karta __Sieć__ w programie Azure Machine Learning Studio umożliwia skonfigurowanie prywatnego punktu końcowego. Jednak wymaga istniejącej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie obszarów roboczych w portalu](how-to-manage-workspace.md).

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Używanie obszaru roboczego w prywatnym punkcie końcowym

Ponieważ komunikacja z obszarem roboczym jest dozwolona tylko w sieci wirtualnej, wszystkie środowiska deweloperskie korzystające z obszaru roboczego muszą należeć do sieci wirtualnej. Na przykład maszyna wirtualna w sieci wirtualnej.

> [!IMPORTANT]
> Aby uniknąć tymczasowego zakłócenia łączności, firma Microsoft zaleca opróżnianie pamięci podręcznej DNS na komputerach łączących się z obszarem roboczym po włączeniu linku prywatnego. 

Aby uzyskać informacje na temat usługi Azure Virtual Machines, zobacz [dokumentację Virtual Machines](/azure/virtual-machines/).


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat zabezpieczania obszaru roboczego Azure Machine Learning, zobacz artykuł [Omówienie izolacji i prywatności w sieci wirtualnej](how-to-network-security-overview.md) .

* Jeśli planujesz korzystać z niestandardowego rozwiązania DNS w sieci wirtualnej, zobacz [jak używać obszaru roboczego z niestandardowym serwerem DNS](how-to-custom-dns.md).

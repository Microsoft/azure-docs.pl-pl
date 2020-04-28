---
title: Używanie Azure Machine Learning za zaporą
titleSuffix: Azure Machine Learning
description: Bezpiecznie używaj Azure Machine Learning za zaporą platformy Azure. Dowiedz się więcej na temat hostów, na których należy zezwolić, aby Azure Machine Learning działać prawidłowo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196324"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>Używanie Azure Machine Learning obszaru roboczego za zaporą platformy Azure

Ten artykuł zawiera informacje dotyczące konfigurowania zapory platformy Azure do użycia z programem Azure Machine Learning.

Za pomocą zapory platformy Azure można kontrolować dostęp do obszaru roboczego Azure Machine Learning i publicznego Internetu. Jeśli nie skonfigurowano go prawidłowo, Zapora może spowodować problemy z używaniem obszaru roboczego.

## <a name="network-rules"></a>Reguły sieci

Na zaporze Utwórz regułę sieci zezwalającą na ruch do i z adresów w tym artykule.

> [!TIP]
> Podczas dodawania reguły sieci należy ustawić __Protokół__ na dowolny, a porty do `*`.
>
> Aby uzyskać więcej informacji na temat konfigurowania zapory platformy Azure, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Hosty firmy Microsoft

Hosty w tej sekcji należą do firmy Microsoft i zapewniają usługi wymagane do prawidłowego działania obszaru roboczego.

| **Nazwa hosta** | **Przeznaczenie** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Klastry szkoleniowe |
| **ml.azure.com** | Studio uczenia maszynowego Azure |
| **\*. azureml.ms** | Używane przez interfejsy API Azure Machine Learning |
| **\*. experiments.azureml.net** | Używane przez eksperymenty działające w Azure Machine Learning|
| **\*. modelmanagement.azureml.net** | Służy do rejestrowania i wdrażania modeli|
| **mlworkspace.azure.ai** | Używane przez Azure Portal podczas wyświetlania obszaru roboczego |
| **\*. aether.ms** | Używane podczas uruchamiania potoków Azure Machine Learning |
| **\*. instances.azureml.net** | Wystąpienia obliczeniowe Azure Machine Learning |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | W usłudze Azure Key Vault |
| **microsoft.com** | Podstawowe obrazy platformy Docker |
| **azurecr.io** | Azure Container Registry |

## <a name="python-hosts"></a>Hosty języka Python

Hosty w tej sekcji służą do instalowania pakietów języka Python. Są one wymagane podczas opracowywania, uczenia i wdrażania. 

| **Nazwa hosta** | **Przeznaczenie** |
| ---- | ---- |
| **anaconda.com** | Używane podczas instalowania pakietów Conda |
| **pypi.org** | Używane podczas instalowania pakietów PIP |

## <a name="r-hosts"></a>Hosty języka R

Hosty w tej sekcji służą do instalowania pakietów języka R. Są one wymagane podczas opracowywania, uczenia i wdrażania.

> [!IMPORTANT]
> Wewnętrznie zestaw R SDK dla Azure Machine Learning używa pakietów języka Python. W związku z tym należy również zezwolić hostom języka Python przez zaporę.

| **Nazwa hosta** | **Przeznaczenie** |
| ---- | ---- |
| **cloud.r-project.org** | Używane podczas instalowania pakietów CRAN. |

Następne kroki

* [[Wdrażanie i Konfigurowanie zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md)]
* [Zabezpieczanie zadań eksperymentowania i wnioskowania usługi Azure ML w ramach Virtual Network platformy Azure](how-to-enable-virtual-network.md)
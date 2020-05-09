---
title: Korzystanie z zapory
titleSuffix: Azure Machine Learning
description: Kontroluj dostęp do obszarów roboczych Azure Machine Learning przy użyciu zapór platformy Azure. Dowiedz się więcej na temat hostów, na których należy zezwolić, aby Azure Machine Learning działać prawidłowo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 40c25dda3fefa9c54df832e16149a68a4aa5a33b
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981969"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Używanie obszaru roboczego za zaporą platformy Azure w celu Azure Machine Learning

W tym artykule dowiesz się, jak skonfigurować zaporę platformy Azure do użycia z obszarem roboczym Azure Machine Learning.

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

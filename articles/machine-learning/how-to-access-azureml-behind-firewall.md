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
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 990a2d5279c796f354055328e6968ea705ea10b2
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873640"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Używanie obszaru roboczego za zaporą dla Azure Machine Learning

W tym artykule dowiesz się, jak skonfigurować zaporę platformy Azure w celu kontrolowania dostępu do obszaru roboczego Azure Machine Learning i publicznego Internetu.   Aby dowiedzieć się więcej na temat zabezpieczania Azure Machine Learning, zobacz [zabezpieczenia przedsiębiorstwa dla Azure Machine Learning](concept-enterprise-security.md)

Informacje zawarte w tym dokumencie opierają się na korzystaniu z [zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md), dlatego można z nich korzystać z innych produktów zapory. Jeśli masz pytania dotyczące sposobu zezwalania na komunikację za pośrednictwem zapory, zapoznaj się z dokumentacją używanej zapory.

## <a name="network-rules"></a>Reguły sieci

Na zaporze Utwórz regułę sieci zezwalającą na ruch do i z adresów w tym artykule.

> [!TIP]
> Podczas dodawania reguły sieci należy ustawić __Protokół__ na dowolny, a porty do `*` .
>
> Aby uzyskać więcej informacji na temat konfigurowania zapory platformy Azure, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="microsoft-hosts"></a>Hosty firmy Microsoft

Jeśli nie skonfigurowano go prawidłowo, Zapora może spowodować problemy z używaniem obszaru roboczego. Istnieją różne nazwy hostów, które są używane w obszarze roboczym Azure Machine Learning.

Hosty w tej sekcji należą do firmy Microsoft i zapewniają usługi wymagane do prawidłowego działania obszaru roboczego.

| **Nazwa hosta** | **Przeznaczenie** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Klastry szkoleniowe |
| **ml.azure.com** | Studio uczenia maszynowego Azure |
| **default.exp-tas.com** | Używane przez Azure Machine Learning Studio |
| **\*. azureml.ms** | Używane przez interfejsy API Azure Machine Learning |
| **\*. experiments.azureml.net** | Używane przez eksperymenty działające w Azure Machine Learning |
| **\*. modelmanagement.azureml.net** | Służy do rejestrowania i wdrażania modeli|
| **mlworkspace.azure.ai** | Używane przez Azure Portal podczas wyświetlania obszaru roboczego |
| **\*. aether.ms** | Używane podczas uruchamiania potoków Azure Machine Learning |
| **\*. instances.azureml.net** | Wystąpienia obliczeniowe Azure Machine Learning |
| **\*. instances.azureml.ms** | Wystąpienia obliczeniowe Azure Machine Learning, gdy w obszarze roboczym jest włączone łącze prywatne |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | W usłudze Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | Microsoft Container Registry dla podstawowych obrazów platformy Docker |
| **your-acr-server-name.azurecr.io** | Wymagany tylko wtedy, gdy Azure Container Registry znajduje się za siecią wirtualną. W tej konfiguracji link prywatny jest tworzony ze środowiska firmy Microsoft do wystąpienia usługi ACR w ramach subskrypcji. Użyj nazwy serwera ACR dla obszaru roboczego Azure Machine Learning. |
| **\*. notebooks.azure.net** | Wymagany przez notesy w programie Azure Machine Learning Studio. |

## <a name="python-hosts"></a>Hosty języka Python

Hosty w tej sekcji służą do instalowania pakietów języka Python. Są one wymagane podczas opracowywania, uczenia i wdrażania. 

| **Nazwa hosta** | **Przeznaczenie** |
| ---- | ---- |
| **anaconda.com** | Służy do instalowania pakietów domyślnych. |
| **\*. anaconda.org** | Służy do pobierania danych repozytorium. |
| **pypi.org** | Służy do wyświetlania listy zależności od domyślnego indeksu (jeśli istnieje), a indeks nie jest zastępowany przez ustawienia użytkownika. Jeśli indeks jest zastępowany, należy również zezwolić na ** \* . pythonhosted.org**. |

## <a name="r-hosts"></a>Hosty języka R

Hosty w tej sekcji służą do instalowania pakietów języka R. Są one wymagane podczas opracowywania, uczenia i wdrażania.

> [!IMPORTANT]
> Wewnętrznie zestaw R SDK dla Azure Machine Learning używa pakietów języka Python. W związku z tym należy również zezwolić hostom języka Python przez zaporę.

| **Nazwa hosta** | **Przeznaczenie** |
| ---- | ---- |
| **cloud.r-project.org** | Używane podczas instalowania pakietów CRAN. |

## <a name="next-steps"></a>Następne kroki

* [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Zabezpieczanie zadań eksperymentowania i wnioskowania usługi Azure ML w ramach Virtual Network platformy Azure](how-to-enable-virtual-network.md)

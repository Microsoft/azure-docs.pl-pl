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
ms.openlocfilehash: d0f30edeb24f3c4abed6f144f3fb7f755cc08a72
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629463"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Używanie obszaru roboczego za zaporą dla Azure Machine Learning

W tym artykule dowiesz się, jak skonfigurować zaporę platformy Azure w celu kontrolowania dostępu do obszaru roboczego Azure Machine Learning i publicznego Internetu.   Aby dowiedzieć się więcej na temat zabezpieczania Azure Machine Learning, zobacz [zabezpieczenia przedsiębiorstwa dla Azure Machine Learning](concept-enterprise-security.md)

Informacje zawarte w tym dokumencie opierają się na korzystaniu z [zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md), dlatego można z nich korzystać z innych produktów zapory. Jeśli masz pytania dotyczące sposobu zezwalania na komunikację za pośrednictwem zapory, zapoznaj się z dokumentacją używanej zapory.

## <a name="application-rules"></a>Reguły aplikacji

Na zaporze Utwórz _regułę aplikacji_ zezwalającą na ruch do i z adresów w tym artykule.

> [!TIP]
> Podczas dodawania reguły sieci należy ustawić __Protokół__ na dowolny, a porty do `*` .
>
> Aby uzyskać więcej informacji na temat konfigurowania zapory platformy Azure, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="routes"></a>Trasy

Podczas konfigurowania trasy wychodzącej dla podsieci zawierającej zasoby Azure Machine Learning należy skorzystać ze wskazówek w sekcji [Wymuszone tunelowanie](how-to-secure-training-vnet.md#forced-tunneling) , aby zabezpieczyć środowisko szkoleniowe.

## <a name="microsoft-hosts"></a>Hosty firmy Microsoft

Jeśli nie skonfigurowano go prawidłowo, Zapora może spowodować problemy z używaniem obszaru roboczego. Istnieją różne nazwy hostów, które są używane w obszarze roboczym Azure Machine Learning.

Hosty w tej sekcji należą do firmy Microsoft i zapewniają usługi wymagane do prawidłowego działania obszaru roboczego.

| **Nazwa hosta** | **Cel** |
| ---- | ---- |
| **login.microsoftonline.com** | Authentication |
| **management.azure.com** | Służy do uzyskiwania informacji o obszarze roboczym |
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
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | Microsoft Container Registry dla podstawowych obrazów platformy Docker |
| **your-acr-server-name.azurecr.io** | Wymagany tylko wtedy, gdy Azure Container Registry znajduje się za siecią wirtualną. W tej konfiguracji link prywatny jest tworzony ze środowiska firmy Microsoft do wystąpienia usługi ACR w ramach subskrypcji. Użyj nazwy serwera ACR dla obszaru roboczego Azure Machine Learning. |
| **\*. notebooks.azure.net** | Wymagany przez notesy w programie Azure Machine Learning Studio. |
| **\*. file.core.windows.net** | Wymagany przez Eksploratora plików w programie Azure Machine Learning Studio. |
| **\*. dfs.core.windows.net** | Wymagany przez Eksploratora plików w programie Azure Machine Learning Studio. |
| **graph.windows.net** | Potrzeba dla notesów |

> [!TIP]
> Jeśli planujesz korzystanie z tożsamości federacyjnych, postępuj zgodnie z [najlepszymi rozwiązaniami dotyczącymi zabezpieczania Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) artykułu.

## <a name="python-hosts"></a>Hosty języka Python

Hosty w tej sekcji służą do instalowania pakietów języka Python. Są one wymagane podczas opracowywania, uczenia i wdrażania. 

| **Nazwa hosta** | **Cel** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Służy do instalowania pakietów domyślnych. |
| **\*. anaconda.org** | Służy do pobierania danych repozytorium. |
| **pypi.org** | Służy do wyświetlania listy zależności od domyślnego indeksu (jeśli istnieje), a indeks nie jest zastępowany przez ustawienia użytkownika. Jeśli indeks jest zastępowany, należy również zezwolić na **\* . pythonhosted.org** . |

## <a name="r-hosts"></a>Hosty języka R

Hosty w tej sekcji służą do instalowania pakietów języka R. Są one wymagane podczas opracowywania, uczenia i wdrażania.

> [!IMPORTANT]
> Wewnętrznie zestaw R SDK dla Azure Machine Learning używa pakietów języka Python. W związku z tym należy również zezwolić hostom języka Python przez zaporę.

| **Nazwa hosta** | **Cel** |
| ---- | ---- |
| **cloud.r-project.org** | Używane podczas instalowania pakietów CRAN. |

## <a name="azure-government-region"></a>Region Azure Government

Adresy URL dla regionów Azure Government.

| **Nazwa hosta** | **Cel** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | Region US-Arizona |
| **usgovvirginia.api.ml.azure.us** | Region US-Virginia |

## <a name="next-steps"></a>Następne kroki

* [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Zabezpieczanie eksperymentów usługi Azure Machine Learning i zadań wnioskowania w ramach sieci wirtualnej platformy Azure](how-to-network-security-overview.md)

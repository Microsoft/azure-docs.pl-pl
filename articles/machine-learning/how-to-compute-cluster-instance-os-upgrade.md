---
title: Uaktualnianie systemu operacyjnego hosta dla klastra obliczeniowego i wystąpienia
titleSuffix: Azure Machine Learning
description: Uaktualnij system operacyjny hosta dla klastra obliczeniowego i wystąpienia obliczeniowego z ubuntu 16.04 LTS do wersji 18.04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7445de8349d025679b1560e065ed15d9eec3b08f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872015"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Uaktualnianie systemu operacyjnego hosta wystąpienia obliczeniowego i klastra obliczeniowego

Azure Machine Learning __obliczeniowych i__ wystąpienie __obliczeniowe są__ zarządzaną infrastrukturą obliczeniową. Jako usługa zarządzana firma Microsoft zarządza hostem systemu operacyjnego oraz zainstalowanymi pakietami i wersjami oprogramowania.

System operacyjny hosta dla klastra obliczeniowego i wystąpienia obliczeniowego to Ubuntu 16.04 LTS. **30 kwietnia 2021** r. w systemie Ubuntu kończy się obsługa wersji 16.04. Począwszy od __15 marca 2021__ r., firma Microsoft automatycznie zaktualizuje system operacyjny hosta do ubuntu 18.04 LTS. Aktualizacja do wersji 18.04 zapewni dalsze aktualizacje zabezpieczeń i wsparcie ze strony społeczności systemu Ubuntu. Ta aktualizacja zostanie wdedywowana w regionach platformy Azure i będzie dostępna we wszystkich regionach do 9 kwietnia __2021 r.__ Aby uzyskać więcej informacji na temat kończenia obsługi wersji 16.04 w systemie [Ubuntu,](https://wiki.ubuntu.com/Releases)zobacz blog poświęcony wersji Ubuntu .

> [!TIP]
> * System operacyjny hosta nie jest wersją systemu operacyjnego, który można określić dla środowiska [podczas](how-to-use-environments.md) trenowania lub wdrażania modelu. Środowiska działają wewnątrz platformy Docker. Na hoście działa system operacyjny platformy Docker.
> * Jeśli obecnie używasz środowisk opartych na systemie Ubuntu 16.04 do trenowania lub wdrażania, firma Microsoft zaleca przejście na obrazy oparte na systemie Ubuntu 18.04. Aby uzyskać więcej informacji, [zobacz Jak używać środowisk](how-to-use-environments.md) i [repozytorium Azure Machine Learning kontenerów.](https://github.com/Azure/AzureML-Containers/tree/master/base)
> * W przypadku korzystania Azure Machine Learning obliczeniowego opartego na systemie Ubuntu 18.04 domyślną wersją języka Python jest _Python 3.8._
## <a name="creating-new-resources"></a>Tworzenie nowych zasobów

Klastry obliczeniowe lub wystąpienia obliczeniowe utworzone po __9 kwietnia 2021__ r. domyślnie używają systemu Ubuntu 18.04 LTS jako systemu operacyjnego hosta. Nie można wybrać innego systemu operacyjnego hosta.

## <a name="upgrade-existing-resources"></a>Uaktualnianie istniejących zasobów

Jeśli masz istniejące klastry obliczeniowe lub wystąpienia obliczeniowe utworzone przed __15 marca 2021__ r., musisz podjąć działania w celu uaktualnienia systemu operacyjnego hosta do systemu Ubuntu 18.04. W zależności od regionu, z Azure Machine Learning z których uzyskujesz dostęp, zalecamy podjęcia tych akcji po 9 kwietnia __2021 r.,__ aby upewnić się, że nasze zmiany zostały wprowadzone we wszystkich regionach:

* __Azure Machine Learning obliczeniowy:__

    * Jeśli klaster jest skonfigurowany z węzłami o minimalnej wartości __= 0,__ zostanie on automatycznie uaktualniony po ukończeniu wszystkich zadań i zmniejszy liczbę węzłów do zera.
    * Jeśli __minimalna liczba węzłów > 0,__ tymczasowo zmień minimalną liczbę węzłów na zero i zezwlij klastrowi na zmniejszenie liczby węzłów do zera.

    Aby uzyskać więcej informacji na temat zmiany minimalnej liczby węzłów, zobacz [polecenie az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ml/computetarget/update#az_ml_computetarget_update_amlcompute) interfejsu wiersza polecenia platformy Azure lub informacje o zestawie SDK [AmlCompute.update().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-)

* __Azure Machine Learning wystąpienia obliczeniowego:__ utwórz nowe wystąpienie obliczeniowe (które będzie używać systemu Ubuntu 18.04) i usuń stare wystąpienie.

    * Każdy notes przechowywany w udziałach plików obszaru roboczego, magazynach danych i zestawach danych będzie dostępny z nowego wystąpienia obliczeniowego.
    * Jeśli utworzono niestandardowe środowiska conda, możesz wyeksportować te środowiska z istniejącego wystąpienia i zaimportować je do nowego wystąpienia. Aby uzyskać informacje na temat eksportowania i importowania programu [Conda, zobacz dokumentację conda](https://docs.conda.io/) w docs.conda.io.

    Aby uzyskać więcej informacji, zobacz artykuły [Co](concept-compute-instance.md) to jest wystąpienie obliczeniowe i Tworzenie wystąpienia obliczeniowego i zarządzanie Azure Machine Learning [wystąpienia obliczeniowego](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Sprawdzanie wersji systemu operacyjnego hosta

Aby uzyskać informacje na temat sprawdzania wersji systemu operacyjnego hosta, zobacz stronę wiki społeczności Ubuntu na temat [sprawdzania wersji systemu Ubuntu.](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)

> [!TIP]
> Aby użyć polecenia `lsb_release -a` z witryny typu wiki, możesz użyć sesji [terminalowej w wystąpieniu obliczeniowym](how-to-access-terminal.md).
## <a name="next-steps"></a>Następne kroki

Jeśli masz dodatkowe pytania lub wątpliwości, skontaktuj się z nami pod adres [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .

---
title: Uaktualnij system operacyjny hosta dla klastra i wystąpienia obliczeniowego
titleSuffix: Azure Machine Learning
description: Uaktualnij system operacyjny hosta dla klastra obliczeniowego i wystąpienia obliczeniowego z Ubuntu 16,04 LTS do 18,04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5f853c19015a70d596c32532a9c280c785b4597b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609547"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Uaktualnij wystąpienie obliczeniowe i system operacyjny hosta klastra obliczeniowego

__Klaster obliczeniowy__ Azure Machine Learning i __wystąpienie obliczeniowe__ są zarządzaną infrastrukturą obliczeniową. Jako usługa zarządzana, firma Microsoft zarządza systemem operacyjnym hosta oraz zainstalowanymi pakietami i wersjami oprogramowania.

System operacyjny hosta dla klastra obliczeniowego i wystąpienia obliczeniowego został Ubuntu 16,04 LTS. **30 kwietnia 2021**, Ubuntu kończy pomoc techniczną dla 16,04. Od __15 marca 2021__, firma Microsoft automatycznie zaktualizuje system operacyjny hosta do Ubuntu 18,04 LTS. Aktualizacja do 18,04 zapewni dalsze aktualizacje zabezpieczeń i pomoc techniczną ze społeczności Ubuntu. Aby uzyskać więcej informacji o końcowym obsłudze Ubuntu dla 16,04, zobacz Blog dotyczący [wersji Ubuntu](https://wiki.ubuntu.com/Releases).

> [!TIP]
> * System operacyjny hosta nie jest wersją systemu operacyjnego, którą można określić dla [środowiska](how-to-use-environments.md) podczas uczenia lub wdrażania modelu. Środowiska są uruchamiane wewnątrz platformy Docker. Platforma Docker jest uruchamiana w systemie operacyjnym hosta.
> * Jeśli obecnie korzystasz ze środowisk Ubuntu 16,04 na potrzeby szkoleń lub wdrożeń, firma Microsoft zaleca, aby przełączyć się do korzystania z obrazów opartych na Ubuntu 18,04. Aby uzyskać więcej informacji, zobacz [jak używać środowisk](how-to-use-environments.md) i [repozytorium kontenerów Azure Machine Learning](https://github.com/Azure/AzureML-Containers/tree/master/base).
> * W przypadku używania wystąpienia obliczeniowego Azure Machine Learning w oparciu o Ubuntu 18,04 domyślna wersja języka Python to _python 3,8_.
## <a name="creating-new-resources"></a>Tworzenie nowych zasobów

Klaster obliczeniowy lub wystąpienia obliczeniowe utworzone po __15 marca 2021__ używają domyślnie Ubuntu 18,04 LTS jako systemu operacyjnego hosta. Nie można wybrać innego systemu operacyjnego hosta.

## <a name="upgrade-existing-resources"></a>Uaktualnianie istniejących zasobów

Jeśli masz istniejące Klastry obliczeniowe lub wystąpienia obliczeniowe utworzone przed __15 marca 2021__, musisz podjąć odpowiednie działania, aby uaktualnić system operacyjny hosta do Ubuntu 18,04:

* __Azure Machine Learning klaster obliczeniowy__:

    * Jeśli klaster jest skonfigurowany z __minimalnymi węzłami = 0__, zostanie automatycznie uaktualniony po zakończeniu wszystkich zadań i zostanie on zmniejszony do zera.
    * Jeśli __minimalna liczba węzłów > 0__, tymczasowo Zmień minimalną liczbę węzłów na zero i Zezwól, aby klaster obniżyć do zero węzłów.

    Aby uzyskać więcej informacji na temat zmiany minimalnych węzłów, zobacz [AZ ml computetarget Update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure CLI polecenie lub informacje o zestawie SDK [amlcompute. Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) .

* __Wystąpienie obliczeniowe Azure Machine Learning__: Utwórz nowe wystąpienie obliczeniowe (które będzie używać Ubuntu 18,04) i usuń stare wystąpienie.

    * Wszystkie notesy przechowywane w udziale plików obszaru roboczego, magazyny danych dla zestawów DataSet będą dostępne z nowego wystąpienia obliczeniowego.
    * Jeśli utworzono niestandardowe środowiska Conda, możesz wyeksportować te środowiska z istniejącego wystąpienia i zaimportować je do nowego wystąpienia. Aby uzyskać informacje na temat eksportu i importu Conda, zobacz [dokumentację Conda](https://docs.conda.io/) w witrynie docs.Conda.IO.

    Aby uzyskać więcej informacji, zobacz artykuł [co to jest wystąpienie obliczeniowe](concept-compute-instance.md) i [tworzenie i zarządzanie Azure Machine Learning wystąpieniami obliczeniowymi](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Sprawdź wersję systemu operacyjnego hosta

Aby uzyskać więcej informacji na temat sprawdzania wersji systemu operacyjnego hosta, zobacz stronę Ubuntu Community wiki na temat [sprawdzania wersji Ubuntu](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Aby użyć `lsb_release -a` polecenia z witryny typu wiki, można [użyć sesji terminala w wystąpieniu obliczeniowym](how-to-access-terminal.md).
## <a name="next-steps"></a>Następne kroki

Jeśli masz jakieś pytania lub wątpliwości, skontaktuj się z nami pod adresem [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
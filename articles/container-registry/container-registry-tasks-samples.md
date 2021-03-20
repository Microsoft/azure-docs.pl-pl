---
title: Przykłady zadań ACR
description: Przykładowe zadania Azure Container Registry (zadania ACR) do kompilowania, uruchamiania i poprawiania obrazów kontenerów
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456084"
---
# <a name="acr-tasks-samples"></a>Przykłady zadań ACR

Ten artykuł zawiera linki do przykładowych `task.yaml` plików i skojarzonych wieloetapowe dockerfile dla kilku scenariuszy [Azure Container Registry zadań](container-registry-tasks-overview.md) (ACR Tasks). 

Aby uzyskać więcej przykładów, zobacz repozytorium [przykładów platformy Azure][task-examples] .

## <a name="scenarios"></a>Scenariusze

* **Obraz kompilacji**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Uruchom kontener**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Kompilowanie i wypychanie obrazu**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kompilowanie i uruchamianie obrazu**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kompilowanie i wypychanie wielu obrazów**  -   [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* Równoległe Kompilowanie **i testowanie obrazów**  -   [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kompilowanie i wypychanie obrazów do wielu rejestrów**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zadaniach ACR:

* [Zadania wieloetapowe](container-registry-tasks-multi-step.md) — ACR przepływy pracy oparte na zadaniach służące do kompilowania, testowania i poprawiania obrazów kontenerów w chmurze.
* [Odwołanie do zadania](container-registry-tasks-reference-yaml.md) — typy etapów zadań, ich właściwości i użycie.
* [Polecenie cmd repozytorium](https://github.com/AzureCR/cmd) — kolekcja kontenerów jako poleceń dla zadań ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

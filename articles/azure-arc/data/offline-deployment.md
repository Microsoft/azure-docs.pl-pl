---
title: Wdrażanie w trybie offline
description: Wdrożenie w trybie offline umożliwia ściąganie obrazów kontenerów z prywatnego rejestru kontenerów zamiast ściągania z Container Registry firmy Microsoft.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5fa0b6ca41349d20614a64006536e78d8ee71844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955369"
---
# <a name="offline-deployment-overview"></a>Przegląd wdrożenia w trybie offline

Zazwyczaj obrazy kontenerów używane podczas tworzenia kontrolera danych usługi Azure ARC, wystąpienia zarządzane SQL i grupy serwerów PostgreSQL do skalowania są bezpośrednio pobierane z Container Registry firmy Microsoft (MCR). W niektórych przypadkach wdrażane środowisko nie będzie miało łączności z Container Registryem firmy Microsoft.  W takich sytuacjach można ściągnąć obrazy kontenerów za pomocą komputera _, który ma_ dostęp do Container Registry firmy Microsoft, a następnie wypchnąć je do prywatnego rejestru kontenerów, który można _połączyć ze środowiska, w_ którym chcesz wdrożyć usługi danych z funkcją Azure Arc.

Ponieważ dostępne są comiesięczne aktualizacje dla usług danych z obsługą usługi Azure Arc i istnieje duża liczba obrazów kontenerów, najlepiej wykonać ten proces ściągania, tagowania i wypychania obrazów kontenera do prywatnego rejestru kontenerów za pomocą skryptu.  Skrypt może być zautomatyzowany lub uruchamiany ręcznie.

[Przykładowy skrypt](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) znajduje się w repozytorium usługi Azure Arc w witrynie GitHub.

> [!NOTE]
> Ten skrypt wymaga instalacji języka Python i [interfejsu wiersza polecenia platformy Docker](https://docs.docker.com/install/).

Skrypt będzie interaktywnie monitował o poniższe informacje.  Alternatywnie, jeśli chcesz, aby skrypt był uruchamiany bez interakcyjnych wierszy, możesz ustawić odpowiednie zmienne środowiskowe przed uruchomieniem skryptu.

|Monit|Zmienna środowiskowa|Uwagi|
|---|---|---|
|Podaj rejestr kontenera źródłowego — naciśnij klawisz ENTER, aby użyć `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Zazwyczaj można ściągnąć obrazy z Container Registry firmy Microsoft, ale jeśli korzystasz z prywatnej wersji zapoznawczej z innym rejestrem, możesz użyć dostarczonych informacji jako części programu w wersji zapoznawczej.|
|Podaj repozytorium rejestru źródłowego kontenera — naciśnij klawisz ENTER, aby użyć `arcdata` :|SOURCE_DOCKER_REPOSITORY|W przypadku ściągania z Container Registry firmy Microsoft repozytorium będzie `arcdata` .|
|Podaj nazwę użytkownika dla rejestru kontenerów źródłowych — naciśnij klawisz ENTER, aby użyć opcji Brak:|SOURCE_DOCKER_USERNAME|Podaj wartość tylko w przypadku ściągania obrazów kontenera ze źródła, które wymaga logowania.  Container Registry firmy Microsoft nie wymaga logowania.|
|Podaj hasło do rejestru źródłowego kontenera — naciśnij klawisz ENTER, aby użyć opcji Brak:|SOURCE_DOCKER_PASSWORD|Podaj wartość tylko w przypadku ściągania obrazów kontenera ze źródła, które wymaga logowania.  Container Registry firmy Microsoft nie wymaga logowania. Jest to monit o zamaskowanie hasła.  Hasło nie zostanie wyświetlone, jeśli wpiszesz lub wkleisz je w programie.|
|Podaj tag obrazu kontenera dla obrazów w źródle — naciśnij klawisz ENTER, aby użyć " `<current monthly release tag>` ":|SOURCE_DOCKER_TAG|Domyślna nazwa tagu zostanie zaktualizowana co miesiąc, aby odzwierciedlić miesiąc i rok bieżącej wersji w Container Registry firmy Microsoft.|
|Podaj nazwę DNS lub adres IP rejestru kontenera docelowego:|TARGET_DOCKER_REGISTRY|Docelowa nazwa DNS lub adres IP.  Jest to rejestr, _do_ którego będą wypychane obrazy.|
|Podaj repozytorium docelowego rejestru kontenerów:|TARGET_DOCKER_REPOSITORY|Repozytorium w rejestrze docelowym, do którego będą wypychane obrazy.|
|Podaj nazwę użytkownika dla rejestru kontenerów docelowych — naciśnij klawisz ENTER, aby użyć opcji Brak:|TARGET_DOCKER_USERNAME|Nazwa użytkownika (jeśli istnieje) służąca do logowania się do docelowego rejestru kontenerów.|
|Podaj hasło do rejestru kontenerów docelowych — naciśnij klawisz ENTER, aby użyć opcji Brak:|TARGET_DOCKER_PASSWORD|Hasło (jeśli istnieje) używane do logowania się do docelowego rejestru kontenerów. Jest to monit o zamaskowanie hasła.  Hasło nie zostanie wyświetlone, jeśli wpiszesz lub wkleisz je w programie.|
|Podaj tag obrazu kontenera dla obrazów w miejscu docelowym:|TARGET_DOCKER_TAG|Zazwyczaj można użyć tego samego tagu co źródło, aby uniknąć pomyłek.|
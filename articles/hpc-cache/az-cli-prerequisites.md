---
title: Wymagania wstępne interfejsu wiersza polecenia platformy Azure dla usługi Azure HPC cache
description: Kroki konfiguracji przed rozpoczęciem korzystania z interfejsu wiersza polecenia platformy Azure w celu utworzenia lub zmodyfikowania pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94654460"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure HPC Cache

Wykonaj następujące kroki, aby przygotować środowisko przed użyciem interfejsu wiersza polecenia platformy Azure w celu utworzenia pamięci podręcznej platformy Azure HPC lub zarządzania nią.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Pamięć podręczna platformy Azure HPC wymaga wersji 2,7 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="set-default-resource-group-optional"></a>Ustaw domyślną grupę zasobów (opcjonalnie)

Większość poleceń pamięci podręcznej HPC wymaga przekazania grupy zasobów pamięci podręcznej. Domyślną grupę zasobów można ustawić za pomocą polecenia [AZ Configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu rozszerzenia interfejsu wiersza polecenia platformy Azure i zalogowaniu się za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć systemy pamięci podręcznej platformy Azure HPC i zarządzać nimi.

* [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md)
* [Interfejs wiersza polecenia platformy Azure HPC — dokumentacja pamięci podręcznej](/cli/azure/ext/hpc-cache/hpc-cache)

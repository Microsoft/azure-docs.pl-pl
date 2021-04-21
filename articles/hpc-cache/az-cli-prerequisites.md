---
title: Wymagania wstępne interfejsu wiersza polecenia platformy Azure Azure HPC Cache
description: Kroki konfiguracji przed użyciem interfejsu wiersza polecenia platformy Azure do utworzenia lub zmodyfikowania Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 30621eceefd69cd3e08de137bb34f1079a17a406
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780492"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure HPC Cache

Wykonaj następujące kroki, aby przygotować środowisko przed użyciem interfejsu wiersza polecenia platformy Azure do utworzenia Azure HPC Cache.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache interfejsu wiersza polecenia platformy Azure wymaga wersji 2.7 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="set-default-resource-group-optional"></a>Ustawianie domyślnej grupy zasobów (opcjonalnie)

Większość poleceń hpc-cache wymaga przekazania grupy zasobów pamięci podręcznej. Domyślną grupę zasobów można ustawić za pomocą az [configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu rozszerzenia interfejsu wiersza polecenia platformy Azure i zalogowaniu się możesz użyć interfejsu wiersza polecenia platformy Azure do tworzenia Azure HPC Cache systemów.

* [Tworzenie Azure HPC Cache](hpc-cache-create.md)
* [Dokumentacja polecenia interfejsu wiersza polecenia platformy Azure hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache)

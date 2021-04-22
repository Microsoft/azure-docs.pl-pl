---
title: Wymagania wstępne interfejsu wiersza polecenia platformy Azure Azure HPC Cache
description: Kroki konfiguracji przed użyciem interfejsu wiersza polecenia platformy Azure do utworzenia lub zmodyfikowania Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0b8e1158bc60c4cceea508db988000fe952a90a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864293"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure HPC Cache

Wykonaj następujące kroki, aby przygotować środowisko przed użyciem interfejsu wiersza polecenia platformy Azure do utworzenia Azure HPC Cache.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache interfejs wiersza polecenia platformy Azure wymaga wersji 2.7 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="set-default-resource-group-optional"></a>Ustawianie domyślnej grupy zasobów (opcjonalnie)

Większość poleceń hpc-cache wymaga przekazania grupy zasobów pamięci podręcznej. Domyślną grupę zasobów można ustawić za pomocą [narzędzia az configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu rozszerzenia interfejsu wiersza polecenia platformy Azure i zalogowaniu się możesz użyć interfejsu wiersza polecenia platformy Azure, aby tworzyć systemy Azure HPC Cache i zarządzać nimi.

* [Tworzenie Azure HPC Cache](hpc-cache-create.md)
* [Dokumentacja hpc-cache interfejsu wiersza polecenia platformy Azure](/cli/azure/hpc-cache)

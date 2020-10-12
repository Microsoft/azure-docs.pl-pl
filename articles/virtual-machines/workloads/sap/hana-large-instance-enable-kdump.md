---
title: Skrypt umożliwiający włączenie kdump w SAP HANA (duże wystąpienia) | Microsoft Docs
description: 'Skrypt umożliwiający włączenie kdump w SAP HANA (duże wystąpienia), typ:'
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d723e95212e457a81eedf7726bf3c5bd2499643
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84488889"
---
# <a name="enable-kdump-service"></a>Włącz usługę kdump

W tym dokumencie opisano szczegółowe informacje dotyczące włączania usługi kdump na dużym wystąpieniu platformy Azure HANA (**Typ i i typ II**)

## <a name="supported-skus"></a>Obsługiwane jednostki SKU

|  Typ dużego wystąpienia Hana   |  Dostawca systemu operacyjnego   |  Wersja pakietu systemu operacyjnego   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Typ I                    |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S224m      |
|   Typ I                    |  Szło        |   SLES 12 SP4         |  S224m      |
|   Typ I                    |  Szło        |   SLES 12 SP2         |  S72        |
|   Typ I                    |  Szło        |   SLES 12 SP2         |  S72m       |
|   Typ I                    |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S72m       |
|   Typ I                    |  Szło        |   SLES 12 SP2         |  S96        |
|   Typ I                    |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S96        |
|   Typ I                    |  Szło        |   SLES 12 SP2         |  S192       |
|   Typ I                    |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S192       |
|   Typ I                    |  Szło        |   SLES 12 SP4         |  S192       |
|   Typ I                    |  Szło        |   SLES 12 SP2         |  S192m      |
|   Typ I                    |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S192m      |
|   Typ I                    |  Szło        |   SLES 12 SP4         |  S192m      |
|   Typ I                    |  Szło        |   SLES 12 SP2         |  S144       |
|   Typ I                    |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S144       |
|   Typ I                    |  Szło        |   SLES 12 SP2         |  S144m      |
|   Typ I                    |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S144m      |
|   Typ II                   |  Szło        |   SLES 12 SP2         |  S384       |
|   Typ II                   |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S384       |
|   Typ II                   |  Szło        |   SLES 12 SP4         |  S384       |
|   Typ II                   |  Szło        |   SLES 12 SP2         |  S384xm     |
|   Typ II                   |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S384xm     |
|   Typ II                   |  Szło        |   SLES 12 SP4         |  S384xm     |
|   Typ II                   |  Szło        |   SLES 12 SP2         |  S576m      |
|   Typ II                   |  Szło        |   SLES 12 Z DODATKIEM SP3         |  S576m      |
|   Typ II                   |  Szło        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa kdump używa `/var/crash` katalogu do zapisu zrzutów, upewnij się, że partycja odpowiada katalogowi z wystarczającą ilością miejsca, aby obsłużyć zrzuty.

## <a name="setup-details"></a>Szczegóły konfiguracji

- Skrypt umożliwiający włączenie kdump można znaleźć [tutaj](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)

- Uruchom ten skrypt na dużym wystąpieniu HANA przy użyciu poniższego polecenia

    > [!NOTE]
    > sudo uprawnienie do uruchomienia tego polecenia.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Jeśli dane wyjściowe polecenia kdump zostały pomyślnie włączone, należy ponownie uruchomić system w celu zastosowania zmiany, a następnie kdump zostanie pomyślnie włączona. Uruchom ponownie system, aby zastosować zmiany.

- Jeśli wykonanie polecenia nie powiodło się, kończy!!!!, a usługa kdump nie jest włączona. Zapoznaj się z sekcją [problem z pomocą techniczną](#support-issue).

## <a name="test-kdump"></a>Test kdump

> [!NOTE]
>  Poniższa operacja spowoduje wyzwolenie awarii jądra i ponownego uruchomienia systemu.

- Wyzwalanie awarii jądra

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Po pomyślnym ponownym uruchomieniu systemu sprawdź `/var/crash` katalog pod kątem dzienników awarii jądra.

- Jeśli `/var/crash` ma katalog z bieżącą datą, kdump został pomyślnie włączony.

## <a name="support-issue"></a>Problem z pomocą techniczną

Jeśli skrypt zakończy się niepowodzeniem z powodu błędu lub kdump nie jest włączony, Zgłoś żądanie obsługi z zespołem pomocy technicznej firmy Microsoft, podając następujące szczegóły.

* Identyfikator subskrypcji (HLI)

* Nazwa serwera

* Dostawca systemu operacyjnego

* Wersja systemu operacyjnego

* Wersja jądra

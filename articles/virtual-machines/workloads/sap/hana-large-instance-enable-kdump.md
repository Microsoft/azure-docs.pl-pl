---
title: Skrypt umożliwiający włączenie kdump w SAP HANA (duże wystąpienia) | Microsoft Docs
description: 'Skrypt umożliwiający włączenie kdump w SAP HANA (duże wystąpienia), typ:'
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4b06cbcbef79e243116bddb33adbcf6476fac8a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213372"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump dla Oprogramowanie SAP HANA na platformie Azure — duże wystąpienia (HLI)

Skonfigurowanie i włączenie kdump jest krokiem, który jest wymagany do rozwiązywania problemów z awariami systemu, które nie mają wyraźnej przyczyny.
Czasami system nieoczekiwanie ulegnie awarii, którego nie można wyjaśnić przez problem ze sprzętem lub infrastrukturą.
W takich przypadkach może to być problem z systemem operacyjnym lub aplikacją, a kdump będzie umożliwiał SUSEowi określenie przyczyny awarii systemu.

## <a name="enable-kdump-service"></a>Włącz usługę kdump

W tym dokumencie opisano szczegółowe informacje dotyczące włączania usługi kdump na dużym wystąpieniu platformy Azure HANA (**Typ i i typ II**)

## <a name="supported-skus"></a>Obsługiwane jednostki SKU

|  Typ dużego wystąpienia Hana   |  Dostawca systemu operacyjnego   |  Wersja pakietu systemu operacyjnego   |  SKU |
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

- Skrypt umożliwiający włączenie kdump można znaleźć [tutaj](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh)
> [!NOTE]
> Ten skrypt jest tworzony na podstawie naszej konfiguracji laboratorium, a klient powinien skontaktować się z dostawcą systemu operacyjnego w celu doprowadzenia dalszych dostrojeń.
> W przypadku nowych i istniejących serwerów w celu zapisania zrzutów i skryptów należy zadbać o skonfigurowanie systemu plików poza jednostką LUN.
> Firma Microsoft nie będzie odpowiedzialna za analizowanie zrzutu. Klient musi otworzyć bilet z dostawcą systemu operacyjnego, aby go przeanalizować.

- Uruchom ten skrypt na dużym wystąpieniu HANA przy użyciu poniższego polecenia

    > [!NOTE]
    > sudo uprawnienie do uruchomienia tego polecenia.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Jeśli dane wyjściowe polecenia kdump zostały pomyślnie włączone, należy ponownie uruchomić system, aby pomyślnie zastosować zmiany.

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

## <a name="related-documents"></a>Powiązane dokumenty
- Aby dowiedzieć się więcej na temat [konfigurowania kdump](https://www.suse.com/support/kb/doc/?id=3374462)

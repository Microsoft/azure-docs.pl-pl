---
title: Omówienie kontroli konserwacji maszyn wirtualnych platformy Azure przy użyciu Azure Portal
description: Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu funkcji kontroli konserwacji.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 290a1e8da4e9b3e8eff171ab2d5837bfc9c381b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552426"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Zarządzanie aktualizacjami platformy przy użyciu kontroli konserwacji 

Zarządzaj aktualizacjami platformy, które nie wymagają ponownego uruchomienia, przy użyciu funkcji kontroli konserwacji. Platforma Azure często aktualizuje swoją infrastrukturę w celu poprawy niezawodności, wydajności, zabezpieczeń lub uruchamiania nowych funkcji. Większość aktualizacji jest niewidoczna dla użytkowników. Niektóre wrażliwe obciążenia, takie jak gry, przesyłanie strumieniowe multimediów i transakcje finansowe, nie mogą tolerować nawet kilku sekund zamarzania maszyny wirtualnej lub odłączenia jej do konserwacji. Sterowanie konserwacją umożliwia zaczekanie na aktualizacje platformy i stosowanie ich w 35-dniowym oknie kroczącym. 

Kontrola konserwacji pozwala określić, kiedy mają być stosowane aktualizacje odizolowanych maszyn wirtualnych i hostów dedykowanych platformy Azure.

Za pomocą kontrolki konserwacji można:
- Aktualizacje wsadowe w jednym pakiecie aktualizacji.
- Zaczekaj do 35 dni, aby zastosować aktualizacje. 
- Automatyzowanie aktualizacji platformy przez skonfigurowanie harmonogramu konserwacji lub przy użyciu [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Konfiguracje konserwacji działają w ramach subskrypcji i grup zasobów. 

## <a name="limitations"></a>Ograniczenia

- Maszyny wirtualne muszą znajdować się na [dedykowanym hoście](./dedicated-hosts.md)lub być tworzone przy użyciu [IZOLOWANEGO rozmiaru maszyny wirtualnej](isolation.md).
- Jeśli harmonogram konserwacji jest zadeklarowany, musi on wynosić co najmniej 2 godziny.
- Po upływie 35 dni zostanie automatycznie zastosowana aktualizacja.
- Użytkownik musi mieć dostęp **współautora zasobów** .

## <a name="management-options"></a>Opcje zarządzania

Konfiguracje konserwacji można tworzyć i zarządzać nimi przy użyciu dowolnej z następujących opcji:

- [Interfejs wiersza polecenia platformy Azure](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Witryna Azure Portal](maintenance-control-portal.md)

Aby uzyskać przykład Azure Functions, zobacz [Planowanie aktualizacji konserwacji przy użyciu sterowania konserwacją i Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz temat [konserwacja i aktualizacje](maintenance-and-updates.md).

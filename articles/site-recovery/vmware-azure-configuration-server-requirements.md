---
title: Odzyskiwanie po awarii oprogramowania VMware — wymagania dotyczące serwera konfiguracji w Azure Site Recovery
description: W tym artykule opisano obsługę i wymagania dotyczące wdrażania serwera konfiguracji na potrzeby odzyskiwania po awarii oprogramowania VMware na platformie Azure przy użyciu Azure Site Recovery
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: aec08aa4f18c69195be91b7544b74fee100f6f6b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580182"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Wymagania serwera konfiguracji dotyczące odzyskiwania po awarii środowiska VMware na platformie Azure

Lokalny serwer konfiguracji jest wdrażany w przypadku używania [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.

- Serwer konfiguracji koordynuje komunikację między lokalnym programem VMware i platformą Azure. Umożliwia również zarządzanie replikacją danych.
- [Dowiedz się więcej](vmware-azure-architecture.md) o składnikach i procesach serwera konfiguracji.

## <a name="configuration-server-deployment"></a>Wdrożenie serwera konfiguracji

W przypadku odzyskiwania po awarii maszyn wirtualnych VMware na platformę Azure należy wdrożyć serwer konfiguracji jako maszynę wirtualną VMware.

- Site Recovery udostępnia szablon komórki jajowe pobrany z Azure Portal i zaimportowania go do vCenter Server w celu skonfigurowania maszyny wirtualnej serwera konfiguracji.
- Podczas wdrażania serwera konfiguracji przy użyciu szablonu komórki jajowe, maszyna wirtualna automatycznie spełnia wymagania wymienione w tym artykule.
- Zdecydowanie zalecamy skonfigurowanie serwera konfiguracji przy użyciu szablonu komórki jajowe. Jeśli jednak konfigurujesz odzyskiwanie po awarii dla maszyn wirtualnych VMware i nie można użyć szablonu komórki jajowe, możesz wdrożyć serwer konfiguracji, korzystając z [poniższych instrukcji](physical-azure-set-up-source.md).
- Jeśli wdrażasz serwer konfiguracji na potrzeby odzyskiwania po awarii lokalnych maszyn fizycznych na platformie Azure, postępuj zgodnie z instrukcjami w [tym artykule](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Następne kroki
Skonfiguruj odzyskiwanie po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.

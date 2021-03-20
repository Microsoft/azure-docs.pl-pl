---
title: Architektura — odzyskiwanie oprogramowania VMware/Physical do lokacji dodatkowej z Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas odzyskiwania po awarii lokalnych maszyn wirtualnych VMware lub serwerów fizycznych z systemem Windows/Linux do pomocniczej lokacji programu VMware z Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: d400e6bcda0a2114d798a3289f01f52b677a6f94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656499"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Architektura replikacji oprogramowania VMware/serwera fizycznego do pomocniczej lokacji lokalnej

W tym artykule opisano architekturę i procesy używane podczas konfigurowania replikacji odzyskiwania po awarii, trybu failover i odzyskiwania lokalnych maszyn wirtualnych VMware lub serwerów fizycznych z systemem Windows/Linux do pomocniczej lokacji programu VMware przy użyciu [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Składniki architektury

**Warstwowy** | **Składnik** | **Szczegóły**
--- | --- | ---
**Azure** | Ten scenariusz jest wdrażany przy użyciu programu InMage Scout. | Aby uzyskać program InMage Scout, konieczna jest subskrypcja platformy Azure.<br/><br/> Po utworzeniu magazynu usługi Recovery Services należy pobrać program InMage Scout i zainstalować najnowsze aktualizacje w celu skonfigurowania wdrożenia.
**Serwer przetwarzania** | Znajduje się w lokacji głównej | Serwer przetwarzania jest wdrażany w celu obsługi pamięci podręcznej, kompresji i optymalizacji danych.<br/><br/> Umożliwia on również obsługę instalacji wypychanej programu Unified Agent na maszynach, które chcesz chronić.
**Serwer konfiguracji** | Znajduje się w lokacji dodatkowej | Serwer konfiguracji umożliwia konfigurowanie i monitorowanie wdrożenia oraz zarządzanie nim za pomocą witryny sieci Web zarządzania lub konsoli vContinuum.
**Serwer vContinuum** | Opcjonalny. Instalowany w tej samej lokalizacji co serwer konfiguracji. | Zapewnia on konsolę do monitorowania chronionego środowiska i zarządzania nim.
**Główny serwer docelowy** | Znajduje się w lokacji dodatkowej | Na głównym serwerze docelowym przechowywane są zreplikowane dane. Odbiera on dane z serwera przetwarzania i tworzy maszynę repliki w lokacji dodatkowej, a także znajdują się na nim punkty przechowywania danych.<br/><br/> Liczba potrzebnych głównych serwerów docelowych zależy od liczby chronionych maszyn.<br/><br/> Aby powrócić po awarii do lokacji głównej, konieczne jest również posiadanie w tej sieci głównego serwera docelowego. Na tym serwerze jest instalowany program Unified Agent.
**Program VMware ESX/ESXi i serwer vCenter** |  Maszyny wirtualne są hostowane na hostach ESX/ESXi. Hosty są zarządzane za pomocą serwera vCenter | Aby móc replikować maszyny wirtualne VMware, konieczne jest posiadanie infrastruktury VMware.
**Maszyny wirtualne/serwery fizyczne** |  Program Unified Agent zainstalowany na przeznaczonych do replikowania maszynach wirtualnych programu VMware i serwerach fizycznych. | Agent działa jako dostawca komunikacji między wszystkimi składnikami.

## <a name="set-up-outbound-network-connectivity"></a>Konfigurowanie wychodzącej łączności sieciowej

Aby Site Recovery działały zgodnie z oczekiwaniami, należy zmodyfikować wychodzącą łączność sieciową, aby umożliwić replikację danego środowiska.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.

### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych adresów URL:

| **Nazwa**                  | **Commercial**                               | **Instytucje rządowe**                                 | **Opis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| Replikacja               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

## <a name="replication-process"></a>Proces replikacji

1. Następnie należy skonfigurować serwery składników w każdej lokacji (konfiguracja, przetwarzanie, główny docelowy) oraz zainstalować program Unified Agent na maszynach, które mają zostać zreplikowane.
2. Po początkowej replikacji agent na każdej maszynie wysyła zmiany replikacji przyrostowej na serwer przetwarzania.
3. Serwer przetwarzania optymalizuje dane i transferuje je na główny serwer docelowy w lokacji dodatkowej. Serwer konfiguracji zarządza procesem replikacji.

![Diagram przedstawiający replikację maszyn wirtualnych VMware i serwerów fizycznych do dodatkowego centrum danych](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>Następne kroki

[Skonfiguruj](vmware-physical-secondary-disaster-recovery.md) odzyskiwanie po awarii maszyn wirtualnych VMware i serwerów fizycznych do lokacji dodatkowej.

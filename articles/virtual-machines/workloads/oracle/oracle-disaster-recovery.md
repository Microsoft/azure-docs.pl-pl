---
title: Omówienie scenariusza odzyskiwania po awarii programu Oracle w środowisku platformy Azure | Microsoft Docs
description: Scenariusz odzyskiwania po awarii dla Oracle Database bazy danych 12c w środowisku platformy Azure
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 8c5281b4fe596cf9807d6f0dacb844562dc152dc
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880628"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Odzyskiwanie po awarii dla Oracle Database bazy danych 12c w środowisku platformy Azure

## <a name="assumptions"></a>Założenia

- Znasz zagadnienia dotyczące projektowania i środowisk platformy Azure z usługą Oracle Data Guard.


## <a name="goals"></a>Cele
- Zaprojektuj topologię i konfigurację, która spełnia wymagania odzyskiwania po awarii (DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenariusz 1. Lokacje podstawowe i DR na platformie Azure

Klient ma bazę danych Oracle skonfigurowaną w lokacji głównej. Lokacja DR znajduje się w innym regionie. Klient korzysta z usługi Oracle Data Guard do szybkiego odzyskiwania między tymi lokacjami. Lokacja główna ma także pomocniczą bazę danych do raportowania i innych celów. 

### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Dwie lokacje (lokacja główna i lokacja odzyskiwania po awarii)
- Dwie sieci wirtualne
- Dwie bazy danych Oracle z funkcją ochrony danych (podstawowa i w stanie gotowości)
- Dwie bazy danych Oracle z złotą bramą lub funkcją ochrony danych (tylko lokacja główna)
- Dwie usługi aplikacji, jeden podstawowy i jeden w lokacji odzyskiwania po awarii
- *Zestaw dostępności,* który jest używany dla usługi bazy danych i aplikacji w lokacji głównej
- Jedna serwera przesiadkowego w każdej lokacji, która ogranicza dostęp do sieci prywatnej i zezwala na logowanie tylko przez administratora
- Serwera przesiadkowego, Usługa aplikacji, baza danych i Brama sieci VPN w różnych podsieciach
- SIECIOWEJ grupy zabezpieczeń wymuszane w podsieciach aplikacji i bazy danych

![Diagram przedstawiający witryny podstawowe i DR na platformie Azure.](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenariusz 2: lokacja podstawowa i usługa odzyskiwania po awarii na platformie Azure

Klient ma lokalną konfigurację bazy danych Oracle (lokacja główna). Lokacja odzyskiwania po awarii znajduje się na platformie Azure. Funkcja Oracle Data Guard służy do szybkiego odzyskiwania między tymi lokacjami. Lokacja główna ma także pomocniczą bazę danych do raportowania i innych celów. 

Istnieją dwie podejścia do tej konfiguracji.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Podejście 1: bezpośrednie połączenia między środowiskiem lokalnym i platformą Azure, wymagające otwartych portów TCP zapory 

Nie zalecamy bezpośrednich połączeń, ponieważ uwidaczniają one porty TCP na świecie zewnętrznym.

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Jedna lokacja DR 
- Jedna sieć wirtualna
- Jedna baza danych Oracle z funkcją Data Guard (aktywna)
- Jedna usługa aplikacji w witrynie odzyskiwania po awarii
- Jeden serwera przesiadkowego, który ogranicza dostęp do sieci prywatnej i zezwala na logowanie tylko przez administratora
- Serwera przesiadkowego, Usługa aplikacji, baza danych i Brama sieci VPN w różnych podsieciach
- SIECIOWEJ grupy zabezpieczeń wymuszane w podsieciach aplikacji i bazy danych
- Zasada sieciowej grupy zabezpieczeń/reguła zezwalająca na przychodzący port TCP 1521 (lub port zdefiniowany przez użytkownika)
- Zasada sieciowej grupy zabezpieczeń/reguła ograniczająca dostęp do sieci wirtualnej tylko w lokalnym adresie IP lub w aplikacji.

![Diagram przedstawiający bezpośrednie połączenia między środowiskiem lokalnym i platformą Azure, które wymagają otwartych portów TCP zapory.](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Podejście 2: sieć VPN typu lokacja-lokacja
Sieci VPN typu lokacja-lokacja jest lepszym rozwiązaniem. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN, zobacz [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu interfejsu wiersza polecenia](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md).

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Jedna lokacja DR 
- Jedna sieć wirtualna 
- Jedna baza danych Oracle z funkcją Data Guard (aktywna)
- Jedna usługa aplikacji w witrynie odzyskiwania po awarii
- Jeden serwera przesiadkowego, który ogranicza dostęp do sieci prywatnej i zezwala na logowanie tylko przez administratora
- Serwera przesiadkowego, Usługa aplikacji, baza danych i Brama sieci VPN znajdują się w różnych podsieciach
- SIECIOWEJ grupy zabezpieczeń wymuszane w podsieciach aplikacji i bazy danych
- Połączenie sieci VPN typu lokacja-lokacja między środowiskiem lokalnym i platformą Azure

![Zrzut ekranu przedstawiający stronę topologii DR](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Materiały uzupełniające

- [Projektowanie i implementowanie bazy danych Oracle na platformie Azure](oracle-design.md)
- [Konfigurowanie środowiska Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie firmy Oracle — Złotej Bramy](configure-oracle-golden-gate.md)
- [Kopia zapasowa Oracle i odzyskiwanie](./oracle-overview.md)


## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)
- [Eksplorowanie przykładów interfejsu wiersza polecenia platformy Azure wdrożenia maszyny wirtualnej](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
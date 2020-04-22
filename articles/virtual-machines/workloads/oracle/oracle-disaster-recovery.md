---
title: Omówienie scenariusza odzyskiwania po awarii Oracle w środowisku platformy Azure | Dokumenty firmy Microsoft
description: Scenariusz odzyskiwania po awarii dla bazy danych Oracle Database 12c w środowisku platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: adf4c54d4f43f806ae985f7c1523303cf574b4c1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687467"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Odzyskiwanie po awarii bazy danych Oracle Database 12c w środowisku platformy Azure

## <a name="assumptions"></a>Założenia

- Masz wiedzę na temat projektowania Oracle Data Guard i środowisk platformy Azure.


## <a name="goals"></a>Cele
- Zaprojektuj topologię i konfigurację, które spełniają wymagania odzyskiwania po awarii(DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenariusz 1: Lokacje podstawowe i główne witryny odzyskiwania po awarii na platformie Azure

Klient ma bazę danych Oracle skonfigurowaną w lokacji głównej. Lokacja odzyskiwania po awarii znajduje się w innym regionie. Klient korzysta z Oracle Data Guard do szybkiego odzyskiwania między tymi witrynami. Lokacja główna ma również pomocniczą bazę danych do raportowania i innych zastosowań. 

### <a name="topology"></a>Topologia

Oto podsumowanie konfiguracji platformy Azure:

- Dwie lokacje (lokacja główna i lokacja odzyskiwania po awarii)
- Dwie sieci wirtualne
- Dwie bazy danych Oracle z funkcją Data Guard (podstawowa i rezerwowa)
- Dwie bazy danych Oracle ze Złotą Bramą lub Data Guard (tylko lokacja główna)
- Dwie usługi aplikacji, jedna podstawowa i jedna w witrynie odzyskiwania po awarii
- *Zestaw dostępności,* który jest używany do obsługi bazy danych i aplikacji w lokacji głównej
- Jeden jumpbox na każdej stronie, który ogranicza dostęp do sieci prywatnej i umożliwia logowanie tylko przez administratora
- Skrzynka szybkiego dostępu, usługa aplikacji, baza danych i brama sieci VPN w oddzielnych podsieciach
- NSG wymuszane w podsieciach aplikacji i bazy danych

![Zrzut ekranu przedstawiający stronę topologii odzyskiwania po awarii](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenariusz 2: Lokacja główna lokalna i lokacja odzyskiwania po awarii na platformie Azure

Klient ma lokalną konfigurację bazy danych Oracle (lokację główną). Witryna odzyskiwania po awarii znajduje się na platformie Azure. Oracle Data Guard służy do szybkiego odzyskiwania między tymi witrynami. Lokacja główna ma również pomocniczą bazę danych do raportowania i innych zastosowań. 

Istnieją dwa podejścia dla tej konfiguracji.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Podejście 1: Bezpośrednie połączenia między środowiskiem lokalnym a platformą Azure, wymagające otwartych portów TCP w zaporze 

Nie zaleca się połączeń bezpośrednich, ponieważ udostępniają one porty TCP światu zewnętrznemu.

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Jedna witryna odzyskiwania po awarii 
- Jedna sieć wirtualna
- Jedna baza danych Oracle z data guard (aktywna)
- Jedna usługa aplikacji w witrynie odzyskiwania po awarii
- Jeden jumpbox, który ogranicza dostęp do sieci prywatnej i umożliwia logowanie tylko przez administratora
- Skrzynka szybkiego dostępu, usługa aplikacji, baza danych i brama sieci VPN w oddzielnych podsieciach
- NSG wymuszane w podsieciach aplikacji i bazy danych
- Zasada/reguła nsg zezwalana na przychodzący port TCP 1521 (lub port zdefiniowany przez użytkownika)
- Zasady/reguła sieciowej grupy sieciowej w celu ograniczenia dostępu do sieci wirtualnej tylko adresów/adresów IP w środowisku lokalnym (DB lub aplikacji)

![Zrzut ekranu przedstawiający stronę topologii odzyskiwania po awarii](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Podejście 2: Sieć VPN typu lokacja-lokacja
Sieć VPN typu lokacja-lokacja to lepsze podejście. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN, zobacz [Tworzenie sieci wirtualnej z połączeniem sieci VPN lokacja lokacja przy użyciu interfejsu wiersza polecenia.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)

#### <a name="topology"></a>Topologia

Poniżej znajduje się podsumowanie konfiguracji platformy Azure:

- Jedna witryna odzyskiwania po awarii 
- Jedna sieć wirtualna 
- Jedna baza danych Oracle z data guard (aktywna)
- Jedna usługa aplikacji w witrynie odzyskiwania po awarii
- Jeden jumpbox, który ogranicza dostęp do sieci prywatnej i umożliwia logowanie tylko przez administratora
- Skrzynka szybkiego dostępu, usługa aplikacji, baza danych i brama sieci VPN znajdują się w oddzielnych podsieciach
- NSG wymuszane w podsieciach aplikacji i bazy danych
- Połączenie sieci VPN między lokacją a platformą Azure

![Zrzut ekranu przedstawiający stronę topologii odzyskiwania po awarii](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Dodatkowa lektura

- [Projektowanie i wdrażanie bazy danych Oracle na platformie Azure](oracle-design.md)
- [Konfigurowanie środowiska Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurowanie Złotej Bramy Oracle](configure-oracle-golden-gate.md)
- [Tworzenie kopii zapasowych i odzyskiwanie kopii zapasowych i odzyskiwania oracle](oracle-backup-recovery.md)


## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie maszyn wirtualnych o wysokiej dostępności](../../linux/create-cli-complete.md)
- [Zapoznaj się z przykładami interfejsu wiersza polecenia platformy Azure dotyczące wdrażania maszyn wirtualnych](../../linux/cli-samples.md)

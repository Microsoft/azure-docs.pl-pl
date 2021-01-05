---
title: Zapewnianie wysokiej dostępności aplikacji w przypadku uruchamiania w oprogramowaniu VMware na platformie Azure
description: Opisuje funkcje wysokiej dostępności CloudSimple na potrzeby rozwiązywania typowych scenariuszy błędów aplikacji dla aplikacji działających w chmurze prywatnej CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8bb1f8bb2aaeab88e5a9ea19534c8983af8c1626
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895754"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Zapewnianie wysokiej dostępności aplikacji w przypadku uruchamiania w oprogramowaniu VMware na platformie Azure

Rozwiązanie CloudSimple zapewnia wysoką dostępność dla aplikacji uruchamianych w oprogramowaniu VMware w środowisku platformy Azure. Poniższa tabela zawiera listę scenariuszy błędów i skojarzonych funkcji wysokiej dostępności.

| Scenariusz niepowodzenia | Czy aplikacja jest chroniona? | Funkcja HA platformy | Funkcja programu VMware HA | Funkcja HA systemu Azure |
------------ | ------------- | ------------ | ------------ | ------------- |
| Awaria dysku | TAK | Szybka wymiana węzła, który uległ awarii | [Informacje o domyślnych zasadach magazynu sieci vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Awaria wentylatora | TAK | Nadmiarowe wentylatory, szybka wymiana węzła zakończonego niepowodzeniem |  |  |
| Awaria karty sieciowej | TAK | Nadmiarowa karta sieciowa, szybka wymiana węzła, który uległ awarii
| Awaria mocy hosta | TAK | Nadmiarowe zasilacze |  |  |
| Niepowodzenie hosta ESXi | TAK | Szybka wymiana węzła, który uległ awarii | [VMware vSphere wysoka dostępność](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Awaria maszyny wirtualnej | TAK | [Moduły równoważenia obciążenia](load-balancers.md)  | [VMware vSphere wysoka dostępność](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer dla bezstanowych maszyn wirtualnych VMware |
| Niepowodzenie portu przełącznika liścia | TAK | Nadmiarowa karta sieciowa |  |  |
| Niepowodzenie przełączania liścia | TAK | Nadmiarowe przełączniki liścia |  |  |
| Awaria stojaka | TAK | Grupy umieszczania |  |  |
| Łączność sieciowa z lokalnym kontrolerem domeny | TAK  | Nadmiarowe usługi sieciowe |  | Nadmiarowe obwody ER |
| Łączność sieciowa z platformą Azure | TAK | |  | Nadmiarowe obwody ER |
| Błąd centrum danych | TAK |  |  | Strefy dostępności |
| Awaria regionalna | TAK  |  |  | Regiony świadczenia usługi Azure |

Rozwiązanie VMware firmy Azure według CloudSimple zapewnia następujące funkcje wysokiej dostępności.

## <a name="fast-replacement-of-failed-node"></a>Szybka wymiana węzła, który uległ awarii

Oprogramowanie płaszczyzny kontrolnej CloudSimple stale monitoruje kondycję klastrów VMware i wykrywa czas awarii węzła ESXi. Następnie automatycznie dodaje nowy host ESXi do klastra programu VMware, którego dotyczy ten klaster, z puli łatwo dostępnych węzłów i przyjmuje węzeł, który uległ awarii z klastra. Ta funkcja zapewnia, że pojemność zapasowa w klastrze programu VMware jest przywracana szybko, aby umożliwić przywrócenie odporności klastra z sieci vSAN i VMware HA.

## <a name="placement-groups"></a>Grupy umieszczania

Użytkownik tworzący chmurę prywatną może wybrać region platformy Azure i grupę umieszczania w wybranym regionie. Grupa umieszczania jest zestawem węzłów rozmieszczonych w wielu stojakach, ale w obrębie tego samego segmentu sieci szkieletowej. Węzły w tej samej grupie umieszczania mogą się łączyć ze sobą przy użyciu maksymalnie dwóch przeskoków dodatkowych przełączników. Grupa umieszczania jest zawsze w obrębie pojedynczej strefy dostępności platformy Azure i obejmuje wiele stojaków. Płaszczyzna sterująca CloudSimple dystrybuuje węzły chmury prywatnej w wielu stojakach na podstawie najlepszego nakładu pracy. Węzły w różnych grupach umieszczania są gwarantowane, aby były umieszczane w różnych stojakach.

## <a name="availability-zones"></a>Strefy dostępności

Strefy dostępności to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Strefy dostępności są specjalnymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Każdy region ma jedną strefę dostępności. Aby uzyskać więcej informacji, zobacz [co to jest strefy dostępności na platformie Azure?](../availability-zones/az-overview.md)

## <a name="redundant-azure-expressroute-circuits"></a>Nadmiarowe obwody usługi Azure ExpressRoute

Łączność z centrum danych z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute ma nadmiarowe obwody, aby zapewnić link łączności sieciowej o wysokiej dostępności.

## <a name="redundant-networking-services"></a>Nadmiarowe usługi sieciowe

Wszystkie usługi sieciowe CloudSimple dla chmury prywatnej (w tym sieci VLAN, zapory, publicznych adresów IP, Internetu i sieci VPN) zostały zaprojektowane tak, aby były wysoce dostępne i mogły obsługiwać umowę SLA usługi.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Load Balancer Azure warstwy 7 dla bezstanowych maszyn wirtualnych VMware

Aby zapewnić wysoką dostępność warstwy sieci Web, użytkownicy mogą umieścić Load Balancer warstwy 7 przed bezstanowymi maszynami wirtualnymi działającymi w środowisku VMware.

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Region świadczenia usługi Azure to zestaw centrów danych wdrożonych w ramach określonego czasu oczekiwania i połączony za pomocą dedykowanej regionalnej sieci o małym opóźnieniu. Aby uzyskać szczegółowe informacje, zobacz [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions).

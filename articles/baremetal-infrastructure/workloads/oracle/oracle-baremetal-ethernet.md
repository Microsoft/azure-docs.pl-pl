---
title: Konfiguracja sieci Ethernet programu BareMetal dla programu Oracle
description: Dowiedz się więcej o konfiguracji interfejsów Ethernet w wystąpieniach BareMetal dla obciążeń Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c57cbc86d17090d6960a334c2790d80b43420aca
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588891"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Konfiguracja sieci Ethernet programu BareMetal dla programu Oracle

W tym artykule przyjrzymy się konfiguracji interfejsów Ethernet w wystąpieniach BareMetal dla obciążeń Oracle.

Każde aprowizowane wystąpienie programu BareMetal dla programu Oracle jest wstępnie skonfigurowane z zestawami interfejsów Ethernet. Interfejsy Ethernet są podzielone na cztery typy:

- Używany do lub przez dostęp klienta.
- Służy do komunikacji między węzłami. Ten interfejs jest konfigurowany na wszystkich serwerach niezależnie od żądanej topologii. Jest on używany tylko w scenariuszach skalowania w zewnątrz.
- Służy do łączności między węzłami i magazynem.
- Służy do konfigurowania odzyskiwania po awarii i łączności z zasięgiem globalnym w celu połączenia między regionami.

## <a name="architecture"></a>Architektura

Na poniższym diagramie przedstawiono architekturę wstępnie skonfigurowanych interfejsów Ethernet infrastruktury BareMetal. 

[![Diagram przedstawiający architekturę wstępnie skonfigurowanych interfejsów Ethernet dla obciążeń Oracle.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

Konfiguracja domyślna jest dostarczany z jednym interfejsem IP klienta (eth1), który łączy się z usługi Azure Virtual Network (VNET), za pomocą którego można uzyskać dostęp do wystąpienia programu BareMetal przy użyciu protokołu Secure Shell (SSH).

> [!NOTE]
> Aby uzyskać inny interfejs klienta (eth10) z innej sieci wirtualnej platformy Azure, skontaktuj się z twoim klientem Microsoft CSA, aby przesłać żądanie obsługi. Na przykład jeśli chcesz testowania/testowania, a także środowiska produkcyjne/po chmurze.

| **Interfejs logiczny karty sieciowej** | **Nazwa systemu operacyjnego RHEL** | **Przypadek użycia** |
| --- | --- | --- |
| A | net1.tenant | Klient do wystąpienia programu BareMetal |
| C | net2.tenant | Węzeł-magazyn; obsługuje koordynację i dostęp do kontrolerów magazynu w celu zarządzania środowiskiem magazynu. |
| B | net3.tenant | Węzeł-węzeł (prywatne połączenie wzajemne) |
| C | net4.tenant | Zarezerwowane/iSCSI |
| C | net5.tenant | Kopia zapasowa zarezerwowana/kopia zapasowa dziennika |
| C | net6.tenant | Tworzenie kopii zapasowej między węzłami storage_Data (RMAN, migawka) |
| C | net7.tenant | Między węzłami storage_dNFS-Pri; zapewnia łączność z macierzą magazynową NetApp. |
| C | net8.tenant | Między węzłami storage_dNFS-sek. zapewnia łączność z macierzą magazynową NetApp. |
| D | net9.tenant | Łączność dr dla konfiguracji globalnego zasięgu w celu uzyskania dostępu do usługi BMI w innym regionie. |
| A | \*net10.tenant | \* Klient do wystąpienia programu BareMetal
 |

W razie potrzeby można samodzielnie zdefiniować więcej kart kontrolera interfejsu sieciowego(NIC). Nie można jednak zmienić  konfiguracji istniejących karty sieciowej.

## <a name="usage-rules"></a>Reguły użycia

W przypadku wystąpień programu BareMetal wartość domyślna będzie mieć dziewięć przypisanych adresów IP na czterech logicznych karty sieciowe. Obowiązują następujące reguły użycia:

- Sieć Ethernet "A" powinna mieć przypisany adres IP spoza zakresu adresów puli adresów IP serwera przesłanego do firmy Microsoft. Ten adres IP nie powinien być utrzymywany w katalogu etc/hosts systemu operacyjnego.
- Sieć Ethernet "B" powinna być utrzymywana wyłącznie w katalogu etc/hosts w celu komunikacji między różnymi wystąpieniami. Zachowaj te adresy IP w konfiguracjach skalowania rzeczywistych klastrów aplikacji Oracle (RAC) jako adresów IP używanych do konfiguracji między węzłami.
- Sieć Ethernet "C" powinna mieć przypisany adres IP używany do komunikacji z magazynem systemu plików NFS. Tego typu adresu nie należy utrzymywać w katalogu etc/hosts.
- Sieć Ethernet "D" powinna być używana wyłącznie do konfigurowania zasięgu globalnego w celu uzyskiwania dostępu do wystąpień programu BareMetal w regionie dr.

## <a name="next-step"></a>Następny krok

Dowiedz się więcej o architekturze BareMetal Infrastructure for Oracle.

> [!div class="nextstepaction"]
> [Architektura systemu BareMetal Infrastructure for Oracle](oracle-baremetal-architecture.md)

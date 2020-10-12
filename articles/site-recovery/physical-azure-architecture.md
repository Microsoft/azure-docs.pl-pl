---
title: Architektura odzyskiwania po awarii serwera fizycznego w Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas odzyskiwania po awarii lokalnych serwerów fizycznych na platformie Azure przy użyciu usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: f2184654a8169cb353fb40fa76f0a7fe9b3df6f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87422661"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Physical server to Azure disaster recovery architecture (Architektura odzyskiwania po awarii z serwera fizycznego na platformę Azure)

W tym artykule opisano architekturę i procesy używane podczas replikacji, przełączania awaryjnego i odzyskiwania fizycznych serwerów z systemami Windows i Linux między lokacją lokalną i platformą Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .

## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i ilustracja przedstawiają ogólny widok składników służących do replikacji serwera fizycznego do platformy Azure.

| **Składnik** | **Wymaganie** | **Szczegóły** |
| --- | --- | --- |
| **Azure** | Subskrypcja platformy Azure i sieć platformy Azure. | Zreplikowane dane z lokalnych maszyn fizycznych są przechowywane na dyskach zarządzanych przez platformę Azure. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych podczas pracy w trybie failover z poziomu lokalnego na platformę Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone. |
| **Serwer przetwarzania** | Instalowany domyślnie razem z serwerem konfiguracji. | Działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do usługi Azure Storage.<br/><br/> Serwer przetwarzania instaluje także usługę mobilności na serwerach, które mają być replikowane.<br/><br/> Wraz z rozwojem wdrożenia można dodać dodatkowe, oddzielne serwery przetwarzania do obsługi większych woluminów ruchu związanego z replikacją. |
| **Główny serwer docelowy** | Instalowany domyślnie razem z serwerem konfiguracji. | Obsługuje dane replikacji podczas powrotu po awarii z platformy Azure.<br/><br/> W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny główny serwer docelowy na potrzeby powrotu po awarii. |
| **Zreplikowane serwery** | Usługa mobilności jest instalowana na każdym replikowanym serwerze. | Zalecamy zezwolenie na automatyczną instalację z serwera przetwarzania. Można też zainstalować usługę ręcznie lub użyć metody automatycznego wdrażania, takiej jak Configuration Manager. |

**Architektura maszyna fizyczna–Azure**

![Składniki](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Konfigurowanie wychodzącej łączności sieciowej

Aby Site Recovery działały zgodnie z oczekiwaniami, należy zmodyfikować wychodzącą łączność sieciową, aby umożliwić replikację danego środowiska.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.

### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych adresów URL:

| **Nazwa**                  | **Commercial**                               | **Instytucje rządowe**                                 | **Opis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Magazyn                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`               | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| Usługa Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| Replikacja               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |


## <a name="replication-process"></a>Proces replikacji

1. Należy skonfigurować wdrożenie, w tym składniki lokalne i usługi platformy Azure. W magazynie Recovery Services należy określić źródło i cel replikacji, skonfigurować serwer konfiguracji, utworzyć zasady replikacji i włączyć replikację.
1. Maszyny są replikowane przy użyciu zasad replikacji, a początkowa kopia danych serwera jest replikowana do usługi Azure Storage.
1. Po zakończeniu replikacji początkowej zostanie rozpoczęta replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są przechowywane w pliku z rozszerzeniem _. HRL_ .
   - Komputery komunikują się z serwerem konfiguracji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.
   - Maszyny wysyłają dane replikacji do serwera przetwarzania na porcie HTTPS 9443 ruchu przychodzącego (można je modyfikować).
   - Serwer konfiguracji organizuje zarządzanie replikacją przy użyciu platformy Azure przez port HTTPS 443.
   - Serwer przetwarzania odbiera dane z maszyn źródłowych, optymalizuje je i szyfruje i wysyła je do usługi Azure Storage za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego.
   - Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Funkcja spójności wielu maszyn wirtualnych jest używana, jeśli wiele maszyn zostanie połączonych w grupę replikacji, która współużytkuje punkty odzyskiwania spójne na poziomie awarii i aplikacji. Te grupy są przydatne, gdy na maszynach są uruchomione te same obciążenia i muszą one być spójne.
1. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie można użyć [publicznej komunikacji równorzędnej](../expressroute/about-public-peering.md) usługi Azure ExpressRoute.

   > [!NOTE]
   > Replikacja nie jest obsługiwana w przypadku sieci VPN typu lokacja-lokacja z lokacji lokalnej lub [prywatnej komunikacji równorzędnej](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)Azure ExpressRoute.

**Proces replikacji fizycznej do platformy Azure**

![Proces replikacji](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji można przeprowadzić drążenie odzyskiwania po awarii (test pracy w trybie failover), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami. Następnie można przełączyć się w tryb failover i powrócić do trybu failover w razie konieczności. Rozważ następujące elementy:

- Planowane przejście w tryb failover nie jest obsługiwane.
- Niepowodzenie z powrotem do lokalnej maszyny wirtualnej VMware. Potrzebna jest lokalna infrastruktura VMware, nawet w przypadku replikowania lokalnych serwerów fizycznych na platformę Azure.
- Pojedyncze maszyny są przełączane w tryb failover lub można utworzyć plany odzyskiwania, aby przełączyć wiele maszyn jednocześnie do trybu failover.
- Po uruchomieniu trybu failover maszyny wirtualne platformy Azure są tworzone na podstawie replikowanych danych w usłudze Azure Storage.
- Po wyzwoleniu początkowej pracy w trybie failover należy zatwierdzić, aby rozpocząć uzyskiwanie dostępu do obciążenia z maszyny wirtualnej platformy Azure.
- Po ponownym udostępnieniu głównej lokacji lokalnej można do niej powrócić po awarii.
- Skonfiguruj infrastrukturę powrotu po awarii, która obejmuje:
  - **Tymczasowy serwer przetwarzania na platformie Azure**: aby powrócić po awarii z platformy Azure, należy skonfigurować maszynę wirtualną platformy Azure do działania jako serwer przetwarzania w celu obsługi replikacji z platformy Azure. Możesz usunąć tę maszynę wirtualną po zakończeniu powrotu po awarii.
  - **Połączenie sieci VPN**: w celu powrotu po awarii wymagane jest połączenie sieci VPN (lub Azure ExpressRoute) z sieci platformy Azure do lokacji lokalnej.
  - **Oddzielny główny serwer docelowy**: domyślnie powrót po awarii jest obsługiwany przez główny serwer docelowy, który został zainstalowany z serwerem konfiguracji na lokalnej maszynie wirtualnej VMware. Jeśli zachodzi potrzeba odtworzenia dużych ilości ruchu, należy skonfigurować oddzielny lokalny główny serwer docelowy.
  - **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Zasady zostały utworzone automatycznie podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
  - **Infrastruktura VMware**: aby powrócić po awarii, potrzebna jest infrastruktura VMware. Nie można powracać po awarii do serwera fizycznego.
- Po zakończeniu awarii następuje awaria w trzech etapach:
  - **Etap 1**. Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w celu replikowania ich z powrotem z platformy Azure do lokalnych maszyn wirtualnych programu VMware.
  - **Etap 2**: uruchamianie trybu failover w lokacji lokalnej.
  - **Etap 3**. po niepomyślnym zakończeniu obciążeń należy ponownie włączyć replikację.

**Powrót po awarii programu VMware z platformy Azure**

![Powrót po awarii](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować odzyskiwanie po awarii dla serwerów fizycznych na platformie Azure, zapoznaj się z [przewodnikiem](physical-azure-disaster-recovery.md).

---
title: Architektura odzyskiwania po awarii maszyny wirtualnej VMware w Azure Site Recovery
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas konfigurowania odzyskiwania po awarii lokalnych maszyn wirtualnych VMware na platformie Azure przy użyciu Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 65778d0a6ba3bd5cdc719609ae4c2d18bf05aab9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424413"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architektura odzyskiwania po awarii oprogramowania VMware na platformę Azure

W tym artykule opisano architekturę i procesy używane podczas wdrażania replikacji odzyskiwania po awarii, trybu failover i odzyskiwania maszyn wirtualnych VMware między lokalną lokacją programu VMware i platformą Azure przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i grafika zawierają ogólny widok składników służących do odzyskiwania po awarii programu VMware do platformy Azure.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure, konto usługi Azure Storage na potrzeby pamięci podręcznej, dysku zarządzanego i sieci platformy Azure. | Zreplikowane dane z lokalnych maszyn wirtualnych są przechowywane w usłudze Azure Storage. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych podczas pracy w trybie failover z poziomu lokalnego na platformę Azure. Maszyny wirtualne platformy Azure nawiązują połączenie z siecią wirtualną platformy Azure, gdy są tworzone.
**Komputer serwera konfiguracji** | Pojedyncza maszyna lokalna. Firma Microsoft zaleca, aby uruchomić ją jako maszynę wirtualną VMware, którą można wdrożyć przy użyciu pobranego szablonu OVF.<br/><br/> Na maszynie działa wszystkie lokalne składniki Site Recovery, takie jak serwer konfiguracji, serwer przetwarzania oraz główny serwer docelowy. | **Serwer konfiguracji**: koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych.<br/><br/> **Serwer przetwarzania**: program jest instalowany domyślnie na serwerze konfiguracji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania; i wysyła je do usługi Azure Storage. Serwer przetwarzania instaluje także usługę Azure Site Recovery Mobility Service na maszynach wirtualnych, które będą replikowane, i przeprowadza automatycznie odnajdywanie lokalnych maszyn wirtualnych. Wraz z rozwojem wdrożenia można dodać dodatkowe, oddzielne serwery przetwarzania do obsługi większych woluminów ruchu związanego z replikacją.<br/><br/> **Główny serwer docelowy**: domyślnie zainstalowany na serwerze konfiguracji. Obsługuje dane replikacji podczas powrotu po awarii z platformy Azure. W przypadku dużych wdrożeń można dodać dodatkowy, oddzielny główny serwer docelowy na potrzeby powrotu po awarii.
**Serwery VMware** | Maszyny wirtualne VMware są hostowane na lokalnych serwerach vSphere ESXi. Zalecamy serwer vCenter do zarządzania hostami. | Podczas wdrażania Site Recovery należy dodać serwery VMware do magazynu Recovery Services.
**Zreplikowane maszyny** | Usługa mobilności jest instalowana na wszystkich zreplikowanych maszynach wirtualnych VMware. | Zalecamy, aby zezwalać na automatyczną instalację z serwera przetwarzania. Alternatywnie możesz zainstalować usługę ręcznie lub użyć zautomatyzowanej metody wdrażania, takiej jak Configuration Manager.

**Architektura VMware–Azure**

![Składniki](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Konfigurowanie wychodzącej łączności sieciowej

Aby Site Recovery działały zgodnie z oczekiwaniami, należy zmodyfikować wychodzącą łączność sieciową, aby umożliwić replikację danego środowiska.

> [!NOTE]
> Usługa Site Recovery nie obsługuje sterowania łącznością sieciową za pomocą uwierzytelniającego serwera proxy.

### <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych adresów URL:

| **Nazwa**                  | **Commercial**                               | **Instytucje rządowe**                                 | **Opis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Magazyn                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Umożliwia zapisanie danych z maszyny wirtualnej na koncie magazynu pamięci podręcznej znajdującym się w regionie źródłowym. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Umożliwia autoryzację i uwierzytelnianie przy użyciu adresów URL usługi Site Recovery. |
| Replikacja               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umożliwia komunikację między maszyną wirtualną a usługą Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umożliwia maszynie wirtualnej zapisywanie danych monitorowania i danych diagnostycznych usługi Site Recovery. |

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny wirtualnej rozpocznie się replikacja początkowa do usługi Azure Storage przy użyciu określonych zasad replikacji. Pamiętaj o następujących kwestiach:
    - W przypadku maszyn wirtualnych VMware replikacja jest na poziomie bloku, blisko ciągłego, przy użyciu agenta usługi mobilności uruchomionego na maszynie wirtualnej.
    - Zostaną zastosowane wszystkie ustawienia zasad replikacji:
        - **Próg punktu odzyskiwania**. To ustawienie nie ma wpływu na replikację. Ułatwia to monitorowanie. Zostanie zgłoszone zdarzenie i opcjonalnie zostanie wysłana wiadomość e-mail, jeśli bieżący cel punktu odzyskiwania przekroczy określony limit progu.
        - **Przechowywanie punktu odzyskiwania**. To ustawienie określa, jak daleko wstecz w czasie ma nastąpić przejście, gdy wystąpi zakłócenie. Maksymalne przechowywanie w magazynie w warstwie Premium wynosi 24 godziny. W przypadku magazynu w warstwie Standardowa jest 72 godzin. 
        - **Migawki spójne z aplikacjami**. Migawkę spójną na poziomie aplikacji można wykonać co 1 do 12 godzin, w zależności od potrzeb aplikacji. Migawki to standardowe migawki obiektów blob platformy Azure. Agent mobilności działający na maszynie wirtualnej żąda migawki VSS zgodnie z tym ustawieniem i zakładki wskazujące punkt w czasie jako punkt spójny aplikacji w strumieniu replikacji.

2. Ruch jest replikowany do publicznych punktów końcowych usługi Azure Storage za pośrednictwem Internetu. Alternatywnie możesz użyć usługi Azure ExpressRoute z usługą [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Replikowanie ruchu za pośrednictwem wirtualnej sieci prywatnej (VPN) typu lokacja-lokacja z lokacji lokalnej do platformy Azure nie jest obsługiwane.
3. Operacja replikacji początkowej zapewnia, że wszystkie dane na komputerze w momencie włączania replikacji są wysyłane do platformy Azure. Po zakończeniu replikacji początkowej zostanie rozpoczęta replikacja zmian różnicowych na platformie Azure. Śledzone zmiany dla maszyny są wysyłane do serwera przetwarzania.
4. Komunikacja odbywa się w następujący sposób:

    - Maszyny wirtualne komunikują się z lokalnym serwerem konfiguracji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.
    - Serwer konfiguracji organizuje replikację za pomocą usługi Azure over port HTTPS 443.
    - Maszyny wirtualne wysyłają dane replikacji do serwera przetwarzania (uruchomionego na komputerze serwera konfiguracji) na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
    - Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.
5. Dane replikacji są najpierw przechowywane na koncie magazynu pamięci podręcznej na platformie Azure. Te dzienniki są przetwarzane, a dane są przechowywane na dysku zarządzanym platformy Azure (wywoływana jako dysk inicjujący ASR). Na tym dysku są tworzone punkty odzyskiwania.

**Proces replikacji z programu VMware do platformy Azure**

![Proces replikacji](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Proces ponownej synchronizacji

1. Czasami podczas replikacji początkowej lub podczas przenoszenia zmian różnicowych mogą wystąpić problemy z łącznością sieciową między maszyną źródłową a serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure. Każdy z nich może prowadzić do błędów transferu danych na platformę Azure.
2. Aby uniknąć problemów z integralnością danych i zminimalizować koszty transferu danych, Site Recovery oznacza maszynę do ponownej synchronizacji.
3. Maszynę można także oznaczyć do ponownej synchronizacji w sytuacjach takich jak następujące, aby zachować spójność między maszyną źródłową i danymi przechowywanymi na platformie Azure
    - Wyłączenie wymuszonego wyłączenia komputera
    - Jeśli komputer przejdzie zmiany konfiguracji, takie jak zmiana rozmiaru dysku (modyfikacja rozmiaru dysku z 2 TB do 4 TB)
4. Ponowna synchronizacja wysyła tylko dane różnicowe na platformę Azure. Transfer danych między środowiskiem lokalnym i platformą Azure jest zminimalizowany przez Obliczanie sum kontrolnych danych między maszyną źródłową i danymi przechowywanymi na platformie Azure.
5. Domyślnie ponowna synchronizacja jest zaplanowana do automatycznego uruchamiania poza godzinami pracy. Jeśli nie chcesz czekać na domyślną ponowną synchronizację poza godzinami, możesz ponownie zsynchronizować maszynę wirtualną ręcznie. W tym celu przejdź do Azure Portal, wybierz maszynę wirtualną > ponownie **zsynchronizuj**.
6. Jeśli domyślna ponowna synchronizacja nie powiedzie się poza godzinami pracy, a wymagana jest ręczna interwencja, na konkretnym komputerze w Azure Portal zostanie wygenerowany błąd. Możesz rozwiązać ten problem i ręcznie wyzwolić ponowną synchronizację.
7. Po ukończeniu ponownej synchronizacji replikacja zmian różnicowych zostanie wznowiona.

## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

Po skonfigurowaniu replikacji i uruchomieniu funkcji drążenia odzyskiwania po awarii (test pracy w trybie failover), aby sprawdzić, czy wszystko działa zgodnie z oczekiwaniami, możesz uruchomić tryb failover i powrót po awarii, jak to konieczne.

1. Uruchamianie nie powiedzie się dla jednej maszyny lub można utworzyć plany odzyskiwania w celu jednoczesnego przełączenia wielu maszyn wirtualnych w tryb failover. Zaletą planu odzyskiwania zamiast pojedynczej pracy awaryjnej komputera jest:
    - Zależności aplikacji można modelować, dołączając wszystkie maszyny wirtualne w aplikacji w ramach jednego planu odzyskiwania.
    - Możesz dodawać skrypty, elementy Runbook platformy Azure i wstrzymywać je w ramach akcji ręcznych.
2. Po wyzwoleniu początkowej pracy w trybie failover należy zatwierdzić, aby rozpocząć uzyskiwanie dostępu do obciążenia z maszyny wirtualnej platformy Azure.
3. Po ponownym udostępnieniu podstawowej lokacji lokalnej można przygotować się do powrotu po awarii. W celu powrotu po awarii należy skonfigurować infrastrukturę powrotu po awarii, w tym:

    * **Tymczasowy serwer przetwarzania na platformie Azure**: aby powrócić po awarii z platformy Azure, należy skonfigurować maszynę wirtualną platformy Azure do działania jako serwer przetwarzania w celu obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
    * **Połączenie sieci VPN**: w celu powrotu po awarii wymagane jest połączenie sieci VPN (lub ExpressRoute) z sieci platformy Azure z lokacją lokalną.
    * **Oddzielny główny serwer docelowy**: domyślnie główny serwer docelowy, który został zainstalowany z serwerem konfiguracji na lokalnej maszynie wirtualnej VMware obsługuje powrót po awarii. Jeśli zachodzi potrzeba odtworzenia dużych ilości ruchu, należy skonfigurować w tym celu oddzielny lokalny główny serwer docelowy.
    * **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Te zasady są tworzone automatycznie podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
4. Gdy składniki są stosowane, powrót po awarii odbywa się w trzech akcjach:

    - Etap 1. Ponowne włączanie ochrony maszyn wirtualnych platformy Azure w celu replikowania ich z powrotem z platformy Azure do lokalnych maszyn wirtualnych programu VMware.
    -  Etap 2: uruchamianie trybu failover w lokacji lokalnej.
    - Etap 3. po niepomyślnym zakończeniu obciążeń ponowne włączenie replikacji lokalnych maszyn wirtualnych jest możliwe.
    
 
**Powrót po awarii programu VMware z platformy Azure**

![Powrót po awarii](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [tym samouczkiem](vmware-azure-tutorial.md) , aby włączyć replikację programu VMware do platformy Azure.

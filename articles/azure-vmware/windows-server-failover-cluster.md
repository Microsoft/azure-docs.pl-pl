---
title: Klaster trybu failover systemu Windows Server na platformie Azure VMware sieci vSAN z natywnymi dyskami udostępnionymi
description: Skonfiguruj usługę Windows Server Failover Clustering (WSFC) w rozwiązaniu Azure VMware i Skorzystaj z rozwiązań wymagających możliwości korzystania z usługi WSFC.
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: d667eef00fcad0e3f5243c6ab580e2e8371c6793
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518997"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Klaster trybu failover systemu Windows Server na platformie Azure VMware sieci vSAN z natywnymi dyskami udostępnionymi

Ten artykuł zawiera instrukcje dotyczące konfigurowania klastra trybu failover systemu Windows Server w rozwiązaniu VMware platformy Azure. Implementacja w tym artykule jest przeznaczona do sprawdzenia koncepcji i celów pilotażowych. Zalecamy używanie konfiguracji klastra w polu (CIB) do momentu udostępnienia zasad umieszczania.

Windows Server failover Cluster (WSFC), wcześniej znany jako usługa Microsoft Service Cluster Service (MSCS), jest funkcją systemu operacyjnego Windows Server (OS). WSFC to funkcja o kluczowym znaczeniu dla firmy, a w przypadku wielu aplikacji jest wymagane. Na przykład dla następujących konfiguracji jest wymagany program WSFC:

- Program SQL Server skonfigurowany jako:
  - Zawsze włączone wystąpienie klastra trybu failover (FCI) w celu zapewnienia wysokiej dostępności na poziomie wystąpienia.
  - Zawsze włączona Grupa dostępności (AG) dla wysokiej dostępności na poziomie bazy danych.
- Usługi plików systemu Windows:
  - Ogólny udział plików uruchomiony w aktywnym węźle klastra.
  - Scale-Out serwera plików (SOFS), w którym są przechowywane pliki w udostępnionych woluminach klastra (CSV).
  - Bezpośrednie miejsca do magazynowania (S2D); dyski lokalne używane do tworzenia pul magazynów w różnych węzłach klastra.

Klaster usługi WSFC można hostować w różnych wystąpieniach rozwiązań VMware platformy Azure, nazywanych klastrami (CAB). Klaster usługi WSFC można również umieścić w jednym węźle rozwiązania VMware platformy Azure. Ta konfiguracja jest znana jako klaster-a-Box (CIB). Nie zaleca się używania rozwiązania CIB dla implementacji produkcyjnej. Czy pojedynczy węzeł rozwiązania VMware platformy Azure zakończył się niepowodzeniem, wszystkie węzły klastra usługi WSFC zostaną wyłączone, a aplikacja będzie mogła spowodować przestoje. Rozwiązanie VMware firmy Azure wymaga co najmniej trzech węzłów w klastrze chmury prywatnej.

Ważne jest wdrożenie obsługiwanej konfiguracji usługi WSFC. Chcesz, aby Twoje rozwiązanie było obsługiwane w vSphere i z rozwiązaniem VMware platformy Azure. Program VMware zawiera szczegółowy dokument dotyczący usługi WSFC w systemie vSphere 6,7, [Instalatora dla klastra trybu failover i usługi klastrowania firmy Microsoft](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Ten artykuł koncentruje się na usłudze WSFC w systemie Windows Server 2016 i w systemie Windows Server 2019. Starsze wersje systemu Windows Server mają [nietypowe wsparcie](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) i dlatego nie są tu rozpatrywane w tym miejscu.

Musisz najpierw [utworzyć usługi WSFC](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster). Aby uzyskać więcej informacji na temat usługi WSFC, zobacz [klaster trybu failover w systemie Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview). Informacje zawarte w tym artykule dotyczą określonego wdrożenia usługi WSFC w rozwiązaniu VMware firmy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Środowisko rozwiązań VMware platformy Azure
- Nośnik instalacyjny systemu operacyjnego Microsoft Windows Server

## <a name="reference-architecture"></a>Architektura referencyjna

Rozwiązanie Azure VMware oferuje natywną obsługę zwirtualizowanych usług WSFC. Obsługuje ona trwałe rezerwacje SCSI (SCSI3PR) na poziomie dysku wirtualnego. Ta obsługa jest wymagana przez usługi WSFC do rozstrzygnięcia dostępu do udostępnionego dysku między węzłami. Obsługa SCSI3PRs umożliwia konfigurowanie usługi WSFC przy użyciu zasobu dyskowego, który jest udostępniany między maszynami wirtualnymi w magazynach danych sieci vSAN.

Na poniższym diagramie przedstawiono architekturę węzłów wirtualnych usługi WSFC w chmurze prywatnej rozwiązania Azure VMware. Pokazuje, gdzie znajduje się rozwiązanie Azure VMware, w tym serwery wirtualne usługi WSFC (czerwone pole) w odniesieniu do szerszej platformy platformy Azure. Na tym diagramie przedstawiono typową architekturę gwiazdy, ale jest możliwe zastosowanie podobnej konfiguracji w przypadku korzystania z wirtualnej sieci WAN platformy Azure. Obie te funkcje oferują wszystkie inne usługi platformy Azure.

[![Diagram przedstawiający architekturę węzłów wirtualnych usługi WSFC w chmurze prywatnej rozwiązania Azure VMware.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Obecnie obsługiwane są następujące konfiguracje:

- System Microsoft Windows Server 2012 lub nowszy.
- Maksymalnie pięć węzłów klastra trybu failover na klaster.
- Do czterech kart PVSCSI na maszynę wirtualną.
- Do 64 dysków na kartę PVSCSI.

## <a name="virtual-machine-configuration-requirements"></a>Wymagania dotyczące konfiguracji maszyny wirtualnej

### <a name="wsfc-node-configuration-parameters"></a>Parametry konfiguracji węzła usługi WSFC

- Zainstaluj najnowsze narzędzia VMware w każdym węźle usługi WSFC.
- Mieszanie dysków nieudostępnionych i udostępnionych na jednej wirtualnej karcie SCSI nie jest obsługiwane. Na przykład jeśli dysk systemowy (dysk C:) jest dołączony do SCSI0:0, pierwszy udostępniony dysk zostanie dołączony do SCSI1:0. Węzeł maszyny wirtualnej usługi WSFC ma ten sam wirtualny kontroler SCSI maksymalnie cztery wirtualne kontrolery SCSI (4).
- Identyfikatory SCSI dysków wirtualnych powinny być spójne między wszystkimi maszynami wirtualnymi obsługującymi węzły tego samego usługi WSFC.

| **Składnik** | **Wymagania** |
| --- | --- |
| Wersja sprzętu maszyny wirtualnej | 11 lub nowszy do obsługi vMotion na żywo. |
| Wirtualna karta sieciowa | VMXNET3 parawirtualne Network Interface Card (karta sieciowa); Włącz skalowanie po stronie odbierającej (RSS) systemu Windows w trybie gościnnym na wirtualnej karcie sieciowej. |
| Pamięć | Użyj pełnej pamięci rezerwacji maszyny wirtualnej dla węzłów w klastrze usługi WSFC. |
| Zwiększ limit czasu we/wy dla każdego węzła usługi WSFC. | Zmodyfikuj HKEY \_ lokalnego \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet do 60 sekund lub więcej. (Jeśli ponownie utworzysz klaster, ta wartość może zostać zresetowana do wartości domyślnej, więc należy ją zmienić ponownie). |
| Monitorowanie kondycji klastra systemu Windows | Wartość parametru SameSubnetThreshold monitorowania kondycji klastra systemu Windows musi zostać zmodyfikowana, aby umożliwić użycie 10 nieodebranych pulsów. Jest to [wartość domyślna w systemie Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). To zalecenie dotyczy wszystkich aplikacji korzystających z usługi WSFC, łącznie z dyskami udostępnionymi i nieudostępnionymi. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Parametry konfiguracji dysków rozruchowych w węźle WSFC


| **Składnik** | **Wymagania** |
| --- | --- |
| Typ kontrolera SCSI | LSI Logic SAS |
| Tryb dysku | Wirtualne |
| Udostępnianie magistrali SCSI | Brak |
| Zmodyfikuj ustawienia zaawansowane wirtualnego kontrolera SCSI obsługującego urządzenie rozruchowe. | Dodaj następujące ustawienia zaawansowane do każdego węzła usługi WSFC:<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSE"<br />Gdzie X jest numerem identyfikatora kontrolera magistrali SCSI urządzenia rozruchowego. Domyślnie X jest ustawiona na 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Parametry konfiguracji dysków udostępnionych węzła usługi WSFC


| **Składnik** | **Wymagania** |
| --- | --- |
| Typ kontrolera SCSI | VMware Paravirtualize (PVSCSI) |
| Tryb dysku | Niezależna — trwałe (krok 2 na ilustracji poniżej). Za pomocą tego ustawienia upewnij się, że wszystkie dyski są wykluczone z migawek. Migawki nie są obsługiwane w przypadku maszyn wirtualnych opartych na usłudze WSFC. |
| Udostępnianie magistrali SCSI | Fizyczne (krok 1 na ilustracji poniżej) |
| Flaga wieloskładnikowego modułu zapisywania | Nieużywane |
| Format dysku | Elastyczna obsługa administracyjna. (Eager o grubości zero (EZT) nie jest wymagana w przypadku sieci vSAN.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Zrzut ekranu przedstawiający stronę Edytowanie ustawień sprzętu wirtualnego.":::

## <a name="non-supported-scenarios"></a>Scenariusze nieobsługiwane

Następujące funkcje nie są obsługiwane w przypadku rozwiązania WSFC w systemie Azure VMware:

- Magazyny danych NFS
- Miejsca do magazynowania
- Sieci vSAN przy użyciu usługi iSCSI
- Sieci vSAN rozproszony klaster
- Ulepszona zgodność vMotion (projekt EVC)
- Odporność na uszkodzenia vSphere (FT)
- Migawki
- VMotion magazynu na żywo (online)
- N-port ID Virtualization (NPIV)

Aktywne zmiany sprzętu maszyny wirtualnej mogą zakłócać puls między węzłami usługi WSFC.

Następujące działania nie są obsługiwane i mogą spowodować przejście w tryb failover węzła usługi WSFC:

- Gorąca Dodawanie pamięci
- Gorące Dodawanie procesora
- Używanie migawek
- Zwiększanie rozmiaru dysku udostępnionego
- Wstrzymywanie i wznawianie stanu maszyny wirtualnej
- Nadmierna ilość pamięci — zobowiązania prowadzące do wymiany lub ESXinia pamięci maszyny wirtualnej
- Hot rozszerzając lokalny plik VMDK, nawet jeśli nie jest skojarzony z kontrolerem udostępniania magistrali SCSI

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Konfigurowanie usługi WSFC z dyskami udostępnionymi w usłudze Azure VMware Solution sieci vSAN

1. Upewnij się, że środowisko Active Directory jest dostępne.
2. Tworzenie maszyn wirtualnych w magazynie danych sieci vSAN.
3. Włącz wszystkie maszyny wirtualne, skonfiguruj nazwę hosta, adresy IP, Dołącz wszystkie maszyny wirtualne do domeny Active Directory i zainstaluj najnowsze dostępne aktualizacje systemu operacyjnego.
4. Zainstaluj najnowsze narzędzia VMware.
5. Włącz i skonfiguruj funkcję klastra trybu failover systemu Windows Server na każdej maszynie wirtualnej.
6. Skonfiguruj monitor klastra dla kworum (Monitor udziału plików działa prawidłowo).
7. Wyłącz wszystkie węzły klastra usługi WSFC.
8. Dodaj co najmniej jeden kontroler SCSI Paravirtual (do czterech) do każdej maszyny wirtualnej usługi WSFC. Użyj ustawień zgodnie z poprzednimi akapitami.
9. Na pierwszym węźle klastra Dodaj wszystkie zbędne dyski udostępnione przy użyciu opcji **Dodaj nowy**  >  **dysk twardy** urządzenia. Udostępnianie dysku powinno być pozostawione jako **nieokreślony** (domyślnie) i tryb dysku jako **niezależny**. Dołącz je do kontrolerów utworzonych w poprzednich krokach.
10. Kontynuuj pracę z pozostałymi węzłami usługi WSFC. Dodaj dyski utworzone w poprzednim kroku, wybierając pozycję **Dodaj nowy**  >  **istniejący dysk twardy**. Pamiętaj, aby zachować te same identyfikatory SCSI dysków we wszystkich węzłach usługi WSFC.
11. Włącz pierwszy węzeł usługi WSFC; Zaloguj się i Otwórz konsolę zarządzania dyskami (MMC). Upewnij się, że dodane dyski udostępnione mogą być zarządzane przez system operacyjny i są inicjowane. Sformatuj dyski i Przypisz literę dysku.
12. Włącz inne węzły usługi WSFC.
13. Dodaj dysk do klastra usługi WSFC przy użyciu **Kreatora dodawania dysku** i dodaj go do udostępniony wolumin klastra.
14. Przetestuj tryb failover za pomocą **Kreatora przenoszenia dysku** i upewnij się, że klaster usługi WSFC z udostępnionymi dyskami działa prawidłowo.
15. Uruchom **kreatora weryfikacji klastra** , aby potwierdzić, czy klaster i jego węzły działają prawidłowo.

    Ważne jest, aby zachować następujące określone elementy z testu walidacji klastra:

       - **Weryfikuj trwałą rezerwację miejsc do magazynowania**. Jeśli nie używasz funkcji miejsca do magazynowania w klastrze (na przykład w przypadku rozwiązania Azure VMware sieci vSAN), ten test nie ma zastosowania. Można zignorować wyniki testu rezerwacji trwałej miejsc do magazynowania, łącznie z tym ostrzeżeniem. Aby uniknąć ostrzeżeń, można wykluczyć ten test.
        
      - **Sprawdź poprawność komunikacji sieciowej**. Test sprawdzania poprawności klastra zgłosi ostrzeżenie, że dostępny jest tylko jeden interfejs sieciowy dla każdego węzła klastra. Można zignorować to ostrzeżenie. Rozwiązanie Azure VMware zapewnia wymaganą dostępność i wydajność, ponieważ węzły są połączone z jednym z segmentów NSX-T. Należy jednak zachować ten element w ramach testu walidacji klastra, ponieważ będzie on sprawdzać poprawność innych aspektów komunikacji sieciowej.

16. Utwórz regułę DRS, aby umieścić maszyny wirtualne usługi WSFC w tych samych węzłach rozwiązań VMware platformy Azure. W tym celu należy mieć regułę koligacji między hostami i MASZYNami wirtualnymi. W ten sposób węzły klastra zostaną uruchomione na tym samym hoście rozwiązań VMware platformy Azure. To polecenie jest przeznaczone do celów pilotażowych do momentu udostępnienia zasad umieszczania.

    >[!NOTE]
    > W tym celu należy utworzyć bilet żądania pomocy technicznej. Nasza organizacja pomocy technicznej systemu Azure będzie mogła Ci pomóc.

## <a name="related-information"></a>Informacje pokrewne

- [Klaster trybu failover w systemie Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Wytyczne dotyczące klastrowania Microsoft w systemie vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Informacje o konfiguracji klastra trybu failover i usługi klastrowania firmy Microsoft (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [Sieci vSAN 6,7 U3-WSFC z dyskami udostępnionymi &amp; (VMware.com) — trwałe rezerwacje SCSI](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Limity rozwiązań VMware platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu usługi WSFC w rozwiązaniu VMware platformy Azure, warto zapoznać się z tematem:

- Konfigurowanie nowej usługi WSFC przez dodanie większej liczby aplikacji, które wymagają możliwości usługi WSFC. Na przykład, SQL Server i SAP ASCS.
- Konfigurowanie rozwiązania do tworzenia kopii zapasowych.
  - [Konfigurowanie Azure Backup Server dla rozwiązania Azure VMware](https://docs.microsoft.com/azure/azure-vmware/set-up-backup-server-for-azure-vmware-solution)
  - [Rozwiązania do tworzenia kopii zapasowych dla maszyn wirtualnych rozwiązania Azure VMware](https://docs.microsoft.com/azure/azure-vmware/ecosystem-back-up-vms)

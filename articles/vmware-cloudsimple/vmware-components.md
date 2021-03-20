---
title: Składniki VMware chmury prywatnej
titleSuffix: Azure VMware Solution by CloudSimple
description: Dowiedz się, w jaki sposób usługa CloudSimple umożliwia natywne wdrażanie oprogramowania VMware w lokalizacjach platformy Azure. Chmury prywatne są zintegrowane z pozostałą częścią chmury platformy Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88142506"
---
# <a name="private-cloud-vmware-components"></a>Składniki VMware chmury prywatnej

Chmura prywatna to izolowany ze stosu oprogramowania VMware (hosty ESXi, vCenter, sieci vSAN i NSX) zarządzane przez serwer vCenter w domenie zarządzania.  Usługa CloudSimple umożliwia natywne wdrażanie oprogramowania VMware na platformie Azure bez systemu operacyjnego w lokalizacjach platformy Azure.  Chmury prywatne są zintegrowane z pozostałą częścią chmury platformy Azure.  Chmura prywatna jest wdrażana z następującymi składnikami stosu VMware:

* **VMware ESXi —** Funkcja hypervisor w dedykowanych węzłach platformy Azure
* **VMware vCenter —** Urządzenie do scentralizowanego zarządzania środowiskiem vSphere w chmurze prywatnej
* **VMware sieci vSAN —** Rozwiązanie infrastruktury z technologią Hyper-zbieżność
* **Centrum danych VMware NSX —** Oprogramowanie do wirtualizacji sieci i zabezpieczeń  

## <a name="vmware-component-versions"></a>Wersje składników VMware

Stos VMware chmury prywatnej jest wdrażany z użyciem następującej wersji oprogramowania.

| Składnik | Wersja | Licencjonowana wersja |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | Program vCenter Standard |
| Sieci vSAN | 6.7 | Przedsiębiorstwa |
| Centrum danych NSX | 2.4.1 | Zaawansowany |

## <a name="esxi"></a>ESXi

VMware ESXi jest instalowany w węzłach CloudSimple, gdy tworzysz chmurę prywatną.  ESXi udostępnia funkcję hypervisor do wdrażania maszyn wirtualnych obciążeń.  Węzły zapewniają infrastrukturę prywatną (obliczeniową i magazynową) o zbieżności funkcji Hyper-in.  Węzły są częścią klastra vSphere w chmurze prywatnej.  Każdy węzeł ma cztery interfejsy sieci fizycznych połączone z siecią underlay.  Dwa fizyczne interfejsy sieciowe są używane do tworzenia **przełącznika rozproszonego vSphere (VDS)** w programie vCenter i dwa są używane do tworzenia **wirtualnego przełącznika rozproszonego NSX (N-VDS)**.  Interfejsy sieciowe są konfigurowane w trybie aktywny-aktywny w celu zapewnienia wysokiej dostępności.

Dowiedz się więcej na VMware ESXi

## <a name="vcenter-server-appliance"></a>Server Appliance vCenter

Program vCenter Server Appliance (VCSA) zapewnia funkcje uwierzytelniania, zarządzania i aranżacji dla rozwiązań VMware przez CloudSimple. VCSA z osadzonym kontrolerem usług platformy (PSC) jest wdrażany podczas tworzenia chmury prywatnej.  VCSA jest wdrażana w klastrze vSphere tworzonym podczas wdrażania chmury prywatnej.  Każda chmura prywatna ma własne VCSA.  Rozszerzenie chmury prywatnej dodaje węzły do VCSA w chmurze prywatnej.

### <a name="vcenter-single-sign-on"></a>Logowanie jednokrotne vCenter

Osadzony kontroler usług platformy w systemie VCSA jest skojarzony z **domeną programu vCenter Single Sign-On**.  Nazwa domeny to **cloudsimple. Local**.  Tworzony jest domyślny użytkownik mający **CloudOwner@cloudsimple.com** dostęp do programu vCenter.  Możesz dodać [źródła tożsamości](set-vcenter-identity.md)lokalnego/usługi Azure Active Directory dla serwera vCenter.

## <a name="vsan-storage"></a>Magazyn sieci vSAN

Chmury prywatne są tworzone za pomocą w pełni skonfigurowanego magazynu sieci vSAN (all-flash) w klastrze.  Aby można było utworzyć klaster vSphere z magazynem danych sieci vSAN, wymagane są co najmniej trzy węzły tej samej jednostki SKU.  Deduplikacja i kompresja są domyślnie włączone w magazynie danych sieci vSAN.  Na każdym węźle klastra vSphere są tworzone dwie grupy dysków. Każda grupa dysków zawiera jeden dysk pamięci podręcznej i trzy dyski pojemności.

Domyślne zasady magazynu sieci vSAN są tworzone w klastrze vSphere i stosowane do magazynu danych sieci vSAN.  Te zasady określają, w jaki sposób obiekty magazynu maszyn wirtualnych są obsługiwane i alokowane w magazynie danych w celu zagwarantowania wymaganego poziomu usługi.  Zasady magazynu definiują **niepowodzenia (FTT)** i **metodę odporności na awarie**.  Można utworzyć nowe zasady magazynu i zastosować je do maszyn wirtualnych. Aby zachować umowę SLA, należy utrzymywać 25% pojemności zapasowej w magazynie danych sieci vSAN.  

### <a name="default-vsan-storage-policy"></a>Domyślne zasady magazynu sieci vSAN

W poniższej tabeli przedstawiono domyślne parametry zasad magazynu sieci vSAN.

| Liczba węzłów w klastrze vSphere | FTT | Metoda odporności na awarie |
|------------------------------------|-----|--------------------------|
| 3 i 4 węzły | 1 | RAID 1 (dublowanie) — tworzy 2 kopie |
| od 5 do 16 węzłów | 2 | RAID 1 (dublowanie) — tworzy 3 kopie |

## <a name="nsx-data-center"></a>Centrum danych NSX

NSX Data Center zapewnia wirtualizację sieci, mikrosegmenty i możliwości zabezpieczeń sieci w chmurze prywatnej.  Wszystkie usługi obsługiwane przez usługę NSX Data Center można skonfigurować w chmurze prywatnej za pomocą usługi NSX.  Podczas tworzenia chmury prywatnej są instalowane i konfigurowane następujące składniki NSX.

* NSXT Manager
* Strefy transportu
* Profil pasma hosta i granicy
* Przełącznik logiczny dla transportu brzegowego, EXT1 i EXT2
* Pula adresów IP dla węzła transportu ESXi
* Pula adresów IP dla węzła transportu brzegowego
* Węzły brzegowe
* DRS regułę antykoligacji dla kontrolerów i maszyn wirtualnych z systemem Edge
* Router warstwy 0
* Włącz protokół BGP na routerze tier0

## <a name="vsphere-cluster"></a>klaster vSphere

Hosty ESXi są skonfigurowane jako klaster, aby zapewnić wysoką dostępność chmury prywatnej.  Podczas tworzenia chmury prywatnej składniki zarządzania programu vSphere są wdrażane w pierwszym klastrze.  Pula zasobów jest tworzona dla składników zarządzania, a wszystkie maszyny wirtualne zarządzania są wdrażane w tej puli zasobów. Nie można usunąć pierwszego klastra, aby zmniejszyć chmurę prywatną.  klaster vSphere zapewnia wysoką dostępność maszyn wirtualnych korzystających z **VSPHERE ha**.  Niepowodzenia do tolerowania są zależne od liczby dostępnych węzłów w klastrze.  Można użyć formuły, ```Number of nodes = 2N+1``` gdzie ```N``` jest liczbą niepowodzeń, które mają być tolerowane.

### <a name="vsphere-cluster-limits"></a>limity klastrów vSphere

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej (pierwszy klaster vSphere) | 3 |
| Maksymalna liczba węzłów w klastrze vSphere w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Maksymalna liczba klastrów vSphere w chmurze prywatnej | 21 |
| Minimalna liczba węzłów w nowym klastrze vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury VMware

Czasami konieczne jest wprowadzenie zmian w konfiguracji infrastruktury VMware. Obecnie te interwały mogą wystąpić co 1-2 miesięcy, ale częstotliwość powinna odrzucać się w czasie. Ten typ konserwacji można zwykle wykonać bez przerywania normalnego użycia usług CloudSimple. W czasie konserwacji programu VMware następujące usługi nadal działają bez żadnego wpływu:

* Aplikacja i płaszczyzny zarządzania VMware
* dostęp vCenter
* Wszystkie sieci i magazyn
* Cały ruch platformy Azure

## <a name="updates-and-upgrades"></a>Aktualizacje i uaktualnienia

CloudSimple jest odpowiedzialny za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NSX) w chmurze prywatnej.

Aktualizacje oprogramowania obejmują:

* **Poprawki**. Poprawki zabezpieczeń lub poprawki błędów wydane przez oprogramowanie VMware.
* **Aktualizacje**. Zmiana wersji pomocniczej składnika stosu VMware.
* **Uaktualnienia**. Główna zmiana wersji składnika stosu VMware.

CloudSimple testuje krytyczną poprawkę zabezpieczeń, gdy tylko staną się dostępne z programu VMware. Zgodnie z umową SLA CloudSimple wprowadza poprawki zabezpieczeń do środowisk chmury prywatnej w ciągu tygodnia.

CloudSimple dostarcza kwartalne aktualizacje dla składników oprogramowania VMware. Po udostępnieniu nowej wersji głównej oprogramowania VMware CloudSimple współpracuje z klientami w celu koordynowania odpowiedniego okna obsługi w celu uaktualnienia.  

## <a name="next-steps"></a>Następne kroki

* [CloudSimple konserwacja i aktualizacje](cloudsimple-maintenance-updates.md)

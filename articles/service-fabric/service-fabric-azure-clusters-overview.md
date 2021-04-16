---
title: Tworzenie klastrów w systemach Windows Server i Linux
description: Service Fabric działają w systemach Windows Server i Linux. Aplikacje można wdrażać i hostować Service Fabric w dowolnym miejscu, w którym można uruchomić system Windows Server lub Linux.
services: service-fabric
documentationcenter: .net
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 4aed4ab38db9f8d8b95647b6662245c93778afed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520160"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Omówienie klastrów Service Fabric na platformie Azure
Klaster Service Fabric to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, w którym mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana węzłem klastra. Klastry można skalować do tysięcy węzłów. Jeśli dodasz nowe węzły do klastra, Service Fabric ponownie zrównoważyć repliki partycji usługi i wystąpienia w ramach zwiększonej liczby węzłów. Ogólna wydajność aplikacji zwiększa się i zmniejsza się poziom skwerowania dostępu do pamięci. Jeśli węzły w klastrze nie są używane wydajnie, można zmniejszyć liczbę węzłów w klastrze. Service Fabric ponownie ponownie zrównoważyć repliki partycji i wystąpienia w ramach zmniejszonej liczby węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

Typ węzła definiuje rozmiar, liczbę i właściwości zestawu węzłów (maszyn wirtualnych) w klastrze. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Typy węzłów służą do definiowania ról zestawu węzłów klastra, takich jak „fronton” lub „zaplecze”. Klaster może mieć więcej niż jeden typ węzła, ale podstawowy typ węzła musi obejmować co najmniej pięć maszyn wirtualnych w przypadku klastrów produkcyjnych (lub co najmniej trzy maszyny wirtualne w przypadku klastrów testowych). [Usługi systemowe Service Fabric](service-fabric-technical-overview.md#system-services) są umieszczane w węzłach podstawowego typu. 

## <a name="cluster-components-and-resources"></a>Składniki i zasoby klastra
Klaster Service Fabric na platformie Azure to zasób platformy Azure, który używa innych zasobów platformy Azure i wchodzi z nimi w interakcje:
* Maszyny wirtualne i wirtualne karty sieciowe
* zestawy skalowania maszyn wirtualnych
* sieci wirtualnych
* moduły równoważenia obciążenia
* konta magazynu
* publiczne adresy IP

![Service Fabric klastra][Image]

### <a name="virtual-machine"></a>Maszyna wirtualna
Maszyna [wirtualna,](../virtual-machines/index.yml) która jest częścią klastra, jest nazywana węzłem, chociaż technicznie węzeł klastra jest Service Fabric uruchomieniowym. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają właściwości, takie jak [właściwości umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Każda maszyna lub maszyna wirtualna ma usługę automatycznego uruchamiania *FabricHost.exe,* która rozpoczyna działanie w czasie rozruchu, a następnie uruchamia dwa pliki *wykonywalne,Fabric.exe* i *FabricGateway.exe*, które są elementem węzła. Wdrożenie produkcyjne to jeden węzeł na maszynę fizyczną lub wirtualną. W przypadku scenariuszy testowania można hostować wiele węzłów na jednej maszynie lub maszynie wirtualnej, uruchamiając wiele wystąpieńFabric.exe *i* *FabricGateway.exe.*

Każda maszyna wirtualna jest skojarzona z wirtualną kartą sieciową ,a każda karta sieciowa ma przypisany prywatny adres IP.  Maszyna wirtualna jest przypisywana do sieci wirtualnej i lokalnego równoważenia za pośrednictwem karty sieciowej.

Wszystkie maszyny wirtualne w klastrze są umieszczane w sieci wirtualnej.  Wszystkie węzły w tym samym zestawie skalowania/typie węzła są umieszczane w tej samej podsieci w sieci wirtualnej.  Te węzły mają tylko prywatne adresy IP i nie są adresowane bezpośrednio poza siecią wirtualną.  Klienci mogą uzyskać dostęp do usług w węzłach za pośrednictwem usługi Azure Load Balancer.

### <a name="scale-setnode-type"></a>Zestaw skalowania/typ węzła
Podczas tworzenia klastra należy zdefiniować co najmniej jeden typ węzła.  Węzły lub maszyny wirtualne w typie węzła mają taki sam rozmiar i cechy, takie jak liczba procesorów CPU, pamięć, liczba dysków i we/wy dysku.  Na przykład jeden typ węzła może być dla małych maszyn wirtualnych frontologu z portami otwartymi dla Internetu, a inny typ węzła może być dla dużych maszyn wirtualnych zadomowień, które przetwarzają dane. W klastrach platformy Azure każdy typ węzła jest mapowany na zestaw [skalowania maszyn wirtualnych](../virtual-machine-scale-sets/index.yml).

Zestawy skalowania mogą być używane do wdrażania kolekcji maszyn wirtualnych i zarządzania nimi jako zestawem. Każdy typ węzła, który definiujesz w klastrze usługi Azure Service Fabric, konfiguruje oddzielny zestaw skalowania. Środowisko Service Fabric uruchomieniowe jest inicjowane na każdej maszynie wirtualnej w zestawie skalowania przy użyciu rozszerzeń maszyn wirtualnych platformy Azure. Poszczególne typy węzłów można niezależnie skalować w górę lub w dół, zmieniać pojemność SKU systemu operacyjnego uruchomionego w każdym węźle klastra, otwierać różne zestawy portów i używać różnych metryk pojemności. Zestaw skalowania ma pięć [domen uaktualniania i](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) pięć [domen](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) błędów i może mieć do 100 maszyn wirtualnych.  Klastry ponad 100 węzłów tworzy się przez utworzenie wielu zestawów skalowania/typów węzłów.

> [!IMPORTANT]
> Wybór liczby typów węzłów dla klastra i właściwości poszczególnych typów węzłów (rozmiar, podstawowy, połączenie z Internetem, liczba maszyn wirtualnych itp.) jest ważnym zadaniem.  Aby uzyskać więcej informacji, przeczytaj [zagadnienia dotyczące planowania pojemności klastra.](service-fabric-cluster-capacity.md)

Aby uzyskać więcej informacji, [przeczytaj Service Fabric typów węzłów i zestawów skalowania maszyn wirtualnych.](service-fabric-cluster-nodetypes.md)

### <a name="azure-load-balancer"></a>Azure Load Balancer
Wystąpienia maszyn wirtualnych są przyłączone za usługą [Azure Load Balancer,](../load-balancer/load-balancer-overview.md)która jest skojarzona z publicznym adresem [IP](../virtual-network/public-ip-addresses.md) i etykietą DNS.  Podczas aprowizowania klastra *&lt; z nazwą klastra &gt;* jest nazwa DNS, nazwa klastra *&lt; &gt; . &lt; location &gt; .cloudapp.azure.com* to etykieta DNS skojarzona z usługą równoważenia obciążenia przed zestawem skalowania.

Maszyny wirtualne w klastrze mają tylko [prywatne adresy IP.](../virtual-network/private-ip-addresses.md)  Ruch zarządzania i ruch usługowy są kierowane za pośrednictwem publicznego równoważenia obciążenia.  Ruch sieciowy jest przekierowyny do tych maszyn za pośrednictwem reguł NAT (klienci łączą się z określonymi węzłami/wystąpieniami) lub reguł równoważenia obciążenia (ruch przechodzi do maszyn wirtualnych okrężnego).  Usługa równoważenia obciążenia ma skojarzony publiczny adres IP z nazwą DNS w formacie: *&lt; clustername &gt; . &lt; lokalizacja &gt; .cloudapp.azure.com*.  Publiczny adres IP to kolejny zasób platformy Azure w grupie zasobów.  W przypadku definiowania wielu typów węzłów w klastrze dla każdego typu węzła/zestawu skalowania jest tworzony usługa równoważenia obciążenia. Można też skonfigurować pojedynczy równoważenie obciążenia dla wielu typów węzłów.  Typ węzła podstawowego ma nazwę klastra etykiety DNS *&lt; &gt; . &lt; lokalizacja &gt; .cloudapp.azure.com*, inne typy węzłów mają etykietę DNS *&lt; clustername &gt; - &lt; nodetype &gt; . &lt; lokalizacja &gt; .cloudapp.azure.com*.

### <a name="storage-accounts"></a>Konta magazynu
Każdy typ węzła klastra jest obsługiwany przez konto [magazynu platformy Azure i](../storage/common/storage-introduction.md) dyski zarządzane.

## <a name="cluster-security"></a>Zabezpieczenia klastra
Klaster Service Fabric to zasób, który należy do Ciebie.  Twoim obowiązkiem jest zabezpieczenie klastrów, aby zapobiec nawiązywaniu z nimi połączenia przez nieautoryzowanych użytkowników. Bezpieczny klaster jest szczególnie ważny w przypadku uruchamiania obciążeń produkcyjnych w klastrze. 

### <a name="node-to-node-security"></a>Zabezpieczenia między węzłami
Zabezpieczenia między węzłami zabezpieczą komunikację między maszynami wirtualnych lub komputerami w klastrze. Ten scenariusz zabezpieczeń zapewnia, że tylko komputery z uprawnieniami do dołączania do klastra mogą uczestniczyć w hostowania aplikacji i usług w klastrze. Service Fabric używa certyfikatów X.509 do zabezpieczania klastra i zapewnienia funkcji zabezpieczeń aplikacji.  Certyfikat klastra jest wymagany do zabezpieczenia ruchu klastra i zapewnienia uwierzytelniania klastra i serwera.  Certyfikaty z podpisem własnym mogą być używane w przypadku klastrów testowych, ale do zabezpieczania klastrów produkcyjnych należy użyć certyfikatu z zaufanego urzędu certyfikacji.

Aby uzyskać więcej informacji, przeczytaj zabezpieczenia między [węzłami](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Zabezpieczenia między klientem a węzłem
Zabezpieczenia klient-węzeł uwierzytelniają klientów i pomagają zabezpieczyć komunikację między klientem a poszczególnymi węzłami w klastrze. Ten typ zabezpieczeń pomaga zagwarantować, że tylko autoryzowani użytkownicy będą mieć dostęp do klastra i aplikacji wdrożonych w klastrze. Klienci są jednoznacznie identyfikowani za pomocą poświadczeń zabezpieczeń certyfikatu X.509. Do uwierzytelniania klientów administratorów lub użytkowników w klastrze można użyć dowolnej liczby opcjonalnych certyfikatów klienta.

Oprócz certyfikatów klienta można skonfigurować Azure Active Directory do uwierzytelniania klientów w klastrze.

Aby uzyskać więcej informacji, [przeczytaj Zabezpieczenia między klientem i węzłem](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Kontrola dostępu oparta na rolach (RBAC) platformy Azure umożliwia przypisywanie precyzyjnej kontroli dostępu do zasobów platformy Azure.  Do subskrypcji, grup zasobów i zasobów można przypisać różne reguły dostępu.  Reguły RBAC platformy Azure są dziedziczone wzdłuż hierarchii zasobów, chyba że zostaną zastąpione na niższym poziomie.  Możesz przypisać dowolne grupy użytkowników lub użytkowników w usłudze AAD przy użyciu reguł RBAC platformy Azure, aby wyznaczeni użytkownicy i grupy mogą modyfikować klaster.  Aby uzyskać więcej informacji, zapoznaj się z [omówieniem kontroli RBAC na platformie Azure.](../role-based-access-control/overview.md)

Service Fabric obsługuje również kontrolę dostępu w celu ograniczenia dostępu do niektórych operacji klastra dla różnych grup użytkowników. Pomaga to zwiększyć bezpieczeństwo klastra. W przypadku klientów, którzy łączą się z klastrem, obsługiwane są dwa typy kontroli dostępu: Rola administratora i Rola użytkownika.  

Aby uzyskać więcej informacji, przeczytaj Service Fabric kontroli dostępu [opartej na rolach.](service-fabric-cluster-security.md#service-fabric-role-based-access-control)

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci 
Sieciowe grupy zabezpieczeń kontrolują ruch przychodzący i wychodzący podsieci, maszyny wirtualnej lub określonej karty sieciowej.  Domyślnie, gdy wiele maszyn wirtualnych jest umieszczanych w tej samej sieci wirtualnej, mogą komunikować się ze sobą za pośrednictwem dowolnego portu.  Jeśli chcesz ograniczyć komunikację między maszynami, możesz zdefiniować sieciowe sieciowe gs, aby segmentować sieć lub odizolować od siebie maszyny wirtualne.  Jeśli w klastrze istnieje wiele typów węzłów, możesz zastosować sieciowe grupy zasobów do podsieci, aby uniemożliwić komunikację między maszynami należącymi do różnych typów węzłów.  

Aby uzyskać więcej informacji, przeczytaj o [grupach zabezpieczeń](../virtual-network/network-security-groups-overview.md)

## <a name="scaling"></a>Skalowanie

Wymagania aplikacji zmieniają się w czasie. Może być konieczne zwiększenie zasobów klastra w celu zaspokojenia zwiększonego obciążenia aplikacji lub ruchu sieciowego albo zmniejszenie zasobów klastra, gdy zapotrzebowanie spada. Po utworzeniu Service Fabric klastra klastra można go skalować w poziomie (zmienić liczbę węzłów) lub w pionie (zmienić zasoby węzłów). Klaster można skalować w dowolnym momencie, nawet wtedy, gdy obciążenia są uruchomione w klastrze. W przypadku skalowania klastra aplikacje również są automatycznie skalowane.

Aby uzyskać więcej informacji, zobacz [Scaling Azure clusters (Skalowanie klastrów platformy Azure).](service-fabric-cluster-scaling.md)

## <a name="upgrading"></a>Uaktualnianie
Klaster usługi Azure Service Fabric to zasób, który należy do Ciebie, ale jest częściowo zarządzany przez firmę Microsoft. Firma Microsoft jest odpowiedzialna za stosowanie poprawek do bazowego systemu operacyjnego i wykonywanie Service Fabric uaktualnień środowiska uruchomieniowego w klastrze. Możesz ustawić klaster tak, aby odbierał automatyczne uaktualnienia środowiska uruchomieniowego, gdy firma Microsoft wyda nową wersję, lub wybrać obsługiwaną wersję środowiska uruchomieniowego. Oprócz uaktualnień środowiska uruchomieniowego można również zaktualizować konfigurację klastra, taką jak certyfikaty lub porty aplikacji.

Aby uzyskać więcej informacji, przeczytaj [uaktualnianie klastrów](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Aby uzyskać dodatkowe [informacje, zobacz Obsługiwane](./service-fabric-versions.md) wersje na platformie Azure


## <a name="next-steps"></a>Następne kroki
Przeczytaj więcej na [temat zabezpieczania,](service-fabric-cluster-security.md) [skalowania](service-fabric-cluster-scaling.md)i [uaktualniania](service-fabric-cluster-upgrade.md) klastrów platformy Azure.

Dowiedz się [więcej Service Fabric pomocy technicznej.](service-fabric-support.md)

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG
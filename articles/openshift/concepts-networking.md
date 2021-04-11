---
title: Koncepcje — diagram sieci dla systemu Azure Red Hat w systemie OpenShift 4
description: Diagram sieciowy i Omówienie sieci Azure Red Hat OpenShift Networking
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720906"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Pojęcia dotyczące sieci na platformie Azure Red Hat OpenShift (ARO)

Ten przewodnik obejmuje omówienie sieci na platformie Azure Red Hat OpenShift na klastrach OpenShift 4, a także diagram i listę ważnych punktów końcowych. Aby uzyskać więcej informacji o podstawowych pojęciach dotyczących sieci OpenShift, zobacz [dokumentację dotyczącą obsługi sieci na platformie Azure Red Hat OpenShift 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Diagram sieciowy usługi Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Po wdrożeniu usługi Azure Red Hat w systemie OpenShift 4 cały klaster jest zawarty w sieci wirtualnej. W ramach tej sieci wirtualnej węzły główne i procesy robocze znajdują się na żywo w ich własnej podsieci. Każda podsieć używa wewnętrznego modułu równoważenia obciążenia i publicznego modułu równoważenia obciążenia.

## <a name="networking-components"></a>Składniki sieciowe

Poniższa lista obejmuje ważne składniki sieci w klastrze Red Hat OpenShift platformy Azure.

* **ARO — innych obszarów roboczych**
    * Jest to punkt końcowy linku prywatnego platformy Azure używany przez inżynierów firmy Microsoft i Red Hat site niezawodność do zarządzania klastrem.
* **ARO — wewnętrzna — lb**
    * Ten punkt końcowy równoważy ruch do serwera interfejsu API. W przypadku tego modułu równoważenia obciążenia węzły główne znajdują się w puli zaplecza.
* **ARO — Public-lb**
    * Jeśli interfejs API jest publiczny, ten punkt końcowy kieruje i równoważy ruch do serwera interfejsu API. Ten punkt końcowy przypisuje publiczny wychodzący adres IP, aby wzorce mogły uzyskać dostęp do Azure Resource Manager i ponownie raportować kondycję klastra.
* **ARO (wewnętrzne)**
    * Ten punkt końcowy równoważy ruch usługi wewnętrznej. W przypadku tego modułu równoważenia obciążenia węzły procesu roboczego znajdują się w puli zaplecza.
    * Ten moduł równoważenia obciążenia nie jest domyślnie tworzony. Ten moduł równoważenia obciążenia jest tworzony po utworzeniu usługi typu równoważenia obciążenia z prawidłowymi adnotacjami. Na przykład: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **ARO — wewnętrzna — lb**
    * Ten punkt końcowy jest używany dla każdego ruchu publicznego. Podczas tworzenia aplikacji i trasy są to ścieżki ruchu przychodzącego.
    * Ten moduł równoważenia obciążenia obejmuje również wychodzące połączenia z Internetu z dowolnego poziomu działającego w węzłach procesu roboczego za pośrednictwem reguł Azure Load Balancer wychodzących.
        * Obecnie nie można konfigurować reguł dla ruchu wychodzącego. Przydzielają one porty TCP 1 024 do każdego węzła.
        * DisableOutboundSnat nie jest skonfigurowana w regułach równoważenia obciążenia, więc w przypadku wszystkich publicznych adresów IP skonfigurowanych w tym ALB.
        * W wyniku dwóch poprzednich punktów jedynym sposobem dodawania tymczasowych portów podłączania jest dodanie publicznych usług typu modułu równoważenia obciążenia do wersji ARO.
* **ARO-wyjście-PIP**
    * Ten punkt końcowy służy jako publiczny adres IP (PIP) dla węzłów procesu roboczego.
    * Ten punkt końcowy umożliwia usługom dodanie określonego adresu IP pochodzącego z klastra Red Hat OpenShift platformy Azure do listy dozwolonych.
* **ARO — sieciowej grupy zabezpieczeń**
    * Po udostępnieniu usługi interfejs API tworzy regułę w tej sieciowej grupie zabezpieczeń, tak aby ruch przepływał i docierał do płaszczyzny kontroli i węzłów.
    * Domyślnie ta sieciowa Grupa zabezpieczeń zezwala na cały ruch wychodzący. Obecnie ruch wychodzący może być ograniczony tylko do płaszczyzny kontroli OpenShift na platformie Azure Red Hat.
* **ARO-controlplane-sieciowej grupy zabezpieczeń**
  * Ten punkt końcowy zezwala tylko na ruch przychodzący przez port 6443 dla węzłów głównych.
* **Azure Container Registry**
    * Jest to rejestr kontenerów, który jest używany wewnętrznie przez firmę Microsoft. Ten rejestr jest tylko do odczytu i nie jest przeznaczony do użycia przez użytkowników systemu Red Hat OpenShift.
        * Ten Rejestr zawiera obrazy platformy hosta i składniki klastra. Na przykład monitorowanie i rejestrowanie kontenerów.
        * Połączenia z tym rejestrem odbywają się za pośrednictwem punktu końcowego usługi (łączność wewnętrzna między usługami platformy Azure).
        * Ten rejestr wewnętrzny nie jest domyślnie dostępny poza klastrem.
* **Link prywatny**
    * Umożliwia łączność sieciową od płaszczyzny zarządzania do klastra dla inżynierów niezawodności firmy Microsoft i Red Hat w celu ułatwienia zarządzania klastrem.

## <a name="networking-policies"></a>Zasady dotyczące sieci

* Ruch przychodzący **: zasady** dotyczące sieci danych przychodzących są obsługiwane w ramach [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Ta zasada sieciowa jest domyślnie włączona i wymuszanie jest wykonywane przez użytkowników. Zasady dotyczące sieci danych przychodzących są zgodne z V1 NetworkPolicy, ale typy wychodzące i IPBlock nie są obsługiwane.

* Ruch wychodzący **: zasady** sieci danych wychodzących są obsługiwane za pomocą funkcji [Zapora ruchu](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) wychodzącego w OpenShift. Istnieje tylko jedna zasada ruchu wychodzącego dla przestrzeni nazw/projektu. Zasady ruchu wychodzącego nie są obsługiwane w przestrzeni nazw "default" i są oceniane w kolejności (od pierwszego do ostatniego).

## <a name="networking-basics-in-openshift"></a>Podstawowe informacje o sieci w OpenShift

OpenShift Networking defined Network [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) służy do konfigurowania sieci nakładki przy użyciu otwartego przełącznika VSwitch [(OVS)](https://www.openvswitch.org/), implementacji OpenFlow opartej na specyfikacji interfejsu sieciowego kontenera (CNI). SDN obsługuje różne wtyczki — zasady sieciowe są wtyczkami używanymi w systemie Red Hat na platformie OpenShift 4. Cała komunikacja sieciowa jest zarządzana przez SDN, więc do sieci wirtualnych nie są potrzebne żadne dodatkowe trasy, aby osiągnąć komunikację pod względem.

## <a name="networking--for-azure-red-hat-openshift"></a>Obsługa sieci na platformie Azure Red Hat OpenShift

Następujące funkcje sieciowe są specyficzne dla systemu Azure Red Hat OpenShift:  
* Użytkownicy mogą utworzyć swój klaster ARO w istniejącej sieci wirtualnej lub utworzyć sieć wirtualną podczas tworzenia klastra.
* W przypadku routingu CIDR sieci usług i usługi można konfigurować.
* Węzły i wzorce znajdują się w różnych podsieciach.
* Węzły i główne podsieci sieci wirtualnej powinny być minimalne/27.
* Domyślna wartość CIDR (pod) to 10.128.0.0/14.
* Domyślny CIDR usługi jest 172.30.0.0/16.
* CIDR i sieciowe usługi nie powinny nakładać się na inne zakresy adresów używane w sieci i nie mogą należeć do zakresu adresów IP sieci wirtualnej w klastrze.
* Wartość CIDR powinna mieć rozmiar minimum/18. (Sieć pod nie obsługuje routingu IP i jest używana tylko wewnątrz OpenShift SDN).
* Każdy węzeł jest przydzielony/23 podsieci (512 adresów IP) dla swoich zasobników. Tej wartości nie można zmienić.
* Nie można dołączyć pod do wielu sieci.
* Nie można skonfigurować statycznego adresu IP dla ruchu wychodzącego. (Jest to funkcja OpenShift. Aby uzyskać więcej informacji, zobacz [Konfigurowanie adresów IP ruchu](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)wychodzącego).

## <a name="network-settings"></a>Ustawienia sieciowe

Następujące ustawienia sieciowe są dostępne dla klastrów usługi Azure Red Hat OpenShift 4:

* **Widoczność interfejsu API** — Ustaw widoczność interfejsu API podczas uruchamiania [polecenia AZ ARO Create](tutorial-create-cluster.md#create-the-cluster).
    * "Publiczny" — serwer interfejsu API jest dostępny dla sieci zewnętrznych.
    * "Prywatny" — serwer interfejsu API przypisany do prywatnego adresu IP z podsieci Master, dostępny tylko przy użyciu połączonych sieci (równorzędne sieci wirtualne, inne podsieci w klastrze). W imieniu klienta zostanie utworzona prywatna strefa DNS.
* **Widoczność** transferu danych przychodzących — Ustaw widoczność interfejsu API podczas uruchamiania [polecenia AZ ARO Create](tutorial-create-cluster.md#create-the-cluster).
    * Trasy "Public" domyślnie będą usługa Load Balancer w warstwie Standardowa publicznego (można to zmienić).
    * Trasy "prywatne" będą domyślnie kierowane do wewnętrznego modułu równoważenia obciążenia (można to zmienić).

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Sieciowe grupy zabezpieczeń są tworzone w grupie zasobów węzła, która jest zablokowana dla użytkowników. Sieciowe grupy zabezpieczeń są przypisywane bezpośrednio do podsieci, a nie na kartach sieciowych węzła. Sieciowe grupy zabezpieczeń są niezmienne, a użytkownicy nie mają uprawnień do ich zmiany.

Publicznie widoczny serwer interfejsu API nie pozwala na tworzenie sieciowych grup zabezpieczeń i przypisywanie ich do kart sieciowych.

## <a name="domain-forwarding"></a>Przekazywanie domen
W systemie Azure Red Hat OpenShift jest używanych CoreDNS. Można skonfigurować przekazywanie domen. Nie można przenieść własnego serwera DNS do sieci wirtualnych. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [korzystania z przekazywania DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Co nowego w programie OpenShift 4,5

Dzięki obsłudze OpenShift 4,5 w systemie Azure Red Hat OpenShift wprowadzono kilka znaczących zmian w architekturze. Te zmiany dotyczą tylko nowo utworzonych klastrów z systemem OpenShift 4,5. Istniejące klastry uaktualnione do OpenShift 4,5 nie będą miały architektury sieci zmienionej przez proces uaktualniania. Użytkownicy będą musieli ponownie utworzyć swoje klastry, aby korzystać z tej nowej architektury.

![Diagram sieciowy usługi Azure Red Hat OpenShift 4,5](./media/concepts-networking/aro-4-5-networking-diagram.png)

Jak przedstawiono na powyższym diagramie, zauważysz, że wprowadzono kilka zmian:
* Wcześniej wykorzystano dwa publiczne LoadBalancers: jeden dla serwera interfejsu API i jeden dla puli węzłów procesu roboczego. Ta aktualizacja architektury została skonsolidowana w ramach jednego modułu równoważenia obciążenia. 
* Aby zmniejszyć złożoność, zostały usunięte dedykowane zasoby adresów IP.
* Płaszczyzna kontroli wyaro teraz udostępnia tę samą siećową grupę zabezpieczeń co węzły procesu roboczego ARO.

Aby uzyskać więcej informacji na temat OpenShift 4,5, zapoznaj się z [informacjami o wersji OpenShift 4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat ruchu wychodzącego i usługi Azure Red Hat OpenShift obsługiwanej przez funkcję transferu danych wychodzących, zobacz dokumentację [zasad obsługi](support-policies-v4.md) .

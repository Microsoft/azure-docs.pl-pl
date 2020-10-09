---
title: Koncepcje — diagram sieci dla systemu Azure Red Hat w systemie OpenShift 4
description: Diagram sieciowy i Omówienie sieci Azure Red Hat OpenShift Networking
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87419975"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Sieć na platformie Azure Red Hat w systemie OpenShift 4

Ten przewodnik obejmuje omówienie sieci na platformie Azure Red Hat w klastrach OpenShift 4, a także diagram i listę ważnych punktów końcowych.

Aby uzyskać więcej informacji o podstawowych pojęciach dotyczących sieci OpenShift, zobacz [dokumentację dotyczącą obsługi sieci na platformie Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Pojęcia dotyczące sieci na platformie Azure Red Hat OpenShift

![Diagram sieciowy usługi Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Po wdrożeniu usługi Azure Red Hat w systemie OpenShift 4 cały klaster jest zawarty w sieci wirtualnej. W ramach tej sieci wirtualnej węzły główne i procesy robocze znajdują się na żywo w ich własnej podsieci. Każda podsieć używa publicznego i wewnętrznego modułu równoważenia obciążenia.

## <a name="explanation-of-endpoints"></a>Wyjaśnienie punktów końcowych

Poniższa lista obejmuje ważne punkty końcowe w klastrze Red Hat OpenShift platformy Azure.

* **ARO — innych obszarów roboczych**
    * Jest to punkt końcowy linku prywatnego platformy Azure używany przez inżynierów niezawodności firmy Microsoft i Red Hat w celu ułatwienia zarządzania klastrem.
* **ARO — wewnętrzna — lb**
    * Ten punkt końcowy równoważy ruch do serwera interfejsu API. W przypadku tego modułu równoważenia obciążenia węzły główne znajdują się w puli zaplecza.
* **ARO — Public-lb**
    * Jeśli interfejs API jest publiczny, ten punkt końcowy kieruje i równoważy ruch do serwera interfejsu API. Ten punkt końcowy przypisuje publiczny wychodzący adres IP, aby wzorce mogły uzyskać dostęp do Azure Resource Manager i ponownie raportować kondycję klastra.
* **ARO (wewnętrzne)**
    * Ten punkt końcowy równoważy ruch usługi wewnętrznej. W przypadku tego modułu równoważenia obciążenia węzły procesu roboczego znajdują się w puli zaplecza.
    * Ten moduł równoważenia obciążenia nie jest domyślnie tworzony. Ten moduł równoważenia obciążenia jest tworzony po utworzeniu usługi typu równoważenia obciążenia z prawidłowymi adnotacjami. Na przykład: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Zasady sieciowe (ruch przychodzący)**
    * Obsługiwane jako część OpenShift SDN
    * Włączone domyślnie przez wymuszanie wykonywane przez klientów
    * Zgodne z V1 NetworkPolicy, jednak typy "wychodzące i IPBlock" nie są jeszcze obsługiwane
    * **firmy**
    * Ten punkt końcowy równoważy ruch do serwera interfejsu API. W przypadku tego modułu równoważenia obciążenia węzły główne znajdują się w puli zaplecza.
  * **ARO — wewnętrzna — lb**
    * Ten punkt końcowy jest używany dla każdego ruchu publicznego. Podczas tworzenia aplikacji i trasy są to ścieżki ruchu przychodzącego.
    * Ten moduł równoważenia obciążenia obejmuje również wychodzące połączenia z Internetu z dowolnego poziomu działającego w węzłach procesu roboczego za pośrednictwem reguł Azure Load Balancer wychodzących.
        * Obecnie nie można konfigurować reguł dla ruchu wychodzącego. Przydzielają one porty TCP 1 024 do każdego węzła.
        * DisableOutboundSnat nie jest skonfigurowana w regułach równoważenia obciążenia, więc w przypadku wszystkich publicznych adresów IP skonfigurowanych w tym ALB.
        * W wyniku dwóch poprzednich punktów jedynym sposobem dodawania tymczasowych portów podłączania jest dodanie publicznych usług typu modułu równoważenia obciążenia do wersji ARO.
* **Zasady sieciowe (ruch wychodzący)**
    * Zasady ruchu wychodzącego są obsługiwane za pomocą funkcji zapory ruchu wychodzącego w OpenShift.
    * Tylko jeden na przestrzeń nazw/projekt.
    * Zasady ruchu wychodzącego nie są obsługiwane w przestrzeni nazw "default".
    * Reguły zasad ruchu wychodzącego są oceniane w kolejności (od pierwszej do ostatniej).
    * **ARO-wyjście-PIP**
        * Ten punkt końcowy służy jako publiczny adres IP (PIP) dla węzłów procesu roboczego.
        * Ten punkt końcowy umożliwia usługom dodanie określonego adresu IP pochodzącego z klastra Red Hat OpenShift platformy Azure do listy dozwolonych.
* **ARO-Node-sieciowej grupy zabezpieczeń**
    * Po udostępnieniu usługi interfejs API tworzy regułę w tej sieciowej grupie zabezpieczeń, aby ruch przepływał i docierał do węzłów.
    * Domyślnie ta sieciowa Grupa zabezpieczeń zezwala na cały ruch wychodzący. Obecnie ruch wychodzący może być ograniczony tylko do płaszczyzny kontroli OpenShift na platformie Azure Red Hat.
* **ARO-controlplane-sieciowej grupy zabezpieczeń**
    * Ten punkt końcowy zezwala tylko na ruch przychodzący przez port 6443 dla węzłów głównych.
* **Azure Container Registry**
    * Jest to rejestr kontenerów, który jest używany wewnętrznie przez firmę Microsoft.
        * Ten Rejestr zawiera obrazy platformy hosta i składniki klastra. Na przykład monitorowanie i rejestrowanie kontenerów.
        * Nie jest przeznaczone do użycia przez klientów usługi Azure Red Hat OpenShift.  
        * Tylko do odczytu.
        * Połączenia z tym rejestrem odbywają się za pośrednictwem punktu końcowego usługi (łączność wewnętrzna między usługami platformy Azure).
        * Ten rejestr wewnętrzny nie jest domyślnie dostępny poza klastrem.
* **Link prywatny**
    * Umożliwia łączność sieciową od płaszczyzny zarządzania do klastra w celu zarządzania klastrem.
    * Inżynierowie niezawodności witryn firmy Microsoft i Red Hat mogą pomóc w zarządzaniu klastrem.

## <a name="networking-basics-in-openshift"></a>Podstawowe informacje o sieci w OpenShift

OpenShift Networking defined Network (SDN) służy do konfigurowania sieci nakładki przy użyciu otwartego przełącznika vSwitch (OVS), implementacji OpenFlow opartej na specyfikacji interfejsu sieciowego kontenera (CNI). SDN obsługuje różne wtyczki, a zasady sieciowe są wtyczkami używanymi na platformie Azure Red Hat OpenShift 4. Cała komunikacja sieciowa jest zarządzana przez SDN, więc do sieci wirtualnych nie są potrzebne żadne dodatkowe trasy, aby osiągnąć komunikację pod względem.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Specyficzne dla platformy Azure Red Hat OpenShift Networking

Następujące funkcje są specyficzne dla systemu Azure Red Hat OpenShift:
* Korzystanie z własnej sieci wirtualnej jest obsługiwane.
* W przypadku routingu CIDR sieci usług i usługi można konfigurować.
* Węzły i wzorce znajdują się w różnych podsieciach.
* Węzły i główne podsieci sieci wirtualnej powinny być minimalne/27.
* Wartość CIDR powinna mieć wartość minimum/18 (sieć pod nie obsługuje adresów IP bez obsługi routingu i jest używana tylko wewnątrz OpenShift SDN).
* Każdy węzeł jest przydzielony/23 podsieci (512 adresów IP) dla swoich zasobników. Tej wartości nie można zmienić.
* Nie można dołączyć pod do wielu sieci.
* Nie można skonfigurować statycznego adresu IP dla ruchu wychodzącego. (Jest to funkcja OpenShift. Aby uzyskać więcej informacji, zobacz [Konfigurowanie adresów IP ruchu](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)wychodzącego).

## <a name="network-settings"></a>Ustawienia sieciowe

Następujące ustawienia sieciowe są dostępne na platformie Azure Red Hat OpenShift 4:

* **Widoczność interfejsu API** — Ustaw widoczność interfejsu API podczas uruchamiania [polecenia AZ ARO Create](tutorial-create-cluster.md#create-the-cluster).
    * "Publiczny" — serwer interfejsu API jest dostępny dla sieci zewnętrznych.
    * "Prywatny" — serwer interfejsu API przypisany do prywatnego adresu IP z podsieci Master, dostępny tylko przy użyciu połączonych sieci (równorzędne sieci wirtualne, inne podsieci w klastrze). W imieniu klienta zostanie utworzona prywatna strefa DNS.
* **Widoczność** transferu danych przychodzących — Ustaw widoczność interfejsu API podczas uruchamiania [polecenia AZ ARO Create](tutorial-create-cluster.md#create-the-cluster).
    * Trasy "Public" będą domyślnie kierowane do publicznej usługi Azure usługa Load Balancer w warstwie Standardowa (można to zmienić).
    * Trasy "prywatne" będą domyślnie kierowane do wewnętrznego modułu równoważenia obciążenia (można to zmienić).

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Sieciowe grupy zabezpieczeń zostaną utworzone w grupie zasobów węzła, która jest zablokowana. Sieciowe grupy zabezpieczeń są przypisywane bezpośrednio do podsieci, a nie na kartach sieciowych węzła. Sieciowe grupy zabezpieczeń są niezmienne, co oznacza, że nie masz uprawnień do ich zmiany. 

Jednak przy użyciu publicznie widocznego serwera interfejsu API nie można tworzyć sieciowych grup zabezpieczeń i przypisywać ich do kart sieciowych.

## <a name="domain-forwarding"></a>Przekazywanie domen
W systemie Azure Red Hat OpenShift jest używanych CoreDNS. Można skonfigurować przekazywanie domen (zobacz dokumentację dotyczącą [korzystania z przekazywania DNS](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) , aby uzyskać więcej informacji).

Obecnie nie można przenieść własnego systemu DNS do sieci wirtualnych.


Aby uzyskać więcej informacji na temat ruchu wychodzącego i usługi Azure Red Hat OpenShift obsługiwanej przez funkcję transferu danych wychodzących, zobacz dokumentację [zasad obsługi](support-policies-v4.md) .

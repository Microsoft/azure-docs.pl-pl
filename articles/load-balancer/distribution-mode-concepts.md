---
title: Azure Load Balancer tryby dystrybucji
description: Rozpocznij naukę w różnych trybach dystrybucji Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99551319"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer tryby dystrybucji

Azure Load Balancer obsługuje dwa tryby dystrybucji dla połączeń routingu z aplikacją ze zrównoważonym obciążeniem:

* Na podstawie skrótu
* Koligacja źródłowego adresu IP

## <a name="hash-based"></a>Na podstawie skrótu

Domyślny tryb dystrybucji dla usługi Azure Load Balancer to skrót krotki z pięcioma elementami. 

Krotka składa się z:
* **Źródłowy adres IP**
* **Port źródłowy**
* **Docelowy adres IP**
* **Port docelowy**
* **Typ protokołu**

Skrót jest używany do mapowania ruchu do dostępnych serwerów. Algorytm zawiera lepkość tylko w ramach sesji transportu. Pakiety znajdujące się w tej samej sesji są kierowane do tego samego adresu IP centrum danych za punktem końcowym ze zrównoważonym obciążeniem. Gdy klient uruchamia nową sesję z tego samego źródłowego adresu IP, port źródłowy zmieni się i spowoduje, że ruch przejdzie do innego punktu końcowego centrum danych.

![Tryb dystrybucji oparty na skrócie pięciu krotek](./media/distribution-mode-concepts/load-balancer-distribution.png)

Tryb oparty na skrócie ma jeden typ konfiguracji:

* **Brak (oparte na skrócie)** — określa, że kolejne żądania z tego samego klienta mogą być obsługiwane przez dowolną maszynę wirtualną.

## <a name="source-ip-affinity"></a>Koligacja źródłowego adresu IP

Ten tryb dystrybucji jest również znany jako koligacja sesji lub koligacja adresów IP klienta. Tryb używa skrótu z dwiema kolekcjami (źródłowy adres IP i docelowy adres IP) lub z trzema kolekcjami (źródłowy adres IP, docelowy adres IP i typ protokołu), aby mapować ruch do dostępnych serwerów. 

Korzystając z koligacji źródłowego adresu IP, połączenia uruchamiane z tego samego komputera klienckiego są przekazywane do tego samego punktu końcowego centrum danych.

Na poniższej ilustracji przedstawiono konfigurację z dwoma spójnymi kolekcjami. Zauważ, że dwie kolekcje są uruchamiane za pomocą modułu równoważenia obciążenia do maszyny wirtualnej 1 (VM1). Kopia zapasowa VM1 jest następnie wykonywana przez VM2 i VM3.

![Tryb dystrybucji koligacji sesji dwóch krotek](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Tryb koligacji źródłowego adresu IP ma dwa typy konfiguracji:

* **Adres IP klienta (koligacja źródłowego adresu IP 2-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną.
* **IP i protokół klienta (koligacja źródłowego adresu IP 3-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta i kombinacji protokołów będą obsługiwane przez tę samą maszynę wirtualną.

## <a name="use-cases"></a>Przypadki zastosowań

Koligacja źródłowego adresu IP z pakietem IP i protokołem klienta (koligacja źródłowego adresu IP 3-krotka), która rozwiązuje niezgodność między Azure Load Balancer i Pulpit zdalny Gateway (Brama usług pulpitu zdalnego). 

Innym scenariuszem przypadku użycia jest przekazywanie multimediów. Przekazywanie danych odbywa się za pośrednictwem protokołu UDP, ale płaszczyzna kontroli jest realizowana za pośrednictwem protokołu TCP:

* Klient uruchamia sesję TCP na adres publiczny ze zrównoważonym obciążeniem i jest kierowany do określonego DIP. Kanał pozostaje aktywny do monitorowania kondycji połączenia.
* Nowa sesja UDP z tego samego komputera klienckiego jest uruchamiana w tym samym publicznym punkcie końcowym ze zrównoważonym obciążeniem. Połączenie jest kierowane do tego samego punktu końcowego DIP co poprzednie połączenie TCP. Przekazywanie multimediów może być wykonywane przy wysokiej przepływności, przy jednoczesnym zachowaniu kanału kontrolnego za pośrednictwem protokołu TCP.

> [!NOTE]
> W przypadku zmiany zestawu ze zrównoważonym obciążeniem przez usunięcie lub dodanie maszyny wirtualnej, rozkład żądań klientów jest obliczany ponownie. Nie można zależeć od nowych połączeń z istniejących klientów, aby zakończyć działanie na tym samym serwerze. Ponadto użycie trybu dystrybucji koligacji źródłowych adresów IP może spowodować nierówne dystrybucję ruchu. Klienci uruchamiani za serwerami proxy mogą być uważani za jedną unikatową aplikację kliencką.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania trybu dystrybucji Azure Load Balancer, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](load-balancer-distribution-mode.md).


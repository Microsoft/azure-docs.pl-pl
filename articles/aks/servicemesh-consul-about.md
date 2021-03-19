---
title: Przegląd Consul
description: Uzyskaj przegląd Consul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83774001"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Omówienie

[Consul][consul] to rozwiązanie sieciowe usług obsługujące wiele centrów danych służące do nawiązywania połączeń i zabezpieczania usług na różnych platformach środowiska uruchomieniowego. [Connect][consul-features] to składnik, który zapewnia możliwości sieci usługi.

## <a name="architecture"></a>Architektura

Consul zapewnia płaszczyznę danych, która składa się z [przyczep][consul-sidecar] opartych na [wysłannikaach][envoy-proxy]domyślnie. Consul ma podłączaną architekturę serwera proxy. Te inteligentne serwery proxy kontrolują cały ruch sieciowy do i z sieci i obciążeń z siatką.

Płaszczyzna kontroli zarządza konfiguracją i zasadami za pośrednictwem następujących [składników][consul-architecture]:

- **Serwer** — Agent Consul działający w trybie serwera, który obsługuje stan klastra Consul.

- **Klient** — Agent Consul uruchomiony w trybie uproszczonego klienta. Każdy węzeł obliczeniowy musi mieć uruchomiony agent klienta. Ten klient brokeruje konfigurację i zasady między obciążeniami a konfiguracją Consul. 

Na poniższym diagramie architektury pokazano, jak działa różne składniki w płaszczyźnie danych i płaszczyzny kontroli.

![Omówienie składników i architektury Consul.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Kryteria wyboru

Ważne jest zrozumienie i rozważenie następujących zagadnień podczas oceniania Consul obciążeń:

- [Zasady Consul](#consul-principles)
- [Możliwości](#capabilities)
- [Scenariusze](#scenarios)


### <a name="consul-principles"></a>Zasady Consul

Następujące zasady [dotyczą][consul-principles] projektu Consul:

- Oparte na **interfejsach API** -codify wszystkie konfiguracje i zasady.

- **Uruchamiaj i łącz z dowolnego miejsca** — łączenie obciążeń na platformach środowiska uruchomieniowego (Kubernetes, maszyny wirtualne, bezserwerowe).

- **Rozszerzając i Integruj** — bezpieczne łączenie obciążeń w całej infrastrukturze.


### <a name="capabilities"></a>Możliwości

Consul zapewnia następujący zestaw możliwości:

- **Siatka** — Brama (wiele centrów danych), maszyny wirtualne (poza węzłami klastra), synchronizacja usługi, wbudowane opcje debugowania

- **Proxy** — wysłannika, wbudowanego serwera proxy, dostępnego do podłączenia, serwera proxy P4 dla obciążeń systemu Windows

- **Zarządzanie ruchem** — Routing, dzielenie, rozdzielczość

- **Zasady** — zamiary, listy ACL

- **Zabezpieczenia** — autoryzacja, uwierzytelnianie, szyfrowanie, tożsamości oparte na SPIFFE, zewnętrzny urząd certyfikacji (magazyn), zarządzanie certyfikatami i rotacja

- **Zauważalność** — metryki, pulpit nawigacyjny interfejsu użytkownika, Prometheus, grafana


### <a name="scenarios"></a>Scenariusze

Consul jest dobrze dostosowane do i sugerowane dla następujących scenariuszy:

- Rozszerzanie istniejących obciążeń połączonych Consul

- Wymagania dotyczące zgodności dotyczące zarządzania certyfikatami

- Siatka usługi wiele klastrów

- Obciążenia oparte na maszynach wirtualnych, które mają być dołączone do sieci usługi



## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano, jak można zainstalować usługę Consul w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalowanie Consul w usłudze Azure Kubernetes Service (AKS)][consul-install]

Możesz również dodatkowo zapoznać się z funkcjami i architekturą Consul:

- [Samouczki Wprowadzenie Consul][consul-getting-started]
- [Funkcje Consul][consul-features]
- [Architektura Consul][consul-architecture]
- [Consul — jak działa połączenie][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md

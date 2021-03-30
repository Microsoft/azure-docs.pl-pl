---
title: Przegląd konsolidatora
description: Uzyskaj przegląd konsolidatora
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593771"
---
# <a name="linkerd"></a>Z konsolidatorem

## <a name="overview"></a>Omówienie

[Konsolidator][linkerd] jest łatwy w użyciu i uproszczona siatka usług.

## <a name="architecture"></a>Architektura

Konsolidator udostępnia płaszczyznę danych, która składa się z [ultralightych][linkerd-proxy] przyczep z wyspecjalizowanym serwerem proxy. Te inteligentne serwery proxy kontrolują cały ruch sieciowy do i z sieci i obciążeń z siatką. Serwery proxy ujawniają także metryki za pośrednictwem punktów końcowych metryk [Prometheus][prometheus] .

Płaszczyzna kontroli zarządza konfiguracją i agregowaną telemetrię za pośrednictwem następujących [składników][linkerd-architecture]:

- **Controller** — udostępnia interfejs API, który umożliwia łączenie wbudowanego interfejsu wiersza polecenia i pulpitu nawigacyjnego. Zapewnia konfigurację dla serwerów proxy.

- **Naciśnij kolejno** pozycje — przeczuj w czasie rzeczywistym na żądania i odpowiedzi.

- **Tożsamość** — zapewnia funkcje tożsamości i zabezpieczeń, które umożliwiają mTLS między usługami.

- **Sieć Web** — udostępnia wbudowany pulpit nawigacyjny.


Na poniższym diagramie architektury pokazano, jak działa różne składniki w płaszczyźnie danych i płaszczyzny kontroli.


![Przegląd składników i architektury konsolidatora.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Kryteria wyboru

Ważne jest zrozumienie i rozważenie następujących zagadnień podczas oceniania konsolidatora dla obciążeń:

- [Zasady projektowania](#design-principles)
- [Możliwości](#capabilities)
- [Scenariusze](#scenarios)


### <a name="design-principles"></a>Zasady projektowania

W następujących zasadach projektowania [Przewodnik][design-principles] po projekcie konsolidatora:

- **Zachowanie** prostoty — musi być łatwe w użyciu i zrozumiałe.

- **Minimalizacja wymagań dotyczących zasobów** — nakładanie minimalnego kosztu wydajności i zasobów.

- **Tylko Pracuj** — nie przerywaj istniejących aplikacji i nie wymagaj skomplikowanej konfiguracji.


### <a name="capabilities"></a>Możliwości

Konsolidator udostępnia następujący zestaw możliwości:

- **Siatka** — opcja debugowania wbudowana

- **Zarządzanie ruchem** — dzielenie, limity czasu, ponowne próby, ruch przychodzący

- **Zabezpieczenia** — szyfrowanie (mTLS), certyfikaty są obracane co 24 godziny

- **Zauważalność** — agregowanie metryk, wybieranie, śledzenie, profile usług i metryki na trasie, pulpit nawigacyjny sieci Web z wykresami topologii, Prometheus, grafana


### <a name="scenarios"></a>Scenariusze

Konsolidator jest dobrze dostosowany do i sugerowany dla następujących scenariuszy:

- Prosta do użycia z tylko istotnym zestawem wymagań dotyczących możliwości

- Małe opóźnienia, niskie obciążenie, z fokusem w celu zaobserwowania i prostego zarządzania ruchem


## <a name="next-steps"></a>Następne kroki

W poniższej dokumentacji opisano, jak można zainstalować konsolidator w usłudze Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalowanie konsolidatora w usłudze Azure Kubernetes Service (AKS)][linkerd-install]

Możesz również dodatkowo poznać funkcje i architekturę konsolidatora:

- [Funkcje konsolidatora][linkerd-features]
- [Architektura konsolidatora][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md
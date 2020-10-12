---
title: Partnerzy monitorowania Service Fabric platformy Azure
description: Dowiedz się, jak monitorować aplikacje, klastry i infrastruktury platformy Azure Service Fabric przy użyciu rozwiązań do monitorowania partnerów.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645722"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Partnerzy monitorowania Service Fabric platformy Azure

W tym artykule przedstawiono sposób monitorowania Service Fabric aplikacji, klastrów i infrastruktury przy użyciu rozwiązań partnerskich. Firma Microsoft pracowała wspólnie z partnerami poniżej, aby utworzyć zintegrowane oferty dla Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Nasza integracja z usługą dynaTrace zapewnia wiele funkcji dostępnych w usłudze Box do monitorowania klastrów Service Fabric. Zainstalowanie dynaTrace OneAgent w wystąpieniach VMSS zapewnia liczniki wydajności i topologię wdrożenia Service Fabric na poziomie aplikacji. DynaTrace to również doskonały wybór dla monitorowania lokalnego. Zapoznaj się z innymi funkcjami wymienionymi w [ogłoszeniu](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) i [instrukcjami dotyczącymi](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) włączania dynaTrace w klastrze. 

## <a name="datadog"></a>Datadog

Usługi Datadog ma rozszerzenie dla VMSS zarówno dla wystąpień systemu Windows, jak i Linux. Za pomocą usługi Datadog można zbierać dzienniki zdarzeń systemu Windows, a tym samym zbierać zdarzenia platformy Service Fabric w systemie Windows. Zapoznaj się z instrukcjami dotyczącymi wysyłania danych diagnostycznych do usługi Datadog w [tym miejscu](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Service Fabric integracja z usługą AppDynamics jest na poziomie aplikacji. Aktualizując zmienne środowiskowe i korzystając z NuGet usługi App Dynamics, można wysłać dane telemetryczne aplikacji do usługi AppDynamics. Zapoznaj się z tymi [instrukcjami](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) , jak zintegrować aplikacje .NET Service Fabric z usługą AppDynamics.

## <a name="new-relic"></a>Nowy Relic

New Relic to inne narzędzie do zarządzania wydajnością aplikacji, które integruje się dobrze z aplikacjami Service Fabric. Możesz zainstalować nowe pakiety NuGet Relic i dodać określone zmienne środowiskowe w plikach manifestu, aby wysłać dane telemetryczne aplikacji do nowej Relic. Zapoznaj się z tymi [instrukcjami](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) , aby włączyć nową telemetrię Relic dla aplikacji .NET Service Fabric.

## <a name="elk"></a>ELK 

Stos ELK to zbiór technologii typu "open source": Elasticsearch, logstash i Kibana. Korzystając z tych technologii w połączeniu, można zbierać, przechowywać i analizować Service Fabric dane monitorowania i diagnostyki. Mamy samouczek, jak to zrobić za pomocą natywnych [aplikacji Java](service-fabric-tutorial-java-elk.md)Service Fabric. 

## <a name="humio"></a>Humio

Humio to usługa zbierania dzienników, która umożliwia zbieranie dzienników z aplikacji i zdarzeń z Service Fabric w chmurze lub lokalnie w czasie rzeczywistym. Oprócz możliwości wglądu na żywo, Humio oferuje najnowocześniejszą funkcję analizy i wizualizacji do wyświetlania i zbierania informacji z diagnostyki. Usługa Humio ma oszczędne plany cenowe i jest zbudowana w celu skalowania w czasie, gdy zachowuje szybko szybką szybkość. Integruje się ona bezpośrednio ze zdarzeniami platformy Service Fabric i telemetrii aplikacji. Więcej informacji na temat integracji z programem Humio i Service Fabric można znaleźć [tutaj](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się [z omówieniem monitorowania i diagnostyki](service-fabric-diagnostics-overview.md) w Service Fabric
* Dowiedz się, jak [zdiagnozować typowe scenariusze](service-fabric-diagnostics-common-scenarios.md) za pomocą naszych narzędzi w pierwszej kolejności

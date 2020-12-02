---
title: Wprowadzenie do chmury wiosennej platformy Azure
description: Poznaj funkcje i korzyści płynące z chmury Azure wiosennej, aby wdrażać aplikacje ze sprężyną Java na platformie Azure i zarządzać nimi.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 74ebdafb835aff75f282b9d6ac02d8ccf672a2be
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501094"
---
# <a name="what-is-azure-spring-cloud"></a>Co to jest usługa Azure Spring Cloud?

Chmura sprężynowa platformy Azure ułatwia wdrażanie aplikacji mikrousług opartych na rozruchu sprężyny na platformie Azure bez zmian w kodzie.  Chmura ze sprężyną systemu Azure zarządza infrastrukturą aplikacji w chmurze, dzięki czemu deweloperzy mogą skupić się na kodzie.  Chmura Wiosenna umożliwia zarządzanie cyklem życia przy użyciu kompleksowych funkcji monitorowania i diagnostyki, zarządzania konfiguracją, odnajdywania usług, integracji ciągłej i ciągłego wdrażania, a także innych elementów.

Chmura sprężynowa platformy Azure obsługuje zarówno aplikacje z [rozruchem ze sprężyną](https://spring.io/projects/spring-boot) Java, jak i ASP.NET Core [steeltoe](https://steeltoe.io/) . Pomoc techniczna steeltoe jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej pozwalają na eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją.  Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji, zobacz [często zadawane pytania](https://azure.microsoft.com/support/faq/) lub plik [support Request](../azure-portal/supportability/how-to-create-azure-support-request.md).

W ramach ekosystemu platformy Azure chmura sprężynowa platformy Azure umożliwia łatwe powiązanie z innymi usługami platformy Azure, w tym z magazynem, bazami danych, monitorowaniem i innymi.

W tym artykule opisano następujące możliwości chmury wiosennej platformy Azure:

* Serwer konfiguracji
* Wdrożenia niebieskie/zielone
* Skalowanie aplikacji
* Integracja z usługą Azure DevOps
* Monitorowanie aplikacji

## <a name="spring-cloud-config-server"></a>Serwer konfiguracji chmury wiosennej

Serwer konfiguracji chmurowej platformy Azure zapewnia konfigurację zewnętrzną w systemie rozproszonym z obsługą serwera i klienta.  Serwer konfiguracji chmury Azure wiosny to centralna lokalizacja do zarządzania właściwościami aplikacji we wszystkich środowiskach. Aby uzyskać więcej informacji, zobacz [informacje dotyczące serwera konfiguracji chmury wiosennej](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Wdrożenia niebieskie/zielone

Chmura sprężynowa platformy Azure obsługuje wdrożenia niebieskie/zielone do zwalniania i aktualizowania kodu w środowiskach produkcyjnych.  Ten wzorzec zarządzania zmianami umożliwia deweloperom Implementowanie funkcji i zmian kodu z zabezpieczeniami natychmiastowego powrotu, gdy jest to konieczne.  Deweloperzy mogą skoncentrować się na pisaniu kodu z wieloma środowiskami produkcyjnymi w celu aktualizowania lub wycofywania zmian w kodzie bez zakłócania działania aplikacji.  Aby dowiedzieć się więcej o środowiskach przejściowych i wdrożeniach Blue/Green, zapoznaj się z tym [artykułem](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>Automatyzacja potoku ciągłej integracji/ciągłego wdrażania

Chmura sprężynowa platformy Azure zapewnia integrację z usługą Azure DevOps przy użyciu interfejsu wiersza polecenia platformy Azure.  Za pomocą usługi Azure DevOps można zautomatyzować integrację i wdrażanie kodu w aplikacji wiosennej.  Aby dowiedzieć się więcej, zapoznaj się z tym [artykułem](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Skalowanie aplikacji

Chmura sprężynowa platformy Azure umożliwia łatwe skalowanie mikrousług na pulpicie nawigacyjnym chmury ze sprężyną na platformie Azure.  Zarówno liczba procesorów wirtualnych vCPU, jak i ilość pamięci dostępnej dla mikrousług można skalować w górę lub w dół odpowiednio do potrzeb.  Skalowanie obowiązuje w kilka sekund i nie wymaga zmiany kodu ani ponownego wdrożenia.  Aby dowiedzieć się więcej, wykonaj kroki tego [samouczka](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitorowanie aplikacji

Rozproszone narzędzia do śledzenia w chmurze sprężynowe umożliwiają deweloperom debugowanie i monitorowanie złożonych połączeń między mikrousługami w aplikacji.  Dzięki integracji [wiosennej chmury Sleuth](https://spring.io/projects/spring-cloud-sleuth) z [Application Insights](../azure-monitor/monitor-reference.md)platformy Azure platforma Azure zapewnia zaawansowane możliwości śledzenia rozproszonego bezpośrednio z Azure Portal.  Aby dowiedzieć się więcej, wykonaj kroki tego [samouczka](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, Wypełnij [chmurę z przewodnikiem Szybki Start](spring-cloud-quickstart.md)

Przykłady są dostępne w serwisie GitHub: [przykłady chmur w chmurze platformy Azure](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
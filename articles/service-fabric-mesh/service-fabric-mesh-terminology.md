---
title: Terminologia dotycząca usługi Azure Service Fabric siatka
description: W tym artykule szczegółowo opisano terminologię używaną przez usługę Azure Service Fabric siatkę, która pomaga lepiej zrozumieć terminy używane w dokumentacji.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: d82df6ee598664c2b85cfa82395ac66f3865cf7f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843010"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia dotycząca Service Fabric siatki

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. W tym artykule szczegółowo opisano terminologię używaną przez usługę Azure Service Fabric siatkę, która pomaga lepiej zrozumieć terminy używane w dokumentacji.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](../service-fabric/index.yml) to platforma systemów rozproszonych Open Source, która ułatwia pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Service Fabric jest koordynatorem, który ma uprawnienia Service Fabric siatką. Service Fabric udostępnia opcje tworzenia i uruchamiania aplikacji mikrousług. Możesz użyć dowolnej platformy, aby napisać usługi i wybrać miejsce uruchamiania aplikacji z wielu opcji środowiska.

## <a name="application-and-service-concepts"></a>Pojęcia dotyczące aplikacji i usług

**Aplikacja siatki Service Fabric**: aplikacje sieci Service Fabric są opisane przez [model zasobów](./service-fabric-mesh-service-fabric-resources.md) (pliki zasobów YAML i JSON) i można je wdrożyć w dowolnym środowisku, w którym Service Fabric działa.

**Aplikacja natywna Service Fabric**: aplikacje natywne Service Fabric są opisane przez [natywny model aplikacji](../service-fabric/service-fabric-application-model.md) (aplikacje oparte na języku XML i manifesty usług).  Aplikacje natywne Service Fabric nie mogą być uruchamiane w Service Fabric siatki.

**Aplikacja**: aplikacja siatki Service Fabric jest jednostką wdrożenia, przechowywania wersji i okresu istnienia aplikacji sieci. Cykl życia każdego wystąpienia aplikacji może być zarządzany niezależnie.  Aplikacje składają się z co najmniej jednego pakietu i ustawień kodu usługi. Aplikacja jest definiowana przy użyciu schematu modelu zasobów platformy Azure (RM).  Usługi są opisane jako właściwości zasobu aplikacji w szablonie Menedżera zasobów.  Aplikacje i woluminy używane przez aplikację są przywoływane przez aplikację.  Podczas tworzenia aplikacji aplikacje, usługi, sieci i woluminy są modelowane przy użyciu modelu zasobów Service Fabric.

**Usługa**: usługa w aplikacji reprezentuje mikrousługę i wykonuje pełną i autonomiczną funkcję. Każda usługa składa się z jednego lub więcej pakietów kodu, które opisują wszystko, czego potrzebujesz do uruchomienia obrazu kontenera skojarzonego z pakietem kodu.  Liczba replik usługi w aplikacji może być skalowana i wychodząca.

**Pakiet kodu**: pakiety kodu opisują wszystko, czego potrzebujesz do uruchomienia obrazu kontenera skojarzonego z pakietem kodu, w tym następujące:

* Nazwa, wersja i rejestr kontenera
* Zasoby procesora i pamięci wymagane dla każdego kontenera
* Punkty końcowe sieci
* Woluminy do zainstalowania w kontenerze, odwołujące się do oddzielnego zasobu woluminu.

## <a name="deployment-and-application-models"></a>Modele wdrożenia i aplikacji 

Aby wdrożyć usługi, należy opisać sposób ich działania. Service Fabric obsługuje trzy różne modele wdrażania:

### <a name="resource-model"></a>Model zasobów
Zasoby Service Fabric są dowolne, które mogą być wdrażane pojedynczo do Service Fabric; obejmuje to aplikacje, usługi, sieci i woluminy. Zasoby są definiowane przy użyciu pliku JSON, który można wdrożyć w punkcie końcowym klastra.  W przypadku Service Fabric siatki używany jest schemat modelu zasobów platformy Azure. Schemat pliku YAML może być również używany do łatwiejszego tworzenia plików definicji. Zasoby można wdrożyć w dowolnym miejscu Service Fabric uruchomienia. Model zasobów to najprostszy sposób opisywania Service Fabric aplikacji. Głównym celem jest proste wdrażanie usług kontenerowych i zarządzanie nimi. Aby dowiedzieć się więcej, Przeczytaj [wprowadzenie do modelu zasobów Service Fabric](./service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Model natywny
Natywny model aplikacji zapewnia aplikacjom pełny dostęp niskiego poziomu do Service Fabric. Aplikacje i usługi są zdefiniowane jako zarejestrowane typy w plikach manifestu XML.

Model natywny obsługuje strukturę Reliable Services, która zapewnia dostęp do interfejsów API środowiska uruchomieniowego Service Fabric i interfejsów API zarządzania klastrami w językach C# i Java. Model natywny obsługuje również dowolne kontenery i pliki wykonywalne.

Model natywny nie jest obsługiwany w środowisku Service Fabric siatki.  Aby uzyskać więcej informacji, zobacz [Omówienie modelu programowania](../service-fabric/service-fabric-choose-framework.md).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) jest częścią projektu platformy Docker. Service Fabric zapewnia ograniczoną obsługę wdrażania aplikacji przy użyciu modelu Docker Compose.

## <a name="environments"></a>Środowiska

Service Fabric to technologia platformy Open Source, w której bazują kilka różnych usług i produktów. Firma Microsoft oferuje następujące opcje:

 - **Siatka Service Fabric**: w pełni zarządzana usługa do uruchamiania aplikacji Service Fabric w Microsoft Azure.
 - **Azure Service Fabric**: Oferta hostowanej Service Fabric klastra platformy Azure. Zapewnia ona integrację między Service Fabric i infrastrukturą platformy Azure oraz zarządzaniem i konfiguracją klastrów Service Fabric.
 - **Service Fabric autonomiczne**: zestaw narzędzi instalacji i konfiguracji do [wdrażania klastrów Service Fabric w dowolnym miejscu](../service-fabric/service-fabric-deploy-anywhere.md) (lokalnie lub u dowolnego dostawcy chmury). Niezarządzana przez platformę Azure.
 - **Service Fabric klaster projektowy**: Program udostępnia lokalne środowisko programistyczne w systemie Windows, Linux lub na komputerach Mac w celu opracowywania aplikacji Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Macierz obsługi środowiska, architektury i modelu wdrażania
Różne środowiska mają różne poziomy obsługi dla struktur i modeli wdrażania. W poniższej tabeli opisano obsługiwane kombinacje platform i modeli wdrażania.

| Typ aplikacji | Opisane przez | Azure Service Fabric Mesh | Klastry usługi Azure Service Fabric (każdy system operacyjny)| Klaster lokalny | Klaster autonomiczny |
|---|---|---|---|---|---|
| Aplikacje siatki Service Fabric | Model zasobów (YAML & JSON) | Obsługiwane |Nieobsługiwane | Windows — obsługiwane, Linux i Mac — nieobsługiwane | Windows — nieobsługiwane |
|Aplikacje natywne Service Fabric | Natywny model aplikacji (XML) | Nieobsługiwane| Obsługiwane|Obsługiwane|Windows — obsługiwane|

W poniższej tabeli opisano różne modele aplikacji i narzędzia, które istnieją dla nich w odniesieniu do Service Fabric.

| Typ aplikacji | Opisane przez | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplikacje siatki Service Fabric | Model zasobów (YAML & JSON) | VS 2017 |Nieobsługiwane |Nieobsługiwane | Tylko obsługiwane środowisko siatki | Nieobsługiwane|
|Aplikacje natywne Service Fabric | Natywny model aplikacji (XML) | VS 2017 i VS 2015| Obsługiwane|Obsługiwane|Obsługiwane|Obsługiwane|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).

Znajdź odpowiedzi na [często zadawane pytania](service-fabric-mesh-faq.md).

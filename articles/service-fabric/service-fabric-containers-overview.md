---
title: Przegląd Service Fabric i kontenerów
description: Omówienie Service Fabric i używania kontenerów do wdrażania aplikacji mikrousług. Ten artykuł zawiera omówienie sposobu użycia kontenerów i dostępne możliwości w Service Fabric.
ms.topic: conceptual
ms.date: 7/9/2020
ms.openlocfilehash: cd0ec7dd2247fdd791df362fa34542178c17df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87091661"
---
# <a name="service-fabric-and-containers"></a>Service Fabric i kontenery

## <a name="introduction"></a>Wprowadzenie

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług i kontenerów oraz zarządzanie nimi.

Service Fabric jest [koordynatorem kontenera](service-fabric-cluster-resource-manager-introduction.md) firmy Microsoft do wdrażania mikrousług w klastrze maszyn. Service Fabric korzyści z lekcji zdobytych w trakcie lat, w których działają usługi w firmie Microsoft na ogromną skalę.

Mikrousługi można tworzyć na wiele sposobów, na przykład korzystając z [modeli programowania usługi Service Fabric](service-fabric-choose-framework.md) czy struktury [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) lub wdrażając [dowolnie wybrany kod](service-fabric-guest-executables-introduction.md). Jeśli chcesz tylko [wdrożyć kontenery i zarządzać nimi](service-fabric-containers-overview.md), Service Fabric jest również doskonałym wyborem.

Domyślnie program Service Fabric wdraża i aktywuje te usługi jako procesy. Procesy zapewniają największą aktywację i najwyższy poziom użycia zasobów w klastrze. Service Fabric może również wdrażać usługi w obrazach kontenera. Możesz również mieszać usługi w procesach i usługach w kontenerach w tej samej aplikacji.

Aby przejść do właściwych i wypróbować kontenery na Service Fabric, wypróbuj Przewodnik Szybki Start, samouczek lub przykład:  

[Szybki Start: wdrażanie aplikacji kontenera systemu Linux w Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Szybki Start: wdrażanie aplikacji kontenera systemu Windows do Service Fabric](service-fabric-quickstart-containers.md)  
[Konteneryzowanie istniejącej aplikacji .NET](service-fabric-host-app-in-a-container.md)  
[Przykłady kontenera usługi Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Co to są kontenery

Kontenery rozwiązują problem z niezawodnymi działami aplikacji w różnych środowiskach obliczeniowych, dostarczając niezmienne środowisko do uruchamiania aplikacji. Kontenery zawijają aplikację i wszystkie jej zależności, takie jak biblioteki i pliki konfiguracji, do własnej izolowanego pola, które zawiera wszystko, co jest potrzebne do uruchamiania oprogramowania wewnątrz kontenera. W każdym przypadku, gdy działa kontener, aplikacja wewnątrz niego zawsze ma wszystko, co jest potrzebne do uruchamiania takich elementów, jak odpowiednie wersje bibliotek zależnych, wszystkie pliki konfiguracji i inne potrzebne do uruchomienia.

Kontenery są uruchamiane bezpośrednio na poziomie jądra i mają izolowany widok systemu plików i innych zasobów. Aplikacja w kontenerze nie ma informacji o innych aplikacjach ani procesach poza kontenerem. Każda aplikacja i jej środowisko uruchomieniowe, zależności i biblioteki systemowe działają wewnątrz kontenera z pełnym dostępem prywatnym do własnego widoku izolowanego kontenera systemu operacyjnego. Oprócz zapewnienia, że wszystkie zależności aplikacji, które mają być uruchamiane w różnych środowiskach obliczeniowych, są proste, a zabezpieczenia i izolacja zasobów to ważne korzyści wynikające z używania kontenerów z Service Fabric — w przeciwnym razie usługi są uruchamiane w ramach procesu.

W porównaniu z maszynami wirtualnymi kontenery mają następujące zalety:

* **Małe**: kontenery używają pojedynczej przestrzeni dyskowej i wersji warstwy oraz aktualizacji w celu zwiększenia wydajności.
* **Szybka**: kontenery nie muszą uruchamiać całego systemu operacyjnego, więc mogą zacząć znacznie szybciej — zwykle w ciągu kilku sekund.
* **Przenośność**: obraz aplikacji kontenera można przenieść do uruchamiania w chmurze, lokalnie, wewnątrz maszyn wirtualnych lub bezpośrednio na maszynach fizycznych.
* **Zarządzanie zasobami**: kontener może ograniczyć zasoby fizyczne, które mogą być używane na hoście.

### <a name="container-types-and-supported-environments"></a>Typy kontenerów i obsługiwane środowiska

Service Fabric obsługuje kontenery w systemach Linux i Windows oraz obsługuje tryb izolacji funkcji Hyper-V w systemie Windows.

#### <a name="docker-containers-on-linux"></a>Kontenery platformy Docker w systemie Linux

Platforma Docker udostępnia interfejsy API umożliwiające tworzenie kontenerów i zarządzanie nimi na kontenerach jądra systemu Linux. Centrum platformy Docker udostępnia centralne repozytorium do przechowywania i pobierania obrazów kontenerów.
Aby zapoznać się z samouczkiem opartym na systemie Linux, zobacz [Tworzenie pierwszej Service Fabric aplikacji kontenera w systemie Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Kontenery systemu Windows Server

System Windows Server 2016 i nowsze oferują dwa różne typy kontenerów, które różnią się w zależności od poziomu izolacji. Kontenery systemu Windows Server i kontenery platformy Docker są podobne, ponieważ zarówno mają przestrzeń nazw, jak i izolację systemu plików, jednocześnie udostępniając jądro na hoście, na którym są uruchomione. W systemie Linux ta izolacja jest tradycyjnie świadczona przez cgroup i przestrzenie nazw, a kontenery systemu Windows Server zachowują się podobnie.

Kontenery systemu Windows z obsługą funkcji Hyper-V zapewniają większą izolację i bezpieczeństwo, ponieważ żaden kontener nie udostępnia jądra systemu operacyjnego żadnym innym kontenerem lub hostem. W przypadku wyższego poziomu izolacji zabezpieczeń kontenery z obsługą funkcji Hyper-V są stosowane do potencjalnie nieszkodliwych scenariuszy z wieloma dzierżawcami.
Aby zapoznać się z samouczkiem opartym na systemie Windows, zobacz [Tworzenie pierwszej Service Fabric aplikacji kontenera w systemie Windows](service-fabric-get-started-containers.md).

Na poniższej ilustracji przedstawiono różne typy wirtualizacji i dostępne poziomy izolacji.
![Platforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scenariusze korzystania z kontenerów

Oto typowe przykłady, w których kontener jest dobry wybór:

* **Przesunięcia i Shift usług IIS**: można umieścić istniejącą aplikację [ASP.NET MVC](https://www.asp.net/mvc) w kontenerze zamiast migrować ją do ASP.NET Core. Te aplikacje ASP.NET MVC są zależne od Internet Information Services (IIS). Te aplikacje można spakować na obrazy kontenerów ze utworzonego obrazu usług IIS i wdrożyć je za pomocą Service Fabric. Zobacz [obrazy kontenerów w systemie Windows Server](/virtualization/windowscontainers/quick-start/quick-start-windows-server) , aby uzyskać informacje o kontenerach systemu Windows.

* **Mieszaj kontenery i Service Fabric mikrousługi**: Użyj istniejącego obrazu kontenera dla części aplikacji. Można na przykład użyć [kontenera Nginx](https://hub.docker.com/_/nginx/) dla frontonu sieci Web aplikacji i usług stanowych w celu uzyskania bardziej intensywnych obliczeń zaplecza.

* Ogranicz **wpływ usług "sąsiedzi" na "zakłócenia"**: możesz użyć funkcji ładu zasobów kontenerów, aby ograniczyć zasoby używane przez usługę na hoście. Jeśli usługi mogą zużywać wiele zasobów i wpływać na wydajność innych (takich jak długotrwała operacja przypominających zapytania), należy rozważyć umieszczenie tych usług w kontenerach, które mają zarządzanie zasobami.

## <a name="service-fabric-support-for-containers"></a>Obsługa usługi Service Fabric dla kontenerów

Service Fabric obsługuje wdrażanie kontenerów platformy Docker w systemach Linux i kontenerów systemu Windows Server w systemie Windows Server 2016 lub nowszym wraz z obsługą trybu izolacji funkcji Hyper-V.

> [!NOTE]
> Kontenery nie są obsługiwane w lokalnym klastrze Service Fabric klastrów (ani w klastrach z systemem Linux w systemie jednopunktowy, ani w klastrach systemu Windows w instalacjach lokalnych Service Fabric).

Service Fabric udostępnia [model aplikacji](service-fabric-application-model.md) , w którym kontener reprezentuje hosta aplikacji, w którym umieszczane są wiele replik usługi. Service Fabric obsługuje również [scenariusz wykonywalny gościa](service-fabric-guest-executables-introduction.md) , w którym nie można używać wbudowanych modeli programowania Service Fabric, ale zamiast tego pakuje istniejącą aplikację, korzystając z dowolnego języka lub platformy, wewnątrz kontenera. Ten scenariusz jest typowym przypadkiem użycia dla kontenerów.

Możesz również uruchomić [usługi Service Fabric w kontenerze](service-fabric-services-inside-containers.md). Obsługa uruchamiania Service Fabric usług w kontenerach jest obecnie ograniczona.

Service Fabric oferuje kilka możliwości kontenera, które ułatwiają tworzenie aplikacji składających się z mikrousług obsługiwanych przez kontenery, takich jak:

* Wdrażanie i aktywacja obrazów kontenerów.
* Zarządzanie zasobami, w tym Ustawianie wartości zasobów domyślnie w klastrach platformy Azure.
* Uwierzytelnianie repozytorium.
* Port kontenera do mapowania portów hosta.
* Odnajdywanie i komunikacja między kontenerami.
* Możliwość konfigurowania i ustawiania zmiennych środowiskowych.
* Możliwość ustawienia poświadczeń zabezpieczeń w kontenerze.
* Wybór różnych trybów sieciowych dla kontenerów.

Aby zapoznać się z kompleksowym omówieniem obsługi kontenerów na platformie Azure, takich jak tworzenie klastra Kubernetes za pomocą usługi Azure Kubernetes, jak utworzyć prywatny rejestr platformy Docker w Azure Container Registry i więcej, zobacz temat [Azure for Containers](../containers/index.yml).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o Service Fabric pomocy technicznej dotyczącej uruchomionych kontenerów. Następnie połączymy się z przykładami każdej z tych funkcji, aby zobaczyć, jak ich używać.

[Tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)  
[Tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Windows](service-fabric-get-started-containers.md)  
[Dowiedz się więcej o kontenerach systemu Windows](/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

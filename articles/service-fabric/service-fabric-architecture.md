---
title: Architektura Service Fabric platformy Azure
description: W tym artykule opisano architekturę Service Fabric, platformę systemów rozproszonych służącą do tworzenia skalowalnych, niezawodnych i łatwych w zarządzaniu aplikacji w chmurze.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76024708"
---
# <a name="service-fabric-architecture"></a>Architektura usługi Service Fabric

Service Fabric jest skompilowana przy użyciu podsystemów warstwowych. Te podsystemy umożliwiają pisanie aplikacji, które są następujące:

* Wysoka dostępność
* Skalowalność
* Zarządzane
* Weryfikowalne

Na poniższym diagramie przedstawiono główne podsystemy Service Fabric.

![Diagram architektury Service Fabric](media/service-fabric-architecture/service-fabric-architecture.png)

W systemie rozproszonym Funkcja bezpiecznego komunikowania się między węzłami w klastrze jest niezwykle istotna. W bazie stosu jest podsystemem transportu, który zapewnia bezpieczną komunikację między węzłami. Powyżej podsystem transportu bazuje na podsystemie federacyjnym, w którym klastry są używane w jednej jednostce (nazwanych klastrach), dzięki czemu Service Fabric mogą wykrywać błędy, wykonywać wybór lidera i zapewniać spójny Routing. Podsystem niezawodności, warstwowy w ramach podsystemu federacyjnego, jest odpowiedzialny za niezawodność usług Service Fabric za pomocą takich mechanizmów jak replikacja, zarządzanie zasobami i przełączanie w tryb failover. Podsystem federacyjny również jest podsystemem hostingu i aktywacji, który zarządza cyklem życia aplikacji w jednym węźle. Podsystem zarządzania zarządza cyklem życia aplikacji i usług. Podsystem testowania ułatwia deweloperom aplikacji testowanie ich usług za pomocą symulowanych błędów przed i po wdrożeniu aplikacji i usług w środowiskach produkcyjnych. Service Fabric zapewnia możliwość rozpoznawania lokalizacji usługi za pomocą podsystemu komunikacji. Modele programowania aplikacji uwidocznione dla deweloperów są warstwami na podstawie tych podsystemów wraz z modelem aplikacji w celu włączenia narzędzi.

## <a name="transport-subsystem"></a>Podsystem transportu

Podsystem transportu implementuje kanał komunikacji typu punkt-punkt. Ten kanał służy do komunikacji w ramach klastrów usługi Service Fabric i komunikacji między klastrem i klientami usługi Service Fabric. Obsługuje jednokierunkowe wzorce komunikacji z odpowiedzią na żądanie, które stanowią podstawę do implementowania emisji i multiemisji w warstwie federacyjnej. Podsystem transportu zabezpiecza komunikację przy użyciu certyfikatów x509 lub zabezpieczeń systemu Windows. Ten podsystem jest używany wewnętrznie przez Service Fabric i nie jest bezpośrednio dostępny dla deweloperów na potrzeby programowania aplikacji.

## <a name="federation-subsystem"></a>Podsystem Federacji

Aby przyczynić się do określenia zestawu węzłów w systemie rozproszonym, musisz mieć spójny widok systemu. Podsystem federacyjny korzysta z elementów podstawowych komunikacji dostarczonych przez podsystem transportu i tworzy różne węzły w pojedynczym ujednoliconym klastrze, którego może przyczynić się do tego. Zapewnia ona podstawowe systemy rozproszone, które są niezbędne przez inne podsystemy — wykrywanie awarii, wybór lidera i spójny Routing. Podsystem Federacji jest oparty na rozproszonych tabelach skrótów z 128-bitową przestrzenią tokenów. Podsystem tworzy topologię pierścienia na węzłach, przy czym każdy węzeł w pierścieniu jest przydzielony podzbiorem przestrzeni tokenów do własności. W przypadku wykrywania awarii warstwa używa mechanizmu dzierżawienia na podstawie pulsu i arbitrażu. Podsystem Federacji gwarantuje również, że w dowolnym momencie istnieje tylko jeden właściciel tokenu w ramach przyłączania i wyjazdu Intricate. Zapewnia to wybór lidera i spójne gwarancje routingu.

## <a name="reliability-subsystem"></a>Podsystem niezawodności

Podsystem niezawodności zapewnia mechanizm, aby zapewnić wysoką dostępność stanu usługi Service Fabric za pomocą *replikatora*, *Menedżer trybu failover* i *modułu zasobów*.

* Replikator gwarantuje, że zmiany stanu w podstawowej replice usługi zostaną automatycznie zreplikowane do replik pomocniczych, zachowując spójność między podstawową i pomocniczą repliką w zestawie replik usługi. Replikator jest odpowiedzialny za zarządzanie kworum między replikami w zestawie replik. Współdziała z jednostką trybu failover, aby uzyskać listę operacji do replikacji, a agent rekonfiguracji udostępnia go konfiguracji zestawu replik. Ta konfiguracja wskazuje, które repliki muszą zostać zreplikowane. Service Fabric zapewnia domyślny Replikator o nazwie Replikator sieci szkieletowej, który może być używany przez interfejs API modelu programowania, aby zapewnić wysoką dostępność i niezawodność stanu usługi.
* Menedżer trybu failover zapewnia, że po dodaniu węzłów do klastra lub usunięciu z niego, obciążenie zostanie automatycznie rozdystrybuowane między dostępnymi węzłami. Jeśli węzeł w klastrze ulegnie awarii, klaster automatycznie skonfiguruje repliki usługi, aby zachować dostępność.
* Menedżer zasobów umieszcza repliki usługi w domenach błędów w klastrze i gwarantuje, że wszystkie jednostki trybu failover działają. Menedżer zasobów również równoważy zasoby usługi w ramach podstawowej udostępnionej puli węzłów klastra, aby osiągnąć optymalny jednolity rozkład obciążenia.

## <a name="management-subsystem"></a>Podsystem zarządzania

Podsystem zarządzania oferuje kompleksowe usługi i zarządzanie cyklem życia aplikacji. Polecenia cmdlet programu PowerShell i administracyjne interfejsy API umożliwiają udostępnianie, wdrażanie, uaktualnianie i cofanie aprowizacji aplikacji bez utraty dostępności. Podsystem zarządzania wykonuje ten proces za pomocą następujących usług.

* **Menedżer klastra**: jest to podstawowa usługa, która współdziała z Menedżer trybu failover od niezawodności, aby umieścić aplikacje w węzłach na podstawie ograniczeń umieszczania usług. Menedżer zasobów w podsystemie pracy awaryjnej gwarantuje, że ograniczenia nigdy nie są przerywane. Menedżer klastra zarządza cyklem życia aplikacji z poziomu inicjowania obsługi administracyjnej. Integruje się z menedżerem kondycji, aby upewnić się, że dostępność aplikacji nie zostanie utracona z perspektywy dotyczącej kondycji semantycznej podczas uaktualniania.
* **Menedżer kondycji**: Ta usługa umożliwia monitorowanie kondycji aplikacji, usług i jednostek klastra. Jednostki klastra (takie jak węzły, partycje usług i repliki) mogą raportować informacje o kondycji, które następnie są agregowane w scentralizowanym magazynie kondycji. Te informacje o kondycji zapewniają ogólną migawkę kondycji usług i węzłów rozproszonych między wieloma węzłami w klastrze, co umożliwia podejmowanie wymaganych działań naprawczych. Interfejsy API zapytań o kondycji umożliwiają wykonywanie zapytań dotyczących zdarzeń kondycji raportowanych do podsystemu kondycji. Interfejsy API zapytań o kondycji zwracają nieprzetworzone dane dotyczące kondycji przechowywane w magazynie kondycji lub zagregowane, interpretowane dane kondycji dla określonej jednostki klastra.
* **Magazyn obrazów**: Ta usługa zapewnia magazyn i dystrybucję plików binarnych aplikacji. Ta usługa udostępnia prosty rozproszony magazyn plików, do którego aplikacje są przekazywane i pobierane z programu.

## <a name="hosting-subsystem"></a>Podsystem hostingu

Menedżer klastra informuje podsystem hostingu (uruchomiony w każdym węźle), które usługi muszą zarządzać dla określonego węzła. Podsystem hostingu następnie zarządza cyklem życia aplikacji w tym węźle. Współdziała ona ze składnikami niezawodności i kondycji, aby zapewnić, że repliki są prawidłowo rozmieszczone i są w dobrej kondycji.

## <a name="communication-subsystem"></a>Podsystem komunikacji

Ten podsystem zapewnia niezawodne komunikaty w klastrze i odnajdowanie usług za pomocą usługi nazewnictwa. Usługa nazewnictwa rozwiązuje nazwy usług do lokalizacji w klastrze i umożliwia użytkownikom zarządzanie nazwami i właściwościami usług. Korzystając z usługi nazewnictwa, klienci mogą bezpiecznie komunikować się z dowolnym węzłem w klastrze w celu rozpoznania nazwy usługi i pobrania metadanych usługi. Przy użyciu prostego interfejsu API klienta nazw użytkownicy Service Fabric mogą opracowywać usługi i klientów z możliwością rozpoznawania bieżącej lokalizacji sieciowej, pomimo że węzeł dynamism lub zmianę rozmiarów klastra.

## <a name="testability-subsystem"></a>Podsystem testowania

Testowanie to pakiet narzędzi przeznaczony specjalnie do testowania usług opartych na Service Fabric. Narzędzia ułatwiają deweloperom łatwe wywoływanie błędów i uruchamianie scenariuszy testowych w celu wykonywania i weryfikowania licznych stanów i przejść, które usługa będzie działać w całym okresie istnienia, w sposób kontrolowany i bezpieczny. Możliwości testowania również zapewniają mechanizm do uruchamiania dłuższych testów, które mogą wykonywać iteracje różnych możliwych awarii bez utraty dostępności. Zapewnia to środowisko testowe w środowisku produkcyjnym.

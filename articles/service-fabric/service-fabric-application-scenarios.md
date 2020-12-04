---
title: Scenariusze i projektowanie aplikacji
description: Omówienie kategorii aplikacji w chmurze w Service Fabric. Omawia projekt aplikacji, który korzysta z usług stanowych i bezstanowych.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 6c3cc931a85b91fc02b8086ca5c2481153691e54
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575673"
---
# <a name="service-fabric-application-scenarios"></a>Scenariusze aplikacji Service Fabric

Usługa Azure Service Fabric oferuje niezawodną i elastyczną platformę, w której można pisać i uruchamiać wiele rodzajów aplikacji i usług firmy. Te aplikacje i mikrousługi mogą być bezstanowe lub stanowe i są zrównoważone przez zasoby na maszynach wirtualnych w celu zmaksymalizowania wydajności.

Unikatowa architektura Service Fabric umożliwia wykonywanie analizy danych niemal w czasie rzeczywistym, obliczeń w pamięci, transakcji równoległych i przetwarzania zdarzeń w aplikacjach. Możesz łatwo skalować aplikacje w zależności od zmieniających się wymagań dotyczących zasobów.

Aby uzyskać wskazówki dotyczące projektowania dotyczące tworzenia aplikacji, Przeczytaj [architekturę mikrousług na platformie Azure Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric) i [najlepsze rozwiązania dotyczące projektowania aplikacji przy użyciu Service Fabric](service-fabric-best-practices-applications.md).

Rozważ użycie Service Fabric platformy dla następujących typów aplikacji:

* **Zbieranie i przetwarzanie danych oraz IoT**: Service Fabric obsługuje dużą skalę i ma małe opóźnienia w ramach usług stanowych. Może ona pomóc w przetwarzaniu danych na milionach urządzeń, gdzie znajdują się dane dotyczące urządzenia i obliczenia.

    Klienci, którzy korzystali z usługi IoT Services przy użyciu Service Fabric obejmują systemy [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure),  [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider elektryczny](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)i [siatkowe](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Gry i interaktywne aplikacje oparte na sesji**: Service Fabric jest przydatne, jeśli aplikacja wymaga operacji odczytu i zapisu o małym opóźnieniu, na przykład w grach online lub w wiadomościach błyskawicznych. Service Fabric umożliwia tworzenie tych interaktywnych, stanowych aplikacji bez konieczności tworzenia oddzielnego magazynu lub pamięci podręcznej. Odwiedź witrynę [Azure gier Solutions](https://azure.microsoft.com/solutions/gaming/) , aby uzyskać wskazówki dotyczące projektowania [Service Fabric w usługach gier](/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Klienci, którzy korzystali z wbudowanych usług gier, obejmują [kolejne gry](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) i [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Klienci z utworzonymi sesjami interaktywnymi obejmują [Honeywell z usługą HoloLens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analiza danych i przetwarzanie przepływu pracy**: aplikacje, które muszą w niezawodny sposób przetwarzać zdarzenia lub strumienie danych, z zoptymalizowanych odczytów i zapisów w Service Fabric. Service Fabric obsługuje także potoki przetwarzania aplikacji, w których wyniki muszą być niezawodne i przesyłane do następnego etapu przetwarzania bez utraty. Te potoki obejmują systemy transakcyjne i finansowe, w których są niezbędne gwarancje spójności danych i obliczeń.

    Klienci, którzy mają wbudowaną służbowe usługi workflow, obejmują [grupę Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [rozwiązania biznesowe kworum](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)i [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Obliczanie danych**: Service Fabric umożliwia tworzenie aplikacji stanowych, które wykonują intensywne obliczanie danych. Service Fabric umożliwia wspólnej lokalizacji przetwarzania (obliczeń) i danych w aplikacjach. 

   Zwykle, gdy aplikacja wymaga dostępu do danych, opóźnienie sieci skojarzone z zewnętrzną pamięcią podręczną danych lub warstwą magazynowania ogranicza czas obliczeń. Stanowe Service Fabric usługi eliminują takie opóźnienie, co pozwala na bardziej zoptymalizowane odczyty i zapisy.

   Rozważmy na przykład aplikację, która wykonuje niemal wybrane rekomendacje w czasie rzeczywistym dla klientów, z wymaganiami dotyczącymi czasu rundy wynoszącą mniej niż 100 milisekund. Charakterystyka opóźnień i wydajności usług Service Fabric Services zapewnia użytkownikom środowisko reagowania w porównaniu z standardowym modelem implementacji, który wymaga pobrania niezbędnych danych z magazynu zdalnego. System ma więcej odpowiedzi, ponieważ Obliczanie wyboru rekomendacji jest wspólnie zlokalizowane z danymi i regułami.

    Klienci, którzy korzystali z usług obliczeniowych, obejmują [odpowiedzi Solidsoft](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) i [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Usługi o wysokiej dostępności**: Service Fabric zapewnia szybką pracę w trybie failover przez utworzenie wielu replik usług dodatkowych. Jeśli węzeł, proces lub indywidualna usługa ulegnie awarii z powodu sprzętu lub innego błędu, jedna z replik pomocniczych jest podwyższana do repliki podstawowej z minimalną utratą usługi.

* **Skalowalne usługi**: poszczególne usługi mogą być podzielone na partycje, co umożliwia skalowanie w poziomie w klastrze. Poszczególne usługi mogą być również tworzone i usuwane na bieżąco. Usługi można skalować w poziomie z kilku wystąpień w kilku węzłach do tysięcy wystąpień w wielu węzłach, a następnie skalować je ponownie w razie potrzeby. Za pomocą Service Fabric można kompilować te usługi i zarządzać pełnymi cyklami życia.

## <a name="application-design-case-studies"></a>Analizy przypadków projektowania aplikacji

Analizy przypadków pokazujące, w jaki sposób Service Fabric jest używany do projektowania aplikacji, jest publikowana na [historiech](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) i [mikrousługach klientów w witrynach platformy Azure](https://azure.microsoft.com/solutions/microservice-applications/) .

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Projektowanie aplikacji składających się z mikrousług bezstanowych i stanowych

Tworzenie aplikacji za pomocą ról procesów roboczych platformy Azure Cloud Services jest przykładem usługi bezstanowej. Z kolei mikrousługi stanowe utrzymują stan autorytatywny poza żądaniem i odpowiedzią. Ta funkcja zapewnia wysoką dostępność i spójność stanu za pomocą prostych interfejsów API, które zapewniają gwarancje transakcyjne obsługiwane przez replikację.

Usługi stanowe w Service Fabric zapewniają wysoką dostępność dla wszystkich typów aplikacji, a nie tylko baz danych i innych magazynów danych. Jest to naturalne postępy. Aplikacje zostały już przeniesione z używania czystych relacyjnych baz danych w celu zapewnienia wysokiej dostępności do baz danych NoSQL. Teraz aplikacje mogą mieć swój stan "gorąca" i zarządzane przez nie dane w celu uzyskania dodatkowych korzyści z wydajności bez utraty niezawodności, spójności i dostępności.

Podczas kompilowania aplikacji, które składają się z mikrousług, zazwyczaj istnieje kombinacja bezstanowych aplikacji sieci Web (takich jak ASP.NET i Node.js) wywoływanych na bezstanowe i stanowe usługi warstwy środkowej. Wszystkie aplikacje i usługi są wdrażane w tym samym klastrze Service Fabric za pomocą poleceń wdrażania Service Fabric. Każda z tych usług jest niezależna w odniesieniu do skalowalności, niezawodności i użycia zasobów. Ta niezależność zwiększa elastyczność i elastyczność w zakresie programowania i zarządzania cyklem życia.

Mikrousługi stanowe upraszczają projekty aplikacji, ponieważ usuwają potrzebę dodatkowych kolejek i pamięci podręcznych, które tradycyjnie nie są wymagane do rozwiązania wymagań dotyczących dostępności i opóźnień w przypadku aplikacji czystych bezstanowych. Ze względu na to, że usługi stanowe mają wysoką dostępność i małe opóźnienia, w aplikacji można zarządzać mniej więcej.

Poniższe diagramy ilustrują różnice między projektowaniem aplikacji, która jest bezstanowa i jednego ze stanowych. Dzięki wykorzystaniu modeli programowania [Reliable Services](service-fabric-reliable-services-introduction.md) i [Reliable Actors](service-fabric-reliable-actors-introduction.md) , usługi stanowe zmniejszają złożoność aplikacji przy jednoczesnym osiągnięciu dużej przepływności i małych opóźnień.

Oto przykładowa aplikacja korzystająca z usług bezstanowych: ![ aplikacja, która korzysta z usług bezstanowych][Image1]

Oto przykładowa aplikacja korzystająca z usług stanowych: ![ aplikacja, która korzysta z usług stanowych][Image2]

## <a name="next-steps"></a>Następne kroki

* Zacznij tworzyć bezstanowe i stanowe usługi przy użyciu Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) i [Reliable Actors](service-fabric-reliable-actors-get-started.md) modelami programowania.
* Odwiedź Centrum architektury platformy Azure, aby uzyskać wskazówki dotyczące [tworzenia mikrousług na platformie Azure](/azure/architecture/microservices/).
* Przejdź do [platformy Azure Service Fabric aplikacji i najlepszych](service-fabric-best-practices-overview.md) rozwiązań dotyczących projektowania aplikacji.

* Zobacz też:
  * [Informacje o mikrousługach](service-fabric-overview-microservices.md)
  * [Definiowanie stanu usługi i zarządzanie nim](service-fabric-concepts-state.md)
  * [Dostępność usług](service-fabric-availability-services.md)
  * [Skalowanie usług](service-fabric-concepts-scalability.md)
  * [Usługi partycji](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png

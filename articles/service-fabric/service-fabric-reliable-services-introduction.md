---
title: Omówienie modelu programowania niezawodnej usługi
description: Dowiedz się więcej o modelu programowania niezawodnej usługi Service Fabric i zacznij pisać własne usługi.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 50b8cae11cef5ee8670e2e0270fd8506b1dad4d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576540"
---
# <a name="reliable-services-overview"></a>Omówienie usług Reliable Services

Platforma Azure Service Fabric upraszcza pisanie i zarządzanie bezstanowe i stanowe usługi. W tym temacie omówiono:

* Reliable Services model programowania dla usług bezstanowych i stanowych.
* Opcje, które należy wykonać podczas pisania niezawodnej usługi.
* Niektóre scenariusze i przykłady użycia Reliable Services i sposobu ich pisania.

*Reliable Services* jest jednym z modeli programowania dostępnych w Service Fabric. Innym jest *niezawodnym* modelem programowania aktora, który zapewnia wirtualną platformę aplikacji [aktora](https://research.microsoft.com/en-us/projects/orleans/) w oparciu o model Reliable Services. Aby uzyskać więcej informacji na temat Reliable Actors, zobacz [wprowadzenie do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric zarządza okresem istnienia usług, od aprowizacji i wdrożenia za pośrednictwem uaktualniania i usuwania, za pośrednictwem [Service Fabric zarządzania aplikacjami](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Co to są Reliable Services

Reliable Services zapewnia prosty, zaawansowany model programistyczny najwyższego poziomu ułatwiający określenie, co jest ważne dla aplikacji. Korzystając z modelu programowania Reliable Services, uzyskasz następujące korzyści:

* Dostęp do Service Fabric interfejsów API. W przeciwieństwie do usług Service Fabric, które są modelowane jako [pliki wykonywalne gościa](service-fabric-guest-executables-introduction.md), Reliable Services mogą bezpośrednio używać interfejsów API Service Fabric. Dzięki temu usługi mogą:
  * Wykonywanie zapytań względem systemu
  * Raportowanie kondycji jednostek w klastrze
  * Otrzymywanie powiadomień o konfiguracji i zmianach kodu
  * Znajdź i Komunikuj się z innymi usługami,
  * Korzystanie z [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md)
  * Dostęp do wielu innych funkcji — wszystko to z modelu programowania pierwszej klasy w kilku językach programowania.
* Prosty model służący do uruchamiania własnego kodu, który jest taki sam jak inne znane modele programowania. Twój kod zawiera dobrze zdefiniowany punkt wejścia i umożliwia łatwe zarządzanie cyklem życia.
* Model komunikacji podłączanej. Używaj wybranego transportu, takiego jak HTTP z [interfejsem API sieci Web](./service-fabric-reliable-services-communication-aspnetcore.md), WebSockets, niestandardowych protokołów TCP lub innych. Reliable Services zapewnić pewne wspaniałe dostępne opcje, których możesz użyć, lub możesz podać własne.
* W przypadku usług stanowych model programowania Reliable Services pozwala spójnie i niezawodne przechowywanie stanu bezpośrednio w ramach usługi przy użyciu [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md). Niezawodne Kolekcje to prosty zestaw klas wysokiej dostępności i niezawodnych kolekcji, które będą znane wszystkim użytkownikom, którzy korzystali z kolekcji języka C#. Tradycyjnie niezawodne zarządzanie Stanami wymaga usług zewnętrznych. Za pomocą niezawodnych kolekcji można przechowywać swój stan obok obliczeń o tej samej wysokiej dostępności i niezawodności, które mają być oczekiwane w przypadku magazynów zewnętrznych z wysoką dostępnością. Ten model zwiększa również opóźnienia, ponieważ są umieszczane w tym miejscu obliczenia i stan, który musi działać.

## <a name="what-makes-reliable-services-different"></a>Co sprawia, że Reliable Services różne

Reliable Services różnią się od wcześniej zapisanych usług, ponieważ Service Fabric oferuje następujące informacje:

* **Niezawodność** — usługa działa nawet w niezawodnych środowiskach, w których maszyny mogą kończyć się niepowodzeniem lub powodować problemy z siecią lub w przypadku, gdy same usługi napotkały błędy i awarie. W przypadku usług stanowych stan jest zachowywany nawet w obecności sieci lub innych awarii.
* **Dostępność** — usługa jest dostępna i odpowiadać. Service Fabric utrzymuje żądaną liczbę uruchomionych kopii.
* **Skalowalność** — usługi są oddzielone od określonego sprzętu i mogą rosnąć lub zmniejszać się zgodnie z potrzebami poprzez dodanie lub usunięcie sprzętu lub innych zasobów. Usługi są łatwe do partycjonowania (szczególnie w przypadku stanowego), aby zapewnić, że usługa może skalować i obsługiwać błędy częściowe. Usługi mogą być tworzone i usuwane dynamicznie za pośrednictwem kodu, co pozwala na odtworzenie większej liczby wystąpień w miarę potrzeb, na przykład w odpowiedzi na żądania klientów. Na koniec Service Fabric zachęca usługi do lekkiej. Service Fabric umożliwia obsługę tysięcy usług w ramach jednego procesu, a nie wymaganie ani podwyższanie poziomu całego wystąpienia lub procesów systemu operacyjnego do jednego wystąpienia usługi.
* **Spójność** — wszelkie informacje przechowywane w niezawodnej usłudze mogą być gwarantowane jako zgodne. Dotyczy to nawet wielu niezawodnych kolekcji w ramach usługi. Zmiany między kolekcjami w ramach usługi mogą być wykonywane w sposób niepodzielny.

## <a name="service-lifecycle"></a>Cykl życia usługi

Niezależnie od tego, czy usługa jest stanowa, czy bezstanowa, Reliable Services zapewnić prosty cykl życia, który umożliwia szybkie podłączenie kodu i rozpoczęcie pracy.  Wprowadzenie nowej usługi i uruchomienia wymaga wdrożenia dwóch metod:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** — ta metoda polega na tym, że usługa definiuje stosy komunikacji, których chce użyć. Stos komunikacyjny, taki jak [interfejs API sieci Web](./service-fabric-reliable-services-communication-aspnetcore.md), definiuje punkt końcowy nasłuchujący lub punkty końcowe usługi (jak klienci docierają do usługi). Definiuje także sposób, w jaki komunikaty pojawiają się w pozostałej części kodu usługi.
* **RunAsync** — ta metoda polega na tym, że Usługa uruchamia swoją logikę biznesową, oraz miejsce, w której można uruchomić wszystkie zadania w tle, które powinny być uruchamiane przez okres istnienia usługi. Podany token anulowania jest sygnałem, kiedy ta pracy powinna zostać zatrzymana. Na przykład, jeśli usługa musi ściągać komunikaty z niezawodnej kolejki i przetwarzać je, jest to miejsce, w którym działa.

Jeśli po raz pierwszy uczysz się o niezawodnych usługach, przeczytaj temat. Jeśli szukasz szczegółowego przewodnika po cyklu życia niezawodnych usług, zapoznaj się z [omówieniem cyklu życia Reliable Services](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Przykładowe usługi

Przyjrzyjmy się bliżej, jak model Reliable Services współpracuje z usługami bezstanowymi i stanowymi.

### <a name="stateless-reliable-services"></a>Bezstanowe Reliable Services

*Usługa bezstanowa* to taka, w której nie ma stanu utrzymywane w ramach usługi w ramach wywołań. Wszelkie obecne Stany są całkowicie jednorazowe i nie wymagają synchronizacji, replikacji, trwałości ani wysokiej dostępności.

Rozważmy na przykład kalkulator, który nie ma pamięci i odbiera wszystkie warunki i operacje, które mają być wykonywane jednocześnie.

W takim przypadku `RunAsync()` (C#) lub `runAsync()` (Java) usługi może być pusta, ponieważ nie ma żadnego przetwarzania zadań w tle wymaganych przez usługę. Po utworzeniu usługi kalkulatora zwraca wartość `ICommunicationListener` (C#) lub `CommunicationListener` (Java) (na przykład [internetowy interfejs API](./service-fabric-reliable-services-communication-aspnetcore.md)) otwierającą punkt końcowy nasłuchiwania na niektórych portach. Ten punkt końcowy nasłuchiwania przechwytuje do różnych metod obliczeń (na przykład: "Add (N1, N2)"), które definiują publiczny interfejs API kalkulatora.

W przypadku wywołania z klienta odpowiednia metoda jest wywoływana i usługa Kalkulator wykonuje operacje na danych i zwraca wynik. Nie przechowuje żadnego stanu.

Nieprzechowywanie żadnego stanu wewnętrznego sprawia, że ten przykładowy Kalkulator jest prosty. Jednak większość usług nie bezstanowo. Zamiast tego Externalize swój stan do innego magazynu. (Na przykład jakakolwiek aplikacja sieci Web, która opiera się na zachowaniu stanu sesji w magazynie zapasowym lub pamięci podręcznej, nie jest bezstanowa).

Typowym przykładem sposobu używania usług bezstanowych w Service Fabric jest jako fronton, który uwidacznia publiczny interfejs API dla aplikacji sieci Web. Usługa frontonu komunikuje się z usługami stanowymi, aby zakończyć żądanie użytkownika. W takim przypadku wywołania od klientów są kierowane do znanego portu, na przykład 80, gdzie usługa bezstanowa nasłuchuje. Ta usługa bezstanowa odbiera wywołanie i określa, czy wywołanie pochodzi od zaufanej strony, a do której usługi jest ona przeznaczona.  Następnie usługa bezstanowa przekazuje wywołanie do poprawnej partycji usługi stanowej i czeka na odpowiedź. Gdy usługa bezstanowa odbierze odpowiedź, odpowiada pierwotnemu klientowi. Przykładem takiej usługi jest *Service Fabric wprowadzenie* przykład ([C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), między innymi Service Fabric przykładów w tym repozytorium.

### <a name="stateful-reliable-services"></a>Stanowe Reliable Services

*Usługa stanowa* to taka, która musi mieć część stanu, która jest spójna i jest obecna, aby usługa działała prawidłowo. Należy wziąć pod uwagę usługę, która ciągle oblicza średnią kroczącą niektórych wartości na podstawie otrzymywanych aktualizacji. W tym celu należy mieć bieżący zestaw żądań przychodzących, które muszą zostać przetworzone, i bieżącą średnią. Każda usługa, która pobiera, przetwarza i zapisuje informacje w zewnętrznym magazynie (na przykład w przypadku obiektów blob platformy Azure lub magazynu tabel), jest stanowa. Po prostu przechowuje swój stan w zewnętrznym magazynie Stanów.

Większość usług dzisiaj przechowuje swoje Stany zewnętrznie, ponieważ magazyn zewnętrzny zapewnia niezawodność, dostępność, skalowalność i spójność tego stanu. W Service Fabric usługi nie są wymagane do przechowywania ich stanu zewnętrznego. W Service Fabric są brane pod uwagę te wymagania dotyczące kodu usługi i stanu usługi.

Załóżmy, że chcemy napisać usługę, która przetwarza obrazy. W tym celu usługa przyjmuje obraz i serię konwersji do wykonania na tym obrazie. Ta usługa zwraca odbiornik komunikacyjny (Załóżmy, że jest to WebAPI), który uwidacznia interfejs API, taki jak `ConvertImage(Image i, IList<Conversion> conversions)` . Po odebraniu żądania usługa zapisuje je w `IReliableQueue` i zwraca identyfikator klienta, aby można było śledzić żądanie.

W tej usłudze `RunAsync()` może być bardziej skomplikowany. W usłudze znajduje się pętla `RunAsync()` , w której są wykonywane żądania ściągnięcia z `IReliableQueue` i wykonywane są wymagane konwersje. Wyniki są przechowywane w `IReliableDictionary` tak, że gdy klient wróci do nich, mogą uzyskać skonwertowane obrazy. Aby upewnić się, że nawet w przypadku wystąpienia awarii obraz nie zostanie utracony, ta niezawodna usługa spowodowałaby wyjęcie z kolejki, przeprowadzenie konwersji i przechowanie wyniku wszystkie w pojedynczej transakcji. W takim przypadku wiadomość zostanie usunięta z kolejki, a wyniki są przechowywane w słowniku wynikowym tylko wtedy, gdy konwersje są kompletne. Alternatywnie usługa może pobrać obraz z kolejki i natychmiast go zapisać w magazynie zdalnym. Zmniejsza to ilość Stanów, którymi usługa musi zarządzać, ale zwiększa złożoność, ponieważ usługa musi przechowywać niezbędne metadane do zarządzania magazynem zdalnym. W obu przypadkach, jeśli coś nie powiodło się w środku, żądanie pozostaje w kolejce oczekujące na przetworzenie.

Mimo że ta usługa jest taka sama jak typowa usługa .NET, różnica polega na tym, że używane struktury danych ( `IReliableQueue` i `IReliableDictionary` ) są udostępniane przez Service Fabric i są wysoce niezawodne, dostępne i spójne.

## <a name="when-to-use-reliable-services-apis"></a>Kiedy używać interfejsów API Reliable Services

Rozważ Reliable Services interfejsów API, jeśli:

* Kod usługi (i opcjonalny stan) powinny być wysoce dostępne i niezawodne.
* Potrzebne są gwarancje transakcyjne w wielu jednostkach stanu (na przykład zamówienia i pozycje wiersza zamówienia).
* Stan aplikacji może być naturalnie modelowany jako niezawodne słowniki i kolejki.
* Twój kod lub stan aplikacji musi mieć wysoką dostępność z odczytami i zapisami o małym opóźnieniu.
* Aplikacja musi kontrolować współbieżność lub stopień szczegółowości operacji transakcyjnych w jednej lub większej liczbie niezawodnych kolekcji.
* Chcesz zarządzać komunikacją lub kontrolować schemat partycjonowania usługi.
* Kod wymaga środowiska uruchomieniowego w dowolnym wątku.
* Aplikacja wymaga dynamicznego tworzenia lub niszczenia niezawodnych słowników, lub całych usług w środowisku uruchomieniowym.
* Należy programistycznie kontrolować funkcje tworzenia kopii zapasowych i przywracania Service Fabric udostępniane w stanie usługi.
* Twoja aplikacja musi obsługiwać historię zmian dla jej jednostek stanu.
* Chcesz opracowywać lub korzystać z niestandardowych dostawców stanu innych firm.

## <a name="next-steps"></a>Następne kroki

* [Reliable Services — Szybki Start](service-fabric-reliable-services-quick-start.md)
* [Niezawodne Kolekcje](service-fabric-reliable-services-reliable-collections.md)
* [Model programowania Reliable Actors](service-fabric-reliable-actors-introduction.md)

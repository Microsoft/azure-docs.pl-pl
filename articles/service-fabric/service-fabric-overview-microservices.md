---
title: Wprowadzenie do mikrousług na platformie Azure
description: Omówienie przyczyn kompilowania aplikacji w chmurze z podejściem mikrousług jest ważne w przypadku nowoczesnych zastosowań aplikacji oraz sposobu, w jaki usługa Azure Service Fabric zapewnia platformę do osiągnięcia tego celu.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d20e04820c87a7390d9c20e511259ee9860c27f5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575656"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Dlaczego warto stosować podejście mikrousług do kompilowania aplikacji

W przypadku deweloperów oprogramowania aplikacja nie jest częścią żadnej nowej. Zwykle używane jest podejście warstwowe z magazynem zaplecza, logiką biznesową warstwy środkowej i interfejsem użytkownika frontonu. Zmiany *w* ciągu ostatnich kilku lat polegają na tym, że deweloperzy tworzą aplikacje rozproszone dla chmury.

Poniżej przedstawiono niektóre zmieniające się potrzeby biznesowe:

* Usługa, która jest zbudowana i oparta na dużej skali, aby dotrzeć do klientów w nowych regionach geograficznych.
* Szybsze dostarczanie funkcji i funkcji w celu elastycznego reagowania na żądania klientów.
* Ulepszone wykorzystanie zasobów w celu obniżenia kosztów.

Te potrzeby biznesowe mają wpływ na *sposób* kompilowania aplikacji.

Aby uzyskać więcej informacji na temat podejścia platformy Azure do mikrousług, zobacz [mikrousługi: obroty aplikacji obsługiwane przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Podejście do projektowania lity a mikrousług

Aplikacje są rozwijane z upływem czasu. Pomyślne aplikacje są rozwijane przez wygodę dla osób. Niepowodzenie aplikacje nie są rozwijane i ostatecznie są przestarzałe. Oto pytanie: jak dużo wiesz już o Twoich wymaganiach i jakie będą w przyszłości? Załóżmy na przykład, że tworzysz aplikację raportowania dla działu w firmie. Upewnij się, że aplikacja ma zastosowanie tylko w ramach firmy i że raporty nie będą dłużej przechowywane. Twoje podejście będzie się różnić od tego, co mówi, tworząc usługę, która zapewnia zawartość wideo na dziesiątki milionów klientów.

Czasami, gdy jest to czynnik prowadzący do prowadzenia koncepcji, można dowiedzieć się, jak to działa. Wiesz, że aplikacja może zostać przeprojektowana później. Istnieje niewielki punkt w przekroczeniu inżynierii, który nigdy nie jest używany. Z drugiej strony, gdy firmy kompilują chmurę, oczekuje się wzrostu i użycia. Wzrost i skala są nieprzewidywalne. Chcemy szybko utworzyć prototyp, jednocześnie wiedząc, że znajdujemy się na ścieżce, która może obsłużyć kolejne sukcesy. Jest to podejście do uruchamiania produkcji oszczędnej: kompilowanie, mierzenie, uczenie i iteracje.

W trakcie oceny ery klient/serwer zamierzamy skupić się na tworzeniu aplikacji warstwowych przy użyciu określonych technologii w poszczególnych warstwach. Termin " *monolityczna* aplikacja" ma na celu opisanie tych metod. Interfejsy, które zaprojektowano między warstwami i w ramach każdej warstwy są używane bardziej blisko siebie. Deweloperzy zaprojektowane i rozłożono klasy, które zostały skompilowane do bibliotek i połączone w kilka plików wykonywalnych i bibliotek DLL.

Istnieją zalety podejścia do projektowania monolitycznego. Aplikacje monolityczne są często prostsze do projektowania, a wywołania między składnikami są szybsze, ponieważ te wywołania często korzystają z komunikacji międzyprocesowej (IPC). Ponadto wszyscy testuje pojedynczy produkt, który bardziej wydajnie korzysta z zasobów ludzkich. Minusem polega na tym, że istnieje ścisłe sprzężenie między warstwami warstwowymi i nie można skalować pojedynczych składników. Jeśli potrzebujesz poprawek lub uaktualnień, musisz poczekać, aż inni ukończyją testy. Trudniejsze jest to elastyczne.

Mikrousługi dotyczą tych downsides i są bardziej ściśle dopasowane z poprzednimi wymaganiami biznesowymi. Jednak mają również zarówno korzyści, jak i zobowiązania. Zalety mikrousług polegają na tym, że każdy z nich zazwyczaj hermetyzuje prostsze funkcje biznesowe, które można skalować w poziomie lub w, testować, wdrażać i zarządzać niezależnie. Jedną z ważnych zalet podejścia mikrousług jest to, że zespoły są sterowane bardziej dzięki scenariuszom biznesowym niż przez technologię. Mniejsze zespoły opracowują mikrousługi na podstawie scenariusza klienta i używają wszelkich technologii, których chcą używać.

Innymi słowy, organizacja nie musi znormalizować Tech, aby zachować aplikacje mikrousług. Poszczególne zespoły mogą wykonywać te działania, co ma sens dla nich, na podstawie ekspertyzy zespołu lub najbardziej odpowiednich do rozwiązania problemu. W przypadku zestawu zalecanych technologii, takich jak określony Magazyn NoSQL lub struktura aplikacji sieci Web, jest preferowany.

Minusem mikrousług polega na tym, że trzeba zarządzać więcej osobnymi jednostkami i zajmować się bardziej złożonymi wdrożeniami i przechowywaniem wersji. Ruch sieciowy między mikrousługami zwiększa się w miarę jak odpowiadające opóźnienia sieci. Wiele rozmów, szczegółowe usługi mogą spowodować okropnej wydajności. Bez narzędzi, które ułatwiają wyświetlanie tych zależności, trudno jest zobaczyć cały system.

Standardy sprawiają, że podejście mikrousług działa przez określenie sposobu komunikowania się i tolerowania tylko potrzebnych rzeczy od usługi, a nie sztywnych umów. Ważne jest, aby zdefiniować te kontrakty w przód w projekcie, ponieważ usługi są od siebie niezależne. Inny opis dotyczący projektowania z podejściem mikrousług jest "szczegółowo zorientowanym na usługi architekturą (SOA)".

***W najprostszej strategii projektowania mikrousług jest to oddzielna Federacja usług z niezależnymi zmianami każdego i uzgodnionych standardów komunikacji.** _

W miarę tworzenia większej liczby aplikacji w chmurze użytkownicy wyszukali, że ta dekompozycja ogólnej aplikacji w niezależny, ukierunkowany na scenariusze usługi jest lepszym długoterminowym podejściem.

## <a name="comparison-between-application-development-approaches"></a>Porównanie metod tworzenia aplikacji

![Programowanie aplikacji Service Fabric platform][Image1]

1) Aplikacja monolityczna zawiera funkcje specyficzne dla domeny i jest zwykle dzielona na warstwy funkcjonalne, takie jak Web, Business i Data.

2) Skalowanie aplikacji monolitycznej jest skalowane przez klonowanie jej na wielu serwerach/maszynach wirtualnych/kontenerach.

3) Aplikacja mikrousług oddziela funkcje do oddzielnych mniejszych usług.

4) Podejście mikrousługowe jest skalowane przez wdrożenie poszczególnych usług niezależnie od tworzenia wystąpień tych usług między serwerami/maszynami wirtualnymi/kontenerami.

Projektowanie za pomocą podejścia mikrousług nie jest odpowiednie dla wszystkich projektów, ale jest bardziej ścisłe w porównaniu z celami biznesowymi opisanymi wcześniej. Począwszy od podejścia monolitycznego, może się okazać, że wiesz, że będziesz mieć możliwość późniejszego ponownego uruchomienia kodu w projekcie mikrousług. Często zaczynasz od monolitycznej aplikacji i wolno ją podzielić na etapy, rozpoczynając od obszarów funkcjonalnych, które muszą być bardziej skalowalne lub elastyczne.

Korzystając z podejścia mikrousług, tworzysz swoją aplikację wielu małych usług. Te usługi działają w kontenerach wdrożonych w klastrze maszyn. Mniejsze zespoły opracowują usługę, która koncentruje się na scenariuszu i niezależnie testuje, wersji, wdraża i skaluje każdą usługę, dzięki czemu cała aplikacja może się rozwijać.

## <a name="what-is-a-microservice"></a>Co to jest mikrousługa?

Istnieją różne definicje mikrousług. Większość z tych cech mikrousług jest ogólnie zaakceptowana:

_ Należy hermetyzować klienta lub scenariusz biznesowy. Jaki problem jest rozwiązywany?
* Opracowane przez niewielki zespół inżynieryjny.
* Zapisanie w dowolnym języku programowania przy użyciu dowolnej platformy.
* Składają się z kodu i opcjonalnego stanu, z których oba są zależne od wersji, wdrożone i skalowane.
* Współdziałanie z innymi mikrousługami za pomocą dobrze zdefiniowanych interfejsów i protokołów.
* Mają unikatowe nazwy (URL), które są używane do rozpoznawania ich lokalizacji.
* Pozostają spójne i dostępne w obecności niepowodzeń.

Aby zsumować sumę:

***Aplikacje mikrousług składają się z małych, niezależnych pod względem wersji i skalowalnych usług ukierunkowanych na klienta, które komunikują się ze sobą za pośrednictwem standardowych protokołów ze dobrze zdefiniowanymi interfejsami.** _

### <a name="written-in-any-programming-language-using-any-framework"></a>Zapisanie w dowolnym języku programowania przy użyciu dowolnej struktury

Jako deweloperzy chcą bezpłatnie wybrać język lub strukturę, w zależności od naszych umiejętności i potrzeb usługi, którą tworzymy. W przypadku niektórych usług można wyrównać zalety wydajności języka C++ powyżej innych elementów. W przypadku innych ułatwień zarządzania zarządzanego w języku C# lub Java mogą być ważniejsze. W niektórych przypadkach może być konieczne użycie określonej biblioteki partnerskiej, technologii magazynowania danych lub metody w celu udostępnienia usługi klientom.

Po wybraniu technologii należy wziąć pod uwagę zarządzanie i skalowanie w cyklu życia usługi.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Umożliwia kod i stan, które mają być niezależnie w wersji, wdrożone i skalowane

Niezależnie od tego, w jaki sposób napisać mikrousługi, kod i opcjonalnie stan, należy polegać na tym, że są one wdrażane, uaktualniane i skalowane. Ten problem trudno rozwiązać, ponieważ jest dostępny do wyboru technologii. Aby skalować, należy zrozumieć, jak podzielić na partycje (lub fragmentu) zarówno kod, jak i stan. Gdy kod i stan używają różnych technologii, które są wspólne dzisiaj, skrypty wdrażania dla mikrousługi muszą mieć możliwość ich skalowania. Ta separacja ma również charakter elastyczny i elastyczność, dzięki czemu można uaktualnić niektóre mikrousługi bez konieczności jednoczesnego uaktualniania wszystkich mikrousług.

Wróćmy do naszego porównania podejść litych i mikrousług przez chwilę. Ten diagram przedstawia różnice w podejściach do przechowywania stanu:

#### <a name="state-storage-for-the-two-approaches"></a>Magazyn Stanów dla dwóch metod

![Magazyn Stanów Service Fabric platformy][Image2]

_*_Podejście monolityczne po lewej stronie ma jedną bazę danych i warstwy określonych technologii._*_

_*_Podejście mikrousługowe, z prawej strony, ma Graf wzajemnie połączonych mikrousług, w których stan jest zazwyczaj objęty mikrousługą i używane są różne technologie._*_

W podejściu monolitycznym aplikacja zwykle używa pojedynczej bazy danych. Zalety korzystania z jednej bazy danych są takie same jak w jednej lokalizacji, co ułatwia ich wdrażanie. Każdy składnik może mieć jedną tabelę do przechowywania stanu. Zespoły muszą ściśle oddzielić stan, który jest wyzwaniem. Niewątpliwie ktoś będzie skłonny dodać kolumnę do istniejącej tabeli klienta, wykonać sprzężenie między tabelami i utworzyć zależności w warstwie magazynu. Po wykonaniu tej czynności nie można skalować pojedynczych składników.

W podejściu mikrousług każda usługa zarządza i przechowuje własny stan. Każda usługa jest odpowiedzialna za skalowanie kodu i stanu razem w celu spełnienia wymagań usługi. Minusem polega na tym, że w przypadku konieczności tworzenia widoków lub zapytań dotyczących danych aplikacji należy wykonać zapytanie w wielu magazynach Stanów. Ten problem jest zwykle rozwiązywany przez oddzielną mikrousługę, która kompiluje widok w kolekcji mikrousług. Jeśli konieczne jest uruchomienie wielu zapytań niezależnych dotyczących danych, należy rozważyć zapisanie danych poszczególnych mikrousług do usługi magazynowania danych w celu analizy w trybie offline.

Mikrousługi mają wersję. Jest możliwe uruchamianie różnych wersji mikrousług obok siebie. Nowsza wersja mikrousług może zakończyć się niepowodzeniem podczas uaktualniania i musi zostać wycofana do wcześniejszej wersji. Przechowywanie wersji jest również pomocne w przypadku testowania A/B, gdzie różni użytkownicy mają różne wersje usługi. Na przykład często należy uaktualnić mikrousługi dla określonego zestawu klientów w celu przetestowania nowych funkcji przed przeprowadzeniem szerszego wdrożenia.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Współdziała z innymi mikrousługami za pomocą dobrze zdefiniowanych interfejsów i protokołów

W ciągu ostatnich 10 lat zostały opublikowane obszerne informacje opisujące wzorce komunikacji w architekturze zorientowanym na usługę. Ogólnie rzecz biorąc, komunikacja usług używa podejścia REST z protokołami HTTP i TCP oraz XML lub JSON jako formatem serializacji. Z punktu widzenia interfejsu jest podejmowane podejście do projektowania sieci Web. Jednak nic nie powinno się zatrzymywać z użyciem protokołów binarnych i własnych formatów danych. Należy pamiętać, że użytkownicy będą mieli trudniejszy czas na korzystanie z mikrousług, jeśli te protokoły i formaty nie są dostępne do otwarcia.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Ma unikatową nazwę (adres URL) służącą do rozpoznawania jej lokalizacji

Mikrousługa musi być adresowana wszędzie tam, gdzie jest uruchomiona. Jeśli myślisz o maszynach, na których jest uruchomiona konkretna mikrousługa, rzeczy mogą być szybko niepotrzebne.

W taki sam sposób, w jaki system DNS rozwiązuje określony adres URL do konkretnej maszyny, mikrousługa potrzebuje unikatowej nazwy, aby można było odnaleźć jej bieżącą lokalizację. Mikrousługi muszą mieć adresy nazw, które są niezależne od infrastruktury, w której są uruchomione. Oznacza to, że istnieje interakcja między wdrożeniem usługi a zakryciem, ponieważ istnieje konieczność korzystania z rejestru usługi. W przypadku niepowodzenia maszyny usługa rejestru musi powiedzieć, w której lokalizacji została przeniesiona usługa.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Pozostaje spójne i dostępne w obecności błędów

Postępowanie z nieoczekiwanymi awariami jest jednym z najtrudniejszych problemów, które należy rozwiązać, szczególnie w systemie rozproszonym. Większość kodu, który piszesz jako Deweloperzy, jest przeznaczony do obsługi wyjątków. Podczas testowania poświęcamy również najwięcej czasu na obsługę wyjątków. Proces jest bardziej wykonywany niż pisanie kodu do obsługi błędów. Co się stanie, gdy maszyna, na której działa mikrousługa, nie powiedzie się? Należy wykryć awarię, która jest własnym problemem. Ale trzeba również ponownie uruchomić mikrousługę.

W celu zapewnienia dostępności mikrousług musi być odporna na awarie i może zostać uruchomiona ponownie na innym komputerze. Oprócz tych wymagań dotyczących odporności dane nie powinny być tracone, a dane muszą pozostać spójne.

Odporność jest trudna do osiągnięcia w przypadku wystąpienia awarii podczas uaktualniania aplikacji. Nie ma potrzeby odzyskiwania mikrousługi, pracy z systemem wdrażania. Należy określić, czy może kontynuować przejście do nowszej wersji lub przywrócenie poprzedniej wersji, aby zachować spójny stan. Należy wziąć pod uwagę kilka pytań, na przykład informacje o tym, czy dostępne są wystarczające komputery, aby kontynuować ich przechodzenie i sposób odzyskiwania poprzednich wersji mikrousług. Aby podejmować te decyzje, potrzebna jest mikrousługa do emitowania informacji o kondycji.

### <a name="reports-health-and-diagnostics"></a>Raporty dotyczące kondycji i diagnostyki

Może wydawać się oczywiste i często pojawia się, a mikrousługa musi zgłosić swoją kondycję i diagnostykę. W przeciwnym razie masz niewielki wgląd w kondycję z perspektywy operacji. Skorelowanie zdarzeń diagnostycznych w zestawie niezależnych usług i rozróżnianie zegara maszynowego, aby zrozumieć kolejność zdarzeń, jest trudne. W taki sam sposób, w jaki współdziałasz z mikrousługą przez uzgodnione protokoły i formaty danych, należy przeprowadzić standaryzację rejestrowania kondycji i zdarzeń diagnostycznych, które ostatecznie zakończą się w magazynie zdarzeń na potrzeby wykonywania zapytań i wyświetlania. Z podejściem mikrousług, różne zespoły muszą zgadzać się w jednym formacie rejestrowania. Musi istnieć spójne podejście do wyświetlania zdarzeń diagnostycznych w aplikacji jako całości.

Kondycja różni się od diagnostyki. Kondycja polega na tym, że usługa mikrousług zgłasza bieżący stan, aby podjąć odpowiednie działania. Dobrym przykładem jest praca z mechanizmami uaktualniania i wdrażania w celu zapewnienia dostępności. Chociaż usługa może być w złej kondycji ze względu na awarię procesu lub ponowny rozruch komputera, usługa może nadal działać. Ostatnim wymaganiem jest, aby sytuacja była gorsza, rozpoczynając od uaktualnienia. Najlepszym rozwiązaniem jest zbadanie pierwszego lub poczekanie na odzyskanie mikrousługi. Zdarzenia dotyczące kondycji z mikrousługi pomagają nam podejmować świadome decyzje i, w efekcie, ułatwiają tworzenie samonaprawczych usług.

## <a name="guidance-for-designing-microservices-on-azure"></a>Wskazówki dotyczące projektowania mikrousług na platformie Azure

Odwiedź centrum architektury platformy Azure, aby uzyskać wskazówki dotyczące [projektowania i tworzenia mikrousług na platformie Azure](/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric jako platforma mikrousług

Platforma Azure Service Fabric wystąpiła, gdy firma Microsoft przeszedł z dostarczania opakowanych produktów, które zazwyczaj były monolityczne, aby dostarczać usługi. Środowisko tworzenia i obsługi dużych usług, takich jak Azure SQL Database i Azure Cosmos DB, Service Fabric. Platforma powstała w miarę upływu czasu, gdy więcej usługi zostały przez niego wdrożone. Service Fabric musiał działać nie tylko na platformie Azure, ale także w autonomicznych wdrożeniach systemu Windows Server.

_*_Celem Service Fabric jest rozwiązanie problemów twardych kompilowania i uruchamiania usługi oraz wydajne korzystanie z zasobów infrastruktury, dzięki czemu zespoły mogą rozwiązywać problemy biznesowe za pomocą podejścia mikrousług._*_

Ten krótki klip wideo zawiera wprowadzenie do usługi Service Fabric i mikrousług:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

Service Fabric pomaga tworzyć aplikacje korzystające z rozwiązań mikrousług, zapewniając:

_ Platforma udostępniająca usługi systemowe do wdrażania, uaktualniania, wykrywania i ponownego uruchamiania usług zakończonych niepowodzeniem, odnajdywania usług, przesyłania komunikatów, zarządzania stanem i monitorowania kondycji.
* Możliwość wdrażania aplikacji uruchamianych w kontenerach lub jako procesy. Service Fabric jest kontenerem i koordynatorem procesów.
* Wydajne programowanie interfejsów API, które ułatwiają tworzenie aplikacji jako mikrousług: [ASP.NET Core, Reliable Actors i Reliable Services](service-fabric-choose-framework.md). Można na przykład uzyskać informacje dotyczące kondycji i diagnostyki albo skorzystać z wbudowanej wysokiej dostępności.

***Service Fabric niezależny od o sposobie tworzenia usługi i można użyć dowolnej technologii. Ale udostępnia wbudowane interfejsy API programowania, które ułatwiają tworzenie mikrousług.** _

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrowanie istniejących aplikacji do Service Fabric

Service Fabric umożliwia ponowne użycie istniejącego kodu i modernizację go przy użyciu nowych mikrousług. Istnieje pięć etapów związanych z modernizacją aplikacji i można ją uruchomić i zatrzymać na dowolnym etapie. Etapy są następujące:

1) Zacznij od tradycyjnego monolitycznej aplikacji.  
2) Dokonać. Użyj kontenerów lub plików wykonywalnych gościa, aby hostować istniejący kod w Service Fabric.  
3) Modernizowanie. Dodaj nowe mikrousługi obok istniejącego kodu kontenerowego.  
4) Wprowadzaj innowacje. Podziel aplikację monolityczną na mikrousługi w zależności od potrzeb.  
5) Przekształcanie aplikacji w mikrousługi. Przekształć istniejące aplikacje monolityczne lub twórz nowe aplikacje Greenfield.

![Migracja do mikrousług][Image3]

Należy pamiętać, że można _start i zatrzymywać na dowolnym z tych etapów *. Nie musisz postępować z następnym etapem. 

Przyjrzyjmy się przykładom dla każdego z tych etapów.

**Migrate (Migracja)**  
Z dwóch powodów wiele firm migruje istniejące aplikacje monolityczne do kontenerów:

* Obniżka kosztów — z powodu konsolidacji i usuwania istniejącego sprzętu lub w przypadku uruchamiania aplikacji z większą gęstością.
* Spójna umowa dotycząca wdrożenia między programowaniem i operacjami.

Obniżki kosztów są proste. W firmie Microsoft wiele istniejących aplikacji jest kontenerów, co prowadzi do milionów dolarów oszczędności. Spójne wdrożenie jest trudniejsze do oszacowania, ale jest równie ważne. Oznacza to, że deweloperzy mogą wybrać odpowiednie technologie, ale operacja akceptuje tylko jedną metodę wdrażania aplikacji i zarządzania nimi. Pozwala to wyeliminować operacje, które nie muszą być związane z złożonością obsługi różnych technologii, bez wymuszania deweloperów, aby wybierać tylko niektóre z nich. Zasadniczo każda aplikacja jest umieszczona w kontenerze na własne obrazy wdrażania.

Wiele organizacji zostało zatrzymanych w tym miejscu. Mają już zalety kontenerów, a Service Fabric zapewnia pełną obsługę zarządzania, w tym wdrażanie, uaktualnień, przechowywanie wersji, wycofywanie i monitorowanie kondycji.

**Modernizowanie**  
Modernizacja to dodawanie nowych usług wraz z istniejącym kodem kontenera. Jeśli zamierzasz napisać nowy kod, najlepiej wykonać małe kroki w dół ścieżki mikrousług. Może to oznaczać dodanie nowego punktu końcowego interfejsu API REST lub nowej logiki biznesowej. W ten sposób należy rozpocząć proces tworzenia nowych mikrousług i opracowywania ich oraz wdrażania.

**Innowacje**  
Podejście mikrousługowe uwzględnia zmieniające się potrzeby biznesowe. Na tym etapie należy zdecydować, czy rozpocząć dzielenie aplikacji monolitycznej na usługi, czy innowacje. Klasycznym przykładem jest to, że baza danych, która jest używana jako kolejka przepływu pracy, stanowi wąskie gardła przetwarzania. W miarę zwiększania się liczby żądań przepływów pracy należy przeprowadzić dystrybucję na potrzeby skalowania. Zapoznaj się z konkretną częścią aplikacji, która nie jest skalowana ani która musi być aktualizowana częściej, i Podziel ją jako mikrousługi i innowacji.

**Przekształcanie aplikacji w mikrousługi**  
Na tym etapie aplikacja jest w pełni złożona z mikrousług (lub podzielona na nie). Aby dotrzeć do tego punktu, przeprowadzono przejazd mikrousług. Możesz zacząć tutaj, ale nie tak, aby to zrobić bez platformy mikrousług, aby ułatwić Ci wymaganie znacznej inwestycji.

### <a name="are-microservices-right-for-my-application"></a>Czy mikrousługi są bezpośrednio dla mojej aplikacji?

Może. W firmie Microsoft, ponieważ coraz więcej zespołów rozpoczęło kompilację w chmurze ze względów firmy, wiele z nich osiąga korzyści wynikające z zastosowania mikrousług. Na przykład usługa Bing korzysta z mikrousług przez lata. W przypadku innych zespołów podejście mikrousług było nowe. Zespoły stwierdziły, że wystąpiły problemy, które mogą zostać rozwiązane poza podstawowymi obszarami siły. Dlatego Service Fabric uzyskano trakcję jako technologia tworzenia usług.

Celem Service Fabric jest zredukowanie złożoności tworzenia aplikacji mikrousług, aby nie trzeba było przełączać się jak wiele kosztów. Uruchamiaj małe, skalowane w razie konieczności, wycofaj usługi, dodawaj nowe i rozwijaj użycie przez klienta. Wiemy również, że istnieje wiele innych problemów, które nie zostały jeszcze rozwiązane, aby zwiększyć możliwości mikrousług dla większości deweloperów. Kontenery i model programowania aktora to przykłady małych kroków w tym kierunku. Wiemy, że coraz więcej innowacji zacznie ułatwić podejście mikrousług.

## <a name="next-steps"></a>Następne kroki

* [Mikrousługi: obroty aplikacji obsługiwane przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Centrum architektury platformy Azure: budowanie mikrousług na platformie Azure](/azure/architecture/microservices/)
* [Najlepsze rozwiązania dotyczące aplikacji i klastrów na platformie Azure Service Fabric](service-fabric-best-practices-overview.md)
* [Przegląd terminologii Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png

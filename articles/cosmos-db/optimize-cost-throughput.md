---
title: Optymalizowanie kosztu przepływności w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zoptymalizować koszty przepływności dla danych przechowywanych w Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ef0462b849210bc9b6963ab25e7a216c978f0568
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281071"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB

Oferując model przepływności aprowizacji, Azure Cosmos DB oferuje przewidywalną wydajność w dowolnej skali. Zarezerwowanie lub zainicjowanie przepływności przed czasem eliminuje "efekt sąsiada" w wydajności. Należy określić dokładną potrzebną przepływność i Azure Cosmos DB gwarantuje, że przepływność została skonfigurowana zgodnie z umową SLA.

Możesz rozpocząć od minimalnej przepływności wynoszącej 400 RU/s i skalować do dziesiątki milionów żądań na sekundę, a nawet więcej. Każde żądanie, które można wydać w odniesieniu do kontenera lub bazy danych usługi Azure Cosmos, takie jak żądanie odczytu, żądanie zapisu, żądanie zapytania, procedury składowane ma odpowiedni koszt odejmowany od zainicjowanej przepływności. Jeśli zainicjujesz 400 RU/s i wystawisz zapytanie, że koszt 40 jednostek ru, będzie można wydać 10 takich zapytań na sekundę. Każde żądanie wykraczające poza ten będzie miało wartość rate-Limited i należy ponowić próbę żądania. Jeśli używasz sterowników klienta, obsługują logikę automatycznego ponawiania.

Przepływność można aprowizować dla baz danych lub kontenerów, a każda strategia może ułatwić oszczędzanie kosztów w zależności od scenariusza.

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>Optymalizuj przez zainicjowanie przepływności na różnych poziomach

* Jeśli zainicjujesz przepływność w bazie danych, wszystkie kontenery, na przykład kolekcje/tabele/wykresy w tej bazie danych, mogą współużytkować przepływność na podstawie obciążenia. Przepływność zarezerwowana na poziomie bazy danych jest współdzielona nierównomiernie, w zależności od obciążenia określonego zestawu kontenerów.

* Jeśli zainicjujesz przepływność na kontenerze, przepływność jest gwarantowana dla tego kontenera, objęta umową SLA. Wybór klucza partycji logicznej jest kluczowy dla równomiernego rozkładu obciążenia we wszystkich partycjach logicznych kontenera. Więcej informacji można znaleźć w artykule [partycjonowanie](partitioning-overview.md) i [skalowanie w poziomie](partitioning-overview.md) .

Poniżej przedstawiono kilka wytycznych dotyczących podejmowania decyzji dotyczących strategii dotyczącej przepływności.

**Rozważ zainicjowanie przepływności dla bazy danych Azure Cosmos Database (zawierającej zestaw kontenerów), jeśli**:

1. Masz kilka dziesiątych kontenerów usługi Azure Cosmos i chcesz podzielić przepływność na niektóre lub wszystkie z nich. 

2. Przeprowadzasz migrację z pojedynczej dzierżawy bazy danych zaprojektowanej do uruchamiania na maszynach wirtualnych hostowanych przez IaaS lub lokalnie, na przykład z NoSQL lub z relacyjnymi bazami danych w celu Azure Cosmos DB. I jeśli masz wiele kolekcji/tabel/wykresów i nie chcesz wprowadzać żadnych zmian w modelu danych. Pamiętaj, że może być konieczne naruszenie niektórych korzyści oferowanych przez Azure Cosmos DB, jeśli nie aktualizujesz modelu danych podczas migracji z lokalnej bazy danych. Zaleca się, aby zawsze uzyskać dostęp do modelu danych, aby uzyskać największą wydajność i zoptymalizować koszty. 

3. Chcesz wchłonąć niezaplanowanych skoków w obciążeniach na podstawie przepływności puli na poziomie bazy danych z nieoczekiwanym wzrostem obciążenia. 

4. Zamiast ustawiać konkretną przepływność w poszczególnych kontenerach, należy zwrócić uwagę na łączną przepływność w zestawie kontenerów w ramach bazy danych.

**Rozważ zainicjowanie przepływności na poszczególnych kontenerach, jeśli:**

1. Masz kilka kontenerów usługi Azure Cosmos. Ponieważ Azure Cosmos DB jest Schema-niezależny od, kontener może zawierać elementy, które mają heterogeniczne schematy i nie wymaga od klientów tworzenia wielu typów kontenerów, po jednym dla każdej jednostki. Jest to zawsze opcja, którą należy wziąć pod uwagę, jeśli grupowanie odnosi się do kontenerów 10-20 w jednym kontenerze. Dzięki 400 jednostek ru dla kontenerów wszystkie kontenery 10-20 mogą być bardziej ekonomiczne. 

2. Chcesz kontrolować przepływność w konkretnym kontenerze i uzyskać gwarantowaną przepływność dla danego kontenera obsługiwanego przez umowę SLA.

**Weź pod uwagę hybrydowe powyższe dwie strategie:**

1. Jak wspomniano wcześniej, Azure Cosmos DB pozwala mieszać i dopasowywać powyższe dwie strategie, dzięki czemu możesz teraz korzystać z kontenerów w usłudze Azure Cosmos Database, co może współdzielić przepływność administracyjną na bazie danych, a także niektóre kontenery w tej samej bazie danych, które mogą mieć dedykowane ilości przewidzianej przepływności. 

2. Powyższe strategie można zastosować, aby korzystać z konfiguracji hybrydowej, w której na poziomie bazy danych jest obsługiwana przepustowość z niektórymi kontenerami, które mają dedykowaną przepływność.

Jak pokazano w poniższej tabeli, w zależności od wyboru interfejsu API, można zainicjować przepływność na różnej szczegółowości.

|Interfejs API|W przypadku **udostępnionej** przepływności Skonfiguruj |W przypadku **dedykowanej** przepływności Skonfiguruj |
|----|----|----|
|Interfejs API SQL|baza danych|Kontener|
|Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB|baza danych|Kolekcja|
|Interfejs API rozwiązania Cassandra|Przestrzeń kluczy|Tabela|
|Interfejs API języka Gremlin|Konto bazy danych|Graph|
|Interfejs API tabel|Konto bazy danych|Tabela|

Dzięki aprowizacji przepływności na różnych poziomach można zoptymalizować koszty na podstawie charakterystyki obciążenia. Jak wspomniano wcześniej, można programowo i w dowolnym momencie zwiększyć lub zmniejszyć zainicjowaną przepływność dla poszczególnych kontenerów lub zbiorowo w zestawie kontenerów. Elastycznie skalowanie przepływności w miarę zmian obciążenia oznacza płatność wyłącznie za skonfigurowane przepływność. Jeśli kontener lub zbiór kontenerów są dystrybuowane w wielu regionach, to przepływność skonfigurowana na kontenerze lub zestaw kontenerów jest gwarantowany do udostępnienia we wszystkich regionach.

## <a name="optimize-with-rate-limiting-your-requests"></a>Optymalizacja z szybkością ograniczania żądań

W przypadku obciążeń, które nie są zależne od opóźnienia, można zapewnić mniejszą przepływność i pozwolić aplikacji na ograniczenie szybkości obsługi, gdy rzeczywista przepływność przekracza zainicjowaną przepływność. Serwer zapobiegawczo żądanie z żądaniem `RequestRateTooLarge` (kod stanu HTTP 429) i zwraca `x-ms-retry-after-ms` nagłówek wskazujący ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowieniem próby wykonania żądania. 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>Logika ponawiania w zestawach SDK 

Natywne zestawy SDK (.NET/.NET Core, Java, Node.js i Python) niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, następna próba powiodła się.

Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób, która jest obecnie ustawiona na 9, może być niewystarczająca. W takich przypadkach klient zgłasza `RequestRateTooLargeException` kod stanu o stanie 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić, ustawiając wartość `RetryOptions` w wystąpieniu ConnectionPolicy. Domyślnie `RequestRateTooLargeException` kod stanu z 429 jest zwracany po upływie skumulowanego czasu oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, być wartością domyślną 9 lub wartości zdefiniowanej przez użytkownika. 

[MaxRetryAttemptsOnThrottledRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?view=azure-dotnet&preserve-view=true) jest ustawiona na 3, więc w tym przypadku, jeśli operacja żądania jest naliczana proporcjonalnie do przekroczenia zarezerwowanej przepływności dla kontenera, operacja żądania jest ponawiana trzy razy przed przekazaniem wyjątku do aplikacji. [MaxRetryWaitTimeInSeconds](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) jest ustawiona na 60, więc w tym przypadku, jeśli łączny czas oczekiwania ponowienia próby (w sekundach) od momentu pierwszego żądania przekracza 60 sekund, zostanie zgłoszony wyjątek.

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>Strategia partycjonowania i koszty aprowizowana przepływność

Dobrą strategią partycjonowania jest optymalizacja kosztów w Azure Cosmos DB. Upewnij się, że nie ma żadnych pochylenia partycji, które są udostępniane przez metryki magazynu. Upewnij się, że nie ma żadnych pochylenia przepływności dla partycji, która jest udostępniona z metrykami przepływności. Upewnij się, że nie ma żadnych pochylenia do określonych kluczy partycji. Klucze dominujące w magazynie są udostępniane za pomocą metryk, ale klucz będzie zależny od wzorca dostępu do aplikacji. Najlepiej jest myśleć o prawidłowym kluczu partycji logicznej. Dobry klucz partycji powinien mieć następującą charakterystykę:

* Wybierz klucz partycji, który rozkłada obciążenie równomiernie we wszystkich partycjach, a nawet w czasie. Innymi słowy, nie należy mieć niektórych kluczy do z większością danych i niektórych kluczy zawierających mniej lub Brak danych. 

* Wybierz klucz partycji, który umożliwia równomierne rozmieszczenie wzorców dostępu między partycjami logicznymi. Obciążenie jest w rozsądny sposób nawet między wszystkimi kluczami. Innymi słowy, większość obciążenia nie powinna być skoncentrowana na kilku określonych kluczach. 

* Wybierz klucz partycji, który ma szeroką gamę wartości. 

Podstawowym pomysłem jest rozłożenie danych i działania w kontenerze w ramach zestawu partycji logicznych, dzięki czemu zasoby magazynu danych i przepływności mogą być dystrybuowane między partycjami logicznymi. Kandydaci dla kluczy partycji mogą zawierać właściwości, które często pojawiają się jako filtr w zapytaniach. Zapytania mogą być efektywnie kierowane przez dołączenie klucza partycji do predykatu filtru. Dzięki takiemu strategii partycjonowania Optymalizacja alokowanej przepływności będzie znacznie łatwiejsza. 

### <a name="design-smaller-items-for-higher-throughput"></a>Projektowanie mniejszych elementów w celu zwiększenia przepływności 

Opłata za żądanie lub koszt przetwarzania żądania danej operacji jest bezpośrednio skorelowane z rozmiarem elementu. Operacje na dużych elementach będą kosztowały więcej niż operacje na mniejszych elementach. 

## <a name="data-access-patterns"></a>Wzorce dostępu do danych 

Jest to zawsze dobrym sposobem, aby logicznie oddzielić dane do logicznych kategorii w zależności od tego, jak często uzyskujesz dostęp do danych. Poprzez kategoryzowanie go jako gorąca, średnia lub zimne dane można dostosować użycie magazynu i wymaganą przepływność. W zależności od częstotliwości dostępu można umieścić dane w oddzielnych kontenerach (na przykład w tabelach, wykresach i kolekcjach) i dostosować przepływność na nich w celu uwzględnienia potrzeb tego segmentu danych. 

Ponadto jeśli używasz Azure Cosmos DB i wiesz, że nie chcesz wyszukiwać według określonych wartości danych lub jeśli nie będą one miały dostępu do nich, należy przechowywać skompresowane wartości tych atrybutów. Dzięki tej metodzie oszczędzasz miejsce w magazynie, miejsce indeksu i przepływność, które są obsługiwane i powodują obniżenie kosztów.

## <a name="optimize-by-changing-indexing-policy"></a>Optymalizacja przez zmianę zasad indeksowania 

Domyślnie Azure Cosmos DB automatycznie indeksuje każdą właściwość każdego rekordu. Jest to w celu ułatwienia programowania i zapewnienia doskonałej wydajności dla wielu różnych typów zapytań ad hoc. Jeśli masz duże rekordy z tysiącami właściwości, płacisz koszt przepływności dla indeksowania każdej właściwości może być nieużyteczny, zwłaszcza jeśli kwerenda dotyczy tylko 10 lub 20 tych właściwości. Ponieważ zbliżasz się do uzyskania dojścia do określonego obciążenia, nasze wskazówki służą do dostrajania zasad indeksu. Pełne szczegóły dotyczące Azure Cosmos DB zasad indeksowania można znaleźć [tutaj](indexing-policies.md). 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>Monitorowanie i wykorzystanie przepływności 

Można monitorować łączną liczbę zainicjowanych jednostek ru, liczbę żądań z ograniczoną szybkością oraz liczbę jednostek ru używanych w Azure Portal. Na poniższej ilustracji przedstawiono przykładową metrykę użycia:

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="Monitoruj jednostki żądań w Azure Portal":::

Możesz również ustawić alerty, aby sprawdzić, czy liczba żądań o ograniczonej szybkości przekracza określony próg. Aby uzyskać więcej informacji, zobacz artykuł [Jak monitorować Azure Cosmos DB](use-metrics.md) artykułu. Te alerty mogą wysyłać wiadomości e-mail do administratorów kont lub wywoływać niestandardowy element webhook protokołu HTTP lub funkcję platformy Azure w celu automatycznego zwiększenia zainicjowanej przepływności. 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>Elastyczne skalowanie przepływności i na żądanie 

Ze względu na to, że opłaty są naliczane zgodnie z zainicjowaną przepływność, dopasowanie nieużywanej przepływności do Twoich potrzeb może pomóc uniknąć naliczania opłat za niewykorzystaną przepływność. Zainicjowaną przepustowość można skalować w górę lub w dół w miarę potrzeb. Jeśli Twoje potrzeby związane z przepływem pracy są bardzo przewidywalne, możesz użyć Azure Functions i użyć wyzwalacza czasomierza, aby [zwiększyć lub zmniejszyć przepływność zgodnie z harmonogramem](scale-on-schedule.md). 

* Monitorowanie użycia jednostek ru i współczynnika żądań z ograniczoną szybkością może ujawnić, że nie ma potrzeby utrzymywania obsługi w całej firmie przez cały dzień lub tydzień. Użytkownik może odbierać mniej ruchu w nocy lub w weekendy. Korzystając z Azure Portal lub Azure Cosmos DB natywnych zestawów SDK lub interfejsu API REST, można w dowolnym momencie skalować swoją zainicjowaną przepływność. Interfejs API REST Azure Cosmos DB udostępnia punkty końcowe do programistycznego aktualizowania poziomu wydajności kontenerów, co pozwala na bezpośrednie dostosowanie przepływności do kodu w zależności od godziny lub dnia tygodnia. Operacja jest wykonywana bez żadnego przestoju i zazwyczaj obowiązuje w czasie krótszym niż minutę. 

* Jednym z obszarów, w których należy skalować przepływność, jest pozyskanie danych do Azure Cosmos DB, na przykład podczas migracji danych. Po zakończeniu migracji można skalować zainicjowaną przepływność w dół w celu obsłużenia stałego stanu rozwiązania.  

* Należy pamiętać, że opłaty są naliczane z dokładnością co godzinę, więc nie będzie można zaoszczędzić żadnych pieniędzy w przypadku zmiany alokowanej przepływności więcej niż godzinę w danym momencie.

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>Określanie przepływności wymaganego dla nowego obciążenia 

Aby określić zainicjowaną przepływność dla nowego obciążenia, można wykonać następujące czynności: 

1. Wykonaj wstępną, przybliżoną ocenę przy użyciu planisty wydajności i Dostosuj swoje oszacowania za pomocą narzędzia Azure Cosmos Explorer w Azure Portal. 

2. Zaleca się utworzenie kontenerów o wyższej przepływności niż oczekiwano, a następnie skalowanie w dół w razie potrzeby. 

3. Zaleca się użycie jednego z natywnych zestawów SDK Azure Cosmos DB, aby skorzystać z automatycznych ponownych prób w przypadku żądań pobrania z ograniczeniami. Jeśli pracujesz na platformie, która nie jest obsługiwana, i użyj interfejsu API REST Cosmos DB, zaimplementuj własne zasady ponawiania prób przy użyciu `x-ms-retry-after-ms` nagłówka. 

4. Upewnij się, że kod aplikacji bezpiecznie obsługuje przypadek, gdy wszystkie próby zakończą się niepowodzeniem. 

5. Można skonfigurować alerty z poziomu Azure Portal, aby otrzymywać powiadomienia o ograniczeniu szybkości. Można zacząć od bardziej ostrożnych limitów, takich jak 10-ograniczonej liczby żądań w ciągu ostatnich 15 minut, i przełączać się do bardziej eagerych reguł po ustaleniu rzeczywistego zużycia. Sporadyczne limity szybkości są bardzo precyzyjne, ale pokazują, że są odtwarzane z ustawionymi limitami i dokładnie tak, jak chcesz. 

6. Użyj monitorowania, aby zrozumieć wzorzec ruchu, więc możesz rozważyć konieczność dynamicznego dostosowywania aprowizacji przepływności w ciągu dnia lub tygodnia. 

7. Regularnie Monitoruj przedziały czasu aprowizacji i zużytą przepływność, aby upewnić się, że nie zainicjowano obsługi więcej niż wymagana liczba kontenerów i baz danych. Niewielka w porównaniu z zainicjowaną przepływność jest dobrym sprawdzaniem bezpieczeństwa.  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>Najlepsze rozwiązania w celu optymalizacji alokowanej przepływności 

Poniższe kroki pomagają zapewnić wysoką skalowalność i opłacalność rozwiązań w przypadku korzystania z Azure Cosmos DB.  

1. Jeśli znacznie przekraczasz zainicjowaną przepływność w kontenerach i bazach danych, zapoznaj się z tematem jednostek ru zainicjowanymi a jednostek ru i Dostosuj obciążenia.  

2. Jedną z metod oszacowania ilości zarezerwowanej przepływności wymaganej przez aplikację jest zarejestrowanie opłaty za jednostkę żądania RU skojarzoną z uruchamianiem typowych operacji względem reprezentatywnego kontenera usługi Azure Cosmos lub bazy danych używanej przez aplikację, a następnie oszacowanie liczby przewidywanych operacji wykonywanych w każdej sekundzie. Pamiętaj, aby mierzyć i uwzględniać typowe zapytania oraz ich użycie. Aby dowiedzieć się, jak oszacować koszty usługi RU dla zapytań programowo lub za pomocą portalu [, zobacz Optymalizacja kosztów zapytań](optimize-cost-queries.md). 

3. Innym sposobem na uzyskanie operacji i ich kosztów w programie jednostek ru jest włączenie dzienników Azure Monitor, co zapewnia podział operacji na czas trwania i opłaty za żądania. Azure Cosmos DB zapewnia opłaty za żądania dla każdej operacji, więc każda opłata za operacje może zostać zapisana z powrotem z odpowiedzi, a następnie użyta do analizy. 

4. Elastycznie można skalować w górę i w dół, aby sprostać potrzebom związanym z obciążeniem. 

5. Możesz dodawać i usuwać regiony skojarzone z Twoim kontem usługi Azure Cosmos w miarę potrzeb i kontrolować koszty. 

6. Upewnij się, że masz równomierny rozkład danych i obciążeń między logicznymi partycjami kontenerów. W przypadku nierównej dystrybucji partycji może to spowodować udostępnienie większej ilości przepływności niż wymagana wartość. Jeśli określisz, że masz skośny rozkład, zalecamy ponowne dystrybuowanie obciążenia równomiernie w obrębie partycji lub ponowne Partycjonowanie danych. 

7. Jeśli masz wiele kontenerów i te kontenery nie wymagają umowy SLA, możesz użyć oferty opartej na bazie danych, aby przypadki, w których nie ma zastosowania przepływność na kontener umowy SLA. Należy określić, które kontenery usługi Azure Cosmos mają być migrowane do oferty przepływności na poziomie bazy danych, a następnie przeprowadzić ich migrację przy użyciu rozwiązania opartego na strumieniu zmian. 

8. Rozważ użycie "Cosmos DB warstwy Bezpłatna" (bezpłatnie przez jeden rok), wypróbuj Cosmos DB (do trzech regionów) lub do pobrania Cosmos DB emulatora na potrzeby scenariuszy tworzenia i testowania. Korzystając z tych opcji do testowania, można znacznie obniżyć koszty.  

9. Można dodatkowo przeprowadzić optymalizację kosztów specyficzną dla obciążenia — na przykład zwiększenie rozmiaru partii, odczytów równoważenia obciążenia w wielu regionach i cofnięcie duplikowania danych, jeśli ma to zastosowanie.

10. Dzięki Azure Cosmos DB zarezerwowanej pojemności można uzyskać znaczne rabaty nawet na 65% przez trzy lata. Azure Cosmos DB zarezerwowany model pojemności jest zobowiązaniem z góry na żądania jednostek żądań w czasie. Rabaty są w warstwach w taki sposób, że jednostki żądań o większej liczbie są używane w dłuższym okresie, tym większy będzie rabat. Rabaty te są stosowane natychmiast. Wszystkie jednostek ru użyte powyżej wartości zainicjowanej są obciążane opłatami na podstawie niezastrzeżonego kosztu wydajności. Aby uzyskać więcej informacji, zobacz [Cosmos DB zarezerwowana pojemność](cosmos-db-reserved-capacity.md)). Należy rozważyć zakup zarezerwowanej pojemności, aby obniżyć koszty związane z przepływem wydajności.  

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)


---
title: Dystrybucja globalna z Azure Cosmos DBą pod okapem
description: Ten artykuł zawiera szczegółowe informacje techniczne dotyczące globalnej dystrybucji Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1b47ad27abbe59eceabd15d091f88f4659d8dad6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486390"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globalna dystrybucja danych z Azure Cosmos DBą pod okapem
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB to fundamentowa usługa na platformie Azure, więc jest wdrażana we wszystkich regionach świadczenia usługi Azure na całym świecie, w tym w publicznych, suwerennych departamentach obrony i instytucji rządowych. W centrum danych firma Microsoft wdraża Azure Cosmos DB na dużych sygnaturach maszyn i zarządza nimi przy użyciu dedykowanego magazynu lokalnego. W centrum danych Azure Cosmos DB jest wdrażana w wielu klastrach, a każdy z nich może uruchamiać wiele generacji sprzętu. Maszyny w klastrze są zwykle rozproszone w domenach błędów 10-20 w celu zapewnienia wysokiej dostępności w regionie. Na poniższej ilustracji przedstawiono topologię globalnego systemu dystrybucji Cosmos DB:

:::image type="content" source="./media/global-dist-under-the-hood/distributed-system-topology.png" alt-text="Topologia systemu" border="false":::

**Globalna dystrybucja w Azure Cosmos DB to gotowe:** W dowolnym momencie za pomocą kilku kliknięć lub programowo przy użyciu jednego wywołania interfejsu API można dodawać lub usuwać regiony geograficzne skojarzone z bazą danych Cosmos. Baza danych Cosmos, z kolei, składa się z zestawu kontenerów Cosmos. W Cosmos DB kontenery pełnią rolę logicznej dystrybucji i skalowalności. Kolekcje, tabele i tworzone przez siebie wykresy są (wewnętrznie) tylko Cosmos kontenery. Kontenery są całkowicie schemat-niezależny od i zapewniają zakres zapytania. Dane w kontenerze Cosmos są automatycznie indeksowane podczas pozyskiwania. Automatyczne indeksowanie umożliwia użytkownikom wykonywanie zapytań dotyczących danych bez problemów z zarządzaniem schematem lub indeksem, szczególnie w przypadku konfiguracji rozproszonej globalnie.  

- W danym regionie dane znajdujące się w kontenerze są dystrybuowane przy użyciu klucza partycji, który jest dostarczany i jest w sposób niewidoczny dla użytkownika zarządzany przez bazowe partycje fizyczne (*Dystrybucja lokalna*).  

- Każda partycja fizyczna jest również replikowana w regionach geograficznych (*dystrybucja globalna*). 

Gdy aplikacja używająca Cosmos DB elastycznie skaluje przepływność na kontenerze Cosmos lub zużywa więcej przestrzeni dyskowej, Cosmos DB nieprzezroczystie obsługuje operacje zarządzania partycjami (dzielenie, klonowanie, usuwanie) we wszystkich regionach. Niezależna od skali, dystrybucji lub niepowodzeń Cosmos DB kontynuuje dostarczanie pojedynczego obrazu systemu danych w kontenerach, które są globalnie dystrybuowane w dowolnej liczbie regionów.  

Jak pokazano na poniższej ilustracji, dane w kontenerze są dystrybuowane w dwóch wymiarach — w regionie i w różnych regionach, na całym świecie:  

:::image type="content" source="./media/global-dist-under-the-hood/distribution-of-resource-partitions.png" alt-text="partycje fizyczne" border="false":::

Partycja fizyczna jest implementowana przez grupę replik nazywaną *zestawem replik*. Każdy komputer obsługuje setki replik odpowiadających różnym partycjom fizycznym w ramach ustalonego zestawu procesów, jak pokazano na powyższym obrazie. Repliki odpowiadające partycjom fizycznym są dynamicznie umieszczane i ładowane na maszynach w klastrze i w centrach danych w danym regionie.  

Replika w unikatowy sposób należy do dzierżawy Azure Cosmos DB. Każda replika jest hostem wystąpienia [aparatu bazy danych](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)Cosmos DB, który zarządza zasobami, a także skojarzonymi indeksami. Aparat bazy danych Cosmos działa w systemie typów opartych na protokole Atom-Record-Sequence (ARS). Aparat jest niezależny od do koncepcji schematu, przez odmycie granicy między strukturą i wartościami wystąpień rekordów. Cosmos DB realizuje pełny agnosticism schematu przez automatyczne indeksowanie wszystkich operacji pozyskiwania w skuteczny sposób, co pozwala użytkownikom na wykonywanie zapytań dotyczących danych dystrybuowanych globalnie, bez konieczności rozwiązywania problemów z zarządzaniem schematami i indeksami.

Aparat bazy danych Cosmos składa się z składników, takich jak implementacja kilku elementów podstawowych koordynacji, środowisko uruchomieniowe języka, procesor zapytań oraz Podsystemy magazynowania i indeksowania odpowiedzialne za magazyn transakcyjny i indeksowanie danych. Aby zapewnić trwałość i wysoką dostępność, aparat bazy danych utrzymuje dane i indeks na dysków SSD i replikuje je między wystąpieniami aparatu bazy danych odpowiednio w ramach zestawów replik. Większe dzierżawy odnoszą się do większej skali przepływności i magazynu oraz mają większe lub więcej replik. Każdy składnik systemu jest w pełni asynchroniczny — brak bloków wątków, a każdy wątek wykonuje krótkotrwałe działania bez ponoszenia zbędnych przełączników wątków. Ograniczanie szybkości i naciśnienie jest podłączane do całego stosu od kontroli przyjęcia do wszystkich ścieżek we/wy. Aparat bazy danych Cosmos został zaprojektowany w celu wykorzystania precyzyjnej współbieżności i zapewnienia wysokiej przepływności podczas pracy w Frugal ilości zasobów systemowych.

Globalna dystrybucja Cosmos DB opiera się na dwóch abstrakcyjnych kluczach — *zestawach replik* i *zestawach partycji*. Zestaw replik jest modularnym blokiem na potrzeby koordynacji, a zestaw partycji jest dynamiczną nakładką jednej lub więcej rozproszonych geograficznie partycji fizycznych. Aby zrozumieć, jak działa dystrybucja globalna, musimy zrozumieć te dwa abstrakcyjne klucze. 

## <a name="replica-sets"></a>Repliki — zestawy

Partycja fizyczna jest przeznaczona do użycia przez samodzielną i dynamiczną obciążenie grupą replik w wielu domenach błędów, nazywanych zestawem replik. Ten zestaw zbiorczo implementuje protokół zreplikowanego komputera stanu, aby dane z partycji fizycznej były wysoce dostępne, trwałe i spójne. Członkostwo w zestawie replik *N* jest dynamiczne — utrzymuje wahania między *NMin* i *nmaks.* na podstawie błędów, operacji administracyjnych oraz czasu niepowodzeń replik w celu ponownego wygenerowania/odzyskania. W oparciu o zmiany członkostwa protokół replikacji również ponownie konfiguruje rozmiar kworum odczytu i zapisu. Aby równomiernie dystrybuować przepływność, która jest przypisana do danej partycji fizycznej, wykorzystujemy dwa pomysły: 

- Po pierwsze koszt przetwarzania żądań zapisu na liderze jest wyższy niż koszt zastosowania aktualizacji na stronie nadzorującej. Co więcej, lider ma więcej zasobów systemowych niż obserwatorzy. 

- Na koniec, jak to możliwe, kworum odczytywania dla danego poziomu spójności składa się wyłącznie z replik kolejnych. Unikamy kontaktowania się z liderem w celu obsługi operacji odczytu, chyba że jest to wymagane. Firma Microsoft stosuje kilka pomysłów z badań wykonywanych w związku z [obciążeniem i pojemnością](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) w systemach opartych na kworum dla [pięciu modeli spójności](consistency-levels.md) obsługiwanych przez Cosmos DB.  

## <a name="partition-sets"></a>Zestawy partycji

Grupa partycji fizycznych, jedna ze wszystkich skonfigurowanych za pomocą regionów bazy danych Cosmos, składa się z tego samego zestawu kluczy replikowanych we wszystkich skonfigurowanych regionach. Ta wyższa wartość pierwotna koordynacji jest nazywana rozłożoną w sposób dynamiczny rozłożeniem *partycji fizycznych* , która zarządza danym zestawem kluczy. Chociaż dana partycja fizyczna (zestaw replik) jest objęta zakresem klastra, zestaw partycji może obejmować klastry, centra danych i regiony geograficzne, jak pokazano na poniższej ilustracji:  

:::image type="content" source="./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png" alt-text="Zestawy partycji" border="false":::

Można traktować zestaw partycji jako geograficznie rozproszony "zestaw" Super Replica ", który składa się z wielu replik i ma ten sam zestaw kluczy. Podobnie jak w przypadku zestawu replik, członkostwo w zestawie partycji jest również dynamiczne — zmienia się w zależności od niejawnych operacji zarządzania partycjami fizycznymi w celu dodania/usunięcia nowych partycji do/z danego zestawu partycji (na przykład w przypadku skalowania przepływności w kontenerze, dodania/usunięcia regionu do bazy danych Cosmos lub w przypadku wystąpienia błędów). Ze względu na to, że każda partycja (zestawu partycji) zarządza członkostwem w zestawie partycji w ramach własnego zestawu replik, członkostwo jest w pełni zdecentralizowane i wysoce dostępne. Podczas ponownej konfiguracji zestawu partycji zostaje także ustanowiona topologia nakładki między partycjami fizycznymi. Topologia jest dynamicznie wybierana na podstawie poziomu spójności, odległości geograficznej i dostępnej przepustowości sieci między źródłową i docelową partycją fizyczną.  

Usługa umożliwia konfigurowanie baz danych Cosmos z jednym regionem zapisu lub wieloma regionami zapisu, a w zależności od wyboru zestawy partycji są skonfigurowane do akceptowania zapisów w dokładnie jednym lub we wszystkich regionach. System korzysta z dwupoziomowego, zagnieżdżonego protokołu consensusu — jeden poziom działa w replikach repliki partycji fizycznej akceptujących zapisy, a drugi działa na poziomie zestawu partycji, aby zapewnić kompletne gwarancje porządkowania dla wszystkich zatwierdzonych zapisów w zestawie partycji. Te wielowarstwowe, zagnieżdżone konsensusy mają kluczowe znaczenie dla implementacji naszych rygorystycznych umowy SLA na potrzeby wysokiej dostępności, a także implementacji modeli spójności, które Cosmos DB oferty klientom.  

## <a name="conflict-resolution"></a>Rozwiązywanie konfliktów

Nasz projekt dotyczący propagacji aktualizacji, rozwiązywania konfliktów i śledzenia związku przyczynowego jest inspirowany od wcześniejszej pracy w ramach [algorytmów epidemii](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i systemu [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) . Chociaż jądro pomysłów zostało przeformułowane i zapewniają wygodną ramkę odniesienia do komunikowania się z projektem systemu Cosmos DB, również zostały poddane znaczącej transformacji po zastosowaniu ich do systemu Cosmos DB. Jest to konieczne, ponieważ w poprzednich systemach nie zaprojektowano ani nie jest to zarządzanie zasobami ani ze skalą, w której Cosmos DB ma działać, ani w celu zapewnienia możliwości (na przykład ograniczenia niezgodności) oraz rygorystycznej i kompleksowej umowy SLA, która Cosmos DB dostarcza klientom.  

Odwołaj, że zestaw partycji jest dystrybuowany do wielu regionów i postępuj zgodnie z Cosmos baz danych (zapisy wieloregionowe), aby replikować dane między partycjami fizycznymi zawierającymi dany zestaw partycji. Każda partycja fizyczna (zestawu partycji) akceptuje operacje zapisu i umożliwia zazwyczaj odczyty na klientach lokalnych dla danego regionu. Zapisy akceptowane przez partycję fizyczną w regionie są trwale zatwierdzone i są wysoce dostępne na partycji fizycznej przed ich potwierdzeniem do klienta. Są to wstępnie zaakceptowane zapisy i są propagowane do innych partycji fizycznych w ramach zestawu partycji przy użyciu kanału antyentropii. Klienci mogą żądać zaakceptowania lub zatwierdzić zapisy przez przekazanie nagłówka żądania. Propagacja anty-entropii (łącznie z częstotliwością propagacji) jest dynamiczna, na podstawie topologii zestawu partycji, regionalnej bliskości partycji fizycznych i skonfigurowanego poziomu spójności. W ramach zestawu partycji Cosmos DB następuje główny schemat zatwierdzania z dynamicznie wybraną partycją arbiter. Wybór arbiter jest dynamiczny i jest integralną częścią ponownej konfiguracji zestawu partycji na podstawie topologii nakładki. Zatwierdzone zapisy (w tym aktualizacje wielowierszowe/wsadowe) są gwarantowane. 

Stosujemy zakodowane zegary wektorowe (zawierające Identyfikator regionu i zegary logiczne odpowiadające każdemu poziomowi konsensusu odpowiednio w zestawie replik i zestawie partycji) w celu wykrywania i rozwiązywania konfliktów aktualizacji. Algorytm wyboru topologii i elementów równorzędnych został zaprojektowany w celu zapewnienia stałej i minimalnej ilości miejsca w magazynie oraz minimalnego obciążenia sieci w wektorach wersji. Algorytm gwarantuje Właściwość Strict zbieżność.  

W przypadku baz danych Cosmos skonfigurowanych z wieloma regionami zapisu System oferuje kilka elastycznych zasad automatycznego rozwiązywania konfliktów dla deweloperów do wyboru, w tym: 

- **Ostatni zapis-WINS (LWW)**, który domyślnie używa zdefiniowanej przez system właściwości sygnatury czasowej (która jest oparta na protokole zegara czasu synchronizacji). Cosmos DB pozwala także określić inną niestandardową Właściwość liczbową, która ma być używana do rozwiązywania konfliktów.  
- **Zdefiniowane przez aplikację zasady rozwiązywania konfliktów** (wyrażone za pośrednictwem procedur scalania), które są przeznaczone do uzgadniania przez aplikacje semantyki konfliktów. Te procedury są wywoływane po wykryciu konfliktów zapisu i zapisu pod auspicjami transakcji bazy danych po stronie serwera. System zapewnia dokładnie gwarancję wykonania procedury scalania w ramach protokołu zobowiązania. Istnieje [kilka przykładów rozwiązywania konfliktów](how-to-manage-conflicts.md) , które mogą być odtwarzane za pomocą programu.  

## <a name="consistency-models"></a>Modele spójności

Niezależnie od tego, czy baza danych Cosmos jest konfigurowana z jednym czy wieloma regionami zapisu, można wybierać spośród pięciu dobrze zdefiniowanych modeli spójności. W przypadku wielu regionów zapisu poniżej przedstawiono kilka istotnych aspektów poziomów spójności:  

Ograniczona nieaktualność gwarantuje, że wszystkie operacje odczytu będą znajdować się w obrębie prefiksów *K* lub *T* sekund od ostatniego zapisu w dowolnym regionie. Ponadto należy zapewnić monotoniczny i z zachowaniem spójnych prefiksów. Protokół antyszpiegowski działa w sposób ograniczony i gwarantuje, że prefiksy nie są gromadzone i nie trzeba stosować naciskania na zapisy. Zapewnianie spójności sesji gwarantuje monotoniczny odczyt, monotoniczny zapis, odczytanie własnych zapisów, zapis następujący: Odczyt i spójne gwarancje dotyczące prefiksów, na całym świecie. W przypadku baz danych skonfigurowanych pod kątem silnej spójności korzyści wynikające z replikacji synchronicznej w różnych regionach nie mają zastosowania do małych i dużych ilości zapisów w wielu regionach zapisu.

Semantyka pięciu modeli spójności w Cosmos DB jest opisana [tutaj](consistency-levels.md)i matematycznie opisana przy użyciu wysokiego poziomu tla [i specyfikacji.](https://github.com/Azure/azure-cosmos-tla)

## <a name="next-steps"></a>Następne kroki

Następnie Dowiedz się, jak skonfigurować dystrybucję globalną przy użyciu następujących artykułów:

* [Dodawanie/usuwanie regionów z poziomu konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Jak utworzyć niestandardowe zasady rozwiązywania konfliktów](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

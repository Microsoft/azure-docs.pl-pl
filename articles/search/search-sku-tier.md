---
title: Wybierz warstwę cenową
titleSuffix: Azure Cognitive Search
description: 'Obsługę administracyjną Wyszukiwanie poznawcze platformy Azure można zainicjować w następujących warstwach: bezpłatna, podstawowa i standardowa, a warstwa standardowa jest dostępna w różnych konfiguracjach zasobów i poziomach pojemności.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4c58968cb6a38a10433915ec8fa00336ccad301e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216414"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Wybierz warstwę cenową dla usługi Azure Wyszukiwanie poznawcze

Podczas [tworzenia usługi wyszukiwania](search-create-service-portal.md)należy wybrać warstwę cenową naprawioną dla okresu istnienia usługi. Wybrana warstwa określa:

+ Liczba indeksów i innych obiektów, które można utworzyć (limity maksymalne)
+ Rozmiar i szybkość partycji (magazyn fizyczny)
+ Stawka rozliczana, stały koszt miesięczny, ale także koszt przyrostowy w przypadku dodawania partycji lub replik

Dodatkowo niektóre [funkcje Premium](#premium-features) są dostarczane z wymaganiami dotyczącymi warstwy.

## <a name="tier-descriptions"></a>Opisy warstw

Warstwy obejmują **bezpłatne**, **podstawowe**, **standardowe** i **zoptymalizowane pod kątem magazynu**. Zoptymalizowane pod kątem Standard i Storage są dostępne z kilkoma konfiguracjami i pojemnościami.

Poniższy zrzut ekranu z Azure Portal zawiera dostępne warstwy, minus ceny (które można znaleźć w portalu i na [stronie cennika](https://azure.microsoft.com/pricing/details/search/)). 

![Warstwy cenowe Wyszukiwanie poznawcze platformy Azure](media/search-sku-tier/tiers.png "Warstwy cenowe Wyszukiwanie poznawcze platformy Azure")

**Bezpłatna** tworzy ograniczoną usługę wyszukiwania dla mniejszych projektów, takich jak uruchamianie samouczków i przykładów kodu. Wewnętrznie repliki i partycje są współużytkowane przez wielu subskrybentów. Nie można skalować bezpłatnej usługi ani uruchamiać znaczących obciążeń.

**Podstawowa** i **standardowa** są najczęściej używanymi warstwami do obciążania, a **standardem** domyślnym. Dzięki dedykowanym zasobom w ramach formantu można wdrożyć większe projekty, zoptymalizować wydajność i zwiększyć pojemność.

Niektóre warstwy są zoptymalizowane pod kątem niektórych typów pracy. Na przykład **standardowa 3 wysoka gęstość (S3 HD)** jest *trybem hostingu* dla S3, gdzie podstawowy sprzęt jest zoptymalizowany pod kątem dużej liczby mniejszych indeksów i jest przeznaczony dla scenariuszy wielodostępnych. Funkcja S3 HD ma taką samą opłatą za jednostkę jak S3, ale sprzęt jest zoptymalizowany pod kątem szybkiego odczytu plików na dużej liczbie mniejszych indeksów.

Warstwy **zoptymalizowane pod kątem magazynu** oferują większą pojemność magazynu przy niższej cenie za TB niż warstwy standardowe. Najważniejszym kompromisem jest wyższy poziom opóźnienia zapytań, który należy zweryfikować w celu spełnienia określonych wymagań aplikacji. Aby dowiedzieć się więcej na temat zagadnień dotyczących wydajności tej warstwy, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).

Więcej informacji o różnych warstwach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/search/), w obszarze [limity usługi w usłudze Azure wyszukiwanie poznawcze](search-limits-quotas-capacity.md) i na stronie portalu podczas aprowizacji usługi.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Dostępność funkcji według warstwy

Większość funkcji jest dostępnych na wszystkich warstwach, w tym w warstwie Bezpłatna. W kilku przypadkach wybrana warstwa będzie miała wpływ na możliwość implementowania funkcji. W poniższej tabeli opisano ograniczenia funkcji, które są związane z warstwą usług.

| Cechy | Ograniczenia |
|---------|-------------|
| [indeksatorów](search-indexer-overview.md) | Indeksatory nie są dostępne w systemie S3 HD.  |
| [Wzbogacanie sztucznej inteligencji](search-security-manage-encryption-keys.md) | Działa w warstwie Bezpłatna, ale nie jest zalecana. |
| [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) | Niedostępne w warstwie Bezpłatna. |
| [Dostęp do zapory IP](service-configure-firewall.md) | Niedostępne w warstwie Bezpłatna. |
| [Prywatny punkt końcowy (Integracja z prywatnym łączem platformy Azure)](service-create-private-endpoint.md) | W przypadku połączeń przychodzących z usługą Search nie jest dostępna w warstwie Bezpłatna. W przypadku połączeń wychodzących przez indeksatory do innych zasobów platformy Azure, niedostępne w przypadku systemu HD lub S3. W przypadku indeksatorów korzystających z programu umiejętności, niedostępne w przypadku platformy Free, Basic, S1 lub S3 HD.|

Funkcje intensywnie korzystające z zasobów mogą nie być dobrze, chyba że zapewnisz wystarczającą pojemność. Na przykład [wzbogacanie systemu AI](cognitive-search-concept-intro.md) ma długotrwałe umiejętności, które przekraczają limit czasu dla bezpłatnej usługi, chyba że zestaw danych jest mały.

## <a name="billable-events"></a>Zdarzenia do rozliczenia

Rozwiązanie utworzone na platformie Azure Wyszukiwanie poznawcze może ponosić koszty w następujący sposób:

+ [Koszt samej usługi](#service-costs) , z systemem 24x7, w minimalnej konfiguracji (jedna partycja i replika), według stawki podstawowej. Można to traktować jako stały koszt działania usługi.

+ Dodawanie pojemności (replik lub partycji), gdzie koszty zwiększają się w przyrostach stawki rozliczeniowej

+ Opłaty za przepustowość (wychodzący transfer danych)

+ Usługi dodatków wymagane do określonych funkcji lub funkcji:

  + Wzbogacanie AI (wymaga [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + Magazyn wiedzy (wymaga [usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + zwiększanie przyrostowe (wymaga [usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/), ma zastosowanie do wzbogacania AI)
  + klucze zarządzane przez klienta i podwójne szyfrowanie (wymaga [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + prywatne punkty końcowe dla modelu dostępu bez Internetu (wymaga [prywatnego linku platformy Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Koszty usług

W przeciwieństwie do maszyn wirtualnych lub innych zasobów, które mogą być wstrzymane, aby uniknąć naliczania opłat, usługa Azure Wyszukiwanie poznawcze jest zawsze dostępna na sprzęcie dedykowanym wyłącznie do użytku. W związku z tym tworzenie usługi jest zdarzeniem rozliczanym rozpoczynającym się podczas tworzenia usługi i kończącym się po usunięciu usługi. 

Opłata minimalna to pierwsza jednostka wyszukiwania (jedna replika x jedna partycja) w stawce płatnej. Ta wartość minimalna jest ustalana w okresie istnienia usługi, ponieważ usługa nie może być uruchomiona na żadnym serwerze niższym niż ta konfiguracja. Poza minimalnym można dodawać repliki i partycje niezależnie od siebie. Przyrostowe zwiększenie wydajności za pośrednictwem replik i partycji spowoduje zwiększenie rachunku na podstawie następującej formuły: [(repliki x partycje x)](#search-units), gdzie opłata naliczana zależy od wybranej warstwy cenowej.

W przypadku szacowania kosztów rozwiązania wyszukiwania należy pamiętać, że ceny i pojemność nie są liniowe. (Podwojenie pojemności więcej niż podwaja koszt). Aby zapoznać się z przykładem sposobu działania formuły, zobacz [Jak przydzielić repliki i partycje](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Opłaty za przepustowość

Korzystanie z [indeksatorów](search-indexer-overview.md) może mieć wpływ na rozliczenia, jeśli źródło danych platformy Azure znajduje się w innym regionie niż usługa Azure wyszukiwanie poznawcze. W tym scenariuszu kosztem przeniesienia danych wychodzących ze źródła danych platformy Azure do usługi Azure Wyszukiwanie poznawcze mogą być koszty. Aby uzyskać szczegółowe informacje, zapoznaj się ze stronami cennika platformy danych platformy Azure.

Opłaty za wychodzące dane można wyeliminować całkowicie, jeśli utworzysz usługę Azure Wyszukiwanie poznawcze w tym samym regionie, w którym zawarto dane. Poniżej przedstawiono niektóre informacje na [stronie cennika dotyczące przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Dane przychodzące: Firma Microsoft nie nalicza opłat za żadne dane przychodzące do żadnej usługi na platformie Azure. 

+ Dane wychodzące: dane wychodzące odnoszą się do wyników zapytania. Wyszukiwanie poznawcze nie nalicza opłat za dane wychodzące, ale opłaty wychodzące z platformy Azure są możliwe, jeśli usługi znajdują się w różnych regionach. Opłaty te nie są faktycznie częścią rachunku na korzystanie z platformy Azure Wyszukiwanie poznawcze. Są one wymienione tutaj, ponieważ w przypadku wysyłania wyników do innych regionów lub aplikacji niezwiązanych z platformą Azure można zobaczyć te koszty w ramach ogólnego rachunku.

### <a name="ai-enrichment-with-cognitive-services"></a>Wzbogacanie AI z Cognitive Services

W przypadku [wzbogacania AI](cognitive-search-concept-intro.md)należy zaplanować [dołączenie zasobów Cognitive Services platformy Azure](cognitive-search-attach-cognitive-services.md), w tym samym regionie co usługa Azure wyszukiwanie poznawcze, w warstwie cenowej S0 na potrzeby przetwarzania płatności zgodnie z rzeczywistym użyciem. Nie ma stałego kosztu związanego z dołączaniem Cognitive Services. Płacisz tylko za potrzebne przetwarzanie.

| Operacja | Wpływ rozliczeń |
|-----------|----------------|
| Łamanie dokumentów, Wyodrębnianie tekstu | Bezpłatna |
| Łamanie dokumentów, wyodrębnianie obrazów | Opłaty są naliczane zgodnie z liczbą obrazów wyodrębnionych z dokumentów. W [konfiguracji indeksatora](/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** jest parametrem, który wyzwala wyodrębnianie obrazów. Jeśli **imageAction** jest ustawiona na wartość "none" (domyślnie), nie zostanie naliczona opłata za Wyodrębnianie obrazu. Stawka wyodrębniania obrazu jest udokumentowana na stronie [szczegóły cennika](https://azure.microsoft.com/pricing/details/search/) usługi Azure wyszukiwanie poznawcze.|
| [Wbudowane umiejętności poznawcze](cognitive-search-predefined-skills.md) | Opłaty są naliczane według tej samej stawki, co w przypadku, gdy zadanie zostało wykonane przy użyciu Cognitive Services bezpośrednio. |
| Umiejętności niestandardowe | Niestandardowa umiejętność zapewnia funkcjonalność. Koszt użycia niestandardowej umiejętności zależy wyłącznie od tego, czy kod niestandardowy wywołuje inne usługi taryfowe. |

Funkcja [wzbogacania (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) umożliwia zapewnienie pamięci podręcznej, która pozwala Indeksatorowi wydajniejsze wykonywanie tylko umiejętności poznawczej, które są niezbędne, jeśli zmodyfikujesz zestawu umiejętności w przyszłości, oszczędzając czas i pieniądze.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Formuła rozliczeń (R x P = SU)

Najważniejszym koncepcją rozliczeniową do zrozumienia dla operacji Wyszukiwanie poznawcze platformy Azure jest *Jednostka wyszukiwania* (Su). Ponieważ platforma Azure Wyszukiwanie poznawcze jest zależna od obu replik i partycji na potrzeby indeksowania i zapytań, nie ma sensu rozliczania tylko jednego lub drugiego. Zamiast tego rozliczenia opierają się na złożonym z nich.

SU to iloczyn *replik* i *partycji* używanych przez usługę: **(R x P = Su)**.

Każda usługa rozpoczyna się od jednego SU (jedna replika pomnożona przez jedną partycję) jako minimum. Wartość maksymalna dla każdej usługi to 36 usług SUs. Tę wartość maksymalną można osiągnąć na wiele sposobów: 6 partycji x 6 replik lub 3 partycji x 12 replik, na przykład. Jest to typowe użycie mniejsze niż całkowita pojemność (na przykład 3-Replica, 3-podzielone usługi są rozliczane jako 9 usług SUs). Zobacz wykres [kombinacji partycji i repliki](search-capacity-planning.md#chart) dla prawidłowych kombinacji.

Stawka rozliczeniowa jest naliczana co godzinę za pomocą funkcji SU. Każda warstwa ma stopniowo wyższą stawkę. Wyższe warstwy są dostarczane z większymi i speediermi partycjami, a to wpływa na ogólną wyższą stawkę godzinową dla tej warstwy. Stawki dla każdej warstwy można wyświetlić na stronie [szczegółów cennika](https://azure.microsoft.com/pricing/details/search/) .

Większość klientów uzyskuje zaledwie część całkowitej pojemności online, utrzymując pozostałe w rezerwie. W przypadku rozliczeń liczba partycji i replik przetworzonych w trybie online, obliczone przez formułę SU, decyduje o godzinie płatności.

## <a name="next-steps"></a>Następne kroki

Zarządzanie kosztami jest integralną częścią planowania pojemności. W następnym kroku przejdź do poniższego artykułu, aby uzyskać wskazówki dotyczące oszacowania pojemności i zarządzania kosztami.

> [!div class="nextstepaction"]
> [Jak zarządzać kosztami i oceniać wydajność na platformie Azure Wyszukiwanie poznawcze](search-sku-manage-costs.md)
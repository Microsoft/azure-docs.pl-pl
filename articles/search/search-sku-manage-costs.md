---
title: Szacowanie kosztów
titleSuffix: Azure Cognitive Search
description: Zapoznaj się z płatnymi zdarzeniami, modelem cen i wskazówkami dotyczącymi zarządzania kosztami uruchamiania usługi Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: e2ab7a007a92bd2306a7a97781af5653f932d0b7
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580605"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Jak oszacować koszty usługi Wyszukiwanie poznawcze platformy Azure i zarządzać nimi

W tym artykule zapoznaj się z modelem cen, płatnymi zdarzeniami oraz wskazówkami dotyczącymi zarządzania kosztami korzystania z usługi Azure Wyszukiwanie poznawcze.

## <a name="pricing-model"></a>Model cen

Architektura skalowalności w usłudze Azure Wyszukiwanie poznawcze opiera się na elastycznych kombinacjach replik i partycji, dzięki czemu można zmieniać pojemność w zależności od tego, czy potrzebujesz więcej zapytań czy możliwości indeksowania, i płacisz tylko za to, czego potrzebujesz.

Ilość zasobów używanych przez usługę wyszukiwania pomnożona przez stawkę rozliczeń ustanowioną przez warstwę usługi określa koszt uruchomienia usługi. Koszty i pojemność są ściśle powiązane. W przypadku szacowania kosztów zrozumienie pojemności wymaganej do uruchomienia obciążeń indeksowania i wykonywania zapytań daje najlepsze rozwiązanie dotyczące przewidywanych kosztów.

Na potrzeby rozliczania istnieją dwie proste formuły, dla których należy pamiętać:

| Formuła | Opis |
|---------|-------------|
| **R x P = SU** | Liczba użytych replik, pomnożona przez liczbę użytych partycji, jest równa liczbie *jednostek wyszukiwania* (Su) używanej przez usługę. SU jest jednostką zasobów i może być partycją lub repliką. |
| **SU * stawka rozliczenia = miesięczne wydatki** | Liczba usług SUs pomnożona przez stawkę rozliczenia w warstwie, w której zainicjowano obsługę, jest głównym wyznacznikiem ogólnego rachunku miesięcznego. Niektóre funkcje lub obciążenia mają zależności od innych usług platformy Azure, które mogą zwiększyć koszt rozwiązania na poziomie subskrypcji. Poniższa sekcja dotyczące rozliczeń zawiera funkcje, które mogą zostać dodane do rachunku. |

Każda usługa rozpoczyna się od jednego SU (jedna replika pomnożona przez jedną partycję) jako minimum. Wartość maksymalna dla każdej usługi to 36 usług SUs. Tę wartość maksymalną można osiągnąć na wiele sposobów: 6 partycji x 6 replik lub 3 partycji x 12 replik, na przykład. Jest to typowe użycie mniejsze niż całkowita pojemność (na przykład 3-Replica, 3-podzielone usługi są rozliczane jako 9 usług SUs). Zobacz wykres [kombinacji partycji i repliki](search-capacity-planning.md#chart) dla prawidłowych kombinacji.

Stawka rozliczeniowa jest naliczana co godzinę za pomocą funkcji SU. Każda warstwa ma stopniowo wyższą stawkę. Wyższe warstwy są dostarczane z większymi i speediermi partycjami, a to wpływa na ogólną wyższą stawkę godzinową dla tej warstwy. Stawki dla każdej warstwy można wyświetlić na stronie [szczegółów cennika](https://azure.microsoft.com/pricing/details/search/) .

Większość klientów uzyskuje zaledwie część całkowitej pojemności online, utrzymując pozostałe w rezerwie. W przypadku rozliczeń liczba partycji i replik przetworzonych w trybie online, obliczone przez formułę SU, decyduje o godzinie płatności. 

## <a name="billable-events"></a>Zdarzenia do rozliczenia

Rozwiązanie utworzone na platformie Azure Wyszukiwanie poznawcze może ponosić koszty w następujący sposób:

+ [Koszt samej usługi](#service-costs) , z systemem 24x7, w minimalnej konfiguracji (jedna partycja i replika), według stawki podstawowej. Można to traktować jako stały koszt działania usługi.

+ Dodanie pojemności (replik lub partycji), w którym koszty zwiększają się w przyrostach stawki do rozliczenia. Jeśli wysoka dostępność jest wymaganiem biznesowym, będziesz potrzebować 3 replik.

+ Opłaty za przepustowość (wychodzący transfer danych)

+ Usługi dodatków wymagane do określonych funkcji lub funkcji:

  + Wzbogacanie AI (wymaga [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + Magazyn wiedzy (wymaga [usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + zwiększanie przyrostowe (wymaga [usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/), ma zastosowanie do wzbogacania AI)
  + klucze zarządzane przez klienta i podwójne szyfrowanie (wymaga [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + prywatne punkty końcowe dla modelu dostępu bez Internetu (wymaga [prywatnego linku platformy Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Koszty usług

W przeciwieństwie do maszyn wirtualnych lub innych zasobów, które mogą być wstrzymane, aby uniknąć naliczania opłat, usługa Azure Wyszukiwanie poznawcze jest zawsze dostępna na sprzęcie dedykowanym wyłącznie do użytku. W związku z tym tworzenie usługi jest zdarzeniem rozliczanym rozpoczynającym się podczas tworzenia usługi i kończącym się po usunięciu usługi. 

Opłata minimalna to pierwsza jednostka wyszukiwania (jedna replika x jedna partycja) w stawce płatnej. Ta wartość minimalna jest ustalana w okresie istnienia usługi, ponieważ usługa nie może być uruchomiona na żadnym serwerze niższym niż ta konfiguracja. 

Poza minimalnym można dodawać repliki i partycje niezależnie od siebie. Przyrostowe zwiększenie wydajności za pośrednictwem replik i partycji spowoduje zwiększenie rachunku na podstawie następującej formuły: **(repliki x partycje x)**, w którym opłata jest naliczana od wybranej warstwy cenowej.

W przypadku szacowania kosztów rozwiązania wyszukiwania należy pamiętać, że ceny i pojemność nie są liniowe (podwojenie pojemności przekracza koszt tej samej warstwy). Ponadto w pewnym momencie przełączenie do wyższej warstwy może zapewnić lepszą i szybszą wydajność w niedalekiej cenie. Aby uzyskać więcej informacji i zapoznać się z przykładem, zobacz [uaktualnianie do warstwy Standardowa S2](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier).

### <a name="bandwidth-charges"></a>Opłaty za przepustowość

Korzystanie z [indeksatorów](search-indexer-overview.md) może mieć wpływ na rozliczenia, jeśli źródło danych platformy Azure znajduje się w innym regionie niż usługa Azure wyszukiwanie poznawcze. W tym scenariuszu kosztem przeniesienia danych wychodzących ze źródła danych platformy Azure do usługi Azure Wyszukiwanie poznawcze mogą być koszty. Aby uzyskać szczegółowe informacje, zapoznaj się ze stronami cennika platformy danych platformy Azure.

Opłaty za wychodzące dane można wyeliminować całkowicie, jeśli utworzysz usługę Azure Wyszukiwanie poznawcze w tym samym regionie, w którym zawarto dane. Poniżej przedstawiono niektóre informacje na [stronie cennika dotyczące przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Dane przychodzące: Firma Microsoft nie nalicza opłat za żadne dane przychodzące do żadnej usługi na platformie Azure. 

+ Dane wychodzące: dane wychodzące odnoszą się do wyników zapytania. Wyszukiwanie poznawcze nie nalicza opłat za dane wychodzące, ale opłaty wychodzące z platformy Azure są możliwe, jeśli usługi znajdują się w różnych regionach.

  Opłaty te nie są faktycznie częścią rachunku na korzystanie z platformy Azure Wyszukiwanie poznawcze. Są one wymienione tutaj, ponieważ w przypadku wysyłania wyników do innych regionów lub aplikacji niezwiązanych z platformą Azure można zobaczyć te koszty w ramach ogólnego rachunku.

### <a name="ai-enrichment-with-cognitive-services"></a>Wzbogacanie AI z Cognitive Services

W przypadku [wzbogacania AI](cognitive-search-concept-intro.md)należy zaplanować [dołączenie zasobów Cognitive Services platformy Azure](cognitive-search-attach-cognitive-services.md), w tym samym regionie co usługa Azure wyszukiwanie poznawcze, w warstwie cenowej S0 na potrzeby przetwarzania płatności zgodnie z rzeczywistym użyciem. Nie ma stałego kosztu związanego z dołączaniem Cognitive Services. Płacisz tylko za potrzebne przetwarzanie.

| Operacja | Wpływ rozliczeń |
|-----------|----------------|
| Łamanie dokumentów, Wyodrębnianie tekstu | Bezpłatna |
| Łamanie dokumentów, wyodrębnianie obrazów | Opłaty są naliczane zgodnie z liczbą obrazów wyodrębnionych z dokumentów. W [konfiguracji indeksatora](/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** jest parametrem, który wyzwala wyodrębnianie obrazów. Jeśli **imageAction** jest ustawiona na wartość "none" (domyślnie), nie zostanie naliczona opłata za Wyodrębnianie obrazu. Stawka wyodrębniania obrazu jest udokumentowana na stronie [szczegóły cennika](https://azure.microsoft.com/pricing/details/search/) usługi Azure wyszukiwanie poznawcze.|
| [Wbudowane umiejętności poznawcze](cognitive-search-predefined-skills.md) | Opłaty są naliczane według tej samej stawki, co w przypadku, gdy zadanie zostało wykonane przy użyciu Cognitive Services bezpośrednio. |
| Umiejętności niestandardowe | Niestandardowa umiejętność zapewnia funkcjonalność. Koszt użycia niestandardowej umiejętności zależy wyłącznie od tego, czy kod niestandardowy wywołuje inne usługi taryfowe. |

Funkcja [wzbogacania (wersja zapoznawcza)](cognitive-search-incremental-indexing-conceptual.md) umożliwia zapewnienie pamięci podręcznej, która pozwala Indeksatorowi wydajniejsze wykonywanie tylko umiejętności poznawczej, które są niezbędne, jeśli zmodyfikujesz zestawu umiejętności w przyszłości, oszczędzając czas i pieniądze.

## <a name="tips-for-managing-costs"></a>Wskazówki dotyczące zarządzania kosztami

Poniższe wskazówki pomogą Ci obniżyć koszty lub skuteczniej zarządzać kosztami:

+ Utwórz wszystkie zasoby w tym samym regionie lub w możliwie najmniejszej liczbie regionów, aby zminimalizować lub wyeliminować opłaty za przepustowość.

+ Konsoliduj wszystkie usługi do jednej grupy zasobów, takiej jak Azure Wyszukiwanie poznawcze, Cognitive Services i inne usługi platformy Azure używane w rozwiązaniu. W Azure Portal Znajdź grupę zasobów i Użyj poleceń **Cost Management** , aby uzyskać wgląd w rzeczywiste i przewidywane wydatki.

+ Rozważ użycie aplikacji sieci Web platformy Azure dla aplikacji frontonu, aby żądania i odpowiedzi były przechowywane w ramach granicy centrum danych.

+ Skalowanie w górę dla operacji intensywnie korzystających z zasobów, takich jak indeksowanie, a następnie korygowanie w dół na potrzeby zwykłych obciążeń zapytań. Rozpocznij od minimalnej konfiguracji Wyszukiwanie poznawcze platformy Azure (jeden element SU składający się z jednej partycji i jednej repliki), a następnie Monitoruj aktywność użytkowników, aby identyfikować wzorce użycia, które wskazują potrzebę większej pojemności. Jeśli istnieje przewidywalny wzorzec, można synchronizować skalę z aktywnością (należy napisać kod w celu zautomatyzowania tego).

+ Usługa Cost Management jest wbudowana w infrastrukturę platformy Azure. Przejrzyj [rozliczenia i zarządzanie kosztami](../cost-management-billing/cost-management-billing-overview.md) , aby uzyskać więcej informacji na temat śledzenia kosztów, narzędzi i interfejsów API.

Tymczasowe wyłączenie usługi wyszukiwania nie jest możliwe. Dedykowane zasoby są zawsze operacyjne, przydzielane do wyłącznego użytku przez okres istnienia usługi. Usuwanie usługi jest trwałe, a także usuwa skojarzone z nią dane.

W odniesieniu do samej usługi jedynym sposobem obniżenia poziomu rachunku jest zredukowanie replik i partycji na poziom, który nadal zapewnia akceptowalną wydajność i zgodność z umową [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), lub tworzenie usługi w niższej warstwie (S1 stawki godzinowe są niższe niż w przypadku stawek S2 i S3). Przy założeniu, że usługa zostanie zainicjowana na niższym końcu projekcji obciążenia, w przypadku skalowalność usługi można utworzyć drugą usługę o większej warstwie, ponownie skompilować indeksy w drugiej usłudze, a następnie usunąć pierwszy z nich.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak monitorować i zarządzać kosztami w ramach subskrypcji platformy Azure.

> [!div class="nextstepaction"]
> [Dokumentacja usługi Azure Cost Management i rozliczeń](../cost-management-billing/cost-management-billing-overview.md)
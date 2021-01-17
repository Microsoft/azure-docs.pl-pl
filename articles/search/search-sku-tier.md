---
title: Wybierz warstwę cenową
titleSuffix: Azure Cognitive Search
description: 'Dowiedz się więcej o warstwach cenowych (lub jednostkach SKU) dla usługi Azure Wyszukiwanie poznawcze. Usługa wyszukiwania może być obsługiwana w następujących warstwach: bezpłatna, podstawowa i standardowa. Standard jest dostępny w różnych konfiguracjach zasobów i poziomach pojemności.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 1a1fc0ce634282ffd4fcf374138fe97a04f32062
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539600"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Wybierz warstwę cenową dla usługi Azure Wyszukiwanie poznawcze

Podczas [tworzenia usługi wyszukiwania](search-create-service-portal.md)należy wybrać warstwę cenową (lub jednostkę SKU), która została rozwiązany przez okres istnienia usługi. Szacowane miesięczne koszty są wyświetlane na stronie **Wybierz warstwę cenową** w portalu. Jeśli tworzysz usługę za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure, warstwa zostanie określona za pomocą **`-Sku`** parametru.

Wybrana warstwa określa:

+ Maksymalna liczba indeksów i innych obiektów, które można utworzyć w usłudze
+ Rozmiar i szybkość partycji (magazyn fizyczny)
+ Stawka rozliczana jako stały koszt miesięczny, ale także koszt przyrostowy w przypadku dodania pojemności

W kilku przypadkach wybrana warstwa określa dostępność [funkcji Premium](#premium-features).

## <a name="tier-descriptions"></a>Opisy warstw

Warstwy obejmują **bezpłatne**, **podstawowe**, **standardowe** i **zoptymalizowane pod kątem magazynu**. Zoptymalizowane pod kątem Standard i Storage są dostępne z kilkoma konfiguracjami i pojemnościami. Poniższy zrzut ekranu z Azure Portal zawiera dostępne warstwy, minus ceny (które można znaleźć w portalu i na [stronie cennika](https://azure.microsoft.com/pricing/details/search/)). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Wykres warstwy cenowej" border="true":::

**Bezpłatna** tworzy ograniczoną usługę wyszukiwania dla mniejszych projektów, takich jak uruchamianie samouczków i przykładów kodu. Wewnętrznie zasoby systemowe są współużytkowane przez wielu subskrybentów. Nie można skalować bezpłatnej usługi ani uruchamiać znaczących obciążeń.

**Podstawowa** i **standardowa** są najczęściej używanymi warstwami do obciążania, z **standardem** domyślnym, ponieważ zapewnia większą elastyczność skalowania dla obciążeń. Dzięki dedykowanym zasobom w ramach formantu można wdrożyć większe projekty, zoptymalizować wydajność i zwiększyć pojemność.

Niektóre warstwy są przeznaczone dla niektórych typów pracy. Na przykład **standardowa 3 wysoka gęstość (S3 HD)** jest *trybem hostingu* dla S3, gdzie podstawowy sprzęt jest zoptymalizowany pod kątem dużej liczby mniejszych indeksów i jest przeznaczony dla scenariuszy wielodostępnych. Funkcja S3 HD ma taką samą opłatą za jednostkę jak S3, ale sprzęt jest zoptymalizowany pod kątem szybkiego odczytu plików na dużej liczbie mniejszych indeksów.

Warstwy **zoptymalizowane pod kątem magazynu** oferują większą pojemność magazynu przy niższej cenie za TB niż warstwy standardowe. Najważniejszym kompromisem jest wyższy poziom opóźnienia zapytań, który należy zweryfikować w celu spełnienia określonych wymagań aplikacji. Aby dowiedzieć się więcej na temat zagadnień dotyczących wydajności tej warstwy, zobacz [zagadnienia dotyczące wydajności i optymalizacji](search-performance-optimization.md).

Więcej informacji o różnych warstwach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/search/), w obszarze [limity usługi w usłudze Azure wyszukiwanie poznawcze](search-limits-quotas-capacity.md) i na stronie portalu podczas aprowizacji usługi.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Dostępność funkcji według warstwy

Większość funkcji jest dostępnych na wszystkich warstwach, w tym w warstwie Bezpłatna. W kilku przypadkach wybrana warstwa będzie miała wpływ na możliwość implementowania funkcji. W poniższej tabeli opisano ograniczenia funkcji, które są związane z warstwą usług.

| Cechy | Ograniczenia |
|---------|-------------|
| [indeksatorów](search-indexer-overview.md) | Indeksatory nie są dostępne w systemie S3 HD.  |
| [Wzbogacanie sztucznej inteligencji](search-security-manage-encryption-keys.md) | Działa w warstwie Bezpłatna, ale nie jest zalecana. |
| [Zarządzane lub zaufane tożsamości dla dostępu wychodzącego (indeksatora)](search-howto-managed-identities-data-sources.md) | Niedostępne w warstwie Bezpłatna.|
| [Klucze szyfrowania zarządzane przez klienta](search-security-manage-encryption-keys.md) | Niedostępne w warstwie Bezpłatna. |
| [Dostęp do zapory IP](service-configure-firewall.md) | Niedostępne w warstwie Bezpłatna. |
| [Prywatny punkt końcowy (Integracja z prywatnym łączem platformy Azure)](service-create-private-endpoint.md) | W przypadku połączeń przychodzących z usługą Search nie jest dostępna w warstwie Bezpłatna. W przypadku połączeń wychodzących przez indeksatory do innych zasobów platformy Azure, niedostępne w przypadku systemu HD lub S3. W przypadku indeksatorów korzystających z programu umiejętności, niedostępne w przypadku platformy Free, Basic, S1 lub S3 HD.|

Funkcje intensywnie korzystające z zasobów mogą nie być dobrze, chyba że zapewnisz wystarczającą pojemność. Na przykład [wzbogacanie systemu AI](cognitive-search-concept-intro.md) ma długotrwałe umiejętności, które przekraczają limit czasu dla bezpłatnej usługi, chyba że zestaw danych jest mały.

## <a name="upper-limits"></a>Górne limity

Warstwy określają maksymalny magazyn samej usługi, a także maksymalną liczbę indeksów, indeksatorów, źródła danych, umiejętności i mapy synonimów, które można utworzyć. Aby całkowicie obważyć wszystkie limity, zobacz [limity usługi w usłudze Azure wyszukiwanie poznawcze](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Rozmiar i szybkość partycji

Cennik warstwy obejmuje szczegóły dotyczące magazynu na partycję z zakresu od 2 GB do warstwy Podstawowa, do 2 TB dla warstw zoptymalizowanych pod kątem magazynu (L2). Inne cechy sprzętu, takie jak szybkość operacji, opóźnienia i szybkości transferu, nie są publikowane, ale warstwy zaprojektowane dla określonych architektur rozwiązań są oparte na sprzęcie, który ma funkcje do obsługi tych scenariuszy.

## <a name="billing-rates"></a>Stawki za rozliczenia

Warstwy mają różne stawki rozliczeń z wyższymi stawkami dla warstw, które działają na tańszym sprzęcie lub zapewniają bardziej kosztowne funkcje. Stawkę rozliczania według warstwy można znaleźć na [stronie cennika platformy Azure](https://azure.microsoft.com/pricing/details/search/) dla usługi Azure wyszukiwanie poznawcze.

Po utworzeniu usługi stawka rozliczeniowa naliczy się zarówno na *stały koszt* działania usługi przez zegar, jak i *koszt przyrostowy* , jeśli zdecydujesz się zwiększyć pojemność.

Usługi wyszukiwania są przydzieleni do zasobów obliczeniowych w postaci *partycji* (dla magazynu) i *replik* (wystąpienia aparatu zapytań). Początkowo usługa jest tworzona przy użyciu jednej z nich, a stawka rozliczeń obejmuje oba zasoby. Jednak w przypadku skalowania pojemności opłaty są naliczane w górę lub w dół w przyrostach stawki do rozliczenia.

Poniższy przykład stanowi ilustrację. Przyjmij hipotetyczną stawkę rozliczeniową wynoszącą $100 miesięcznie. Jeśli utrzymujesz, że usługa wyszukiwania ma swoją początkową pojemność jednej partycji i jedną replikę, wówczas $100 jest to, czego można oczekiwać na koniec miesiąca. Jeśli jednak dodasz dwie więcej replik w celu uzyskania wysokiej dostępności, miesięczne rozliczenie będzie zwiększać do $300 ($100 dla pierwszej pary partycji repliki, a następnie $200 dla dwóch replik).

Ten model cen jest oparty na koncepcji zastosowania stawki rozliczeń do *jednostek wyszukiwania* liczb (Su) używanych przez usługę wyszukiwania. Wszystkie usługi są początkowo inicjowane przy użyciu jednego elementu SU, ale można zwiększyć program SUs, dodając partycje lub repliki do obsługi większych obciążeń. Aby uzyskać więcej informacji, zobacz [jak oszacować koszty usługi wyszukiwania](search-sku-manage-costs.md).

## <a name="next-steps"></a>Następne kroki

Najlepszym sposobem wybrania warstwy cenowej jest rozpoczęcie pracy z warstwą tańszą, a następnie umożliwienie korzystania z nich i testowania w celu zapewnienia tej usługi lub utworzenia nowej. W przypadku kolejnych kroków zalecamy utworzenie usługi wyszukiwania w warstwie, która może obsłużyć poziom testowania proponowanego do wykonania, a następnie przejrzeć poniższe wskazówki dotyczące zaleceń dotyczących oszacowania kosztów i pojemności.

+ [Tworzenie usługi wyszukiwania](search-create-service-portal.md)
+ [Szacowanie kosztów](search-sku-manage-costs.md)
+ [Oszacowanie wydajności](search-sku-manage-costs.md)
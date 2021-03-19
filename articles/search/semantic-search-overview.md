---
title: Wyszukiwanie semantyczne
titleSuffix: Azure Cognitive Search
description: Dowiedz się, w jaki sposób Wyszukiwanie poznawcze używać modeli wyszukiwania semantycznego analizy głębokiej w usłudze Bing, aby wyniki wyszukiwania były bardziej intuicyjne.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: references_regions
ms.openlocfilehash: 443d6349aab68fd05edfe4c4007fd043c932f4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604274"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Wyszukiwanie semantyczne na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT]
> Wyszukiwanie semantyczne jest w publicznej wersji zapoznawczej, dostępne tylko za pomocą interfejsu API REST w wersji zapoznawczej. Funkcje w wersji zapoznawczej są oferowane w postaci, w której znajdują się [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), i nie mają gwarancji, że ta sama implementacja jest ogólnie dostępna. Te funkcje są rozliczane. Aby uzyskać więcej informacji, zobacz [dostępność i Cennik](semantic-search-overview.md#availability-and-pricing).

Wyszukiwanie semantyczne to zbiór funkcji związanych z zapytaniami, które obsługują bardziej naturalne środowisko zapytań o wyższej jakości. 

Te możliwości obejmują semantyczne przeklasyfikowanie wyników wyszukiwania, a także funkcję wyodrębniania podpisów i odpowiedzi, z wyróżnieniem semantyki w odniesieniu do odpowiednich terminów i fraz. Do wyodrębniania i klasyfikowania są używane najnowocześniejsze modele. Aby zachować szybką wydajność, którą użytkownicy oczekują od wyszukiwania, podsumowanie semantyczne i Klasyfikacja są stosowane do pierwszych 50 wyników, które są oceniane zgodnie z [domyślnym algorytmem oceniania podobieństwa](index-similarity-and-scoring.md#similarity-ranking-algorithms). Przy użyciu tych wyników jako dokumentu korpus, klasyfikacja semantyczna ocenia wyniki w oparciu o siłę semantyki dopasowania.

Podstawową technologią jest technologia Bing i Microsoft Research oraz zintegrowana z infrastrukturą Wyszukiwanie poznawcze jako funkcją dodatkową. Aby uzyskać więcej informacji na temat analizy semantyki do tworzenia kopii zapasowych, zobacz artykuł o sposobie, w [jaki AI z usługi Bing korzysta z platformy Azure wyszukiwanie poznawcze (blog Microsoft Research)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Poniższy klip wideo zawiera przegląd możliwości.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Składniki i przepływ pracy

Wyszukiwanie semantyczne usprawnia precyzję i cofa Dodawanie następujących możliwości:

| Cecha | Opis |
|---------|-------------|
| [Sprawdzanie pisowni](speller-how-to-add.md) | Koryguje literówki przed przekazaniem warunków zapytania do aparatu wyszukiwania. |
| [Klasyfikacja semantyczna](semantic-ranking.md) | Używa znaczenia kontekstu lub semantyki do obliczenia nowego wyniku istotności. |
| [Podpisy semantyczne i najważniejsze](semantic-how-to-query-request.md) | Zdania i frazy z dokumentu, który najlepiej podsumowują zawartość, z wyróżnionymi fragmentami na potrzeby szybkiego skanowania. Napisy podsumowujące wynik są przydatne, gdy poszczególne pola zawartości są zbyt gęste dla strony wyników. Wyróżniony tekst podnosi najbardziej odpowiednie warunki i frazy, dzięki czemu użytkownicy mogą szybko określić, dlaczego dopasowanie zostało uznane za istotne. |
| [Odpowiedzi semantyczne](semantic-answers.md) | Opcjonalna i dodatkowa podstruktura zwrócona przez zapytanie semantyczne. Zapewnia bezpośrednią odpowiedź na zapytanie, które wygląda jak pytanie. |

### <a name="order-of-operations"></a>Kolejność operacji

Składniki wyszukiwania semantycznego poszerzają istniejący potok wykonywania zapytania w obu kierunkach. Jeśli włączysz korektę pisowni, narzędzie [sprawdzania](speller-how-to-add.md) pisowni poprawi literówki na początku, zanim warunki zapytania osiągnie wyszukiwarkę.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Składniki semantyczne w wykonaniu zapytania" border="true":::

Wykonywanie zapytań jest wykonywane w zwykły sposób, z analizowaniem, analizą i skanowaniem w odniesieniu do odwróconych indeksów. Aparat pobiera dokumenty przy użyciu dopasowania tokenu i ocenia wyniki przy użyciu [domyślnego algorytmu określania podobieństwa](index-similarity-and-scoring.md#similarity-ranking-algorithms). Wyniki są obliczane na podstawie stopnia podobieństwa do języka między terminami zapytania i pasujących terminów w indeksie. Jeśli zostały zdefiniowane, na tym etapie są również stosowane profile oceniania. Wyniki są następnie przenoszone do podsystemu wyszukiwania semantycznego.

W kroku przygotowania dokument korpus zwrócony z początkowego zestawu wyników jest analizowany na poziomie zdania i akapitu, aby znaleźć fragmenty, które podsumowują każdy dokument. W przeciwieństwie do wyszukiwania słów kluczowych ten krok polega na tym, że w celu oszacowania zawartości jest wykorzystywany odczyt i zrozumienie maszyn. W ramach kompozycji wyniku zapytanie semantyczne zwraca napisy i odpowiedzi. Aby je sformułować, wyszukiwanie semantyczne używa reprezentacji języka do wyodrębniania i wyróżniania kluczowych fragmentów, które najlepiej podsumowują wynik. Jeśli zapytanie wyszukiwania jest pytaniem, a odpowiedzi są wymagane — odpowiedź będzie zawierać również fragment tekstu, który najlepiej odpowiada na pytanie, zgodnie z opisem w zapytaniu wyszukiwania. W przypadku obu napisów i odpowiedzi istniejący tekst jest używany w składzie. Modele semantyczne nie tworzą nowych zdań lub fraz z dostępnej zawartości ani nie stosują logiki, aby dotrzeć do nowych wniosków. W krótkim przypadku system nigdy nie będzie zwracać zawartości, która jeszcze nie istnieje.

Wyniki są następnie ponownie oceniane w oparciu o [podobieństwo pojęć związanych](semantic-ranking.md) z terminami zapytań.

Aby używać możliwości semantycznych w zapytaniach, należy wprowadzić niewielkie modyfikacje [żądania wyszukiwania](semantic-how-to-query-request.md), ale nie jest wymagane dodatkowe konfigurowanie ani ponowne indeksowanie.

## <a name="availability-and-pricing"></a>Dostępność i Cennik

Funkcje semantyczne są dostępne poprzez [rejestrację](https://aka.ms/SemanticSearchPreviewSignup)rejestracji, w usługach wyszukiwania utworzonych w warstwie Standardowa (S1, S2, S3), które znajdują się w jednym z następujących regionów: Północne stany USA, zachodnie stany USA, zachodnie stany USA 2, Wschodnie stany USA 2, Europa Północna, Europa Zachodnia. 

Korekta pisowni jest dostępna w tych samych regionach, ale nie ma ograniczeń warstwy. Jeśli masz istniejącą usługę, która spełnia kryteria warstwy i regionu, wymagane jest tylko rejestrowanie się.

Od 2 marca do 1 kwietnia, korekta pisowni i Klasyfikacja semantyczna są oferowane bezpłatnie. Po 1 kwietnia opłaty za korzystanie z tej funkcji będą naliczane. Oczekiwany koszt wynosi około USD $500 miesięcznie dla zapytań 250 000. Szczegółowe informacje o kosztach można znaleźć na [stronie cennika wyszukiwanie poznawcze](https://azure.microsoft.com/pricing/details/search/) , a następnie [oszacować koszty i zarządzać nimi](search-sku-manage-costs.md).

## <a name="next-steps"></a>Następne kroki

Nowy typ zapytania umożliwia tworzenie struktur klasyfikacji i odpowiedzi w przeszukiwaniu semantycznym.

[Utwórz zapytanie semantyczne](semantic-how-to-query-request.md) , aby rozpocząć. Lub zapoznaj się z następującymi artykułami, aby uzyskać pokrewne informacje.

+ [Dodaj sprawdzanie pisowni do terminów zapytania](speller-how-to-add.md)
+ [Zwróć odpowiedź semantyczną](semantic-answers.md)
+ [Klasyfikacja semantyczna](semantic-ranking.md)
+ [Przedstawiamy wyszukiwanie semantyczne (wpis w blogu)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Znajdź zrozumiałe informacje przy użyciu funkcji semantycznych (Pokaż wideo AI)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)
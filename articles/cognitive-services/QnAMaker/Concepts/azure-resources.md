---
title: Zasoby platformy Azure — QnA Maker
description: QnA Maker używa kilku źródeł platformy Azure, z których każdy ma inny cel. Zrozumienie, w jaki sposób są one używane pojedynczo, umożliwia zaplanowanie i wybranie odpowiedniej warstwy cenowej lub Dowiedz się, kiedy zmienić warstwę cenową. Zrozumienie, jak są używane w połączeniu, umożliwia znajdowanie i rozwiązywanie problemów, gdy wystąpią.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: f5a248b8701c71b2dcf0415c6656b9b565535232
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096539"
---
# <a name="azure-resources-for-qna-maker"></a>Zasoby platformy Azure dla QnA Maker

QnA Maker używa kilku źródeł platformy Azure, z których każdy ma inny cel. Zrozumienie, w jaki sposób są one używane pojedynczo, umożliwia zaplanowanie i wybranie odpowiedniej warstwy cenowej lub Dowiedz się, kiedy zmienić warstwę cenową. Zrozumienie, jak są używane _w połączeniu_ , umożliwia znajdowanie i rozwiązywanie problemów, gdy wystąpią.

## <a name="resource-planning"></a>Planowanie zasobów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Podczas pierwszego opracowania QnA Maker bazy wiedzy w fazie prototypu często istnieje pojedynczy zasób QnA Maker do testowania i produkcji.

Po przejściu do fazy opracowywania projektu należy rozważyć następujące kwestie:

* Ile języków będzie przechowywany system bazy wiedzy?
* Ile regionów potrzebujesz do udostępnienia bazy wiedzy?
* Ile dokumentów w każdej domenie będą przechowywane w systemie?

Zaplanuj, aby jeden zasób QnA Maker zatrzymał wszystkie bazy wiedzy, które mają ten sam język, ten sam region i tę samą kombinację domeny podmiotu.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Podczas pierwszego opracowania QnA Maker zarządzanej bazy wiedzy w fazie prototypu często istnieje pojedynczy QnA Maker zarządzany zasób do testowania i produkcji.

Po przejściu do fazy opracowywania projektu należy rozważyć następujące kwestie:

* Ile języków będzie przechowywany system bazy wiedzy?
* Ile regionów potrzebujesz do udostępnienia bazy wiedzy?
* Ile dokumentów w każdej domenie będą przechowywane w systemie?

---

## <a name="pricing-tier-considerations"></a>Zagadnienia dotyczące warstw cenowych

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Zwykle istnieją trzy parametry, które należy wziąć pod uwagę:

* **Przepływność wymagana przez usługę**:
    * Wybierz odpowiedni [Plan aplikacji](https://azure.microsoft.com/pricing/details/app-service/plans/) dla usługi App Service na podstawie Twoich potrzeb. Możesz [skalować w górę](../../../app-service/manage-scale-up.md) lub w dół aplikacji.
    * Powinno to mieć wpływ na wybór jednostki SKU platformy Azure **Wyszukiwanie poznawcze** , zobacz [tutaj](../../../search/search-sku-tier.md)więcej szczegółów. Ponadto może być konieczne dostosowanie Wyszukiwanie poznawcze [pojemności](../../../search/search-capacity-planning.md) z replikami.

* **Rozmiar i liczba baz wiedzy**: wybierz odpowiednią [jednostkę SKU usługi Azure Search](https://azure.microsoft.com/pricing/details/search/) dla danego scenariusza. Zazwyczaj użytkownik decyduje o liczbie potrzebnych baz wiedzy na podstawie liczby różnych domen podmiotu. Gdy domena podmiotu (dla jednego języka) powinna znajdować się w jednej bazie wiedzy.

    Bazy wiedzy o N-1 można opublikować w określonej warstwie, gdzie N to maksymalna liczba indeksów dozwolonych w warstwie. Sprawdź również maksymalny rozmiar i liczbę dokumentów dozwolonych dla każdej warstwy.

    Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). 15. indeks jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania.

* **Liczba dokumentów jako źródła**: bezpłatna jednostka SKU usługi zarządzania QNA Makerą ogranicza liczbę dokumentów, którymi można zarządzać za pośrednictwem portalu i interfejsów API do 3 (o rozmiarze 1 MB). Standardowa jednostka SKU nie ma ograniczeń do liczby dokumentów, którymi można zarządzać. Więcej szczegółów można znaleźć [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono ogólne wytyczne.

|                            | Zarządzanie QnA Maker | App Service | Azure Cognitive Search | Ograniczenia                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Eksperymentowanie**        | Bezpłatna jednostka SKU             | Warstwa Bezpłatna   | Warstwa Bezpłatna    | Publikowanie do 2 artykułów bazy wiedzy, rozmiar 50 MB  |
| **Środowisko deweloperskie/testowe**   | Standardowy SKU         | Udostępniona      | Podstawowa        | Publikowanie do 14 artykułów bazy wiedzy, rozmiar 2 GB    |
| **Środowisko produkcyjne** | Standardowy SKU         | Podstawowa       | Standardowa (Standard)     | Publikowanie do 49 artykułów bazy wiedzy, rozmiar 25 GB |

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Zwykle istnieją trzy parametry, które należy wziąć pod uwagę:

* **Przepływność wymagana przez usługę**:
    * QnA Maker Managed (wersja zapoznawcza) to bezpłatna usługa, a przepływność jest obecnie ograniczona do 10 TPS dla interfejsów API zarządzania i interfejsów API przewidywania.
    * Powinno to mieć wpływ na wybór jednostki SKU platformy Azure **Wyszukiwanie poznawcze** , zobacz [tutaj](../../../search/search-sku-tier.md)więcej szczegółów. Ponadto może być konieczne dostosowanie Wyszukiwanie poznawcze [pojemności](../../../search/search-capacity-planning.md) z replikami.

* **Rozmiar i liczba baz wiedzy**: wybierz odpowiednią [jednostkę SKU usługi Azure Search](https://azure.microsoft.com/pricing/details/search/) dla danego scenariusza. Zazwyczaj użytkownik decyduje o liczbie potrzebnych baz wiedzy na podstawie liczby różnych domen podmiotu. Gdy domena podmiotu (dla jednego języka) powinna znajdować się w jednej bazie wiedzy.

    Za pomocą QnA Maker Managed (wersja zapoznawcza) masz możliwość skonfigurowania usługi QnA Maker dla artykułów bazy wiedzy w jednym języku lub w wielu językach. Możesz wybrać tę opcję podczas tworzenia pierwszej bazy wiedzy w usłudze Managed QnA Maker (wersja zapoznawcza).

    ![QnA Maker zarządzane (wersja zapoznawcza) wybór bazy wiedzy dla wielu języków](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    Można opublikować bazy wiedzy N-1 jednego języka lub N/2 baz wiedzy różnych języków w danej warstwie, gdzie N to maksymalna liczba indeksów dozwolonych w warstwie. Sprawdź również maksymalny rozmiar i liczbę dokumentów dozwolonych dla każdej warstwy.

    Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy tego samego języka (1 indeks na opublikowaną bazę wiedzy). 15. indeks jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania. Jeśli zdecydujesz się na posiadanie baz wiedzy w różnych językach, możesz publikować tylko 7 baz wiedzy.

* **Liczba dokumentów jako źródła**: zarządzana QNA Maker (wersja zapoznawcza) to bezpłatna usługa i nie ma żadnych ograniczeń dotyczących liczby dokumentów, które można dodać jako źródła. Więcej szczegółów można znaleźć [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono ogólne wytyczne.

|                            |Azure Cognitive Search | Ograniczenia                      |
| -------------------------- |------------ | -------------------------------- |
| **Eksperymentowanie**        |Warstwa Bezpłatna    | Publikowanie do 2 artykułów bazy wiedzy, rozmiar 50 MB  |
| **Środowisko deweloperskie/testowe**   |Podstawowa        | Publikowanie do 14 artykułów bazy wiedzy, rozmiar 2 GB    |
| **Środowisko produkcyjne** |Standardowa     | Publikowanie do 49 artykułów bazy wiedzy, rozmiar 25 GB |

---

## <a name="recommended-settings"></a>Zalecane ustawienia

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

|ZAPYTAŃ docelowy | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 replika   | S1, 1 replika    |
| 50         | S3, 10 replik       | S1, 12 replik         |
| 80         | S3, 10 replik      |  S3, 12 replik  |
| 100         | P3V2, 10 replik  | S3, 12 replik, 3 partycje   |
| 200 do 250         | P3V2, 20 replik | S3, 12 replik, 3 partycje    |

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

QnA Maker zarządzany to bezpłatna usługa, a przepływność jest obecnie ograniczona do 10 transakcji na sekundę dla interfejsów API zarządzania i interfejsów API przewidywania. Aby uzyskać 10 transakcji na sekundę dla usługi, zalecamy użycie jednostki SKU S1 (1 wystąpienie) usługi Azure Wyszukiwanie poznawcze.

---

## <a name="when-to-change-a-pricing-tier"></a>Kiedy należy zmienić warstwę cenową

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

|Uaktualnienie|Przyczyna|
|--|--|
|[Uaktualnij](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) Jednostka SKU zarządzania QnA Maker|Chcesz mieć więcej par QnA lub źródeł dokumentów w bazie wiedzy.|
|[Uaktualnij](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service jednostki SKU i sprawdzanie warstwy Wyszukiwanie poznawcze i [Tworzenie replik wyszukiwanie poznawcze](../../../search/search-capacity-planning.md)|Baza wiedzy musi obtworzyć więcej żądań z aplikacji klienckiej, takich jak rozmowa bot.|
|[Uaktualnij](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Usługa Wyszukiwanie poznawcze platformy Azure|Planowane jest posiadanie wielu baz wiedzy.|

Pobierz najnowsze aktualizacje środowiska uruchomieniowego, [aktualizując App Service w Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

[Uaktualnij](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Usługa Azure Wyszukiwanie poznawcze w przypadku planowania wielu baz wiedzy.

---

## <a name="keys-in-qna-maker"></a>Klucze w QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Usługa QnA Maker obsługuje dwa rodzaje kluczy: **klucze autorstwa** i **klucze punktu końcowego zapytania** używane w środowisku uruchomieniowym hostowanym w usłudze App Service.

Te klucze są używane podczas wykonywania żądań do usługi za pomocą interfejsów API.

![Zarządzanie kluczami](../media/authoring-key.png)

|Nazwa|Lokalizacja|Przeznaczenie|
|--|--|--|
|Klucz tworzenia/subskrypcji|[Witryna Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Te klucze są używane w celu uzyskania dostępu do [interfejsów API usługi zarządzania QNA Maker](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Te interfejsy API umożliwiają edytowanie pytań i odpowiedzi w bazie wiedzy oraz publikowanie bazy wiedzy. Te klucze są tworzone podczas tworzenia nowej usługi QnA Maker.<br><br>Znajdź te klucze w zasobie **Cognitive Services** na stronie **klucze i punkt końcowy** .|
|Klucz punktu końcowego zapytania|[Portal usługi QnA Maker](https://www.qnamaker.ai)|Te klucze służą do wysyłania zapytań do opublikowanego punktu końcowego bazy wiedzy w celu uzyskania odpowiedzi na pytanie użytkownika. Ten punkt końcowy zapytania jest zazwyczaj używany w bot rozmowy lub w kodzie aplikacji klienta, który nawiązuje połączenie z usługą QnA Maker. Te klucze są tworzone podczas publikowania bazy wiedzy QnA Maker.<br><br>Te klucze znajdują się na stronie **ustawień usługi** . Znajdź Tę stronę w menu użytkownika w prawym górnym rogu strony z menu rozwijanego.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Znajdź klucze tworzenia w Azure Portal

Klucze tworzenia można wyświetlić i zresetować z poziomu Azure Portal, w którym został utworzony zasób QnA Maker.

1. Przejdź do zasobu QnA Maker w Azure Portal i wybierz zasób, który ma _Cognitive Services_ typ:

    ![Lista zasobów QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do pozycji **klucze i punkt końcowy**:

    ![Klucz subskrypcji zarządzany przez QnA Maker (wersja zapoznawcza)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Znajdowanie kluczy punktu końcowego zapytania w portalu QnA Maker

Punkt końcowy znajduje się w tym samym regionie, w którym znajduje się zasób, ponieważ klucze punktów końcowych są używane do wywołania bazy wiedzy.

Kluczami punktów końcowych można zarządzać z poziomu [portalu QNA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu QNA Maker](https://qnamaker.ai), przejdź do swojego profilu, a następnie wybierz pozycję **Ustawienia usługi**:

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Wyświetl lub zresetuj klucze:

    > [!div class="mx-imgBorder"]
    > ![Menedżer kluczy punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Odśwież klucze, jeśli uważasz, że zostały naruszone. Może to wymagać odpowiednich zmian w aplikacji klienckiej lub kodzie bot.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Usługa Managed QnA Maker (wersja zapoznawcza) zajmuje się dwoma rodzajami kluczy: **klucze tworzenia** i **klucze wyszukiwanie poznawcze platformy Azure** używane do uzyskiwania dostępu do usługi w ramach subskrypcji klienta.

Te klucze są używane podczas wykonywania żądań do usługi za pomocą interfejsów API.

![Zarządzanie kluczami — wersja zapoznawcza](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Nazwa|Lokalizacja|Przeznaczenie|
|--|--|--|
|Klucz tworzenia/subskrypcji|[Witryna Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Te klucze są używane w celu uzyskania dostępu do [interfejsów API usługi zarządzania QNA Maker](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Te interfejsy API umożliwiają edytowanie pytań i odpowiedzi w bazie wiedzy oraz publikowanie bazy wiedzy. Te klucze są tworzone podczas tworzenia nowej usługi QnA Maker.<br><br>Znajdź te klucze w zasobie **Cognitive Services** na stronie **klucze i punkt końcowy** .|
|Klucz administratora usługi Azure Wyszukiwanie poznawcze|[Witryna Azure Portal](../../../search/search-security-api-keys.md)|Te klucze są używane do komunikowania się z usługą wyszukiwania poznawczego platformy Azure wdrożoną w ramach subskrypcji platformy Azure użytkownika. Po skojarzeniu wyszukiwania poznawczego platformy Azure z usługą QnA Maker Managed (wersja zapoznawcza) klucz administratora jest automatycznie przenoszona do usługi QnA Maker. <br><br>Te klucze można znaleźć w zasobie **usługi Azure wyszukiwanie poznawcze** na stronie **klucze** .|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Znajdź klucze tworzenia w Azure Portal

Klucze tworzenia można wyświetlać i resetować z Azure Portal, w którym utworzono zasób QnA Maker Managed (wersja zapoznawcza).

1. Przejdź do zasobu Managed QnA Maker (wersja zapoznawcza) w Azure Portal i wybierz zasób, który ma *Cognitive Services* typ:

    ![Lista zasobów z zarządzaną QnA Maker (wersja zapoznawcza)](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. Przejdź do pozycji **klucze i punkt końcowy**:

    ![Klucz subskrypcji zarządzany przez QnA Maker (wersja zapoznawcza)](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>Aktualizowanie zasobów

Dowiedz się, jak uaktualnić zasoby używane przez bazę wiedzy. QnA Maker zarządzane (wersja zapoznawcza) jest **bezpłatna** w wersji zapoznawczej. 

---

## <a name="management-service-region"></a>Region usługi zarządzania

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Usługa zarządzania QnA Maker jest używana tylko dla portalu QnA Maker i do wstępnego przetwarzania danych. Ta usługa jest dostępna tylko w regionie **zachodnie stany USA** . W tym regionie zachodnie stany USA nie są przechowywane żadne dane klienta.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

W programie QnA Maker Managed (wersja zapoznawcza) zarówno zarządzanie, jak i usługi predykcyjne, znajdują się w tym samym regionie. Obecnie QnA Maker zarządzane (wersja zapoznawcza) jest dostępna w **regionach Południowo-środkowe stany USA, Europa Północna i Australia Wschodnia**.

---

## <a name="resource-naming-considerations"></a>Zagadnienia dotyczące nazewnictwa zasobów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Nazwa zasobu dla QnA Maker zasobu, na przykład `qna-westus-f0-b` , jest również używana do nazywania innych zasobów.

Okno Azure Portal Create umożliwia utworzenie zasobu QnA Maker i wybranie warstw cenowych dla innych zasobów.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający Azure Portal dla QnA Maker tworzenia zasobów](../media/concept-azure-resource/create-blade.png)

Po utworzeniu zasobów mają one taką samą nazwę, z wyjątkiem opcjonalnego zasobu Application Insights, który postpends znaki nazwy.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę zasobów Azure Portal](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Utwórz nową grupę zasobów podczas tworzenia zasobu QnA Maker. Umożliwia wyświetlenie wszystkich zasobów skojarzonych z zasobem QnA Maker podczas wyszukiwania według grupy zasobów.

> [!TIP]
> Użyj konwencji nazewnictwa, aby wskazać warstwy cenowe w ramach nazwy zasobu lub grupy zasobów. Po otrzymaniu błędów z tworzenia nowej bazy wiedzy lub dodaniu nowych dokumentów Wyszukiwanie poznawcze limitu warstwy cenowej jest często spotykany problem.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Nazwa zasobu QnA Maker dla zasobu zarządzanego (wersja zapoznawcza), na przykład `qna-westus-f0-b` , jest również używana do nazywania innych zasobów.

Okno Azure Portal Create umożliwia utworzenie zasobu zarządzanego QnA Maker (wersja zapoznawcza) i wybranie warstw cenowych dla innych zasobów.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający Azure Portal tworzenia zasobów zarządzanych przez QnA Maker (wersja zapoznawcza) ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) po utworzeniu zasobów ma taką samą nazwę.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę zasobów Azure Portal QnA Maker zarządzane (wersja zapoznawcza)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> Utwórz nową grupę zasobów podczas tworzenia zasobu QnA Maker. Pozwala to wyświetlić wszystkie zasoby skojarzone z zasobem zarządzanych QnA Maker (wersja zapoznawcza) podczas wyszukiwania według grupy zasobów.

> [!TIP]
> Użyj konwencji nazewnictwa, aby wskazać warstwy cenowe w ramach nazwy zasobu lub grupy zasobów. Po otrzymaniu błędów z tworzenia nowej bazy wiedzy lub dodaniu nowych dokumentów Wyszukiwanie poznawcze limitu warstwy cenowej jest często spotykany problem.

---

## <a name="resource-purposes"></a>Cele zasobów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Każdy zasób platformy Azure utworzony przy użyciu QnA Maker ma określony cel:

* Zasób QnA Maker
* Zasób Wyszukiwanie poznawcze
* App Service
* Usługa planu aplikacji
* Usługa Application Insights

### <a name="qna-maker-resource"></a>Zasób QnA Maker

Zasób QnA Maker zapewnia dostęp do interfejsów API tworzenia i publikowania, a także na podstawie drugiej warstwy klasyfikacyjnej (NLP) (ranga #2) par QnA w czasie wykonywania.

Druga klasyfikacja stosuje inteligentne filtry, które mogą zawierać metadane i monity monitujące.

#### <a name="qna-maker-resource-configuration-settings"></a>Ustawienia konfiguracji zasobów QnA Maker

Podczas tworzenia nowej bazy wiedzy w [portalu QNA Maker](https://qnamaker.ai)ustawienie **języka** jest jedynym ustawieniem stosowanym na poziomie zasobu. Język jest wybierany podczas tworzenia pierwszej bazy wiedzy dla zasobu.

### <a name="cognitive-search-resource"></a>Zasób Wyszukiwanie poznawcze

Zasób [Wyszukiwanie poznawcze](../../../search/index.yml) jest używany do:

* Przechowywanie par QnA
* Podaj początkową klasyfikację (#1 rangi) par QnA w czasie wykonywania

#### <a name="index-usage"></a>Użycie indeksu

Zasób zachowuje jeden indeks do działania jako indeks testu, a pozostałe indeksy są skorelowane do jednej opublikowanej bazy wiedzy.

Zasób cenowy do przechowywania 15 indeksów będzie zawierał 14 opublikowanych baz wiedzy, a jeden indeks jest używany do testowania wszystkich baz wiedzy. Ten indeks testu jest partycjonowany przez bazę wiedzy, dzięki czemu zapytanie korzystające z interaktywnego okienka testów użyje indeksu testu, ale zwróci tylko wyniki z konkretnej partycji skojarzonej z określoną bazą wiedzy.

#### <a name="language-usage"></a>Użycie języka

Pierwsza baza wiedzy utworzona w zasobie QnA Maker służy do określenia _jednego_ zestawu języka dla zasobu wyszukiwanie poznawcze i wszystkich jego indeksów. Dla usługi QnA Maker można _ustawić tylko jeden język_ .

#### <a name="using-a-single-cognitive-search-service"></a>Korzystanie z pojedynczej usługi Wyszukiwanie poznawcze

Jeśli utworzysz usługę QnA i jej zależności (takie jak wyszukiwanie) w portalu, zostanie utworzona usługa wyszukiwania i zostanie ona połączona z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie App Service tak, aby korzystało już z istniejącej usługi wyszukiwania, i usunąć właśnie utworzoną.

Dowiedz się, [jak skonfigurować](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QNA Maker, aby używać innego zasobu usługi poznawczej niż ten, który został utworzony w ramach procesu tworzenia zasobu QNA Maker.

### <a name="app-service-and-app-service-plan"></a>Usługa App Service i plan usługi App Service

[Usługa App Service](../../../app-service/index.yml) jest używana przez aplikację kliencką do uzyskiwania dostępu do opublikowanych baz wiedzy za pośrednictwem punktu końcowego środowiska uruchomieniowego.

Aby wysłać zapytanie do opublikowanej bazy wiedzy, wszystkie opublikowane bazy wiedzy używają tego samego punktu końcowego adresu URL, ale Określ **Identyfikator bazy wiedzy** w ramach trasy.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) służy do zbierania dzienników rozmowy i telemetrii. Przejrzyj typowe [zapytania Kusto](../how-to/get-analytics-knowledge-base.md) , aby uzyskać informacje o usłudze.

### <a name="share-services-with-qna-maker"></a>Udostępnianie usług za pomocą QnA Maker

QnA Maker tworzy kilka zasobów platformy Azure. Aby ograniczyć zarządzanie i korzystać z zalet udostępniania kosztów, Skorzystaj z poniższej tabeli, aby zrozumieć, co można i czego nie można udostępnić:

|Usługa|Udostępnij|Przyczyna|
|--|--|--|
|Cognitive Services|X|Niemożliwa przez projekt|
|Plan usługi App Service|✔|Stałe miejsce na dysku przydzielono dla planu App Service. Jeśli inne aplikacje, które współużytkują ten sam plan App Service, użyją znacznego miejsca na dysku, wystąpienie App Service QnAMaker napotka problemy.|
|App Service|X|Niemożliwa przez projekt|
|Application Insights|✔|Mogą być udostępniane|
|Search Service|✔|1. `testkb` jest zarezerwowaną nazwą usługi QnAMaker. nie może być używana przez inne osoby.<br>2. Mapa synonimów o nazwie `synonym-map` jest zarezerwowana dla usługi QnAMaker.<br>3. liczba opublikowanych baz wiedzy jest ograniczona przez warstwę usługi wyszukiwania. Jeśli dostępne są wolne indeksy, można z nich korzystać inne usługi.|

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Każdy zasób platformy Azure utworzony przy użyciu QnA Maker zarządzane (wersja zapoznawcza) ma określony cel:

* Zasób QnA Maker
* Zasób Wyszukiwanie poznawcze

### <a name="qna-maker-resource"></a>Zasób QnA Maker

Zasób zarządzany QnA Maker (wersja zapoznawcza) zapewnia dostęp do interfejsów API tworzenia i publikowania, hostuje środowisko uruchomieniowe klasyfikacji oraz udostępnia dane telemetryczne.

### <a name="azure-cognitive-search-resource"></a>Zasób Wyszukiwanie poznawcze platformy Azure

Zasób [Wyszukiwanie poznawcze](../../../search/index.yml) jest używany do:

* Przechowywanie par QnA
* Podaj początkową klasyfikację (#1 rangi) par QnA w czasie wykonywania

#### <a name="index-usage"></a>Użycie indeksu

Możesz publikować bazy wiedzy o N-1 w jednym języku lub N/2 bazach wiedzy różnych języków w danej warstwie, gdzie N to maksymalna liczba indeksów dozwolona w warstwie Wyszukiwanie poznawcze platformy Azure. Sprawdź również maksymalny rozmiar i liczbę dokumentów dozwolonych dla każdej warstwy.

Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy tego samego języka (1 indeks na opublikowaną bazę wiedzy). 15. indeks jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania. Jeśli zdecydujesz się na posiadanie baz wiedzy w różnych językach, możesz publikować tylko 7 baz wiedzy.

#### <a name="language-usage"></a>Użycie języka

Dzięki programowi QnA Maker Managed (wersja zapoznawcza) masz możliwość skonfigurowania usługi QnA Maker dla baz wiedzy w jednym języku lub w wielu językach. Wybór ten należy wykonać podczas tworzenia pierwszej bazy wiedzy w usłudze QnA Maker. Zobacz [tutaj](#pricing-tier-considerations) , jak włączyć ustawienie języka dla bazy wiedzy.

---

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o QnA Maker [bazie wiedzy](../How-To/manage-knowledge-bases.md)
* Omówienie [cyklu życia bazy wiedzy](development-lifecycle-knowledge-base.md)
* Przejrzyj [limity](../limits.md) usługi i bazy wiedzy

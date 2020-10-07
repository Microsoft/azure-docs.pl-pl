---
title: Zasoby platformy Azure — QnA Maker
description: QnA Maker używa kilku źródeł platformy Azure, z których każdy ma inny cel. Zrozumienie, w jaki sposób są one używane pojedynczo, umożliwia zaplanowanie i wybranie odpowiedniej warstwy cenowej lub Dowiedz się, kiedy zmienić warstwę cenową. Zrozumienie, jak są używane w połączeniu, umożliwia znajdowanie i rozwiązywanie problemów, gdy wystąpią.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 62f627fb9765f2a86a373f74c33437680c9305af
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777058"
---
# <a name="azure-resources-for-qna-maker"></a>Zasoby platformy Azure dla QnA Maker

QnA Maker używa kilku źródeł platformy Azure, z których każdy ma inny cel. Zrozumienie, w jaki sposób są one używane pojedynczo, umożliwia zaplanowanie i wybranie odpowiedniej warstwy cenowej lub Dowiedz się, kiedy zmienić warstwę cenową. Zrozumienie, jak są używane _w połączeniu_ , umożliwia znajdowanie i rozwiązywanie problemów, gdy wystąpią.

## <a name="resource-planning"></a>Planowanie zasobów

Podczas pierwszego opracowania QnA Maker bazy wiedzy w fazie prototypu często istnieje pojedynczy zasób QnA Maker do testowania i produkcji.

Po przejściu do fazy opracowywania projektu należy rozważyć następujące kwestie:

* Liczba języków, w których będzie przechowywany system bazy wiedzy
* Liczba regionów, w których baza wiedzy ma być dostępna w/z
* Liczba dokumentów w każdej domenie, które będą przechowywane w systemie

Zaplanuj, aby jeden zasób QnA Maker zatrzymał wszystkie bazy wiedzy, które mają ten sam język, ten sam region i tę samą kombinację domeny podmiotu.

## <a name="pricing-tier-considerations"></a>Zagadnienia dotyczące warstw cenowych

Zwykle istnieją trzy parametry, które należy wziąć pod uwagę:

* **Przepływność wymagana przez usługę**:
    * Wybierz odpowiedni [Plan aplikacji](https://azure.microsoft.com/pricing/details/app-service/plans/) dla usługi App Service na podstawie Twoich potrzeb. Możesz [skalować w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub w dół aplikacji.
    * Powinno to mieć wpływ na wybór jednostki SKU platformy Azure **Wyszukiwanie poznawcze** , zobacz [tutaj](https://docs.microsoft.com/azure/search/search-sku-tier)więcej szczegółów. Ponadto może być konieczne dostosowanie Wyszukiwanie poznawcze [pojemności](../../../search/search-capacity-planning.md) z replikami.

* **Rozmiar i liczba baz wiedzy**: wybierz odpowiednią [jednostkę SKU usługi Azure Search](https://azure.microsoft.com/pricing/details/search/) dla danego scenariusza. Zazwyczaj użytkownik decyduje o liczbie potrzebnych baz wiedzy na podstawie liczby różnych domen podmiotu. Gdy domena podmiotu (dla jednego języka) powinna znajdować się w jednej bazie wiedzy.

    Bazy wiedzy o N-1 można opublikować w określonej warstwie, gdzie N to maksymalna liczba indeksów dozwolonych w warstwie. Sprawdź również maksymalny rozmiar i liczbę dokumentów dozwolonych dla każdej warstwy.

    Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). 15. indeks jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania.

* **Liczba dokumentów jako źródła**: bezpłatna jednostka SKU usługi zarządzania QNA Makerą ogranicza liczbę dokumentów, którymi można zarządzać za pośrednictwem portalu i interfejsów API do 3 (o rozmiarze 1 MB). Standardowa jednostka SKU nie ma ograniczeń do liczby dokumentów, którymi można zarządzać. Więcej szczegółów można znaleźć [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono ogólne wytyczne.

|                            | Zarządzanie QnA Maker | App Service | Azure Cognitive Search | Ograniczenia                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Eksperymentowanie**        | Bezpłatna jednostka SKU             | Warstwa Bezpłatna   | Warstwa Bezpłatna    | Publikowanie do 2 artykułów bazy wiedzy, rozmiar 50 MB  |
| **Środowisko deweloperskie/testowe**   | Standardowy SKU         | Shared      | Podstawowy        | Publikowanie do 14 artykułów bazy wiedzy, rozmiar 2 GB    |
| **Środowisko produkcyjne** | Standardowy SKU         | Podstawowa       | Standardowa (Standard)     | Publikowanie do 49 artykułów bazy wiedzy, rozmiar 25 GB |

## <a name="recommended-settings"></a>Zalecane ustawienia

|ZAPYTAŃ docelowy | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 wystąpienie   | S1, 1 wystąpienie    |
| 50         | S3, 10 wystąpień       | S1, 12 wystąpień         |
| 80         | S3, 10 wystąpień      |  S3, 12 wystąpień  |
| 100         | P3V2, 10 wystąpień  | S3, 12 wystąpień, 3 partycje   |
| 200 do 250         | P3V2, 20 wystąpień | S3, 12 wystąpień, 3 partycje    |

## <a name="when-to-change-a-pricing-tier"></a>Kiedy należy zmienić warstwę cenową

|Uaktualnienie|Przyczyna|
|--|--|
|[Uaktualnij](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) Jednostka SKU zarządzania QnA Maker|Chcesz mieć więcej par QnA lub źródeł dokumentów w bazie wiedzy.|
|[Uaktualnij](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service jednostki SKU i sprawdzanie warstwy Wyszukiwanie poznawcze i [Tworzenie replik wyszukiwanie poznawcze](../../../search/search-capacity-planning.md)|Baza wiedzy musi obtworzyć więcej żądań z aplikacji klienckiej, takich jak rozmowa bot.|
|[Uaktualnij](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Usługa Wyszukiwanie poznawcze platformy Azure|Planowane jest posiadanie wielu baz wiedzy.|

Pobierz najnowsze aktualizacje środowiska uruchomieniowego, [aktualizując App Service w Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Zagadnienia dotyczące nazewnictwa zasobów

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

## <a name="resource-purposes"></a>Cele zasobów

Każdy zasób platformy Azure utworzony przy użyciu QnA Maker ma określony cel:

* Zasób QnA Maker
* Zasób Wyszukiwanie poznawcze
* App Service
* Usługa planu aplikacji
* Usługa Application Insights


### <a name="cognitive-search-resource"></a>Zasób Wyszukiwanie poznawcze

Zasób [Wyszukiwanie poznawcze](../../../search/index.yml) jest używany do:

* Przechowywanie par QnA
* Podaj początkową klasyfikację (#1 rangi) par QnA w czasie wykonywania

#### <a name="index-usage"></a>Użycie indeksu

Zasób zachowuje jeden indeks do działania jako indeks testu, a pozostałe indeksy są skorelowane do jednej opublikowanej bazy wiedzy.

Zasób cenowy do przechowywania 15 indeksów będzie zawierał 14 opublikowanych baz wiedzy, a jeden indeks jest używany do testowania wszystkich baz wiedzy. Ten indeks testu jest partycjonowany przez bazę wiedzy, dzięki czemu zapytanie korzystające z interaktywnego okienka testów użyje indeksu testu, ale zwróci tylko wyniki z konkretnej partycji skojarzonej z określoną bazą wiedzy.

#### <a name="language-usage"></a>Użycie języka

Pierwsza baza wiedzy utworzona w zasobie QnA Maker służy do określenia _jednego_ zestawu języka dla zasobu wyszukiwanie poznawcze i wszystkich jego indeksów. Dla usługi QnA Maker można _ustawić tylko jeden język_ .

### <a name="qna-maker-resource"></a>Zasób QnA Maker

Zasób QnA Maker zapewnia dostęp do interfejsów API tworzenia i publikowania, a także na podstawie drugiej warstwy klasyfikacyjnej (NLP) (ranga #2) par QnA w czasie wykonywania.

Druga klasyfikacja stosuje inteligentne filtry, które mogą zawierać metadane i monity monitujące.

#### <a name="qna-maker-resource-configuration-settings"></a>Ustawienia konfiguracji zasobów QnA Maker

Podczas tworzenia nowej bazy wiedzy w [portalu QNA Maker](https://qnamaker.ai)ustawienie **języka** jest jedynym ustawieniem stosowanym na poziomie zasobu. Język jest wybierany podczas tworzenia pierwszej bazy wiedzy dla zasobu.

### <a name="app-service-and-app-service-plan"></a>Usługa App Service i plan usługi App Service

[Usługa App Service](../../../app-service/index.yml) jest używana przez aplikację kliencką do uzyskiwania dostępu do opublikowanych baz wiedzy za pośrednictwem punktu końcowego środowiska uruchomieniowego.

Aby wysłać zapytanie do opublikowanej bazy wiedzy, wszystkie opublikowane bazy wiedzy używają tego samego punktu końcowego adresu URL, ale Określ **Identyfikator bazy wiedzy** w ramach trasy.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) służy do zbierania dzienników rozmowy i telemetrii. Przejrzyj typowe [zapytania Kusto](../how-to/get-analytics-knowledge-base.md) , aby uzyskać informacje o usłudze.

## <a name="share-services-with-qna-maker"></a>Udostępnianie usług za pomocą QnA Maker

QnA Maker tworzy kilka zasobów platformy Azure. Aby ograniczyć zarządzanie i korzystać z zalet udostępniania kosztów, Skorzystaj z poniższej tabeli, aby zrozumieć, co można i czego nie można udostępnić:

|Usługa|Udostępnij|Przyczyna|
|--|--|--|
|Cognitive Services|X|Niemożliwa przez projekt|
|Plan usługi App Service|✔|Stałe miejsce na dysku przydzielono dla planu App Service. Jeśli inne aplikacje, które współużytkują ten sam plan App Service, użyją znacznego miejsca na dysku, wystąpienie App Service QnAMaker napotka problemy.|
|App Service|X|Niemożliwa przez projekt|
|Application Insights|✔|Mogą być udostępniane|
|Search Service|✔|1. `testkb` jest zarezerwowaną nazwą usługi QnAMaker. nie może być używana przez inne osoby.<br>2. Mapa synonimów o nazwie `synonym-map` jest zarezerwowana dla usługi QnAMaker.<br>3. liczba opublikowanych baz wiedzy jest ograniczona przez warstwę usługi wyszukiwania. Jeśli dostępne są wolne indeksy, można z nich korzystać inne usługi.|

### <a name="using-a-single-cognitive-search-service"></a>Korzystanie z pojedynczej usługi Wyszukiwanie poznawcze

Jeśli utworzysz usługę QnA i jej zależności (takie jak wyszukiwanie) w portalu, zostanie utworzona usługa wyszukiwania i zostanie ona połączona z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie App Service tak, aby korzystało już z istniejącej usługi wyszukiwania, i usunąć właśnie utworzoną.

Dowiedz się, [jak skonfigurować](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QNA Maker, aby używać innego zasobu usługi poznawczej niż ten, który został utworzony w ramach procesu tworzenia zasobu QNA Maker.

## <a name="management-service-region"></a>Region usługi zarządzania

Usługa zarządzania QnA Maker jest używana tylko dla portalu QnA Maker i do wstępnego przetwarzania danych. Ta usługa jest dostępna tylko w regionie **zachodnie stany USA** . W tym regionie zachodnie stany USA nie są przechowywane żadne dane klienta.

## <a name="keys-in-qna-maker"></a>Klucze w QnA Maker

Usługa QnA Maker obsługuje dwa rodzaje kluczy: **klucze autorstwa** i **klucze punktu końcowego zapytania** używane w środowisku uruchomieniowym hostowanym w usłudze App Service.

Jeśli szukasz **klucza subskrypcji**, [terminologia została zmieniona](#subscription-keys).

Te klucze są używane podczas wykonywania żądań do usługi za pomocą interfejsów API.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

|Nazwa|Lokalizacja|Przeznaczenie|
|--|--|--|
|Klucz tworzenia|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Te klucze są używane w celu uzyskania dostępu do [interfejsów API usługi zarządzania QNA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Te interfejsy API umożliwiają edytowanie pytań i odpowiedzi w bazie wiedzy oraz publikowanie bazy wiedzy. Te klucze są tworzone podczas tworzenia nowej usługi QnA Maker.<br><br>Znajdź te klucze w zasobie **Cognitive Services** na stronie **klucze** .|
|Klucz punktu końcowego zapytania|[Portal usługi QnA Maker](https://www.qnamaker.ai)|Te klucze służą do wysyłania zapytań do opublikowanego punktu końcowego bazy wiedzy w celu uzyskania odpowiedzi na pytanie użytkownika. Ten punkt końcowy zapytania jest zazwyczaj używany w bot rozmowy lub w kodzie aplikacji klienta, który nawiązuje połączenie z usługą QnA Maker. Te klucze są tworzone podczas publikowania bazy wiedzy QnA Maker.<br><br>Te klucze znajdują się na stronie **ustawień usługi** . Znajdź Tę stronę w menu użytkownika w prawym górnym rogu strony z menu rozwijanego.|

### <a name="subscription-keys"></a>Klucze subskrypcji

Kluczowe terminy tworzenia i wykonywania zapytań są poprawnymi postanowieniami. Poprzedni termin był **kluczem subskrypcji**. Jeśli zostanie wyświetlona inna dokumentacja odwołująca się do kluczy subskrypcji, są one równoważne z tworzeniem kluczy punktu końcowego tworzenia i wykonywania zapytań (użytych w czasie wykonywania).

Aby dowiedzieć się, który klucz należy znaleźć, należy znać dostęp do klucza, zarządzania bazami wiedzy lub zapytania bazy wiedzy.

## <a name="recommended-settings-for-network-isolation"></a>Zalecane ustawienia izolacji sieci

* Ochrona zasobów usługi poznawczej przed dostępem publicznym przez [skonfigurowanie sieci wirtualnej](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal).
* Ochrona App Service (QnA Runtime) z dostępu publicznego:
    * Zezwalaj na ruch tylko z adresów IP usługi poznawczej. Są one już zawarte w tagu usługi "CognitiveServicesManagement". Jest to wymagane do tworzenia interfejsów API (Utwórz/zaktualizuj KB), aby odpowiednio wywoływać usługę App Service i zaktualizować usługę Azure Search.
    * Upewnij się, że można również zezwolić na inne punkty wejścia, takie jak usługa bot, Portal QnA Maker (może to być Corpnet) itp., aby uzyskać dostęp do interfejsu API przewidywania "GenerateAnswer".
    * Zapoznaj się z [dodatkowymi informacjami na temat tagów usługi.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o QnA Maker [bazie wiedzy](knowledge-base.md)
* Omówienie [cyklu życia bazy wiedzy](development-lifecycle-knowledge-base.md)
* Przejrzyj [limity](../limits.md) usługi i bazy wiedzy


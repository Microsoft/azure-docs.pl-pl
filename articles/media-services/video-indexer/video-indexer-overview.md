---
title: Co to jest Video Indexer?
titleSuffix: Azure Media Services
description: Ten artykuł zawiera omówienie usługi indeksatora wideo usługi Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: juliako
ms.openlocfilehash: 1c7156da171a101f04db9f83df587f5b8bc71e8f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687113"
---
# <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Indeksator wideo (VI) to rozwiązanie sztucznej inteligencji usługi Azure Media Services i część marki Usługi Azure Cognitive Services. Indeksator wideo zapewnia możliwość wyodrębniania szczegółowych informacji (bez potrzeby analizy danych lub umiejętności kodowania) przy użyciu modeli uczenia maszynowego opartych na wielu kanałach (głos, wokal, wizualny). Można dodatkowo dostosować i wyszkolić modele. Usługa umożliwia głębokie wyszukiwanie, zmniejsza koszty operacyjne, umożliwia nowe możliwości zarabiania i tworzy nowe środowisko użytkownika w dużych archiwach filmów (z niskimi barierami wejścia).

Aby rozpocząć wyodrębnianie statystyk za pomocą indeksatora wideo, musisz utworzyć konto i przesłać filmy. Gdy przesyłasz filmy do indeksatora wideo, analizuje zarówno efekty wizualne, jak i dźwięk, uruchamiając różne modele SI. Gdy indeksator wideo analizuje twój film, szczegółowe informacje, które są wyodrębniane przez modele AI.

Poniższy diagram jest ilustracją, a nie technicznym wyjaśnieniem, jak indeksator wideo działa w wewnętrznej bazy danych.

![Diagram przepływu indeksatora wideo usługi Azure Media Services](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia

Jako ważne przypomnienie musisz przestrzegać wszystkich obowiązujących przepisów podczas korzystania z indeksatora wideo i nie możesz używać indeksatora wideo ani żadnej usługi platformy Azure w sposób, który narusza prawa innych osób lub może być szkodliwy dla innych osób.

Przed przesłaniem jakichkolwiek wideo/obrazów do video Indexer, Użytkownik musi mieć wszystkie odpowiednie prawa do korzystania z wideo/obrazu, w tym, jeśli jest to wymagane przez prawo, wszystkie niezbędne zgody od osób fizycznych (jeśli istnieją) w wideo /obraz, do wykorzystania, przetwarzania i przechowywania ich danych w Video Indexer i azure. Niektóre jurysdykcje mogą nakładać specjalne wymogi prawne dotyczące gromadzenia, przetwarzania i przechowywania danych online określonych kategorii danych, takich jak dane biometryczne. Przed użyciem video indexer i platformy Azure do przetwarzania i przechowywania wszelkich danych podlegających specjalnym wymogom prawnym, Należy zapewnić zgodność z takimi wymogami prawnymi, które mogą mieć zastosowanie do Ciebie.

Aby dowiedzieć się więcej o zgodności, prywatności i bezpieczeństwie w video indexerze, odwiedź [Centrum zaufania](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)firmy Microsoft . W przypadku obowiązków firmy Microsoft w zakresie ochrony prywatności, praktyk w zakresie przetwarzania danych i ich przechowywania, w tym sposobu usuwania danych, należy zapoznać się z [Zasadami zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, [Warunkami korzystania z usług online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") i [uzupełnieniem dotyczącym przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Korzystając z Video Indexer, użytkownik wyraża zgodę na przestrzeganie OST, DPA i Oświadczenia o ochronie prywatności.

## <a name="what-can-i-do-with-video-indexer"></a>Co mogę zrobić z video indexerem?

Statystyki indeksatora wideo można zastosować do wielu scenariuszy, wśród nich są:

* *Głębokie wyszukiwanie:* skorzystaj ze statystyk wyodrębnionych z filmu, aby poprawić jakość wyszukiwania w bibliotece wideo. Na przykład indeksowanie wypowiedzianych słów i twarzy może umożliwić wyszukiwanie momentów w filmie, w którym dana osoba wypowiedziała określone słowa lub gdy dwie osoby były widziane razem. Wyszukiwanie na podstawie takich informacji z filmów ma zastosowanie do agencji informacyjnych, instytucji edukacyjnych, nadawców, właścicieli treści rozrywkowych, aplikacji BIZNESOWYCH LOB i ogólnie do każdej branży, która ma bibliotekę wideo, której użytkownicy muszą przeszukiwać.
* *Tworzenie treści:* twórz zwiastuny, wyróżnij bębny, treści w mediach społecznościowych lub klipy z wiadomościami na podstawie statystyk, które indeksator wideo wyodrębnia z twoich treści. Klatki kluczowe, znaczniki scen i znaczniki czasu dla osób i wyglądy etykiet sprawiają, że proces tworzenia jest znacznie płynniejszy i łatwiejszy oraz pozwala uzyskać dostęp do części wideo potrzebnych do tworzenia zawartości.
* *Ułatwienia dostępu:* Niezależnie od tego, czy chcesz udostępnić zawartość osobom niepełnosprawnym, czy chcesz, aby twoje treści były dystrybuowane do różnych regionów w różnych językach, możesz użyć transkrypcji i tłumaczenia dostarczonego przez indeksator wideo w wielu językach.
* *Zarabianie:* Indeksator wideo może pomóc zwiększyć wartość filmów. Na przykład branże, które opierają się na przychodach z reklam (media informacyjne, media społecznościowe itd.) mogą wyświetlać trafne reklamy, używając wyodrębnionych statystyk jako dodatkowych sygnałów do serwera reklam.
* *Moderowanie zawartości:* użyj modeli moderowania treści tekstowych i wizualnych, aby chronić użytkowników przed nieodpowiednimi treściami i sprawdzać, czy publikowana zawartość jest zgodna z wartościami organizacji. Możesz automatycznie zablokować niektóre filmy lub powiadomić użytkowników o treści.
* *Rekomendacje:* statystyki wideo mogą być wykorzystane do zwiększenia zaangażowania użytkowników, podświetlając odpowiednie momenty wideo dla użytkowników. Oznaczając każdy film dodatkowymi metadanymi, możesz polecić użytkownikom najbardziej odpowiednie filmy i wyróżnić części filmu, które będą odpowiadały ich potrzebom.

## <a name="features"></a>Funkcje

Na poniższej liście przedstawiono szczegółowe informacje, które można pobrać z filmów przy użyciu modeli wideo i audio indeksatora wideo:

### <a name="video-insights"></a>Wgląd w wideo

* **Wykrywanie twarzy**: wykrywa i grupuje twarze pojawiające się w nagraniu wideo.
* **Identyfikacja gwiazd:** Video Indexer automatycznie identyfikuje ponad milion gwiazd , takich jak światowi liderzy, aktorzy, aktorki, sportowcy, naukowcy, biznes i liderzy technologii na całym świecie. Dane o tych celebrytach można również znaleźć na różnych stronach internetowych (IMDB, Wikipedia i tak dalej).
* **Identyfikowanie twarzy na podstawie konta**: usługa Video Indexer szkoli model na podstawie określonego konta. Następnie rozpoznaje twarze w filmie na podstawie wyszkolonego modelu. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu osoby z witryny indeksatora wideo](customize-person-model-with-website.md) i [dostosowywanie modelu osoby za pomocą interfejsu API indeksatora wideo](customize-person-model-with-api.md).
* **Wyodrębnianie miniatur dla twarzy** ("najlepsza twarz"): Automatycznie identyfikuje najlepiej przechwyconą twarz w każdej grupie ścian (na podstawie jakości, rozmiaru i pozycji czołowej) i wyodrębnia ją jako zasób obrazu.
* **Wizualne rozpoznawanie tekstu** (OCR): wyodrębnia tekst, który jest wizualnie wyświetlany w filmie.
* **Moderowanie zawartości wizualnej**: wykrywa widoczną zawartość dla dorosłych i/lub erotyczną.
* **Identyfikowanie etykiet**: identyfikuje widoczne obiekty i czynności.
* **Segmentacja sceny**: Określa, kiedy scena zmienia się w filmie na podstawie wskazówek wizualnych. Scena przedstawia pojedyncze zdarzenie i składa się z serii kolejnych ujęć, które są semantycznie związane.
* **Wykrywanie strzałów:** Określa, kiedy ujęcie zmienia się w filmie na podstawie wskazówek wizualnych. Ujęcie to seria klatek pobranych z tej samej kamery filmowej. Aby uzyskać więcej informacji, zobacz [Sceny, zdjęcia i klatki kluczowe](scenes-shots-keyframes.md).
* **Wykrywanie czarnych ramek**: identyfikuje pojawiające się w nagraniu wideo czarne ramki.
* **Wyodrębnianie ramek kluczowych**: wykrywa ramki kluczowe w nagraniu wideo.
* **Kredyty kroczące**: Określa początek i koniec kredytów kroczących na końcu programów telewizyjnych i filmów.
* **Wykrywanie animowanych znaków** (podgląd): wykrywanie, grupowanie i rozpoznawanie znaków w animowanej zawartości poprzez integrację z [niestandardową wizją usług Cognitive Services.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Aby uzyskać więcej informacji, zobacz [Animowane wykrywanie znaków](animated-characters-recognition.md).
* **Wykrywanie typu zdjęć redakcyjnych:** Oznaczanie zdjęć na podstawie ich typu (np. szeroki strzał, średni strzał, zbliżenie, ekstremalne zbliżenie, dwa ujęcia, wiele osób, na zewnątrz i w pomieszczeniach, i tak dalej). Aby uzyskać więcej informacji, zobacz [Wykrywanie typu zdjęcia redakcyjnego](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Szczegółowe informacje audio

* **Transkrypcja audio:** Konwertuje mowę na tekst w 12 językach i umożliwia rozszerzenia. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, mandaryński chiński, japoński, arabski, rosyjski, brazylijski portugalski, hindi i koreański.
* **Automatyczne wykrywanie języka**: automatycznie rozpoznaje dominujący język mówiony. Obsługiwane języki to angielski, hiszpański, francuski, niemiecki, włoski, mandaryński chiński, japoński, rosyjski i brazylijski portugalski. Jeśli nie można zidentyfikować języka z ufnością, Indeksator wideo zakłada, że językiem mówionym jest angielski. Aby uzyskać więcej informacji, zobacz [Model identyfikacji języka](language-identification-model.md).
* **Wielojęzyczna identyfikacja mowy i transkrypcja** (wersja zapoznawcza): automatycznie identyfikuje język mówiony w różnych segmentach z audio. Wysyła każdy segment pliku multimedialnego do transkrypcji, a następnie łączy transkrypcję z powrotem do jednej ujednoliconej transkrypcji. Aby uzyskać więcej informacji, zobacz [Automatyczne identyfikowanie i transkrybowanie zawartości wielojęzycznej](multi-language-identification-transcription.md).
* **Napisy (transkrypcja)**: tworzy napisy w trzech formatach: VTT, TTML i SRT.
* **Przetwarzanie dwóch kanałów:** Automatycznie wykrywa oddzielne transkrypcje i scala się z pojedynczą osią czasu.
* **Redukcja szumów:** Usuwa dźwięk telefonii lub hałaśliwe nagrania (na podstawie filtrów Skype).
* **Dostosowywanie transkrypcji** (CRIS): Trenuje niestandardowe modele mowy do tekstu w celu utworzenia transkrypcji specyficznych dla branży. Aby uzyskać więcej informacji, zobacz [Dostosowywanie modelu języka z witryny indeksatora wideo](customize-language-model-with-website.md) i [dostosowywanie modelu języka za pomocą interfejsów API indeksatora wideo](customize-language-model-with-api.md).
* **Wyliczenie prelegenta**: Mapy i rozumie, który mówca mówił, które słowa i kiedy.
* **Statystyki prelegentów**: Zawiera statystyki dotyczące wskaźników mowy prelegentów.
* **Moderowanie zawartości tekstowej**: wykrywa jawny tekst w transkrypcji audio.
* **Efekty dźwiękowe:** Identyfikuje efekty dźwiękowe, takie jak klaszcze dłoni, mowa i cisza.
* **Wykrywanie emocji**: Identyfikuje emocje na podstawie mowy (co się mówi) i tonalności głosu (jak to się mówi). Emocją może być radość, smutek, gniew lub strach.
* **Tłumaczenie**: tworzy tłumaczenia transkrypcji audio na 54 języki.

### <a name="audio-and-video-insights-multi-channels"></a>Wgląd audio i wideo (wielokanasowy)

Podczas indeksowania przez jeden kanał, wynik częściowy dla tych modeli będą dostępne.

* **Wyodrębnianie słów kluczowych**: Wyodrębnia słowa kluczowe z mowy i tekstu wizualnego.
* **Wyodrębnianie nazwanych jednostek**: Wyodrębnia marki, lokalizacje i osoby z mowy i tekstu wizualnego za pośrednictwem przetwarzania języka naturalnego (NLP).
* **Ustalanie tematów**: ustala główne tematy na podstawie transkrypcji. W cenę zakwaterowania wliczono taksonomię IPTC drugiego poziomu.
* **Artefakty**: wyodrębnia rozbudowany zestaw artefaktów „następnego poziomu szczegółów” dla poszczególnych modeli.
* **Analiza tonacji**: rozpoznaje pozytywne, negatywne i neutralne opinie w padających wypowiedziach i tekście widocznym w nagraniu wideo.

## <a name="how-can-i-get-started-with-video-indexer"></a>Jak mogę rozpocząć pracę z video indexer?

Dostęp do funkcji indeksatora wideo można uzyskać na trzy sposoby:

* Portal indeksatora wideo: łatwe w użyciu rozwiązanie, które pozwala ocenić produkt, zarządzać kontem i dostosować modele.

    Aby uzyskać więcej informacji o portalu, zobacz [Wprowadzenie do witryny indeksatora wideo](video-indexer-get-started.md)w witrynie sieci Web .  

* Integracja interfejsu API: wszystkie możliwości indeksatora wideo są dostępne za pośrednictwem interfejsu API REST, który umożliwia zintegrowanie rozwiązania z aplikacjami i infrastrukturą.

    Aby rozpocząć pracę jako deweloper, zobacz [Korzystanie z interfejsu API REST indeksatora wideo](video-indexer-use-apis.md).

* Osadzany widżet: umożliwia osadzenie w aplikacji szczegółowych informacji indeksatora wideo, odtwarzacza i edytora.

    Aby uzyskać więcej informacji, zobacz [Osadzanie widżetów wizualnych w aplikacji](video-indexer-embed-widgets.md).

Jeśli korzystasz z witryny sieci Web, szczegółowe informacje są dodawane jako metadane i są widoczne w portalu. Jeśli używasz interfejsów API, szczegółowe informacje są dostępne jako plik JSON.

## <a name="next-steps"></a>Następne kroki

Możesz już rozpocząć pracę z usługą Video Indexer. Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Zacznij łączec z witryną video indexer](video-indexer-get-started.md).
- [Przetwarzaj zawartość za pomocą interfejsu API REST programu Video Indexer](video-indexer-use-apis.md).
- [Osadzanie widżetów wizualnych w aplikacji](video-indexer-embed-widgets.md).

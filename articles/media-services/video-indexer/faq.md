---
title: Często zadawane pytania dotyczące indeksatora wideo — Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące indeksatora wideo usługi Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: dc57978dd881532cab59150dec921df9ffa958c3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767197"
---
# <a name="video-indexer-frequently-asked-questions"></a>Indeksator wideo często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące indeksatora wideo.

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Indeksator wideo to usługa sztucznej inteligencji, która jest częścią usługi Microsoft Azure Media Services. Indeksator wideo zapewnia aranżację wielu modeli uczenia maszynowego, które umożliwiają łatwe wyodrębnianie głębokiego wglądu z wideo. Aby zapewnić zaawansowane i dokładne informacje, Video Indexer korzysta z wielu kanałów wideo: audio, mowy i wizualne. Statystyki indeksatora wideo mogą być używane na wiele sposobów, na przykład na przykład na poprawę wykrywalności i dostępności treści, tworzenie nowych możliwości zarabiania lub tworzenie nowych środowisk korzystających z tych statystyk. WideoIndyestrator udostępnia interfejs internetowy do testowania, konfiguracji i dostosowywania modeli na koncie. Deweloperzy mogą używać interfejsu API opartego na rest do integracji indeksatora wideo z systemem produkcyjnym. 

### <a name="what-can-i-do-with-video-indexer"></a>Co mogę zrobić z video indexerem?

Niektóre operacje, które indeksator wideo można wykonać na pliki multimedialne obejmują:

* Identyfikowanie i wyodrębnianie mowy oraz identyfikowanie mówców.
* Identyfikowanie i wyodrębnianie tekstu na ekranie w filmie.
* Wykrywanie obiektów w pliku wideo.
* Identyfikowanie marek (na przykład: Microsoft) na podstawie ścieżek dźwiękowych i tekstu na ekranie w filmie.
* Wykrywanie i rozpoznawanie twarzy z bazy danych gwiazd i zdefiniowanej przez użytkownika bazy danych twarzy.
* Wyodrębnianie tematów omawianych, ale niekoniecznie wymienionych w treściach audio i wideo.
* Tworzenie napisów lub napisów z ścieżki dźwiękowej.

Aby uzyskać więcej informacji i więcej funkcji indeksatora wideo, zobacz [Omówienie](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Jak rozpocząć pracę z video indexerem?

Indeksator wideo zawiera bezpłatną ofertę próbną, która zapewnia 600 minut w interfejsie internetowym i 2400 minut za pośrednictwem interfejsu API. Możesz [zalogować się do interfejsu internetowego indeksatora wideo](https://www.videoindexer.ai/) i wypróbować go dla siebie przy użyciu dowolnej tożsamości sieci Web i bez konieczności konfigurowania subskrypcji platformy Azure. Postępuj zgodnie z [tym laboratorium łatwe wprowadzenie,](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) aby lepiej zrozumieć, jak korzystać z indeksatora wideo.

Aby indeksować filmy wideo i muchy audio na dużą skalę, można połączyć indeksator wideo z płatną subskrypcją platformy Microsoft Azure. Więcej informacji na temat cen można znaleźć na stronie [z cennikiem.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

Więcej informacji na temat rozpoczynania pracy można znaleźć w programie [Wprowadzenie](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Czy do korzystania z indeksatora wideo potrzebuję umiejętności kodowania?

Za pomocą internetowego interfejsu indeksatora wideo można oceniać, konfigurować i zarządzać kontem **bez konieczności kodowania.**  Gdy będziesz gotowy do tworzenia bardziej złożonych aplikacji, możesz użyć [interfejsu API indeksatora wideo,](https://api-portal.videoindexer.ai/) aby zintegrować indeksator wideo z własnymi aplikacjami, witrynami sieci Web lub [niestandardowymi przepływami pracy przy użyciu technologii bezserwerowych, takich jak usługi Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) lub Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Czy do korzystania z indeksatora wideo potrzebuję umiejętności uczenia maszynowego?

Nie, indeksator wideo zapewnia integrację wielu modeli uczenia maszynowego w jednym potoku. Indeksowanie pliku wideo lub audio za pośrednictwem wideoindyferatora pobiera pełny zestaw szczegółowych informacji wyodrębnionych na jednej udostępnionej osi czasu bez żadnych umiejętności uczenia maszynowego lub wiedzy na temat algorytmów potrzebnych ze strony klienta.

### <a name="what-media-formats-does-video-indexer-support"></a>Jakie formaty multimediów obsługuje indeksator wideo?

Indeksator wideo obsługuje większość popularnych formatów multimediów. Aby uzyskać więcej informacji, zapoznaj się z listą [standardowych formatów usługi Azure Media Encoder.](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats)

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Jak przesłać plik multimedialny do indeksatora wideo i jakie są ograniczenia?

W portalu internetowym indeksatora wideo można przekazać plik multimedialny za pomocą okna dialogowego przekazywania plików lub wskazując adres URL, który bezpośrednio hostuje plik źródłowy (patrz [przykład](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Każdy adres URL, który obsługuje zawartość multimedialną przy użyciu elementu iFrame lub kodu osadzania, nie będzie działać (patrz [przykład).](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11) 

Aby uzyskać więcej informacji, zobacz ten [poradnik](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

#### <a name="limitations"></a>Ograniczenia

* Nazwa filmu nie może przekraczać 80 znaków.
* Jeśli prześlesz film za pomocą tablicy bajtów, rozmiar wideo jest ograniczony do 2 GB (i 30 GB w przypadku korzystania z adresu URL). 

Aby zapoznać się z pełną listą, zobacz [Przekazywanie zagadnień i ograniczeń](upload-index-videos.md#uploading-considerations-and-limitations).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Jak długo trwa indeksator wideo, aby wyodrębnić wnioski z mediów?

Czas potrzebny do indeksowania pliku wideo lub audio, zarówno przy użyciu interfejsu API indeksatora wideo i interfejsu internetowego indeksatora wideo, zależy od wielu parametrów, takich jak długość i jakość pliku, liczba szczegółowych informacji znalezionych w pliku, liczba [dostępnych jednostek zarezerwowanych](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) i czy [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) jest włączony, czy nie. Zalecamy uruchomienie kilku plików testowych z własną zawartością i podjęcie średniej, aby uzyskać lepszy pomysł.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Czy można tworzyć niestandardowe przepływy pracy w celu automatyzacji procesów za pomocą indeksatora wideo?

Tak, indeksator wideo można zintegrować z technologiami bezserwerowymi, takimi jak aplikacje logiki, przepływ i [usługi Azure Functions.](https://azure.microsoft.com/services/functions/) Więcej informacji na temat łączników [aplikacji logiki](https://azure.microsoft.com/services/logic-apps/) i [przepływu](https://flow.microsoft.com/en-us/) dla indeksatora wideo można [znaleźć tutaj](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). Niektóre projekty automatyzacji wykonywane przez partnerów można zobaczyć w repozytorium [próbek indeksatora](https://github.com/Azure-Samples/media-services-video-indexer) wideo.

### <a name="in-which-azure-regions-is-video-indexer-available"></a>W jakich regionach platformy Azure jest dostępny indeksator wideo?

Możesz zobaczyć, które regiony platformy Azure Indeksator wideo jest dostępny na stronie [regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Czy mogę dostosować modele indeksatora wideo dla mojego konkretnego przypadku użycia? 

Tak. W Video Indexer możesz dostosować niektóre z dostępnych modeli, aby lepiej dopasować je do swoich potrzeb. 

Na przykład nasz model Osoby obsługuje out-of-the-box 1,000,000 twarze rozpoznawania sławna, ale można również trenować go rozpoznać inne twarze, które nie są w tej bazie danych. 

Aby uzyskać szczegółowe informacje, zobacz artykuły dotyczące dostosowywania [modeli person,](customize-person-model-overview.md) [brands](customize-brands-model-overview.md)i [language.](customize-language-model-overview.md) 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Czy mogę edytować filmy w bibliotece?

Tak. Naciśnij przycisk **edytuj wideo** z ekranu biblioteki lub przycisk otwórz **w edytorze** z wyświetlacza odtwarzacza, aby przejść do karty **Projekty.** Możesz utworzyć nowy projekt i dodać więcej filmów z biblioteki, aby edytować je razem, po zakończeniu możesz renderować wideo i pobierać. 

Jeśli chcesz uzyskać szczegółowe informacje na temat nowego filmu, zindeksuj go za pomocą indeksatora wideo, aby pojawił się w bibliotece z jego spostrzeżeniami.

### <a name="what-is-the-sla-for-video-indexer"></a>Co to jest la dla indeksatora wideo?

Usługa SLA usługi Azure Media Service obejmuje indeksatora wideo i można je znaleźć na stronie [umowy SLA.](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) Umowy SLA dotyczą tylko płatnych kont indeksatora wideo i nie mają zastosowania do bezpłatnego okresu próbnego.

## <a name="privacy-questions"></a>Pytania dotyczące prywatności

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Czy pliki wideo i audio są indeksowane przez indeksator wideo?

Tak, chyba że plik zostanie usunięty z indeksatora wideo, za pomocą witryny wideo indeksatora lub interfejsu API, pliki wideo i audio są przechowywane. W przypadku bezpłatnej wersji próbnej pliki wideo i audio, które zostały indeksowane, są przechowywane w regionie Azure we wschodnich stanach USA. W przeciwnym razie pliki wideo i audio są przechowywane na koncie magazynu subskrypcji platformy Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Czy mogę usunąć pliki przechowywane w portalu indeksatora wideo?

Tak, zawsze możesz usunąć pliki wideo i audio, a także wszelkie metadane i szczegółowe informacje wyodrębnione z nich przez indeksator wideo. Po usunięciu pliku z video indexer, plik i jego metadane i szczegółowe informacje są trwale usuwane z video indexer. Jeśli jednak zaimplementowano własne rozwiązanie do tworzenia kopii zapasowych w usłudze Azure Storage, plik pozostanie w magazynie platformy Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Czy mogę kontrolować dostęp użytkownika do mojego konta video indexer?

Tak, tylko administratorzy kont mogą zapraszać i zapraszać osoby do swoich kont, a także przypisywać, kto ma uprawnienia do edycji i kto ma dostęp tylko do odczytu.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Kto ma dostęp do moich plików wideo i audio, które zostały zindeksowane i/lub przechowywane przez indeksator wideo oraz wyodrębnione metadane i szczegółowe informacje?

Do treści wideo lub audio, które mają publicznie dostęp do ustawień prywatności, dostęp mają każdy, kto ma link do treści wideo lub audio oraz jej spostrzeżeń. Treści wideo lub audio, które mają prywatność jako ustawienie prywatności, są dostępne tylko dla użytkowników, którzy zostali zaproszeni na konto zawartości wideo lub audio. Ustawienie prywatności zawartości ma również zastosowanie do metadanych i szczegółowych informacji wyodrębnianych przez indeksator wideo. Ustawienie prywatności należy przypisać podczas przesyłania pliku wideo lub audio. Po indeksowaniu można również zmienić ustawienie prywatności.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Jaki dostęp ma firma Microsoft do moich plików wideo lub audio, które zostały zindeksowane i/lub przechowywane przez indeksator wideo oraz wyodrębnione metadane i szczegółowe informacje?

Zgodnie z [warunkami dotyczącymi usług online platformy Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) użytkownik jest całkowicie właścicielem zawartości, a firma Microsoft będzie uzyskiwać dostęp tylko do zawartości oraz do metadanych i szczegółowych informacji, które indeksator wideo wyodrębnia z zawartości zgodnie z OST i Zasadami zachowania poufności informacji firmy Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Czy modele niestandardowe, które budujemy na moim koncie indeksatora wideo, są dostępne dla innych kont?

 Nie, modele niestandardowe utworzone na koncie nie są dostępne dla żadnego innego konta. Video Indexer obecnie pozwala na tworzenie niestandardowych [marek,](customize-brands-model-overview.md) [język](customize-language-model-overview.md)i [modele osób](customize-person-model-overview.md) na koncie. Te modele są dostępne tylko na koncie, na którym utworzono modele.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Czy zawartość indeksowana przez indeksator wideo jest przechowywana w regionie platformy Azure, w którym używam indeksatora wideo?

Tak, zawartość i jej szczegółowe informacje są przechowywane w regionie platformy Azure, chyba że masz ręczną konfigurację w subskrypcji platformy Azure, która używa wielu regionów platformy Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Jaka jest polityka prywatności dla Video Indexer?

Indeksator wideo jest objęty [Zasadami zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement). W zasadach zachowania poufności informacji wyjaśniono dane osobowe przetwarzane przez firmę Microsoft, sposób ich przetwarzania i w jakich celach firma Microsoft przetwarza. Aby dowiedzieć się więcej o prywatności, odwiedź [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Jakie certyfikaty posiada Video Indexer?

Video Indexer posiada obecnie certyfikat SOC. Aby zapoznać się z certyfikatem indeksatora wideo, zapoznaj się z [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Jaka jest różnica między filmami prywatnymi a publicznymi? 

Gdy filmy są przesyłane do indeksatora wideo, możesz wybrać jeden z dwóch ustawień prywatności: prywatny i publiczny. Publiczne filmy są dostępne dla każdego, w tym anonimowych i niezidentyfikowanych użytkowników. Osoby prywatne są ograniczone wyłącznie do członków konta. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Próbowałem przesłać film jako publiczny i został on oznaczony za nieodpowiednie lub obraźliwe treści, co to znaczy? 

Podczas przesyłania wideo do video Indexer, automatyczna analiza zawartości jest wykonywana przez algorytmy i modele, aby upewnić się, że nie nieodpowiednie treści będą prezentowane publicznie. Jeśli film okaże się podejrzany jako zawierający treści jawne, nie będzie można ustawić go jako publicznego. Jednak członkowie konta nadal mogą uzyskiwać do niego dostęp jako prywatny film wideo (wyświetlaj go, pobieraj szczegółowe informacje i wyodrębniane artefakty i wykonuj inne operacje dostępne dla członków konta).   

Aby ustawić film do publicznego dostępu, można: 

* Skompiluj własną warstwę interfejsu (na przykład aplikację lub witrynę sieci Web) i użyj jej do interakcji z usługą indeksatora wideo. W ten sposób film pozostaje prywatny w naszym portalu, a twoi użytkownicy mogą z nim wchodzić w interakcje za pośrednictwem interfejsu. Na przykład nadal można uzyskać szczegółowe informacje lub zezwolić na wyświetlanie wideo we własnym interfejsie. 
* Poproś o przegląd zawartości przez człowieka, co spowodowałoby usunięcie ograniczenia przy założeniu, że zawartość nie jest jawna. 

    Tę opcję można zbadać, jeśli witryna sieci Web indeksatora wideo jest używana bezpośrednio przez użytkowników jako warstwa interfejsu, oraz do publicznego (nieuwierzyfowanego) wyświetlania. 

## <a name="api-questions"></a>Pytania dotyczące interfejsu API

### <a name="what-apis-does-video-indexer-offer"></a>Jakie interfejsy API oferuje indeksator wideo?

Interfejsy API indeksatora wideo umożliwiają indeksowanie, wyodrębnianie metadanych, zarządzanie zasobami, tłumaczenie, osadzanie, dostosowywanie modeli i inne. Aby uzyskać bardziej szczegółowe informacje na temat korzystania z interfejsu API indeksatora wideo, zapoznaj się z [portalem deweloperskim indeksatora wideo](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Jakie sdeksy klientów oferuje Video Indexer?

Obecnie nie są oferowane żadne sdk klienta. Zespół video indexer pracuje nad SDK i planuje dostarczyć je wkrótce.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Jak rozpocząć pracę z interfejsem API indeksatora wideo?

Postępuj [zgodnie z samouczkiem: rozpocznij pracę z interfejsem API indeksatora wideo](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Jaka jest różnica między interfejsem API indeksatora wideo a interfejsem API usługi Azure Media Service w wersji 3?

Obecnie istnieją pewne nakładanie się funkcji oferowanych przez interfejs API indeksatora wideo i interfejsu API usługi Azure Media Service w wersji 3. Więcej informacji na temat porównywania obu usług można znaleźć [tutaj.](compare-video-indexer-with-media-services-presets.md)

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Co to jest token dostępu do interfejsu API i dlaczego jest potrzebny?

Interfejs API indeksatora wideo zawiera interfejs API autoryzacji i interfejs API operacji. Interfejs API autoryzacji zawiera wywołania, które dają token dostępu. Każde wywołanie interfejsu API operacji powinno być skojarzone z tokenem dostępu zgodnym z zakresem autoryzacji wywołania.

Tokeny dostępu są potrzebne do używania interfejsów API indeksatora wideo do celów zabezpieczeń. Dzięki temu wszelkie połączenia są nawiązywać od Ciebie lub tych, którzy mają uprawnienia dostępu do Twojego konta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaka jest różnica między tokenem dostępu do konta, tokenem dostępu użytkownika i tokenem dostępu do wideo?

* Poziom konta — tokeny dostępu poziomu konta umożliwiają wykonywanie operacji na poziomie konta lub pliku wideo. Na przykład prześlij film, wystaw wszystkie filmy i uzyskaj wgląd w film.
* Poziom użytkownika — tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na poziomie użytkownika. Na przykład uzyskiwanie informacji o skojarzonych kontach.
* Poziom wideo — tokeny dostępu poziomu wideo umożliwiają wykonywanie operacji na określonym pliku wideo. Na przykład pobranie szczegółowych informacji o wideo, pobranie napisów, pobranie widżetów itp.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak często muszę uzyskać nowy token dostępu? Kiedy tokeny dostępu tracą ważność?

Tokeny dostępu wygasają co godzinę, więc musisz wygenerować nowy token dostępu co godzinę. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Jakie są opcje logowania do portalu dewelopera indeksatora wideo?

Możesz zalogować się za pomocą usługi Azure AD, konta Microsoft, konta Google lub konta na Facebooku. 

Po zarejestrowaniu konta e-mail przy użyciu dostawcy tożsamości nie można używać tego konta e-mail u innego dostawcy tożsamości.

## <a name="billing-questions"></a>Pytania dotyczące rozliczeń

### <a name="how-much-does-video-indexer-cost"></a>Ile kosztuje indeksator wideo?

Indeksator wideo używa prostego modelu cenowego płatności zgodnie z rzeczywistym użyciem na podstawie czasu trwania wprowadzania zawartości, które indeksujesz. Za kodowanie, przesyłanie strumieniowe, przechowywanie, korzystanie z sieci i jednostki zarezerwowane dla nośników mogą obowiązywać dodatkowe opłaty. Aby uzyskać więcej informacji, zobacz stronę [z cennikiem.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

### <a name="when-am-i-billed-for-using-video-indexer"></a>Kiedy naliczane są naliczane płatności za korzystanie z indeksatora wideo?

Podczas wysyłania wideo do indeksowania, użytkownik zdefiniuje indeksowanie do analizy wideo, analizy audio lub obu. Określi to, które jednostki SKU zostaną obciążone. Jeśli podczas przetwarzania występuje błąd poziomu krytycznego, zostanie zwrócony kod błędu jako odpowiedź. W takim przypadku nie ma żadnych rozliczeń.  Błąd krytyczny może być spowodowany przez błąd w naszym kodzie lub błąd krytyczny w zależności wewnętrznej usługi. Błędy, takie jak nieprawidłowa identyfikacja lub wyodrębnianie wglądu nie są uważane za krytyczne i zwracana jest odpowiedź. W każdym przypadku, gdy zwracana jest prawidłowa (kod błędu) odpowiedź, następuje rozliczenie.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Czy Video Indexer oferuje bezpłatną wersję próbną?

Tak, Video Indexer oferuje bezpłatną wersję próbną, która zapewnia pełną obsługę i funkcjonalność interfejsu API. Istnieje przydział 600 minut wartości wideo dla użytkowników interfejsu internetowego i 2400 minut dla użytkowników interfejsu API. 

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)

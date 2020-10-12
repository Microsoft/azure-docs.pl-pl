---
title: Często zadawane pytania dotyczące Video Indexer — Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Video Indexer Azure Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: d533735af340b23e8a79b6c5953df748a548609c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87046968"
---
# <a name="video-indexer-frequently-asked-questions"></a>Video Indexer często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Video Indexer.

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Video Indexer jest sztuczną usługą analizy, która jest częścią Microsoft Azure Media Services. Video Indexer zapewnia aranżację wielu modeli uczenia maszynowego, które umożliwiają łatwe wyodrębnienie szczegółowego wglądu w dane wideo. Aby zapewnić zaawansowane i dokładne informacje, Video Indexer wykorzystuje wiele kanałów wideo: audio, mowy i wizualizacji. Szczegółowe informacje na temat Video Indexer mogą być używane na wiele sposobów, takich jak ulepszanie możliwości odnajdywania zawartości i ułatwień dostępu, tworzenie nowych zyskówych szans i tworzenie nowych środowisk korzystających ze szczegółowych informacji. Video Indexer udostępnia interfejs oparty na sieci Web służący do testowania, konfigurowania i dostosowywania modeli na Twoim koncie. Deweloperzy mogą korzystać z interfejsu API opartego na protokole REST, aby integrować Video Indexer w systemie produkcyjnym. 

### <a name="what-can-i-do-with-video-indexer"></a>Co mogę zrobić z Video Indexer?

Niektóre operacje, które Video Indexer mogą wykonywać na plikach multimedialnych, to m.in.:

* Identyfikowanie i wyodrębnianie mowy i identyfikowanie głośników.
* Identyfikowanie i Wyodrębnianie tekstu na ekranie wideo.
* Wykrywanie obiektów w pliku wideo.
* Zidentyfikuj znaki marki (na przykład Microsoft) z ścieżek audio i tekstu na ekranie wideo.
* Wykrywanie i rozpoznawanie twarzy z bazy danych osobistości i zdefiniowanej przez użytkownika bazy danych twarzy.
* Wyodrębnianie tematów omówionych, ale niekoniecznie wymienionych w zawartości audio i wideo.
* Tworzenie podpisów kodowanych lub napisów z ścieżki audio.

Aby uzyskać więcej informacji i dowiedzieć się więcej o Video Indexer funkcjach, zobacz [Omówienie](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Jak mogę rozpocząć pracę z usługą Video Indexer?

Video Indexer obejmuje bezpłatną ofertę próbną, która zapewnia 600 minut w interfejsie internetowym i 2 400 minut za pośrednictwem interfejsu API. Możesz [zalogować się do interfejsu opartego na sieci web video Indexer](https://www.videoindexer.ai/) i wypróbować go samodzielnie przy użyciu dowolnej tożsamości sieci Web i bez konieczności konfigurowania subskrypcji platformy Azure. Skorzystaj z [tego prostego laboratorium wprowadzającego](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) , aby lepiej zrozumieć sposób korzystania z video Indexer.

Aby zindeksować pliki wideo i audio na dużą skalę, możesz połączyć Video Indexer z subskrypcją płatną Microsoft Azure. Więcej informacji na temat cennika można znaleźć na stronie [cennika](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

Więcej informacji na temat rozpoczynania pracy można znaleźć w [temacie Rozpoczynanie pracy.](video-indexer-get-started.md)

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Czy muszę mieć umiejętność kodowania, aby korzystać z Video Indexer?

Za pomocą interfejsu opartego na sieci Web Video Indexer można oszacować i skonfigurować konto oraz zarządzać nim **bez konieczności kodowania**.  Gdy wszystko jest gotowe do opracowania bardziej złożonych aplikacji, można użyć [interfejsu API Video Indexer](https://api-portal.videoindexer.ai/) , aby zintegrować Video Indexer z własnymi aplikacjami, witrynami sieci Web lub [niestandardowymi przepływami pracy przy użyciu technologii bezserwerowych, takich jak Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) lub Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Czy muszę mieć umiejętność uczenia maszynowego, aby korzystać z Video Indexer?

Nie, Video Indexer zapewnia integrację wielu modeli uczenia maszynowego w jednym potoku. Indeksowanie pliku wideo lub audio za pośrednictwem Video Indexer umożliwia pobranie pełnego zestawu szczegółowych informacji na jednej wspólnej osi czasu bez znajomości umiejętności uczenia maszynowego ani wiedzy na temat algorytmów potrzebnych w części klienta.

### <a name="what-media-formats-does-video-indexer-support"></a>Jakie formaty multimediów Video Indexer obsługiwać?

Video Indexer obsługuje najpopularniejsze formaty multimediów. Aby uzyskać więcej informacji, zapoznaj się z listą [formatów standardowych Azure Media Encoder](../latest/media-encoder-standard-formats.md) .

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Jak mogę przekazać plik multimedialny do Video Indexer i jakie są ograniczenia?

W portalu opartym na sieci Web Video Indexer można przekazać plik multimedialny przy użyciu okna dialogowego przekazywania plików lub wskazując adres URL, który bezpośrednio hostuje plik źródłowy (zobacz [przykład](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Wszystkie adresy URL, które obsługują zawartość multimedialną przy użyciu elementu iFrame lub kodu osadzania, nie będą działały (zobacz [przykład](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). 

Aby uzyskać więcej informacji, zobacz ten [Przewodnik](./upload-index-videos.md).

#### <a name="limitations"></a>Ograniczenia

* Nazwa filmu wideo nie może być dłuższa niż 80 znaków.
* Jeśli przekażesz wideo przy użyciu tablicy bajtowej, rozmiar filmu wideo jest ograniczony do 2 GB (i 30 GB przy użyciu adresu URL). 

Aby uzyskać pełną listę, zobacz temat [przekazywanie zagadnień i ograniczeń](upload-index-videos.md#uploading-considerations-and-limitations).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Jak długo trwa Video Indexer w celu wyodrębnienia szczegółowych informacji z nośnika?

Czas potrzebny na indeksowanie pliku wideo lub audio, zarówno przy użyciu interfejsu API Video Indexer, jak i interfejsu opartego na sieci Web Video Indexer, zależy od wielu parametrów, takich jak długość i jakość pliku, liczba szczegółowych informacji znalezionych w pliku, liczba dostępnych [jednostek zarezerwowanych](../previous/media-services-scale-media-processing-overview.md) oraz czy [punkt końcowy przesyłania strumieniowego](../previous/media-services-streaming-endpoints-overview.md) jest włączony. Zalecamy uruchomienie kilku plików testowych przy użyciu własnej zawartości i wypróbowanie lepszego pomysłu.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Czy można tworzyć niestandardowe przepływy pracy do automatyzowania procesów z Video Indexer?

Tak, możesz zintegrować Video Indexer z technologiami bezserwerowymi, takimi jak Logic Apps, Flow i [Azure Functions](https://azure.microsoft.com/services/functions/). Więcej szczegółów na temat [aplikacji logiki](https://azure.microsoft.com/services/logic-apps/) i łączników [przepływu](https://flow.microsoft.com/en-us/) dla Video Indexer można znaleźć [tutaj](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). Niektóre projekty automatyzacji wykonywane przez partnerów można zobaczyć w repozytorium [Video Indexer przykładów](https://github.com/Azure-Samples/media-services-video-indexer) .

### <a name="in-which-azure-regions-is-video-indexer-available"></a>W jakich regionach platformy Azure są dostępne indeksatory wideo?

Możesz sprawdzić, które regiony platformy Azure Video Indexer są dostępne na stronie [regiony](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) .

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Czy mogę dostosować modele Video Indexer dla określonego przypadku użycia? 

Tak. W Video Indexer można dostosować niektóre dostępne modele, aby lepiej odpowiadały potrzebom. 

Na przykład nasz model osoby obsługuje gotowe do użycia 1 000 000 twarzy osobistości, ale można również przeszkolić go do rozpoznawania innych twarzy, które nie znajdują się w tej bazie danych. 

Aby uzyskać szczegółowe informacje, zobacz artykuł dotyczący dostosowywania [osób](customize-person-model-overview.md), [marek](customize-brands-model-overview.md)i modeli [językowych](customize-language-model-overview.md) . 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Czy mogę edytować filmy wideo w mojej bibliotece?

Tak. Naciśnij przycisk **Edytuj wideo** z poziomu ekranu biblioteki lub **Otwórz w edytorze** na ekranie odtwarzacza, aby przejść do karty **projekty** . Możesz utworzyć nowy projekt i dodać więcej filmów wideo z biblioteki, aby edytować je razem, po zakończeniu możesz renderować wideo i pobrać. 

Jeśli chcesz uzyskać wgląd w nowe wideo, Indeksuj go za pomocą Video Indexer i pojawi się w bibliotece wraz ze szczegółowymi informacjami.

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>Czy można indeksować wiele strumieni lub kanałów audio?

Jeśli istnieje wiele strumieni audio, Video Indexer przyjmuje pierwsze napotkane i przetworzy tylko ten strumień. W przypadku dowolnego Video Indexer procesów strumieni audio pobiera różne kanały (jeśli istnieją) i przetwarza je ze sobą jako mono. W przypadku strumieni/manipulacji kanałów można użyć poleceń narzędzia FFmpeg w pliku przed jego indeksowaniem.

### <a name="what-is-the-sla-for-video-indexer"></a>Co to jest umowa SLA dla Video Indexer?

Umowa SLA usługi Azure Media Service obejmuje Video Indexer i można ją znaleźć na stronie [umów SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) . Umowa SLA dotyczy tylko Video Indexer płatnych kont i nie dotyczy bezpłatnej wersji próbnej.

## <a name="privacy-questions"></a>Pytania dotyczące prywatności

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Czy pliki wideo i audio są indeksowane przez Video Indexer zapisane?

Tak, chyba że usuniesz plik z Video Indexer przy użyciu witryny sieci Web lub interfejsu API Video Indexer, pliki wideo i audio są przechowywane. W przypadku bezpłatnej wersji próbnej pliki wideo i audio, które są indeksowane, są przechowywane w regionie platformy Azure Wschodnie stany USA. W przeciwnym razie pliki wideo i audio są przechowywane na koncie magazynu subskrypcji platformy Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Czy mogę usunąć pliki, które są przechowywane w portalu Video Indexer?

Tak. możesz zawsze usuwać pliki wideo i audio, a także wszystkie metadane i szczegółowe informacje wyodrębnione z nich przez Video Indexer. Po usunięciu pliku z Video Indexer plik oraz jego metadane i szczegółowe dane zostaną trwale usunięte z Video Indexer. Jeśli jednak wdrożono własne rozwiązanie do tworzenia kopii zapasowych w usłudze Azure Storage, plik pozostanie w usłudze Azure Storage.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Czy mogę kontrolować dostęp użytkowników do konta Video Indexer?

Tak. tylko Administratorzy konta mogą zapraszać i zapraszać osoby do swoich kont, a także przypisywać osoby, które mają uprawnienia do odczytu.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Kto ma dostęp do moich plików wideo i audio, które są indeksowane i/lub przechowywane przez Video Indexer oraz zaekstrahowanych metadanych i szczegółowych informacji?

Dostęp do zawartości wideo lub audio, która ma być publiczna jako ustawienia prywatności, jest dostępny dla każdego, kto ma link do zawartości wideo lub audio i jego szczegółowych informacji. Dostęp do zawartości wideo lub audio, która ma charakter prywatny jako ustawienia prywatności, jest możliwy tylko dla użytkowników, którzy zostali zaproszeni na konto zawartości wideo lub audio. Ustawienie prywatności zawartości dotyczy również metadanych i szczegółowych informacji, które Video Indexer wyodrębniania. Ustawienie prywatności należy przypisać podczas przekazywania pliku wideo lub audio. Można również zmienić ustawienie prywatności po indeksie.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Jaki dostęp ma firma Microsoft do moich plików wideo lub audio, które są indeksowane i/lub przechowywane przez Video Indexer, a także metadane i szczegółowe informacje, które zostały wyodrębnione?

Zgodnie z [postanowieniami dotyczącymi usług Azure Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (ost) masz kompletną zawartość, a firma Microsoft będzie uzyskiwać dostęp do zawartości i metadanych oraz szczegółowych informacji, które Video Indexer wyodrębniane z zawartości według pliku ost i zasad zachowania poufności informacji firmy Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Czy modele niestandardowe, które zostały skompilowane w ramach konta Video Indexer dostępne dla innych kont?

 Nie, modele niestandardowe tworzone na koncie nie są dostępne dla żadnego innego konta. Video Indexer obecnie umożliwia tworzenie niestandardowych [marek](customize-brands-model-overview.md), [języków](customize-language-model-overview.md)i modeli [osób](customize-person-model-overview.md) na koncie. Te modele są dostępne tylko na koncie, w którym zostały utworzone modele.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Czy zawartość jest indeksowana przez Video Indexer w regionie świadczenia usługi Azure, w którym używam Video Indexer?

Tak, zawartość i jej szczegółowe informacje są przechowywane w regionie świadczenia usługi Azure, chyba że masz ręczną konfigurację w ramach subskrypcji platformy Azure korzystającej z wielu regionów świadczenia usługi Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Jakie są zasady zachowania poufności informacji dla Video Indexer?

Video Indexer podlega [zasadom zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement). W zasadach zachowania poufności informacji są wyjaśnione dane osobowe dotyczące procesów firmy Microsoft, sposobu ich przetwarzania i działania przez firmę Microsoft. Aby dowiedzieć się więcej o ochronie prywatności, odwiedź [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Jakie certyfikaty ma Video Indexer?

Video Indexer obecnie ma certyfikat SOC. Aby zapoznać się z certyfikatem Video Indexer, zapoznaj się z [Centrum zaufania Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Jaka jest różnica między prywatnym i publicznym wideo? 

Po przekazaniu wideo do Video Indexer można wybrać jedną z dwóch ustawień prywatności: prywatne i publiczne. Publiczne wideo są dostępne dla każdego, w tym anonimowe i niezidentyfikowane użytkownicy. Prywatne są ograniczone wyłącznie do członków konta. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Próbuję przekazać wideo jako publiczne i były oflagowane dla nieodpowiedniej lub obraźliwej zawartości, co oznacza? 

Podczas przekazywania wideo do Video Indexer, Automatyczna analiza zawartości jest wykonywana przez algorytmy i modele, aby upewnić się, że żadna nieodpowiednia zawartość nie zostanie ujawniona publicznie. Jeśli film wideo będzie podejrzany o podejrzenie, że zawiera jawną zawartość, nie będzie można ustawić go jako publiczny. Jednak członkowie konta mogą nadal uzyskać do niego dostęp jako prywatny film wideo (wyświetlić go, pobrać informacje i wyodrębnione artefakty oraz wykonać inne operacje dostępne dla członków konta).   

Aby ustawić wideo dla dostępu publicznego, można wykonać jedną z: 

* Utwórz własną warstwę interfejsu (na przykład aplikację lub witrynę sieci Web) i użyj jej do współpracy z usługą Video Indexer. W ten sposób film wideo pozostaje prywatny w naszym portalu, a użytkownicy mogą korzystać z niego za pomocą interfejsu. Możesz na przykład uzyskać szczegółowe informacje lub zezwolić na wyświetlanie wideo w Twoim interfejsie. 
* Zażądaj humanitarnego przeglądu zawartości, co spowoduje usunięcie ograniczenia, przy założeniu, że zawartość nie jest jawna. 

    Ta opcja może zostać zbadana, jeśli witryna sieci Web Video Indexer jest używana bezpośrednio przez użytkowników jako warstwa interfejsu i dla publicznego (nieuwierzytelnionego) wyświetlania. 

## <a name="api-questions"></a>Pytania dotyczące interfejsu API

### <a name="what-apis-does-video-indexer-offer"></a>Jakie interfejsy API oferuje Video Indexer?

Interfejsy API Video Indexer umożliwiają indeksowanie, wyodrębnianie metadanych, zarządzanie zasobami, tłumaczenie, osadzanie, Dostosowywanie modeli i nie tylko. Aby uzyskać bardziej szczegółowe informacje na temat korzystania z interfejsu API Video Indexer, zapoznaj się z tematem [Video Indexer Portal dla deweloperów](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Jakie zestawy SDK klienta Video Indexer oferować?

Obecnie nie ma dostępnych zestawów SDK klienta. Zespół Video Indexer pracuje nad zestawami SDK i planami w celu ich uzyskania wkrótce.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Jak mogę rozpocząć pracę z interfejsem API Video Indexer?

[Samouczek: Rozpoczynanie pracy z interfejsem API Video Indexer](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Jaka jest różnica między interfejsem API Video Indexer i interfejsem API usługi Azure Media Service v3?

Obecnie istnieją pewne nakładające się funkcje interfejsu API Video Indexer i interfejsu API usługi Azure Media Service v3. Więcej informacji na temat sposobu porównywania obu usług można znaleźć [tutaj](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Co to jest token dostępu interfejsu API i dlaczego jest potrzebny?

Interfejs API Video Indexer zawiera interfejs API autoryzacji i interfejs API operacji. Interfejs API autoryzacji zawiera wywołania, które dają dostęp do tokenu dostępu. Każde wywołanie interfejsu API operacji powinno być skojarzone z tokenem dostępu zgodnym z zakresem autoryzacji wywołania.

Tokeny dostępu są konieczne do używania interfejsów API Video Indexer ze względów bezpieczeństwa. Daje to pewność, że wszystkie wywołania pochodzą z użytkownika lub osoby, które mają uprawnienia dostępu do Twojego konta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaka jest różnica między tokenem dostępu do konta, tokenem dostępu użytkowników i tokenem dostępu wideo?

* Poziom konta — tokeny dostępu poziomu konta umożliwiają wykonywanie operacji na poziomie konta lub pliku wideo. Na przykład Przekaż wideo, Wyświetl listę wszystkich filmów wideo i uzyskaj wgląd w dane wideo.
* Tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na poziomie użytkownika. Na przykład uzyskiwanie informacji o skojarzonych kontach.
* Poziom wideo — tokeny dostępu poziomu wideo umożliwiają wykonywanie operacji na określonym pliku wideo. Na przykład pobranie szczegółowych informacji o wideo, pobranie napisów, pobranie widżetów itp.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak często należy uzyskać nowy token dostępu? Kiedy tokeny dostępu wygasają?

Tokeny dostępu wygasają co godzinę, więc musisz wygenerować nowy token dostępu co godzinę. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Jakie są opcje logowania do Video Indexer portalu dla deweloperów?

Możesz zalogować się przy użyciu usługi Azure AD, konto Microsoft, konta Google lub konta w serwisie Facebook. 

Po zarejestrowaniu konta e-mail przy użyciu dostawcy tożsamości nie można użyć tego konta e-mail z innym dostawcą tożsamości.

## <a name="billing-questions"></a>Pytania dotyczące rozliczeń

### <a name="how-much-does-video-indexer-cost"></a>Ile kosztuje Video Indexer?

Video Indexer korzysta z prostego modelu cen z płatność zgodnie z rzeczywistym użyciem na podstawie czasu trwania indeksu zawartości wprowadzonej przez użytkownika. Dodatkowe opłaty mogą dotyczyć kodowania, przesyłania strumieniowego, magazynu, użycia sieci i jednostek zarezerwowanych multimediów. Aby uzyskać więcej informacji, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

### <a name="when-am-i-billed-for-using-video-indexer"></a>Kiedy naliczane są opłaty za korzystanie z Video Indexer?

Podczas wysyłania wideo do indeksowania, użytkownik definiuje indeksowanie do analizy wideo, analizy audio lub obu tych metod. Spowoduje to określenie, które jednostki SKU będą naliczone. Jeśli wystąpi błąd krytyczny podczas przetwarzania, kod błędu zostanie zwrócony jako odpowiedź. W takim przypadku nie odbywa się rozliczanie.  Błąd krytyczny może być spowodowany przez usterkę w naszym kodzie lub krytyczny błąd w wewnętrznej zależności, która ma usługa. Błędy, takie jak niepoprawna identyfikacja lub wyodrębnianie szczegółowych informacji, nie są uznawane za krytyczne i zwracana jest odpowiedź. W każdym przypadku, gdy zwracana jest prawidłowa odpowiedź (kod bez błędu), naliczane są rozliczenia.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Czy Video Indexer oferować bezpłatną wersję próbną?

Tak, Video Indexer oferuje bezpłatną wersję próbną, która zapewnia pełną funkcjonalność usługi i interfejsu API. Liczba filmów wideo przeznaczonych dla użytkowników interfejsu internetowego i 2 400 minut jest przydziałem 600 minut w przypadku usługi API users. 

## <a name="next-steps"></a>Następne kroki

[Omówienie](video-indexer-overview.md)

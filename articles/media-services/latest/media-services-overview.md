---
title: Azure Media Services wersja 3 — Omówienie: Azure Media Services opis: ogólne omówienie Azure Media Services v3 z linkami do przewodników Szybki Start, samouczków i przykładów kodu.
usługi: Media-Services documentationcenter: na Author: IngridAtMicrosoft Manager: femila Editor: "" Tags: "" słowa kluczowe: usługi Azure Media Services, Stream, broadcast, Live, offline

MS. Service: Media-Services MS. devlang: Multiple MS. temat: przegląd ms.tgt_pltfrm: wiele MS. obciążenie: Media MS. Date: 3/10/2021 MS. Author: inhenkel MS. Custom: MVC
#<a name="customer-intent-as-a-developer-or-a-content-provider-i-want-to-encode-stream-on-demand-or-live-analyze-my-media-content-so-that-my-customers-can-view-the-content-on-a-wide-variety-of-browsers-and-devices-gain-valuable-insights-from-recorded-content"></a>Intencja klienta: jako deweloper lub dostawca zawartości chcę kodować, przesyłać strumieniowo (na żądanie lub na żywo), analizować zawartość multimedialną, aby moi klienci mogli: wyświetlać zawartość w wielu różnych przeglądarkach i urządzeniach, uzyskiwać cenne informacje z zapisanej zawartości.
---

# <a name="azure-media-services-v3-overview"></a>Omówienie usługi Azure Media Services w wersji 3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services to oparta na chmurze platforma umożliwiająca tworzenie rozwiązań pozwalających na osiągnięcie przesyłania strumieniowego wideo w jakości odpowiedniej do emisji, zwiększenie dostępności i dystrybucji, analizowanie zawartości i wykonywanie wielu innych operacji. Bez względu na to, czy jesteś deweloperem aplikacji, działem obsługi, agencją rządową, czy firmą rozrywkową, Media Services pomaga w tworzeniu aplikacji, które zapewniają obsługę multimediów dla dużych odbiorców na współczesnych najpopularniejszych urządzeniach przenośnych i przeglądarek.

Zestawy SDK Media Services V3 są oparte na [specyfikacji Media Services v3 openapi (Swagger)](https://aka.ms/ams-v3-rest-sdk).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia

Ważną kwestią jest przestrzeganie wszystkich obowiązujących przepisów dotyczących używania Azure Media Services i nie można używać Media Services lub żadnej usługi platformy Azure w sposób naruszający prawa innych osób lub mogą być szkodliwe dla innych osób.

Przed przekazaniem dowolnych filmów wideo/obrazów do Media Services należy dysponować wszystkimi odpowiednimi prawami do używania wideo/obrazu, w tym, gdzie jest to wymagane przez prawo, do użycia, przetwarzania i przechowywania danych w usłudze Media Services i na platformie Azure. Niektóre jurysdykcje mogą nakładać specjalne wymagania prawne dotyczące zbierania, przetwarzania online i przechowywania określonych kategorii danych, takich jak dane biometryczne. Przed rozpoczęciem korzystania z Media Services i platformy Azure do przetwarzania i przechowywania danych z zastrzeżeniem szczególnych wymagań prawnych należy zapewnić zgodność z wszelkimi wymaganiami prawnymi, które mogą mieć zastosowanie do Ciebie.

Aby dowiedzieć się więcej o zgodności, prywatności i zabezpieczeniach w Media Services odwiedź [Centrum zaufania](https://www.microsoft.com/trust-center/?rtc=1)firmy Microsoft. W przypadku zobowiązań związanych z ochroną prywatności firmy Microsoft, obsługi i przechowywania danych, w tym sposobu usuwania danych, zapoznaj się z zasadami [zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, [postanowieniami dotyczącymi usług online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") i [uzupełnieniem przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Korzystając z Media Services, wyrażasz zgodę na powiązanie plików OST, DPA i zasad zachowania poufności informacji.
 
## <a name="what-can-i-do-with-media-services"></a>Co mogę zrobić za pomocą usługi Media Services?

Media Services pozwala tworzyć różne przepływy pracy multimedialnej w chmurze. Poniżej przedstawiono kilka przykładów tego, co można zrobić z Media Services:

* Dostarczaj wideo w różnych formatach, aby można je było odtworzyć w różnych przeglądarkach i na różnych urządzeniach. W przypadku przesyłania strumieniowego na żądanie i na żywo do różnych klientów (urządzenia przenośne, Telewizja, komputer i tak dalej) zawartość wideo i audio musi być odpowiednio zakodowana i opakowana. Aby poznać sposób dostarczania i strumieniowego przesyłania takiej zawartości, zobacz [Szybki start: kodowanie i przesyłanie strumieniowe plików](stream-files-dotnet-quickstart.md).
* Przesyłaj strumieniowo wydarzenia sportowe na żywo do dużych odbiorców w trybie online, takich jak piłka nożna, siatkówki, szkoły i duże szkoły, i nie tylko.
* Emitowanie publicznych spotkań i wydarzeń, takich jak korytarze miasta, spotkania Rady miasta i treści prawne.
* Analizuj nagrania wideo lub zawartość audio. Aby na przykład osiągnąć wyższe zadowolenie klientów, organizacje mogą wyodrębnić mowę jako tekst i tworzyć indeksy wyszukiwania oraz pulpity nawigacyjne. Następnie mogą oni wyodrębnić informacje dotyczące typowych skarg, źródeł skarg i inne odpowiednie dane.
* Utwórz serwis wideo subskrypcji i przesyłaj strumieniowo zawartość chronioną przez technologię DRM, gdy klient (na przykład studio filmowe) chce ograniczyć dostęp i używanie zastrzeżonych prac objętych prawami autorskimi.
* Dostarczaj zawartość offline do odtwarzania w samolotach, pociągach i samochodach. Klient może pobrać zawartość do odtworzenia na swój telefon lub tablet, jeśli przewiduje, że zostanie odłączony od sieci.
* Zaimplementuj platformę wideo edukacyjnej uczenia maszynowego za pomocą Azure Media Services i [platformy Azure interfejsy API usług Cognitive Services](../../index.yml?pivot=products&panel=ai) na potrzeby podpisania mowy i tekstu, tłumaczenia na wiele języków i tak dalej.
* Dzięki usłudze Azure Media Services oraz [interfejsom API usług Cognitive Services](../../index.yml?pivot=products&panel=ai) możesz dodawać napisy i podpisy do filmów, aby spełnić potrzeby szerszego grona odbiorców (na przykład osób z wadami słuchu lub osób chcących jednocześnie czytać w innym języku).
* Włącz usługę Azure CDN, aby osiągnąć wysoką skalowalność w celu zapewnienia lepszej obsługi błyskawicznego zwiększenia obciążenia (na przykład na początku wydarzenia prezentującego nowy produkt).

## <a name="how-can-i-get-started-with-v3"></a>Jak można zacząć korzystać z wersji 3?

Dowiedz się, jak kodować i spakować zawartość, przesyłać strumieniowo filmy wideo na żądanie, emitować na żywo i analizować filmy wideo za pomocą Media Services v3. Samouczki, dokumentacja interfejsu API i inne dokumenty przedstawiające sposób zapewnienia bezpiecznej transmisji strumieniowej materiałów wideo lub audio na żywo lub na żądanie, którą można skalować na potrzeby milionów użytkowników.

> [!TIP]
> Przed rozpoczęciem opracowywania, przejrzyj: [podstawowe koncepcje](concepts-overview.md) , w tym ważne pojęcia, takie jak pakowanie, kodowanie i ochrona, i [Programowanie przy użyciu interfejsów API Media Services v3](media-services-apis-overview.md) , które zawierają informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa i tak dalej.

### <a name="sdks"></a>Zestawy SDK

Rozpocznij programowanie przy użyciu [zestawów SDK klienta Azure Media Services v3](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Przewodniki Szybki start  

Przewodniki Szybki Start pokazują podstawowe instrukcje dotyczące pierwszego dnia dla nowych klientów, aby szybko wypróbować Media Services.

* [Przesyłanie strumieniowe plików wideo — .NET](stream-files-dotnet-quickstart.md)
* [Pliki wideo strumieniowego — interfejs wiersza polecenia](stream-files-cli-quickstart.md)
* [Przesyłanie strumieniowe wideo — Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Samouczki

W samouczkach przedstawiono procedury oparte na scenariuszu dla niektórych najważniejszych zadań Media Services.

* [Kodowanie pliku zdalnego i strumieniowe przesyłanie wideo — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumieniowe przesyłanie wideo — .NET](stream-files-tutorial-with-api.md)
* [Przesyłanie strumieniowe na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie wideo — .NET](analyze-videos-tutorial.md)
* [Szyfrowanie dynamiczne AES-128 — .NET](drm-playready-license-template-concept.md)

### <a name="samples"></a>Samples

Użyj [tej przeglądarki przykładów](/samples/browse/?products=azure-media-services) do przeglądania przykładów kodu Azure Media Services.

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

Przewodniki z poradnikami zawierają przykłady kodu, które pokazują, jak wykonać zadanie. W tej sekcji znajdziesz wiele przykładów. Oto kilka z nich:

* [Tworzenie konta — interfejs wiersza polecenia](./account-create-how-to.md)
* [Dostęp do interfejsów API — interfejs wiersza polecenia](./access-api-howto.md)
* [Koduj przy użyciu protokołu HTTPS jako dane wejściowe zadania — .NET](job-input-from-http-how-to.md)  
* [Monitorowanie zdarzeń — portal](monitoring/monitor-events-portal-how-to.md)
* [Szyfrowanie dynamiczne za pomocą technologii wielowątkowości platformy .NET](drm-protect-with-drm-tutorial.md) 
* [Jak kodować przy użyciu niestandardowego przekształcenia interfejsu wiersza polecenia](transform-custom-preset-cli-how-to.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Poznaj podstawowe koncepcje](concepts-overview.md)

---
title: Co to są Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services sprawia, że AI jest dostępne dla każdego dewelopera bez konieczności uczenia maszynowego i wiedzy z zakresu nauki o danych. Wystarczy wykonać wywołanie interfejsu API z aplikacji, aby dodać do aplikacji możliwość wyświetlania (zaawansowanego wyszukiwania i rozpoznawania obrazów), słyszenia, wypowiadania, wyszukiwania i podejmowania decyzji.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: cognitive services, cognitive intelligence, cognitive solutions, ai services, cognitive understanding, cognitive features
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: c89131cc34d45ea94f3bb290ac11ec86f4b83be3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587616"
---
# <a name="what-are-azure-cognitive-services"></a>Co to są Azure Cognitive Services?

Azure Cognitive Services to usługi oparte na chmurze z interfejsami API REST i zestawami SDK biblioteki klienta, które ułatwiają tworzenie analiz poznawczych w aplikacjach. Funkcje poznawcze można dodawać do aplikacji bez konieczności posiadania umiejętności sztucznej inteligencji (AI) ani nauki o danych. Azure Cognitive Services obejmują różne usługi AI, które umożliwiają tworzenie rozwiązań poznawczych, które mogą widzieć, słyszeć, mówić, rozumieć, a nawet podejmować decyzje.

## <a name="categories-of-cognitive-services"></a>Kategorie Cognitive Services

Katalog usług Cognitive Services, które zapewniają zrozumienie poznawcze, jest skategoryzowany na pięć głównych filarów:

* Wizja
* Mowa
* Język
* Decyzja
* Wyszukaj

Poniższe sekcje tego artykułu zawierają listę usług, które są częścią tych pięciu filarów.

## <a name="vision-apis"></a>Interfejsy API obrazów

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Przetwarzanie obrazów](./computer-vision/index.yml "Przetwarzanie obrazów")|Usługa przetwarzanie obrazów zapewnia dostęp do zaawansowanych algorytmów poznawczych do przetwarzania obrazów i zwracania informacji. Zobacz [przetwarzanie obrazów Szybki start,](./computer-vision/quickstarts-sdk/client-library.md) aby rozpocząć pracę z usługą.|
|[Custom Vision Service](./custom-vision-service/index.yml "Custom Vision Service")|Usługa Custom Vision Service umożliwia tworzenie, wdrażanie i ulepszanie własnych klasyfikatorów obrazów. Klasyfikator obrazów to usługa AI, która stosuje etykiety do obrazów na podstawie ich cech wizualnych. |
|[Rozpoznawanie twarzy](./face/index.yml "Rozpoznawanie twarzy")| Usługa rozpoznawania twarzy zapewnia dostęp do zaawansowanych algorytmów rozpoznawania twarzy, umożliwiając wykrywanie i rozpoznawanie atrybutów twarzy. Aby [rozpocząć](./face/quickstarts/client-libraries.md) pracę z usługą, zobacz Przewodnik Szybki start dla rozpoznawania twarzy.|
|[Rozpoznawanie formularzy](./form-recognizer/index.yml "Rozpoznawanie formularzy")|Rozpoznawanie formularzy identyfikuje i wyodrębnia pary klucz-wartość oraz dane tabeli z dokumentów formularzy; Następnie wyprowadza dane strukturalne, w tym relacje w oryginalnym pliku. Zobacz [Rozpoznawanie formularzy Szybki start,](./form-recognizer/quickstarts/client-library.md) aby rozpocząć pracę.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer umożliwia wyodrębnianie szczegółowych informacji z wideo. Zobacz [Video Indexer Szybki start,](/azure/media-services/video-indexer/video-indexer-get-started) aby rozpocząć pracę.|

## <a name="speech-apis"></a>Interfejsy API rozpoznawania mowy

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Usługa rozpoznawania mowy](./speech-service/index.yml "Usługa rozpoznawania mowy")|Usługa rozpoznawania mowy dodaje do aplikacji funkcje z obsługą mowy. Usługa rozpoznawania mowy oferuje różne możliwości, takie jak tłumaczenie mowy na tekst, tekst na mowę, tłumaczenie mowy i wiele innych.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Interfejsy API języka

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "Language Understanding")|Language Understanding (LUIS) to oparta na chmurze usługa konwersacyjna sztucznej inteligencji, która stosuje niestandardową inteligencję uczenia maszynowego do konwersacyjnego tekstu w języku naturalnym użytkownika w celu przewidywania ogólnego znaczenia i wyciągnięcia odpowiednich szczegółowych informacji. [Zobacz przewodnik Szybki start usługi LUIS,](./luis/get-started-portal-build-app.md) aby rozpocząć pracę z usługą.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker umożliwia tworzenie usługi pytań i odpowiedzi z częściowo ustrukturyzowanej zawartości. [Zobacz QnA Maker Szybki start,](./qnamaker/quickstarts/create-publish-knowledge-base.md) aby rozpocząć pracę z usługą.|
|[Analiza tekstu](./text-analytics/index.yml "Analiza tekstu")| analiza tekstu przetwarzanie języka naturalnego na nieprzetworzonego tekście na podstawie analizy tonacji, wyodrębniania kluczowych fraz i wykrywania języka. Zobacz [analiza tekstu Szybki start,](./text-analytics/quickstarts/client-libraries-rest-api.md) aby rozpocząć pracę z usługą.|
|[Translator](./translator/index.yml "Translator")|Translator zapewnia tłumaczenie tekstu oparte na komputerze niemal w czasie rzeczywistym.|
| [Czytnik immersyjny](./immersive-reader/index.yml "Czytnik immersyjny") | Czytnik immersyjny dodaje funkcje odczytywania i rozumienia ekranu do aplikacji. Zobacz [Czytnik immersyjny Szybki start,](./immersive-reader/quickstarts/client-libraries.md) aby rozpocząć pracę z usługą. |

## <a name="decision-apis"></a>Interfejsy API podejmowania decyzji

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Narzędzie do wykrywania anomalii](./anomaly-detector/index.yml "Narzędzie do wykrywania anomalii") |Narzędzie do wykrywania anomalii umożliwia monitorowanie i wykrywanie nieprawidłowości w danych szeregów czasu. Zobacz [Narzędzie do wykrywania anomalii Szybki start,](./anomaly-detector/quickstarts/client-libraries.md) aby rozpocząć pracę z usługą|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator monitorowanie możliwej obraźliwej, niepożądanej i ryzykownej zawartości. Zobacz [Content Moderator Szybki start,](./content-moderator/client-libraries.md) aby rozpocząć pracę z usługą.|
|[Doradca w zakresie metryk](./metrics-advisor/index.yml) (wersja zapoznawcza) | Doradca w zakresie metryk oferuje dostosowywalne wykrywanie anomalii w przypadku danych szeregów czasowych o wielu różnych możliwościach oraz w pełni funkcjonalny portal internetowy ułatwiający korzystanie z usługi. Zobacz [Doradca w zakresie metryk Szybki start,](./metrics-advisor/quickstarts/rest-api-and-client-library.md) aby rozpocząć pracę z usługą. |
|[Personalizacja](./personalizer/index.yml "Personalizacja")|Personalizacja pozwala wybrać najlepsze środowisko do pokazania użytkownikom, ucząc się na ich zachowaniach w czasie rzeczywistym. Aby [rozpocząć](./personalizer/quickstart-personalizer-sdk.md) pracę z usługą, zobacz Przewodnik Szybki start usługi Personalizacja.|

## <a name="search-apis"></a>Interfejsy API wyszukiwania

> [!NOTE]
> Szukasz [Azure Cognitive Search](../search/index.yml)? Mimo że używa Cognitive Services do wykonywania niektórych zadań, jest to inna technologia wyszukiwania, która obsługuje inne scenariusze.

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Wyszukiwanie wiadomości (Bing)](/azure/cognitive-services/bing-news-search/ "Wyszukiwanie wiadomości (Bing)")|wyszukiwanie wiadomości Bing zwraca listę artykułów z wiadomościami, które są istotne dla zapytania użytkownika.|
|[Wyszukiwanie klipów wideo w usłudze Bing](/azure/cognitive-services/Bing-Video-Search/ "Wyszukiwanie klipów wideo w usłudze Bing")|wyszukiwanie wideo Bing zwraca listę filmów wideo, które są istotne dla zapytania użytkownika.|
|[Wyszukiwanie w Internecie Bing](./bing-web-search/index.yml "Wyszukiwanie w Internecie Bing")|wyszukiwanie w sieci Web Bing zwraca listę wyników wyszukiwania określonych jako istotne dla zapytania użytkownika.|
|[Automatyczne sugerowanie Bing](/azure/cognitive-services/Bing-Autosuggest "Automatyczne sugerowanie Bing")|automatyczne sugerowanie Bing umożliwia wysyłanie częściowego terminu zapytania wyszukiwania do usługi Bing i uzyskiwanie listy sugerowanych zapytań.|
|[Wyszukiwanie niestandardowe Bing](/azure/cognitive-services/bing-custom-search "Wyszukiwanie niestandardowe Bing")|wyszukiwanie niestandardowe Bing umożliwia tworzenie dostosowanych funkcji wyszukiwania dla owanych tematów.|
|[Wyszukiwanie jednostek Bing](/azure/cognitive-services/bing-entities-search/ "Wyszukiwanie jednostek Bing")|wyszukiwanie jednostek Bing zwraca informacje o jednostkach, które usługi Bing określi jako istotne dla zapytania użytkownika.|
|[Wyszukiwanie obrazów w usłudze Bing](/azure/cognitive-services/bing-image-search "Wyszukiwanie obrazów w usłudze Bing")|wyszukiwanie obrazów Bing zwraca wyświetlanie obrazów określonych jako istotne dla zapytania użytkownika.|
|[Wyszukiwanie wizualne Bing](/azure/cognitive-services/bing-visual-search "Wyszukiwanie wizualne Bing")|wyszukiwanie wizualne Bing zwraca szczegółowe informacje o obrazie, takie jak podobne obrazy, źródła zakupów produktów znalezionych na obrazie i powiązane wyszukiwania.|
|[Wyszukiwanie lokalnych firm w usłudze Bing](/azure/cognitive-services/bing-local-business-search/ "Wyszukiwanie lokalnych firm w usłudze Bing")| Wyszukiwanie lokalnych firm Bing API umożliwia aplikacjom znajdowanie informacji kontaktowych i informacji o lokalizacji lokalnych firm na podstawie zapytań wyszukiwania.|
|[Sprawdzanie pisowni Bing](/azure/cognitive-services/bing-spell-check/ "Sprawdzanie pisowni Bing")|sprawdzanie pisowni Bing umożliwia wykonywanie kontekstowego sprawdzania pisowni i gramatyki.|

## <a name="get-started-with-cognitive-services"></a>Wprowadzenie do usług Cognitive Services

Rozpocznij od utworzenia zasobu Cognitive Services z praktycznego przewodnika Szybki start przy użyciu następujących metod:

* [Witryna Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Interfejs wiersza polecenia platformy Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "Interfejs wiersza polecenia platformy Azure")
* [Biblioteki klienta zestawu Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Szablony usługi Azure Resource Manager (ARM)](./create-account-resource-manager-template.md?tabs=portal "Szablony usługi Azure Resource Manager (ARM)")

## <a name="using-cognitive-services-in-different-development-environments"></a>Używanie Cognitive Services w różnych środowiskach deweloperskich

Dzięki platformie Azure Cognitive Services masz dostęp do kilku opcji developmentowych, takich jak:

* Narzędzia automatyzacji i integracji, takie Logic Apps i Power Automate.
* Opcje wdrażania, takie Azure Functions i App Service. 
* Cognitive Services kontenerów platformy Docker w celu zapewnienia bezpiecznego dostępu.
* Narzędzia takie jak Apache Spark, Azure Databricks, Azure Synapse Analytics i Azure Kubernetes Service dla scenariuszy danych big data. 

Aby dowiedzieć się więcej, [zobacz Cognitive Services opcje tworzenia oprogramowania.](./cognitive-services-development-options.md)

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Bezpieczne Cognitive Services korzystanie z usługi

Azure Cognitive Services udostępnia warstwowy model zabezpieczeń, [](authentication.md "uwierzytelnianie") w tym uwierzytelnianie za pośrednictwem poświadczeń usługi Azure Active Directory, prawidłowy klucz zasobu i sieci [wirtualne platformy Azure.](cognitive-services-virtual-networks.md "Sieci wirtualne platformy Azure")

## <a name="containers-for-cognitive-services"></a>Kontenery usług Cognitive Services

 Azure Cognitive Services udostępnia kilka kontenerów platformy Docker, które umożliwiają korzystanie z tych samych interfejsów API, które są dostępne lokalnie na platformie Azure. Użycie tych kontenerów zapewnia elastyczność przybliżania Cognitive Services danych ze względu na zgodność, zabezpieczenia lub inne przyczyny operacyjne. Dowiedz się więcej o [Cognitive Services kontenerów.](cognitive-services-container-support.md "Kontenery usług Cognitive Services")

## <a name="regional-availability"></a>Dostępność w regionach

Interfejsy API w Cognitive Services są hostowane w rosnącej sieci centrów danych zarządzanych przez firmę Microsoft. Dostępność regionalną dla każdego interfejsu API można znaleźć na liście [regionów platformy Azure.](https://azure.microsoft.com/regions "Lista regionów platformy Azure")

Szukasz regionu, którego jeszcze nie obsługujemy? Daj nam znać, składając żądanie dotyczące funkcji na naszym [forum UserVoice.](https://cognitive.uservoice.com/ "Forum UserVoice")

## <a name="supported-cultural-languages"></a>Obsługiwane języki kulturowe

Cognitive Services obsługuje szeroką gamę języków kulturowych na poziomie usługi. Dostępność języka dla każdego interfejsu API można znaleźć na [liście obsługiwanych języków.](language-support.md "lista obsługiwanych języków")

## <a name="certifications-and-compliance"></a>Certyfikaty i zgodność

Cognitive Services otrzymał certyfikaty, takie jak CSA STAR Certification, FedRAMP Moderate i HIPAA BAA. Możesz pobrać [certyfikaty](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "pobieranie") dla własnych inspekcji i przeglądów zabezpieczeń.

Aby poznać zasady zarządzania prywatnością i danymi, przejdź do [Centrum zaufania](https://servicetrust.microsoft.com/ "Centrum zaufania").

## <a name="support"></a>Pomoc techniczna

Cognitive Services udostępnia kilka opcji pomocy technicznej, które ułatwiają przechodzenie do przodu w tworzeniu inteligentnych aplikacji. Cognitive Services także silną społeczność deweloperów, którzy mogą pomóc w odpowiadania na konkretne pytania. Aby uzyskać pełną listę dostępnych opcji, zobacz [opcje pomocy Cognitive Services pomocy.](cognitive-services-support-options.md "Cognitive Services pomocy technicznej i pomocy")

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta usług Cognitive Services](cognitive-services-apis-create-account.md "Tworzenie konta usług Cognitive Services")
* [Co nowego w Cognitive Services dokumentów](whats-new-docs.md "Co nowego w Cognitive Services dokumentów")
* [Planowanie kosztów dla Cognitive Services](plan-manage-costs.md)

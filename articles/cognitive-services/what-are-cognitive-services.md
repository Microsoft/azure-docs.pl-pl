---
title: Co to jest platforma Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services sprawia, że AI jest dostępny dla każdego dewelopera, bez konieczności uczenia maszynowego i analizy danych. Wystarczy wykonać wywołanie interfejsu API z aplikacji, aby dodać możliwość wyświetlania (zaawansowanego wyszukiwania i rozpoznawania obrazów), słyszenia, mówienia, wyszukiwania i podejmowania decyzji w aplikacjach.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: usługi poznawcze, analiza poznawcze, rozwiązania poznawcze, usługi AI, zrozumienie poznawcze i funkcje poznawcze
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 2cc0ef4ed0e7a63a822efc34e223cbec20815dd9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521479"
---
# <a name="what-are-azure-cognitive-services"></a>Co to jest platforma Azure Cognitive Services?

Usługa Azure Cognitive Services to usługi oparte na chmurze, które udostępniają interfejsy API REST i zestawy SDK biblioteki klienckiej, które ułatwiają tworzenie analiz poznawczej w aplikacjach. Możesz dodawać funkcje poznawcze do aplikacji bez posiadania sztucznej analizy lub umiejętności związanych z nauką danych. Usługa Azure Cognitive Services obejmuje różne usługi AI, które umożliwiają tworzenie rozwiązań poznawczych, które mogą wyświetlać, słyszeć, mówić, rozumieć, a nawet podejmować decyzje.

## <a name="categories-of-cognitive-services"></a>Kategorie Cognitive Services

Wykaz usług poznawczych, które udostępniają zrozumienie poznawcze, jest podzielony na pięć głównych filarów:

* Wizja
* Mowa
* Język
* Decyzja
* Wyszukiwanie

Poniższe sekcje tego artykułu zawierają listę usług, które są częścią tych pięciu filarów.

## <a name="vision-apis"></a>Interfejsy API przetwarzania

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Przetwarzanie obrazów")|Usługa przetwarzanie obrazów zapewnia dostęp do zaawansowanych algorytmów poznawczych służących do przetwarzania obrazów i zwracania informacji.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Custom Vision Service")|Custom Vision Service umożliwia tworzenie klasyfikatorów niestandardowych.|
|[Rozpoznawanie twarzy](https://docs.microsoft.com/azure/cognitive-services/face/ "Rozpoznawanie twarzy")| Usługa kroju obsługi zapewnia dostęp do zaawansowanych algorytmów, co umożliwia wykrywanie i rozpoznawanie atrybutów.|
|[Rozpoznawanie formularzy](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Rozpoznawanie formularzy")|Aparat rozpoznawania formularzy identyfikuje i wyodrębnia pary klucz-wartość oraz dane tabeli z dokumentów formularzy; następnie dane są wyprowadzane z uwzględnieniem relacji w oryginalnym pliku.|
|[Aparat rozpoznawania atramentu](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Rozpoznawanie pisma odręcznego") (wycofywanie)|Aparat rozpoznawania pisma odręcznego pozwala rozpoznawać i analizować dane cyfrowego pociągnięć odręcznych, kształtów i zawartości napisanej ręcznie, a następnie wyprowadzać strukturę dokumentu ze wszystkimi rozpoznanymi obiektami.|
|[Indeksator wideo](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Indeksator wideo")|Video Indexer umożliwia wyodrębnienie szczegółowych informacji z filmu wideo.|

## <a name="speech-apis"></a>Interfejsy API mowy

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Usługa rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Usługa rozpoznawania mowy")|Usługa Speech dodaje funkcje z obsługą mowy do aplikacji. Usługa mowy oferuje różne funkcje, takie jak Zamiana mowy na tekst, zamiana tekstu na mowę, Tłumaczenie mowy i wiele innych.|
<!--
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Interfejsy API języka

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Usługa Language Understanding (LUIS) umożliwia aplikacji rozpoznawanie poleceń wydawanych naturalnymi słowami.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker umożliwia tworzenie usługi pytań i odpowiedzi na podstawie zawartości z częściową strukturą.|
|[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Analiza tekstu")| Analiza tekstu zapewnia przetwarzanie języka naturalnego przez nieprzetworzony tekst do analizy tonacji, wyodrębnianie kluczowych fraz i wykrywanie języka.|
|[Translator](https://docs.microsoft.com/azure/cognitive-services/translator/ "Translator")|Translator obsługuje tłumaczenie tekstu oparte na maszynach niemal w czasie rzeczywistym.|
| [Czytnik immersyjny](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/ "Czytnik immersyjny") | Czytnik immersyjny dodaje do aplikacji funkcje odczytu i zrozumienia ekranu. |

## <a name="decision-apis"></a>Interfejsy API decyzji

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Narzędzie do wykrywania anomalii](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Narzędzie do wykrywania anomalii") |Detektor anomalii umożliwia monitorowanie i wykrywanie nieprawidłowych danych szeregów czasowych.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator zapewnia monitorowanie dla możliwej obraźliwej, niepożądanej i ryzykownej zawartości.|
|[Klasyfikator metryk](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor) (wersja zapoznawcza) | Usługa Advisor Metrics oferuje dostosowywalne wykrywanie anomalii w danych szeregów czasowych w wielu variateach oraz w pełni wyróżniony Portal sieci Web, który ułatwia korzystanie z usługi.|
|[Personalizacja](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizacja")|Personalizowanie umożliwia wybranie najlepszego środowiska do wyświetlania użytkownikom, uczenie się od ich zachowania w czasie rzeczywistym.|

## <a name="search-apis"></a>Interfejsy API wyszukiwania

> [!NOTE]
> Szukasz [usługi Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/)? Chociaż używa Cognitive Services niektórych zadań, jest to inna Technologia wyszukiwania, która obsługuje inne scenariusze.

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Wyszukiwanie wiadomości (Bing)](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Wyszukiwanie wiadomości (Bing)")|Wyszukiwanie wiadomości Bing zwraca listę artykułów z wiadomościami ustalonych jako istotne dla zapytania użytkownika.|
|[Wyszukiwanie klipów wideo w usłudze Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Wyszukiwanie klipów wideo w usłudze Bing")|Wyszukiwanie wideo Bing zwraca listę filmów wideo, które zostały określone jako istotne dla zapytania użytkownika.|
|[Wyszukiwanie w sieci Web (Bing)](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Wyszukiwanie w sieci Web (Bing)")|Wyszukiwanie w sieci Web Bing zwraca listę wyników wyszukiwania, które zostały uznane za istotne dla zapytania użytkownika.|
|[Automatyczne sugerowanie Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Automatyczne sugerowanie Bing")|Automatyczne sugerowanie Bing umożliwia wysłanie terminu zapytania wyszukiwania częściowego do usługi Bing i odwracanie listy sugerowanych zapytań.|
|[Wyszukiwanie niestandardowe Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Wyszukiwanie niestandardowe Bing")|Wyszukiwanie niestandardowe Bing umożliwia tworzenie dostosowanych środowisk wyszukiwania dla tematów, które Cię interesują.|
|[Wyszukiwanie jednostek Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Wyszukiwanie jednostek Bing")|Wyszukiwanie jednostek Bing zwraca informacje o jednostkach, które są określane przez usługę Bing, w odniesieniu do zapytania użytkownika.|
|[Wyszukiwanie obrazów w usłudze Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Wyszukiwanie obrazów w usłudze Bing")|Wyszukiwanie obrazów Bing zwraca wyświetlanie obrazów określonych jako istotne dla zapytania użytkownika.|
|[Wyszukiwanie wizualne Bing](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Wyszukiwanie wizualne Bing")|Funkcja wyszukiwanie wizualne Bing zapewnia szczegółowe informacje o obrazie, takie jak obrazy podobne wizualnie, źródła zakupów dla produktów znalezionych w obrazie i powiązane wyszukiwania.|
|[Wyszukiwanie lokalnych firm w usłudze Bing](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Wyszukiwanie lokalnych firm w usłudze Bing")| Interfejs API wyszukiwania lokalnego usługi Bing umożliwia aplikacjom Znajdowanie informacji o kontakcie i lokalizacji na temat lokalnych firm w oparciu o zapytania wyszukiwania.|
|[Sprawdzanie pisowni Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Sprawdzanie pisowni Bing")|Sprawdzanie pisowni Bing umożliwia wykonywanie gramatycznych kontekstowych i sprawdzanie pisowni.|

## <a name="get-started-with-cognitive-services"></a>Wprowadzenie do usług Cognitive Services

Zacznij od utworzenia zasobu Cognitive Services za pomocą praktycznych przewodników Szybki Start przy użyciu następujących metod:

* [Witryna Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Interfejs wiersza polecenia platformy Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "Interfejs wiersza polecenia platformy Azure")
* [Biblioteki klienckie zestawu Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "poznawcze-Services-API-Create-Account-Client-Library? pivots = programowanie-Language-CSharp")
* [Szablony usługi Azure Resource Manager (ARM)](resource-manager-template.md?tabs=portal "Szablony usługi Azure Resource Manager (ARM)")

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Bezpieczne używanie Cognitive Services

Usługa Azure Cognitive Services zapewnia warstwowy model zabezpieczeń, w tym [uwierzytelnianie](authentication.md "uwierzytelnianie") za pośrednictwem Azure Active Directory poświadczeń, prawidłowego klucza zasobów i [sieci wirtualnych platformy Azure](cognitive-services-virtual-networks.md "Sieci wirtualne platformy Azure").

## <a name="containers-for-cognitive-services"></a>Kontenery usług Cognitive Services

 Cognitive Services udostępnia kontenery do wdrożenia w chmurze platformy Azure lub lokalnie. Dowiedz się więcej na temat [kontenerów Cognitive Services](cognitive-services-container-support.md "Kontenery usług Cognitive Services").

## <a name="regional-availability"></a>Dostępność regionalna

Interfejsy API w Cognitive Services są hostowane w coraz większej sieci centrów danych zarządzanych przez firmę Microsoft. Dostępność regionalną poszczególnych interfejsów API na [liście regionów platformy Azure](https://azure.microsoft.com/regions "Lista regionów platformy Azure").

Szukasz jeszcze nieobsługiwanego regionu? Daj nam znać, podając wniosek o funkcję na naszym [forum](https://cognitive.uservoice.com/ "Forum UserVoice")w witrynie UserVoice.

## <a name="supported-cultural-languages"></a>Obsługiwane języki kulturowe

Cognitive Services obsługuje szeroką gamę języków kulturowych na poziomie usług. Dostępność języka dla każdego interfejsu API można znaleźć na [liście obsługiwanych języków](language-support.md "Lista obsługiwanych języków").

## <a name="certifications-and-compliance"></a>Certyfikaty i zgodność

Cognitive Services przyznano certyfikaty, takie jak "CSA gwiazdy", FedRAMP umiarkowane i HIPAA BAA. Możesz [pobrać](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "pobieranie") certyfikaty dotyczące własnych inspekcji i przeglądów zabezpieczeń.

Aby poznać prywatność i zarządzanie danymi, przejdź do [Centrum zaufania](https://servicetrust.microsoft.com/ "Centrum zaufania").

## <a name="support"></a>Pomoc techniczna

Cognitive Services oferuje kilka opcji pomocy technicznej, które ułatwiają przechodzenie do przodu przy tworzeniu inteligentnych aplikacji. Cognitive Services również ma mocną społeczność deweloperów, którzy mogą pomóc w udzieleniu odpowiedzi na określone pytania. Aby uzyskać pełną listę dostępnych opcji, zobacz [Cognitive Services pomocy technicznej i opcji pomocy](cognitive-services-support-options.md "Pomoc techniczna Cognitive Services i opcje pomocy").

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta usług Cognitive Services](cognitive-services-apis-create-account.md "Tworzenie konta usług Cognitive Services")
* [Co nowego w dokumentacji Cognitive Services](whats-new-docs.md "Co nowego w dokumentacji Cognitive Services")
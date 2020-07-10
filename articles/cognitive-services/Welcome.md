---
title: Co to jest platforma Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services platformy Azure to interfejsy API, zestawy SDK i usługi, których można używać z usługą Microsoft Azure do kompilowania inteligentnych aplikacji.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 12/19/2019
ms.author: nitinme
ms.openlocfilehash: f45cf27f427625571a58022c199391cac9a05bd2
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146923"
---
# <a name="what-are-azure-cognitive-services"></a>Co to jest platforma Azure Cognitive Services?

Usługa Azure Cognitive Services to interfejsy API, zestawy SDK i usługi dostępne w celu ułatwienia deweloperom kompilowania inteligentnych aplikacji bez konieczności podawania umiejętności i wiedzy o niebezpośredniej lub naukowej wiedzy. Dzięki platformie Azure Cognitive Services deweloperzy mogą łatwo dodawać funkcje poznawcze do swoich aplikacji. Celem Cognitive Services platformy Azure jest ułatwienie deweloperom tworzenia aplikacji, które mogą wyświetlać, słyszeć, mówić i rozumieć, a nawet zacząć z przyczyn. Katalog usług w ramach platformy Azure Cognitive Services można podzielić na pięć głównych filarów, mowy, języka, wyszukiwanie w sieci Web i decyzji.

## <a name="vision-apis"></a>Interfejsy API przetwarzania

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Przetwarzanie obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Przetwarzanie obrazów")|Usługa przetwarzanie obrazów zapewnia dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Custom Vision Service")|Custom Vision Service umożliwia tworzenie klasyfikatorów niestandardowych.|
|[Rozpoznawanie twarzy](https://docs.microsoft.com/azure/cognitive-services/face/ "Rozpoznawanie twarzy")| Usługa kroju obsługi zapewnia dostęp do zaawansowanych algorytmów, co umożliwia wykrywanie i rozpoznawanie atrybutów.|
|[Aparat rozpoznawania formularzy](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Rozpoznawanie formularzy") (wersja zapoznawcza)|Aparat rozpoznawania formularzy identyfikuje i wyodrębnia pary klucz-wartość oraz dane tabeli z dokumentów formularzy; następnie dane są wyprowadzane z uwzględnieniem relacji w oryginalnym pliku.|
|[Aparat rozpoznawania atramentu](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Rozpoznawanie pisma odręcznego") (wersja zapoznawcza)|Aparat rozpoznawania pisma odręcznego pozwala rozpoznawać i analizować dane cyfrowego pociągnięć odręcznych, kształtów i zawartości napisanej ręcznie, a następnie wyprowadzać strukturę dokumentu ze wszystkimi rozpoznanymi obiektami.|
|[Indeksator wideo](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Indeksator wideo")|Video Indexer umożliwia wyodrębnienie szczegółowych informacji z filmu wideo.|

## <a name="speech-apis"></a>Interfejsy API mowy

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Usługa rozpoznawania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Usługa rozpoznawania mowy")|Usługa Speech dodaje funkcje z obsługą mowy do aplikacji.|
|[Interfejs API rozpoznawania osoby mówiącej](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Interfejs API rozpoznawania osoby mówiącej") (wersja zapoznawcza)|Interfejs API rozpoznawania osoby mówiącej zapewnia algorytmy identyfikacji i weryfikacji osoby mówiącej.|
|[Rozpoznawanie mowy Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home "Rozpoznawanie mowy Bing") (wycofywanie)|Interfejs API rozpoznawania mowy Bing zapewnia łatwy sposób tworzenia funkcji z obsługą mowy w aplikacjach.|
|[Tłumaczenie mowy w usłudze translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Tłumaczenie mowy w usłudze Translator") (wycofywanie)|Tłumaczenie mowy w usłudze Translator to usługa tłumaczenia maszynowego.|

> [!NOTE]
> Szukasz [usługi Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/)? Chociaż używa Cognitive Services niektórych zadań, jest to inna Technologia wyszukiwania, która obsługuje inne scenariusze.


## <a name="language-apis"></a>Interfejsy API języka

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Usługa Language Understanding (LUIS) umożliwia aplikacji zrozumienie, co osoba chce w swoich własnych słowach.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker umożliwia tworzenie usługi pytań i odpowiedzi na podstawie zawartości z częściową strukturą.|
|[Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Analiza tekstu")|Analiza tekstu zapewnia przetwarzanie języka naturalnego w tekście nieprzetworzonym do analizy tonacji, wyodrębnianie kluczowych fraz i wykrywanie języka.|
|[Translator](https://docs.microsoft.com/azure/cognitive-services/translator/ "Translator")|Translator obsługuje tłumaczenie tekstu oparte na maszynach niemal w czasie rzeczywistym.|


## <a name="search-apis"></a>Interfejsy API wyszukiwania

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

## <a name="decision-apis"></a>Interfejsy API decyzji

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Detektor anomalii](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Narzędzie do wykrywania anomalii") (wersja zapoznawcza)|Detektor anomalii umożliwia monitorowanie i wykrywanie nieprawidłowych danych szeregów czasowych.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator zapewnia monitorowanie dla możliwej obraźliwej, niepożądanej i ryzykownej zawartości.|
|[Personalizacja](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizacja")|Personalizowanie umożliwia wybranie najlepszego środowiska do wyświetlania użytkownikom, uczenie się od ich zachowania w czasie rzeczywistym.|

## <a name="subscription-management"></a>Zarządzanie subskrypcjami

Po zalogowaniu się przy użyciu konta Microsoft możesz uzyskiwać dostęp do [moich subskrypcji](https://www.microsoft.com/cognitive-services/subscriptions "Moje subskrypcje") , aby wyświetlić używane produkty, pozostały limit przydziału oraz możliwość dodawania dodatkowych produktów do subskrypcji.

## <a name="upgrade-to-unlock-limits"></a>Uaktualnij do limitów odblokowania

Wszystkie interfejsy API mają warstwę bezpłatna, która ma limity użycia i przepływności.  Limity te można zwiększyć przy użyciu płatnej oferty i wybierając odpowiednią opcję warstwy cenowej podczas wdrażania usługi w Azure Portal. [Dowiedz się więcej o ofertach i cenach](https://azure.microsoft.com/pricing/details/cognitive-services/ "oferty i ceny"). Musisz skonfigurować konto subskrybenta platformy Azure z kartą kredytową i numerem telefonu. Jeśli masz specjalne wymagania lub po prostu chcesz skontaktować się ze sprzedażą, kliknij przycisk "Skontaktuj się z nami" w górnej części strony z cennikiem.

## <a name="regional-availability"></a>Dostępność regionalna

Interfejsy API w Cognitive Services są hostowane w coraz większej sieci centrów danych zarządzanych przez firmę Microsoft. Dostępność regionalną poszczególnych interfejsów API na [liście regionów platformy Azure](https://azure.microsoft.com/regions).

Szukasz jeszcze nieobsługiwanego regionu? Daj nam znać, podając wniosek o funkcję na naszym [forum](https://cognitive.uservoice.com/)w witrynie UserVoice.

## <a name="supported-cultural-languages"></a>Obsługiwane języki kulturowe

 Cognitive Services obsługuje szeroką gamę języków kulturowych na poziomie usług. Dostępność języka dla każdego interfejsu API można znaleźć na [liście obsługiwanych języków](language-support.md).

## <a name="securing-resources"></a>Zabezpieczanie zasobów

Usługa Azure Cognitive Services zapewnia warstwowy model zabezpieczeń, w tym [uwierzytelnianie](authentication.md) za pośrednictwem Azure Active Directory poświadczeń, prawidłowego klucza zasobów i [sieci wirtualnych platformy Azure](cognitive-services-virtual-networks.md).

## <a name="container-support"></a>Obsługa kontenerów

 Cognitive Services udostępnia kontenery do wdrożenia w chmurze platformy Azure lub lokalnie. Dowiedz się więcej na temat [kontenerów Cognitive Services](cognitive-services-container-support.md).

## <a name="certifications-and-compliance"></a>Certyfikaty i zgodność

Cognitive Services przyznano certyfikaty, takie jak "CSA gwiazdy", FedRAMP umiarkowane i HIPAA BAA.

Możesz [pobrać](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) certyfikaty dotyczące własnych inspekcji i przeglądów zabezpieczeń.

Aby poznać prywatność i zarządzanie danymi, przejdź do [Centrum zaufania](https://servicetrust.microsoft.com/).

## <a name="support"></a>Pomoc techniczna

Cognitive Services udostępnić kilka [opcji pomocy technicznej](cognitive-services-support-options.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta usług Cognitive Services](cognitive-services-apis-create-account.md)

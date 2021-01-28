---
title: Cognitive Services i Machine Learning
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak usługi Azure Cognitive Services wygląda w porównaniu z innymi ofertami platformy Azure w zakresie uczenia maszynowego.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 77464ccfb5b80c84f091d8e6bc491908baed6486
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955007"
---
# <a name="cognitive-services-and-machine-learning"></a>Usługi Cognitive Services i uczenie maszynowe

Cognitive Services zapewnia możliwości uczenia maszynowego w celu rozwiązywania ogólnych problemów, takich jak analizowanie tekstu emocjonalnej tonacji lub analizowanie obrazów w celu rozpoznawania obiektów lub twarzy. Korzystanie z tych usług nie wymaga specjalnej wiedzy z zakresu uczenia maszynowego ani nauki o danych. 

[Cognitive Services](./what-are-cognitive-services.md) jest grupą usług, z których każda obsługuje różne, uogólnione funkcje prognozowania. Usługi są podzielone na różne kategorie, aby pomóc w znalezieniu odpowiedniej usługi. 

|Kategoria usługi|Przeznaczenie|
|--|--|
|[Decyzja](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Twórz aplikacje wyświetlające rekomendacje pozwalające skuteczniej podejmować lepsze decyzje.|
|[Język](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Umożliwiaj aplikacjom przetwarzanie języka naturalnego za pomocą wbudowanych skryptów, ocenianie tonacji i uczenie się rozpoznawania potrzeb użytkowników.|
|[Wyszukiwanie](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Dodawanie interfejsów API wyszukiwania Bing do aplikacji i wykorzystanie możliwości przeczesywania miliardów stron internetowych, obrazów, wideo i wiadomości za pomocą jednego wywołania interfejsu API.|
|[Mowa](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Konwertuj mowę na tekst i tekst na naturalnie brzmiącą mowę. Tłumacz jeden język na inny oraz włącz weryfikację i rozpoznawanie osoby mówiącej.|
|[Obraz](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Rozpoznawaj, identyfikuj, podpisuj, indeksuj i moderuj swoje zdjęcia, klipy wideo i zawartość elektronicznego pisma odręcznego.|
||||

Użyj Cognitive Services, gdy:

* Można użyć uogólnionego rozwiązania.
* Uzyskaj dostęp do rozwiązania z interfejsu API REST programowania lub zestawu SDK. 

Użyj innego rozwiązania do uczenia maszynowego, gdy:

* Konieczne jest wybranie algorytmu i konieczność uczenia się na bardzo konkretnych danych.

## <a name="what-is-machine-learning"></a>Co to jest uczenie maszynowe?

Uczenie maszynowe to koncepcja, w której można połączyć dane i algorytm, aby rozwiązać konkretną potrzebę. Gdy dane i algorytm są przeszkolone, dane wyjściowe są modelem, którego można użyć ponownie z różnymi danymi. Przeszkolony model udostępnia szczegółowe informacje na podstawie nowych danych. 

Proces tworzenia systemu uczenia maszynowego wymaga pewnej znajomości uczenia maszynowego lub nauki dotyczącej danych.

Uczenie maszynowe jest dostępne przy użyciu [produktów i usług Azure Machine Learning (AML)](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context).

## <a name="what-is-a-cognitive-service"></a>Co to jest usługa poznawcze?

Usługa poznawcze udostępnia część lub wszystkie składniki w rozwiązaniu do uczenia maszynowego: dane, algorytmy i model szkolony. Te usługi są przeznaczone do wymagania ogólnej wiedzy na temat danych bez potrzeby korzystania z uczenia maszynowego i analizy danych. Te usługi zapewniają zarówno interfejs API REST, jak i zestaw SDK oparty na języku. W związku z tym musisz mieć wiedzę dotyczącą języka programowania, aby korzystać z usług.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Jak są podobne Cognitive Services i Azure Machine Learning (AML)?

Oba mają cel do zastosowania sztucznej analizy (AI) w celu zwiększenia działania biznesowego, Chociaż każda z nich zapewnia, że poszczególne oferty są inne. 

Zwykle odbiorcy są różne:

* Cognitive Services są przeznaczone dla deweloperów bez środowiska uczenia maszynowego.
* Azure Machine Learning jest dostosowany do celów analityków danych. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Czym różni się usługa poznawcze od uczenia maszynowego?

Usługa poznawcze zapewnia dla Ciebie model szkolony. Dzięki temu dane i algorytm są razem dostępne z interfejsów API REST lub zestawu SDK. Tę usługę można zaimplementować w kilka minut, w zależności od scenariusza.  Usługa poznawcze zapewnia odpowiedzi na ogólne problemy, takie jak kluczowe frazy w identyfikacji tekstu lub elementu w obrazach. 

Uczenie maszynowe to proces, który zwykle wymaga dłuższego czasu na pomyślne zaimplementowanie. Ten czas jest poświęcany na zbieranie danych, czyszczenie, przekształcanie, wybieranie algorytmów, uczenie modelu i wdrażanie w celu uzyskania tego samego poziomu funkcjonalności zapewnianego przez usługę poznawczej. W przypadku uczenia maszynowego istnieje możliwość zapewnienia odpowiedzi na wysoce wyspecjalizowane i/lub konkretne problemy. Problemy z uczeniem maszynowym wymagają znajomości określonego zagadnienia i danych dotyczących rozważanego problemu, a także doświadczenia w nauce danych.

## <a name="what-kind-of-data-do-you-have"></a>Jakiego rodzaju dane masz?

Cognitive Services, jako Grupa usług, może wymagać braku, niektórych lub wszystkich danych niestandardowych dla nauczonego modelu. 

### <a name="no-additional-training-data-required"></a>Nie są wymagane żadne dodatkowe dane szkoleniowe

Usługi zapewniające w pełni szkolony model mogą być traktowane jako _nieprzezroczyste_. Nie musisz wiedzieć, jak działają lub jakie dane zostały użyte do ich uczenia. Dane można przenieść do w pełni przeszkolonego modelu, aby uzyskać prognozę. 

### <a name="some-or-all-training-data-required"></a>Wymagane są niektóre lub wszystkie dane szkoleniowe

Niektóre usługi umożliwiają przenoszenie własnych danych, a następnie uczenie modelu. Dzięki temu można zwiększyć model przy użyciu danych i algorytmu usługi z własnymi danymi. Dane wyjściowe odpowiadają Twoim potrzebom. Podczas przenoszenia własnych danych może być konieczne oznakowanie danych w sposób specyficzny dla usługi. Na przykład jeśli przekazujesz model do identyfikacji kwiatów, możesz udostępnić katalog kwiatów i lokalizację kwitnienia w każdym obrazie, aby szkolić model. 

Usługa może _umożliwiać_ dostarczenie danych w celu zwiększenia własnych danych. Usługa może _wymagać_ podania danych. 

### <a name="real-time-or-near-real-time-data-required"></a>Wymagane dane w czasie rzeczywistym lub niemal w czasie rzeczywistym

Aby można było utworzyć skuteczny model, usługa może potrzebować danych w czasie rzeczywistym lub niemal w czasie rzeczywistym. Te usługi przetwarzają duże ilości danych modelu. 

## <a name="service-requirements-for-the-data-model"></a>Wymagania dotyczące usługi dla modelu danych

Następujące dane klasyfikują poszczególne usługi według rodzaju danych, które umożliwia lub wymaga.

|Usługa poznawcze|Nie są wymagane żadne dane szkoleniowe|Podajesz niektóre lub wszystkie dane szkoleniowe|Zbieranie danych w czasie rzeczywistym lub niemal w czasie rzeczywistym|
|--|--|--|--|
|[Narzędzie do wykrywania anomalii](./Anomaly-Detector/overview.md)|x|x|x|
|Wyszukiwanie Bing |x|||
|[Przetwarzanie obrazów](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./custom-vision-service/overview.md)||x||
|[Rozpoznawanie twarzy](./Face/Overview.md)|x|x||
|[Rozpoznawanie formularzy](./form-recognizer/overview.md)||x||
|[Czytnik immersyjny](./immersive-reader/overview.md)|x|||
|[Rozpoznawanie pisma odręcznego](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizacja](./personalizer/what-is-personalizer.md)|y|y|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Aparat rozpoznawania osoby mówiącej](./speech-service/speaker-recognition-overview.md)||x||
|[Zamiana tekstu na mowę (TTS)](speech-service/text-to-speech.md)|x|x||
|[Zamiana mowy na mowę na tekst (monitora STT)](speech-service/speech-to-text.md)|x|x||
|[Tłumaczenie mowy](speech-service/speech-translation.md)|x|||
|[Analiza tekstu](./text-analytics/overview.md)|x|||
|[Translator](./translator/translator-info-overview.md)|x|||
|[Translator — translator niestandardowy](./translator/custom-translator/overview.md)||x||

* Personalizacja wymaga tylko danych szkoleniowych zbieranych przez usługę (w czasie rzeczywistym) do oszacowania zasad i danych. Program personalizujer nie potrzebuje dużych historycznych zestawów danych dla szkolenia z góry lub partii. 

## <a name="where-can-you-use-cognitive-services"></a>Gdzie można korzystać z Cognitive Services?
 
Usługi są używane w dowolnej aplikacji, która może tworzyć interfejsy API REST lub wywołania zestawu SDK. Przykładami aplikacji są: Web Sites, botów, Virtual lub Mixed Reality, Desktop i Mobile. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Jak usługa Azure Wyszukiwanie poznawcze związana z Cognitive Services?

[Azure wyszukiwanie poznawcze](../search/search-what-is-azure-search.md) to oddzielna usługa wyszukiwania w chmurze, która opcjonalnie używa Cognitive Services do dodawania obrazów i przetwarzania języka naturalnego do indeksowania obciążeń. Cognitive Services jest udostępniana w usłudze Azure Wyszukiwanie poznawcze za pomocą [wbudowanych umiejętności](../search/cognitive-search-predefined-skills.md) , które zawijają poszczególne interfejsy API. Możesz użyć bezpłatnego zasobu do przewodników, ale zaplanować tworzenie i dołączanie [zasobów](../search/cognitive-search-attach-cognitive-services.md) płatnych dla większych woluminów.

## <a name="how-can-you-use-cognitive-services"></a>Jak można używać Cognitive Services?

Każda usługa zawiera informacje o danych. Można łączyć usługi ze sobą w rozwiązania łańcucha, takie jak konwertowanie mowy (audio) na tekst, tłumaczenie tekstu na wiele języków, a następnie użycie przetłumaczonych języków w celu uzyskania odpowiedzi z bazy wiedzy. Chociaż Cognitive Services można używać do samodzielnego tworzenia inteligentnych rozwiązań, można je również łączyć z tradycyjnymi projektami uczenia maszynowego, aby uzupełniać modele lub przyspieszyć proces opracowywania. 

Cognitive Services, które udostępniają eksportowane modele dla innych narzędzi uczenia maszynowego:

|Usługa poznawcze|Informacje o modelu|
|--|--|
|[Custom Vision](./custom-vision-service/overview.md)|[Export](./Custom-Vision-Service/export-model-python.md) for Tensorflow for Android, CoreML for IOS11, ONNX for Windows ml|

## <a name="learn-more"></a>Więcej tutaj

* [Przewodnik po architekturze — jakie są produkty uczenia maszynowego w firmie Microsoft?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Uczenie maszynowe — wprowadzenie do uczenia głębokiego i uczenia maszynowego](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Następne kroki

* Utwórz konto usługi poznawczej w [Azure Portal](cognitive-services-apis-create-account.md) lub przy użyciu [interfejsu wiersza polecenia platformy Azure](./cognitive-services-apis-create-account-cli.md).
* Dowiedz się, jak [uwierzytelniać](authentication.md) się w usłudze poznawczej.
* [Rejestrowanie diagnostyczne](diagnostic-logging.md) służy do identyfikowania problemów i debugowania. 
* Wdróż usługę poznawczej w [kontenerze](cognitive-services-container-support.md)platformy Docker.
* Bądź na bieżąco z [aktualizacjami usług](https://azure.microsoft.com/updates/?product=cognitive-services).
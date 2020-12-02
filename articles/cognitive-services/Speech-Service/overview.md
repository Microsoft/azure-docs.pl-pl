---
title: Czym jest usługa rozpoznawania mowy?
titleSuffix: Azure Cognitive Services
description: Usługa mowy to zjednoczenie zamiany mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy na pojedynczą subskrypcję platformy Azure. Dodaj mowę do aplikacji, narzędzi i urządzeń za pomocą zestawu Speech SDK, zestawu Speech Devices SDK lub interfejsów API REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: 3b20773de62f036b507f1612f2b38d613d54ba2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485199"
---
# <a name="what-is-the-speech-service"></a>Czym jest usługa rozpoznawania mowy?

Usługa mowy to zjednoczenie zamiany mowy na tekst, tekstu na mowę i tłumaczenia mowy na jedną subskrypcję platformy Azure. Można łatwo włączyć obsługę mowy dla aplikacji, narzędzi i urządzeń za pomocą [interfejsu wiersza polecenia mowy](spx-overview.md), [zestawu Speech SDK](./speech-sdk.md), [zestawu Speech Devices SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android), [mowy Studio](https://speech.microsoft.com/)lub [interfejsów API REST](#reference-docs).

> [!IMPORTANT]
> Usługa mowy została zastąpiona interfejs API rozpoznawania mowy Bing i tłumaczenie mowy w usłudze Translator. Zapoznaj się z sekcją _migracja_ , aby uzyskać instrukcje dotyczące migracji.

Poniższe funkcje są częścią usługi mowy. Skorzystaj z łączy w tej tabeli, aby dowiedzieć się więcej na temat typowych przypadków użycia dla każdej funkcji, lub przejrzyj odwołanie do interfejsu API.

| Usługa | Cechy | Opis | SDK | REST |
|---------|---------|-------------|-----|------|
| [Zamiana mowy na tekst](speech-to-text.md) | Zamiana mowy na tekst w czasie rzeczywistym | Funkcja zamiany mowy na tekst przekształca lub tłumaczy strumienie audio lub pliki lokalne na tekst w czasie rzeczywistym, gdy aplikacje, narzędzia lub urządzenia mogą korzystać z lub wyświetlać. Używaj zamiany mowy na tekst za pomocą [Language Understanding (Luis)](../luis/index.yml) , aby uzyskać intencje użytkownika z uzyskanego mowy i korzystać z poleceń głosowych. | [Tak](./speech-sdk.md) | [Tak](#reference-docs) |
| | [Zamiana mowy na tekst w usłudze Batch](batch-transcription.md) | Funkcja zamiany mowy na tekst w usłudze Batch umożliwia asynchroniczne transkrypcję zamiany mowy na tekst dużych ilości danych audio mowy przechowywanych w usłudze Azure Blob Storage. Oprócz konwersji dźwięku zamiany mowy na tekst, funkcja zamiany mowy na tekst usługi Batch umożliwia również obsługę diarization i tonacji. | Nie | [Tak](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Konwersacja z obsługą kilku urządzeń](multi-device-conversation.md) | Łączenie wielu urządzeń lub klientów w konwersacji w celu wysyłania komunikatów na mowę lub tekstowych przy użyciu łatwej obsługi transkrypcji i tłumaczenia| Tak | Nie |
| | [Transkrypcja konwersacji](./conversation-transcription.md) | Włącza rozpoznawanie mowy w czasie rzeczywistym, identyfikację głosu i diarization. Doskonale nadaje się do jego przepisywaniach spotkań osób z możliwością odróżniania głośników. | Tak | Nie |
| | [Tworzenie modeli Custom Speech](#customize-your-speech-experience) | Jeśli używasz funkcji zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, możesz tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż. | Nie | [Tak](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Zamiana tekstu na mowę](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na wymowę przez człowieka, który używa [języka SSML (Speech syntezing Language)](speech-synthesis-markup.md). Wybieraj spośród standardowych głosów i głosów neuronowych (zobacz [Obsługa języka](language-support.md)). | [Tak](./speech-sdk.md) | [Tak](#reference-docs) |
| | [Tworzenie niestandardowych głosów](#customize-your-speech-experience) | Twórz niestandardowe czcionki głosowe, które są unikatowe dla danej marki lub produktu. | Nie | [Tak](#reference-docs) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia tłumaczenie mowy w czasie rzeczywistym na wiele języków w aplikacjach, narzędziach i urządzeniach. Ta usługa umożliwia tłumaczenie mowy na mowę i zamianę mowy na tekst. | [Tak](./speech-sdk.md) | Nie |
| [Asystenci głosowi](voice-assistants.md) | Asystenci głosowi | Asystenci głosu korzystający z usługi mowy umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla aplikacji i środowisk. Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która korzysta z kanału mowy linii Direct line lub zintegrowanego polecenia niestandardowego (wersja zapoznawcza) w celu ukończenia zadania. | [Tak](voice-assistants.md) | Nie |
| [Rozpoznawanie osoby mówiącej](speaker-recognition-overview.md) | Weryfikacja & tożsamość osoby mówiącej | Usługa rozpoznawanie osoby mówiącej zapewnia algorytmy weryfikujące i identyfikujące głośniki przy użyciu ich unikatowych cech głosowych. Rozpoznawanie osoby mówiącej jest używany do odpowiedzi na pytanie "kto mówi?". | Tak | [Tak](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Wypróbuj bezpłatnie usługę rozpoznawania mowy

W przypadku następujących kroków wymagane są zarówno konto Microsoft, jak i konto platformy Azure. Jeśli nie masz konto Microsoft, możesz skorzystać z jednej bezpłatnej rejestracji w [portalu konto Microsoft](https://account.microsoft.com/account). Wybierz pozycję **Zaloguj się przy użyciu konta Microsoft** a następnie, gdy zostanie wyświetlony monit o zalogowanie się, wybierz pozycję **Utwórz konto Microsoft**. Postępuj zgodnie z instrukcjami, aby utworzyć i zweryfikować nowe konto Microsoft.

Gdy masz konto Microsoft, przejdź do [strony tworzenia konta platformy Azure](https://azure.microsoft.com/free/ai/), wybierz pozycję **Rozpocznij bezpłatnie** i Utwórz nowe konto platformy Azure przy użyciu konto Microsoft. Oto, [jak zarejestrować się w celu korzystania z bezpłatnego konta platformy Azure](https://www.youtube.com/watch?v=GWT2R1C_uUU).

> [!NOTE]
> Po zarejestrowaniu się w celu korzystania z bezpłatnego konta platformy Azure jest on dostępny w wysokości $200 w ramach kredytu na korzystanie z usługi, który można zastosować do płatnej subskrypcji usługi mowy, która jest ważna przez maksymalnie 30 dni. Usługi platformy Azure są wyłączone, gdy środki wygaśnie lub wygasną po upływie 30 dni. Aby nadal korzystać z usług platformy Azure, musisz uaktualnić konto. Aby uzyskać więcej informacji, zobacz [jak uaktualnić bezpłatne konto platformy Azure](../../cost-management-billing/manage/upgrade-azure-subscription.md). 
>
> Usługa mowy ma dwie warstwy usług: bezpłatna (F0) i subskrypcja (S0), które mają różne ograniczenia i zalety. W przypadku korzystania z bezpłatnej warstwy usługi mowy o niskiej pojemności można zachować tę bezpłatną subskrypcję nawet po wygaśnięciu bezpłatnej wersji próbnej lub opłaty za usługę. Aby uzyskać więcej informacji, zobacz [Cognitive Services Cennik — Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Tworzenie zasobu platformy Azure

Aby dodać zasób usługi mowy (warstwa Bezpłatna lub płatna) do konta platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) za pomocą konta Microsoft.

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu. Jeśli nie widzisz opcji **Utwórz zasób**, zawsze możesz ją znaleźć, wybierając menu rozwijane w lewym górnym rogu ekranu.

1. W **nowym** oknie wpisz "mowę" w polu wyszukiwania, a następnie naciśnij klawisz ENTER.

1. W wynikach wyszukiwania wybierz pozycję **Speech**.

   ![Wyniki wyszukiwania mowy](media/index/speech-search.png)

1. Wybierz pozycję **Utwórz**, a następnie:

   - Nadaj unikatowej nazwie nowemu zasobowi. Nazwa pomaga rozróżnić wiele subskrypcji powiązanych z tą samą usługą.
   - Wybierz subskrypcję platformy Azure, z którą jest skojarzony nowy zasób, aby określić, w jaki sposób opłaty są naliczane. Poniżej przedstawiono wprowadzenie do [tworzenia subskrypcji platformy Azure](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal) w Azure Portal.
   - Wybierz [region](regions.md) , w którym będzie używany zasób. Azure to globalna platforma w chmurze, która jest ogólnie dostępna w wielu regionach na całym świecie. Aby uzyskać najlepszą wydajność, wybierz region, który znajduje się najbliżej Ciebie lub gdzie działa aplikacja. Dostępność usługi mowy różni się od różnych regionów. Upewnij się, że zasób jest tworzony w obsługiwanym regionie. Zobacz [region obsługa usługi Speech Services](./regions.md#speech-to-text-text-to-speech-and-translation).
   - Wybierz opcję bezpłatna (F0) lub płatna (S0). Aby uzyskać pełne informacje na temat cen i przydziałów użycia dla każdej warstwy, wybierz pozycję **Wyświetl pełne szczegóły cennika** lub zobacz [Cennik usługi Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Limity dotyczące zasobów znajdują się w temacie [limity Cognitive Services platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits).
   - Utwórz nową grupę zasobów dla tej subskrypcji mowy lub Przypisz subskrypcję do istniejącej grupy zasobów. Grupy zasobów ułatwiają zachowanie różnych subskrypcji platformy Azure.
   - Wybierz pozycję **Utwórz**. Spowoduje to przejście do omówienia wdrażania i wyświetlenie komunikatów o postępie wdrażania.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Wdrożenie nowego zasobu mowy trwa kilka minut. 

### <a name="find-keys-and-region"></a>Znajdź klucze i region

Aby znaleźć klucze i region ukończonego wdrożenia, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) za pomocą konta Microsoft.

2. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz nazwę zasobu Cognitive Services.

3. W okienku po lewej stronie w obszarze **Zarządzanie zasobami** wybierz pozycję **klucze i punkt końcowy**.

Każda subskrypcja ma dwa klucze: Możesz użyć dowolnego klawisza w aplikacji. Aby skopiować/wkleić klucz do edytora kodu lub innej lokalizacji, wybierz przycisk Kopiuj obok każdego klucza, Przełącz system Windows, aby wkleić zawartość schowka do żądanej lokalizacji.

Ponadto Skopiuj `LOCATION` wartość, która jest identyfikatorem regionu (np. `westus`, `westeurope` ) dla wywołań zestawu SDK.

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi poznawczej. Nie udostępniaj kluczy. Przechowuj je bezpiecznie — na przykład przy użyciu Azure Key Vault. Zalecamy także regularne ponowne generowanie tych kluczy. Tylko jeden klucz jest wymagany do wywołania interfejsu API. Po ponownym wygenerowaniu pierwszego klucza można użyć drugiego klawisza w celu uzyskania ciągłego dostępu do usługi.

## <a name="complete-a-quickstart"></a>Kończenie szybkiego startu

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uczenia podstawowych wzorców projektowych i uruchamiania kodu w mniej niż 10 minut. Zapoznaj się z poniższą listą przewodnika Szybki Start dla każdej funkcji.

* [Przewodnik Szybki start dotyczący zamiany mowy na tekst](get-started-speech-to-text.md)
* [Przewodnik Szybki start dotyczący zamiany tekstu na mowę](get-started-text-to-speech.md)
* [Przewodnik Szybki start dotyczący tłumaczenia mowy](./get-started-speech-translation.md)
* [Szybki start — rozpoznawanie intencji](quickstarts/intent-recognition.md)
* [Rozpoznawanie osoby mówiącej — Szybki Start](./get-started-speaker-recognition.md)

Gdy masz okazję do rozpoczęcia korzystania z usługi mowy, wypróbuj nasze samouczki pokazujące, jak rozwiązać różne scenariusze.

- [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Samouczek: Voice enable the bot with Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Samouczek: Tworzenie aplikacji kolby do tłumaczenia tekstu, analizowanie tonacji i syntezowanie przetłumaczonego tekstu na mowę, REST](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>Pobieranie przykładowego kodu

Przykładowy kod jest dostępny w witrynie GitHub dla usługi Speech. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi. Użyj tych linków, aby wyświetlić zestaw SDK i przykłady REST:

- [Przykłady zamiany mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Dostosowywanie środowiska mowy

Usługa mowy dobrze współpracuje z wbudowanymi modelami, ale warto dodatkowo dostosowywać i dostrajać środowisko dla danego produktu lub środowiska. Opcje dostosowania przestają być od modelu akustycznego na unikatowe czcionki głosowe dla marki.

Inne produkty oferują modele mowy dopasowane do określonych celów, takich jak opieka zdrowotna i ubezpieczenie, ale są dostępne dla wszystkich użytkowników. Dostosowanie w usłudze Azure Speech jest częścią *unikatowej* przewagi konkurencyjnej, która jest niedostępna dla wszystkich użytkowników lub klientów. Innymi słowy, modele są prywatne i dostosowane do użycia tylko w przypadku korzystania z programu.

| Usługa rozpoznawania mowy | Platforma | Opis |
| -------------- | -------- | ----------- |
| Zamiana mowy na tekst | [Custom Speech](https://aka.ms/customspeech) | Dostosuj modele rozpoznawania mowy do Twoich potrzeb i dostępnych danych. Przezwyciężenie barier rozpoznawania mowy, takich jak styl mowy, słownictwo i hałas w tle. |
| Zamiana tekstu na mowę | [Niestandardowy głos](https://aka.ms/customvoice) | Utwórz rozpoznawalny, unikatowy głos dla aplikacji zamieniających tekst na mowę, które korzystają z Twoich dostępnych danych mowy. Można dokładniej dostosować dane wyjściowe głosu przez dostosowanie zestawu parametrów głosowych. |

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker

[Używanie kontenerów usługi mowy](speech-container-howto.md) do wdrażania funkcji interfejsu API w środowisku lokalnym. Te kontenery platformy Docker umożliwiają przełączenie usługi bliżej danych pod kątem zgodności, bezpieczeństwa lub innych przyczyn operacyjnych. Usługa mowy oferuje następujące kontenery:

* Standardowe Zamiana mowy na tekst
* Custom Speech do tekstu
* Standardowe Zamiana tekstu na mowę
* Neuronowych Zamiana tekstu na mowę
* Niestandardowa Zamiana tekstu na mowę (wersja zapoznawcza)
* Wykrywanie języka mowy (wersja zapoznawcza)

## <a name="reference-docs"></a>Dokumentacja dokumentacji

- [Zestaw SDK rozpoznawania mowy](./speech-sdk.md)
- [Zestaw Speech Devices SDK](speech-devices-sdk.md)
- [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
- [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zamiany mowy na tekst](./get-started-speech-to-text.md) 
>  [Wprowadzenie do zamiany tekstu na mowę](get-started-text-to-speech.md)
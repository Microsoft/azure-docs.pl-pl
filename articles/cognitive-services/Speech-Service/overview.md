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
ms.date: 08/20/2020
ms.author: trbye
ms.openlocfilehash: 29289166dca2ee87055c8bcd4e453fc02966c0e5
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427177"
---
# <a name="what-is-the-speech-service"></a>Czym jest usługa rozpoznawania mowy?

Usługa mowy to zjednoczenie zamiany mowy na tekst, tekstu na mowę i tłumaczenia mowy na jedną subskrypcję platformy Azure. Można łatwo włączyć obsługę mowy dla aplikacji, narzędzi i urządzeń za pomocą [interfejsu wiersza polecenia mowy](spx-overview.md), [zestawu Speech SDK](speech-sdk-reference.md), [zestawu Speech Devices SDK](https://aka.ms/sdsdk-quickstart), [mowy Studio](https://speech.microsoft.com/)lub [interfejsów API REST](rest-apis.md).

> [!IMPORTANT]
> Usługa mowy została zastąpiona interfejs API rozpoznawania mowy Bing i tłumaczenie mowy w usłudze Translator. Zapoznaj się z sekcją _migracja_ , aby uzyskać instrukcje dotyczące migracji.

Poniższe funkcje są częścią usługi mowy. Skorzystaj z łączy w tej tabeli, aby dowiedzieć się więcej na temat typowych przypadków użycia dla każdej funkcji, lub przejrzyj odwołanie do interfejsu API.

| Usługa | Cecha | Opis | SDK | REST |
|---------|---------|-------------|-----|------|
| [Zamiana mowy na tekst](speech-to-text.md) | Zamiana mowy na tekst w czasie rzeczywistym | Funkcja zamiany mowy na tekst przekształca lub tłumaczy strumienie audio lub pliki lokalne na tekst w czasie rzeczywistym, gdy aplikacje, narzędzia lub urządzenia mogą korzystać z lub wyświetlać. Używaj zamiany mowy na tekst za pomocą [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) , aby uzyskać intencje użytkownika z uzyskanego mowy i korzystać z poleceń głosowych. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Zamiana mowy na tekst w usłudze Batch](batch-transcription.md) | Funkcja zamiany mowy na tekst w usłudze Batch umożliwia asynchroniczne transkrypcję zamiany mowy na tekst dużych ilości danych audio mowy przechowywanych w usłudze Azure Blob Storage. Oprócz konwersji dźwięku zamiany mowy na tekst, funkcja zamiany mowy na tekst usługi Batch umożliwia również obsługę diarization i tonacji. | Nie | [Tak](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Konwersacja z obsługą kilku urządzeń](multi-device-conversation.md) | Łączenie wielu urządzeń lub klientów w konwersacji w celu wysyłania komunikatów na mowę lub tekstowych przy użyciu łatwej obsługi transkrypcji i tłumaczenia| Tak | Nie |
| | [Transkrypcja konwersacji](conversation-transcription-service.md) | Włącza rozpoznawanie mowy w czasie rzeczywistym, identyfikację głosu i diarization. Doskonale nadaje się do jego przepisywaniach spotkań osób z możliwością odróżniania głośników. | Tak | Nie |
| | [Tworzenie modeli Custom Speech](#customize-your-speech-experience) | Jeśli używasz funkcji zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, możesz tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż. | Nie | [Tak](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Zamiana tekstu na mowę](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na wymowę przez człowieka, który używa [języka SSML (Speech syntezing Language)](speech-synthesis-markup.md). Wybieraj spośród standardowych głosów i głosów neuronowych (zobacz [Obsługa języka](language-support.md)). | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Tworzenie niestandardowych głosów](#customize-your-speech-experience) | Twórz niestandardowe czcionki głosowe, które są unikatowe dla danej marki lub produktu. | Nie | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia tłumaczenie mowy w czasie rzeczywistym na wiele języków w aplikacjach, narzędziach i urządzeniach. Ta usługa umożliwia tłumaczenie mowy na mowę i zamianę mowy na tekst. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nie |
| [Asystenci głosowi](voice-assistants.md) | Asystenci głosowi | Asystenci głosu korzystający z usługi mowy umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla aplikacji i środowisk. Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która korzysta z kanału mowy linii Direct line lub zintegrowanego polecenia niestandardowego (wersja zapoznawcza) w celu ukończenia zadania. | [Tak](voice-assistants.md) | Nie |
| [Rozpoznawanie osoby mówiącej](speaker-recognition-overview.md) | Weryfikacja & tożsamość osoby mówiącej | Usługa rozpoznawanie osoby mówiącej zapewnia algorytmy weryfikujące i identyfikujące głośniki przy użyciu ich unikatowych cech głosowych. Rozpoznawanie osoby mówiącej jest używany do odpowiedzi na pytanie "kto mówi?". | Tak | [Tak](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Wypróbuj bezpłatnie usługę rozpoznawania mowy

W przypadku następujących kroków wymagane są zarówno konto Microsoft, jak i konto platformy Azure. Jeśli nie masz konto Microsoft, możesz skorzystać z jednej bezpłatnej rejestracji w [portalu konto Microsoft](https://account.microsoft.com/account). Wybierz pozycję **Zaloguj się przy użyciu konta Microsoft** a następnie, gdy zostanie wyświetlony monit o zalogowanie się, wybierz pozycję **Utwórz konto Microsoft**. Postępuj zgodnie z instrukcjami, aby utworzyć i zweryfikować nowe konto Microsoft.

Gdy masz konto Microsoft, przejdź do [strony tworzenia konta platformy Azure](https://azure.microsoft.com/free/ai/), wybierz pozycję **Rozpocznij bezpłatnie**i Utwórz nowe konto platformy Azure przy użyciu konto Microsoft.

> [!NOTE]
> Usługa mowy ma dwie warstwy usług: bezpłatna i subskrypcja, które mają różne ograniczenia i korzyści. Po zarejestrowaniu się w celu uzyskania bezpłatnego konta platformy Azure otrzymasz od $200 w wysokości, które można zastosować do płatnej subskrypcji usługi mowy, która jest ważna przez maksymalnie 30 dni.
>
> W przypadku korzystania z bezpłatnej warstwy usługi mowy o niskiej pojemności można zachować tę bezpłatną subskrypcję nawet po wygaśnięciu bezpłatnej wersji próbnej lub opłaty za usługę.
>
> Aby uzyskać więcej informacji, zobacz [Cognitive Services Cennik — Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Tworzenie zasobu platformy Azure

Aby dodać zasób usługi mowy (warstwa Bezpłatna lub płatna) do konta platformy Azure:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) za pomocą konta Microsoft.

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu. Jeśli nie widzisz opcji **Utwórz zasób**, zawsze możesz ją znaleźć, wybierając menu rozwijane w lewym górnym rogu ekranu.

1. W **nowym** oknie wpisz "mowę" w polu wyszukiwania, a następnie naciśnij klawisz ENTER.

1. W wynikach wyszukiwania wybierz pozycję **Speech**.

   ![Wyniki wyszukiwania mowy](media/index/speech-search.png)

1. Wybierz pozycję **Utwórz**, a następnie:

   - Nadaj unikatowej nazwie nowemu zasobowi. Nazwa pomaga rozróżnić wiele subskrypcji powiązanych z tą samą usługą.
   - Wybierz subskrypcję platformy Azure, z którą jest skojarzony nowy zasób, aby określić, w jaki sposób opłaty są naliczane.
   - Wybierz [region](regions.md) , w którym będzie używany zasób.
   - Wybierz opcję bezpłatna (F0) lub płatna (S0). Aby uzyskać pełne informacje na temat cen i przydziałów użycia dla każdej warstwy, wybierz pozycję **Wyświetl pełne szczegóły cennika**. Aby uzyskać ograniczenia dotyczące zasobów, które można utworzyć dla każdej subskrypcji, zobacz [limity Cognitive Services platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits).
   - Utwórz nową grupę zasobów dla tej subskrypcji mowy lub Przypisz subskrypcję do istniejącej grupy zasobów. Grupy zasobów ułatwiają zachowanie różnych subskrypcji platformy Azure.
   - Wybierz pozycję **Utwórz**. Spowoduje to przejście do omówienia wdrażania i wyświetlenie komunikatów o postępie wdrażania.
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Wdrożenie nowego zasobu mowy trwa kilka minut. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu** , a następnie w okienku nawigacji po lewej stronie wybierz pozycję **klucze** , aby wyświetlić klucze subskrypcji usługi mowy. Każda subskrypcja ma dwa klucze: Możesz użyć dowolnego klawisza w aplikacji. Aby szybko skopiować/wkleić klucz do edytora kodu lub innej lokalizacji, wybierz przycisk Kopiuj obok każdego klucza, Przełącz system Windows, aby wkleić zawartość schowka do żądanej lokalizacji.

> [!IMPORTANT]
> Te klucze subskrypcji są używane do uzyskiwania dostępu do interfejsu API usługi poznawczej. Nie udostępniaj kluczy. Przechowuj je bezpiecznie — na przykład przy użyciu Azure Key Vault. Zalecamy także regularne ponowne generowanie tych kluczy. Tylko jeden klucz jest wymagany do wywołania interfejsu API. Po ponownym wygenerowaniu pierwszego klucza można użyć drugiego klawisza w celu uzyskania ciągłego dostępu do usługi.

## <a name="complete-a-quickstart"></a>Kończenie szybkiego startu

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uczenia podstawowych wzorców projektowych i uruchamiania kodu w mniej niż 10 minut. Zapoznaj się z poniższą listą przewodnika Szybki Start dla każdej funkcji.

* [Przewodnik Szybki start dotyczący zamiany mowy na tekst](get-started-speech-to-text.md)
* [Przewodnik Szybki start dotyczący zamiany tekstu na mowę](get-started-text-to-speech.md)
* [Przewodnik Szybki start dotyczący tłumaczenia mowy](speech-translation-basics.md)
* [Szybki start — rozpoznawanie intencji](quickstarts/intent-recognition.md)
* [Rozpoznawanie osoby mówiącej — Szybki Start](speaker-recognition-basics.md)

Gdy masz okazję do rozpoczęcia korzystania z usługi mowy, wypróbuj nasze samouczki pokazujące, jak rozwiązać różne scenariusze.

- [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Samouczek: Voice enable the bot with Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Samouczek: Tworzenie aplikacji kolby do tłumaczenia tekstu, analizowanie tonacji i syntezowanie przetłumaczonego tekstu na mowę, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

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

## <a name="reference-docs"></a>Dokumentacja dokumentacji

- [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
- [Zestaw Speech Devices SDK](speech-devices-sdk.md)
- [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
- [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zamiany mowy na tekst](speech-to-text-basics.md) 
>  [Wprowadzenie do zamiany tekstu na mowę](get-started-text-to-speech.md)

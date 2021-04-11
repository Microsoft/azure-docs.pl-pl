---
title: Instalowanie i uruchamianie kontenerów platformy Docker dla interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Użyj kontenerów platformy Docker do interfejs API analizy tekstu, aby przeprowadzić przetwarzanie w języku naturalnym, takie jak analiza tonacji, lokalnie.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
keywords: Analiza lokalna, Docker, kontener, tonacji, przetwarzanie języka naturalnego
ms.openlocfilehash: 012e725e31097af5af634a1aba7693048c4c6b3e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277474"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalowanie i uruchamianie kontenerów analizy tekstu

> [!NOTE]
> * Kontener dla analiza tonacji i wykrywania języka jest teraz ogólnie dostępny. Kontener wyodrębniania fraz klucza jest dostępny jako niezależna publiczna wersja zapoznawcza.
> * Łączenie jednostek i NER nie są obecnie dostępne jako kontener.
> * Dostęp do analiza tekstu dla kontenera kondycji wymaga [formularza żądania](https://aka.ms/csgate). Obecnie nie są naliczane opłaty za użycie.
> * Lokalizacje obrazu kontenera mogły być ostatnio zmienione. Przeczytaj ten artykuł, aby wyświetlić zaktualizowaną lokalizację dla tego kontenera.

Kontenery umożliwiają uruchamianie interfejsów API analizy tekstu we własnym środowisku i doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. Kontenery analiza tekstu zapewniają zaawansowane przetwarzanie języka naturalnego w przypadku nieprzetworzonego tekstu i zawierają trzy główne funkcje: tonacji Analysis, wyodrębnianie kluczowych fraz i wykrywanie języka. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

> [!IMPORTANT]
> Bezpłatne konto jest ograniczone do 5 000 transakcji miesięcznie, a tylko <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">warstwy cenowe</a> **bezpłatna** i **standardowa** są prawidłowe dla kontenerów. Aby uzyskać więcej informacji na temat stawek żądań transakcji, zobacz [limity danych](../concepts/data-limits.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić dowolny z kontenerów analiza tekstu, należy mieć komputer hosta i środowiska kontenerów.

## <a name="preparation"></a>Przygotowanie

Przed użyciem kontenerów analiza tekstu należy spełnić następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows** program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.| 
|Zasób analiza tekstu |Aby można było używać kontenera, musisz mieć:<br><br>[Zasób analiza tekstu](../../cognitive-services-apis-create-account.md) platformy Azure z bezpłatną [warstwą cenową](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)(F0) lub standardową. Musisz uzyskać skojarzony klucz interfejsu API i identyfikator URI punktu końcowego, przechodząc do strony **klucz zasobu i punkt końcowy** w Azure Portal. <br><br>**{API_KEY}**: jeden z dwóch dostępnych kluczy zasobów. <br><br>**{ENDPOINT_URI}**: punkt końcowy zasobu. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

Jeśli używasz analiza tekstu dla kontenera kondycji, odpowiednie potwierdzenie [AI](https://docs.microsoft.com/legal/cognitive-services/text-analytics/transparency-note-health)  (Rai) muszą również być obecne z wartością `accept` .

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

W poniższej tabeli opisano minimalne i zalecane specyfikacje kontenerów analiza tekstu. Wymagane są co najmniej 2 gigabajty (GB) pamięci, a każdy rdzeń procesora CPU musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy. Są również wyświetlane listy dozwolonych transakcji na sekcję (TPS).

|  | Minimalne specyfikacje hosta | Zalecane specyfikacje hosta | Minimalna TPS | Maksymalna TPS|
|---|---------|-------------|--|--|
| **Wykrywanie języka, wyodrębnianie kluczowych fraz**   | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci |15 | 30|
| **Analiza tonacji**   | 1 rdzeń, 2 GB pamięci | 4 rdzenie, 8 GB pamięci |15 | 30|
| **Analiza tekstu w celu uzyskania kondycji 1 dokumentu/żądania**   |  4 rdzeń, 10 GB pamięci | 6 rdzeń, pamięć 12GB |15 | 30|
| **Analiza tekstu dla dokumentów/żądań o kondycji 10**   |  6 rdzeń, pamięć 16GB | 8 rdzeni, pamięć 20 GB |15 | 30|

Rdzeń procesora CPU i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Obrazy kontenerów dla analiza tekstu są dostępne w programie Microsoft Container Registry.

# <a name="sentiment-analysis"></a>[analiza tonacji ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Wyodrębnianie kluczowych fraz (wersja zapoznawcza)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Analiza tekstu dla kondycji (wersja zapoznawcza)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontenery. Kontener będzie nadal działać do momentu jego zatrzymania.

> [!IMPORTANT]
> * Polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\` jako znaku kontynuacji wiersza. Zastąp lub usuń to w zależności od wymagań systemu operacyjnego hosta. 
> * `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).
> * Kontenery analizy tonacji i wykrywania języka są ogólnie dostępne. Kontener wyodrębniania fraz klucza używa wersji 2 interfejsu API i jest w wersji zapoznawczej.

# <a name="sentiment-analysis"></a>[Analiza tonacji](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Wyodrębnianie kluczowych fraz (wersja zapoznawcza)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Wykrywanie języka](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Analiza tekstu dla kondycji (wersja zapoznawcza)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań.

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](../text-analytics-resource-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery analiza tekstu wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _Analiza tekstu_ na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów analiza tekstu. Podsumowanie:

* Analiza tekstu oferuje trzy kontenery systemu Linux dla platformy Docker, hermetyzowając różne możliwości:
   * *Analiza tonacji*
   * *Wyodrębnianie kluczowych fraz (wersja zapoznawcza)* 
   * *Wykrywanie języka*
   * *Analiza tekstu dla kondycji (wersja zapoznawcza)*
* Obrazy kontenerów są pobierane z programu Microsoft Container Registry (MCR) lub w wersji zapoznawczej repozytorium kontenerów.
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach analiza tekstu, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (np. tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](../text-analytics-resource-container-config.md) dla ustawień konfiguracji
* Zapoznaj się z [często zadawanymi pytaniami](../text-analytics-resource-faq.md) , aby rozwiązać problemy związane z działaniem.

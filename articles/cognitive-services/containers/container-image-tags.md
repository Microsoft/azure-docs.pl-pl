---
title: Tagi obrazu kontenera usług Cognitive Services
titleSuffix: Azure Cognitive Services
description: Kompleksowa lista wszystkich tagów obrazu kontenera usługi poznawczej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 0efb278c217ede94f67b47250633622501a24414
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412548"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Informacje o wersji i znacznikach obrazu kontenera Cognitive Services platformy Azure

Usługa Azure Cognitive Services oferuje wiele obrazów kontenerów. Rejestry kontenerów i odpowiednie repozytoria różnią się między obrazami kontenerów. Każda nazwa obrazu kontenera oferuje wiele tagów. Tag obrazu kontenera jest mechanizmem przechowywania wersji obrazu kontenera. Ten artykuł jest przeznaczony do użycia jako wyczerpujące odwołanie do wyświetlania wszystkich Cognitive Services obrazów kontenerów i ich dostępnych tagów.

> [!TIP]
> W przypadku korzystania z programu [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) należy zwrócić uwagę na wielkość liter w rejestrze kontenerów, repozytorium, nazwę obrazu kontenera i odpowiedni tag — w miarę **uwzględniania wielkości** liter.

## <a name="anomaly-detector"></a>Narzędzie do wykrywania anomalii

Obraz kontenera [wykrywania anomalii][ad-containers] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/decision` repozytorium i ma nazwę `anomaly-detector` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Poprzednie wersje](#tab/previous)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Odczytaj OCR (optyczne rozpoznawanie znaków)

Obraz kontenera OCR do [Przetwarzanie obrazów][cv-containers] odczytu można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services` repozytorium i ma nazwę `read` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Informacje o wersji dla `v2.0.013250001-amd64-preview` :

* Dalsze zmniejszenie użycia pamięci dla kontenera.
* Zewnętrzna pamięć podręczna jest wymagana w przypadku instalacji wielowymiarowej. Na przykład skonfiguruj Redis do buforowania.
* Naprawione brakujące wyniki, gdy pamięć podręczna Redis jest skonfigurowana i `ResultExpirationPeriod` jest ustawiona na 0.
* Usuń ograniczenie rozmiaru treści żądania 26MB. Kontener może teraz akceptować >pliki 26MB.
* Dodaj sygnaturę czasową i skompiluj wersję do rejestrowania w konsoli.

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Poprzednie wersje](#tab/previous)

Informacje o wersji dla programu `1.1.013050001-amd64-preview`

* Dodano `ReadEngineConfig:ResultExpirationPeriod` konfigurację inicjowania kontenera, aby określić, kiedy system ma czyścić wyniki rozpoznawania. 
    * Ustawienie jest w godzinach, a wartość domyślna to 48 godzin.
    * Ustawienie może zmniejszyć użycie pamięci na potrzeby przechowywania wyników, szczególnie w przypadku używania kontenera magazynu w pamięci.
    * Przykład 1. ReadEngineConfig: ResultExpirationPeriod = 1, system wyczyści wynik rozpoznawania 1hr po procesie.
    * Jeśli ta konfiguracja jest ustawiona na 0, system wyczyści wynik rozpoznawania po pobraniu wyniku.

* Naprawiono błąd wewnętrzny serwera 500 podczas przekazywania nieprawidłowego formatu obrazu do systemu. Zostanie teraz zwrócony błąd 400:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Rozpoznawanie formularzy

Obraz kontenera [aparatu rozpoznawania formularzy][fr-containers] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/custom-form` repozytorium i ma nazwę `labeltool` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Poprzednie wersje](#tab/previous)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

Obraz kontenera [Luis][lu-containers] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/language` repozytorium i ma nazwę `luis` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech do tekstu

Obraz kontenera [Custom Speech-to-Text][sp-cstt] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `custom-speech-to-text` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Uwaga dotycząca wersji `2.6.0-amd64` :

**Funkcje**
* Obsługa PhraseList v2 
* Listy fraz są obsługiwane w następujących ustawieniach regionalnych:
    * EN-au
    * EN-CA
    * pl GB
    * dodatek EN-in
    * EN-NZ
    * en-us
    * zh-cn
* Obsługa pobierania niestandardowego modelu podstawowego. 
    * Używanie `BaseModelLocale=<locale>` z `docker run` poleceniem
* Pełna migracja do programu .NET 3,1

**Prefix**
* Rozwiązano problem polegający na tym, że wynik pewności był zawsze 1 w trybie Diarization
* Przeprowadzono migrację do interfejsu API textanalytics 3,0

Zwróć uwagę na to, że ze względu na listy uwzględnionych fraz rozmiar tego obrazu kontenera wzrósł.

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

Uwaga dotycząca wersji `2.5.0-amd64` :

**Funkcje**
* Obsługa niestandardowych wymowy w modelach niestandardowych
* Obsługa platformy Azure i chmury dla instytucji rządowych USA

**Prefix**
* Naprawa przebiegu z powodu problemu z niegłównym użytkownikiem w trybie Diarization

| Tagi obrazu                    | Uwagi               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   Wersja 1.    |

---

## <a name="custom-text-to-speech"></a>Niestandardowa Zamiana tekstu na mowę

Niestandardowy obraz kontenera [zamiany tekstu na mowę][sp-ctts] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `custom-text-to-speech` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Uwaga dotycząca wersji `1.8.0-amd64` :

**Funkcje**
* Pełna migracja do programu .NET 3,1

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

Uwaga dotycząca wersji `1.7.0-amd64` :

**Funkcja**
* Częściowo zmigrowane do programu .NET 3,1

| Tagi obrazu                    | Uwagi               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   Wersja 1.    |

---

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Obraz kontenera [zamiany mowy na tekst][sp-stt] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `speech-to-text` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Pełną listę tagów można znaleźć [w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Ponieważ funkcja zamiany mowy na tekst v 2.5.0, obrazy są obsługiwane w regionie *instytucji rządowych Stanów Zjednoczonych Wirginia* . W przypadku korzystania z tego regionu należy użyć punktu końcowego rozliczeń *Wirginia dla instytucji rządowych USA* i kluczy interfejsu API.

# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Uwaga dotycząca wersji `2.6.0-amd64-<locale>` :

**Funkcje**
* Uaktualniono do najnowszych modeli i w pełni zmigrowane do programu .NET 3,1
* Obsługa PhraseList v2
* Listy fraz są obsługiwane w następujących ustawieniach regionalnych:
    * EN-au
    * EN-CA
    * pl GB
    * dodatek EN-in
    * EN-NZ
    * en-us
    * zh-cn
* Obsługa nowych ustawień regionalnych `cs-CZ` 
    * Wielkie litery i interpunkcja nie są obecnie obsługiwane.

**Prefix**
* Rozwiązuje problem polegający na tym, że wyniki pewności były zawsze 1 w trybie Diarization
* Migracja przy użyciu interfejsu API textanalytics 3,0

Zwróć uwagę na to, że ze względu na listy uwzględnionych fraz rozmiar tego obrazu kontenera wzrósł. 

| Tagi obrazu                    | Uwagi                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Obraz kontenera z `en-US` ustawieniami regionalnymi.                                                             |
| `2.6.0-amd64-<locale>`        | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `2.6.0-amd64-en-us`. |

Dla tego kontenera dostępne są następujące ustawienia regionalne.

| Ustawienia regionalne dla programu v 2.6.0           | Uwagi                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Obraz kontenera z `ar-AE` ustawieniami regionalnymi. |
| `ar-eg`                     | Obraz kontenera z `ar-EG` ustawieniami regionalnymi. |
| `ar-kw`                     | Obraz kontenera z `ar-KW` ustawieniami regionalnymi. |
| `ar-qa`                     | Obraz kontenera z `ar-QA` ustawieniami regionalnymi. |
| `ar-sa`                     | Obraz kontenera z `ar-SA` ustawieniami regionalnymi. |
| `ca-es`                     | Obraz kontenera z `ca-ES` ustawieniami regionalnymi. |
| `cs-cz`                     | Obraz kontenera z `cs-CZ` ustawieniami regionalnymi. |
| `da-dk`                     | Obraz kontenera z `da-DK` ustawieniami regionalnymi. |
| `de-de`                     | Obraz kontenera z `de-DE` ustawieniami regionalnymi. |
| `en-au`                     | Obraz kontenera z `en-AU` ustawieniami regionalnymi. |
| `en-ca`                     | Obraz kontenera z `en-CA` ustawieniami regionalnymi. |
| `en-gb`                     | Obraz kontenera z `en-GB` ustawieniami regionalnymi. |
| `en-in`                     | Obraz kontenera z `en-IN` ustawieniami regionalnymi. |
| `en-nz`                     | Obraz kontenera z `en-NZ` ustawieniami regionalnymi. |
| `en-us`                     | Obraz kontenera z `en-US` ustawieniami regionalnymi. |
| `es-es`                     | Obraz kontenera z `es-ES` ustawieniami regionalnymi. |
| `es-mx`                     | Obraz kontenera z `es-MX` ustawieniami regionalnymi. |
| `fi-fi`                     | Obraz kontenera z `fi-FI` ustawieniami regionalnymi. |
| `fr-ca`                     | Obraz kontenera z `fr-CA` ustawieniami regionalnymi. |
| `fr-fr`                     | Obraz kontenera z `fr-FR` ustawieniami regionalnymi. |
| `gu-in`                     | Obraz kontenera z `gu-IN` ustawieniami regionalnymi. |
| `hi-in`                     | Obraz kontenera z `hi-IN` ustawieniami regionalnymi. |
| `it-it`                     | Obraz kontenera z `it-IT` ustawieniami regionalnymi. |
| `ja-jp`                     | Obraz kontenera z `ja-JP` ustawieniami regionalnymi. |
| `ko-kr`                     | Obraz kontenera z `ko-KR` ustawieniami regionalnymi. |
| `mr-in`                     | Obraz kontenera z `mr-IN` ustawieniami regionalnymi. |
| `nb-no`                     | Obraz kontenera z `nb-NO` ustawieniami regionalnymi. |
| `nl-nl`                     | Obraz kontenera z `nl-NL` ustawieniami regionalnymi. |
| `pl-pl`                     | Obraz kontenera z `pl-PL` ustawieniami regionalnymi. |
| `pt-br`                     | Obraz kontenera z `pt-BR` ustawieniami regionalnymi. |
| `pt-pt`                     | Obraz kontenera z `pt-PT` ustawieniami regionalnymi. |
| `ru-ru`                     | Obraz kontenera z `ru-RU` ustawieniami regionalnymi. |
| `sv-se`                     | Obraz kontenera z `sv-SE` ustawieniami regionalnymi. |
| `ta-in`                     | Obraz kontenera z `ta-IN` ustawieniami regionalnymi. |
| `te-in`                     | Obraz kontenera z `te-IN` ustawieniami regionalnymi. |
| `th-th`                     | Obraz kontenera z `th-TH` ustawieniami regionalnymi. |
| `tr-tr`                     | Obraz kontenera z `tr-TR` ustawieniami regionalnymi. |
| `zh-cn`                     | Obraz kontenera z `zh-CN` ustawieniami regionalnymi. |
| `zh-hk`                     | Obraz kontenera z `zh-HK` ustawieniami regionalnymi. |
| `zh-tw`                     | Obraz kontenera z `zh-TW` ustawieniami regionalnymi. |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

Uwaga dotycząca wersji `2.5.0-amd64-<locale>` :

**Funkcje**
* Obsługa chmury dla instytucji rządowych USA platformy Azure

**Prefix**
* Rozwiązuje problem z uruchamianiem jako użytkownik spoza katalogu głównego w trybie Diarization

| Tagi obrazu                  | Uwagi                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `2.5.0-amd64-en-us`.  |

Dla tego kontenera dostępne są następujące ustawienia regionalne.

| Ustawienia regionalne dla programu v 2.5.0           | Uwagi                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Obraz kontenera z `ar-AE` ustawieniami regionalnymi. |
| `ar-eg`                     | Obraz kontenera z `ar-EG` ustawieniami regionalnymi. |
| `ar-kw`                     | Obraz kontenera z `ar-KW` ustawieniami regionalnymi. |
| `ar-qa`                     | Obraz kontenera z `ar-QA` ustawieniami regionalnymi. |
| `ar-sa`                     | Obraz kontenera z `ar-SA` ustawieniami regionalnymi. |
| `ca-es`                     | Obraz kontenera z `ca-ES` ustawieniami regionalnymi. |
| `da-dk`                     | Obraz kontenera z `da-DK` ustawieniami regionalnymi. |
| `de-de`                     | Obraz kontenera z `de-DE` ustawieniami regionalnymi. |
| `en-au`                     | Obraz kontenera z `en-AU` ustawieniami regionalnymi. |
| `en-ca`                     | Obraz kontenera z `en-CA` ustawieniami regionalnymi. |
| `en-gb`                     | Obraz kontenera z `en-GB` ustawieniami regionalnymi. |
| `en-in`                     | Obraz kontenera z `en-IN` ustawieniami regionalnymi. |
| `en-nz`                     | Obraz kontenera z `en-NZ` ustawieniami regionalnymi. |
| `en-us`                     | Obraz kontenera z `en-US` ustawieniami regionalnymi. |
| `es-es`                     | Obraz kontenera z `es-ES` ustawieniami regionalnymi. |
| `es-mx`                     | Obraz kontenera z `es-MX` ustawieniami regionalnymi. |
| `fi-fi`                     | Obraz kontenera z `fi-FI` ustawieniami regionalnymi. |
| `fr-ca`                     | Obraz kontenera z `fr-CA` ustawieniami regionalnymi. |
| `fr-fr`                     | Obraz kontenera z `fr-FR` ustawieniami regionalnymi. |
| `gu-in`                     | Obraz kontenera z `gu-IN` ustawieniami regionalnymi. |
| `hi-in`                     | Obraz kontenera z `hi-IN` ustawieniami regionalnymi. |
| `it-it`                     | Obraz kontenera z `it-IT` ustawieniami regionalnymi. |
| `ja-jp`                     | Obraz kontenera z `ja-JP` ustawieniami regionalnymi. |
| `ko-kr`                     | Obraz kontenera z `ko-KR` ustawieniami regionalnymi. |
| `mr-in`                     | Obraz kontenera z `mr-IN` ustawieniami regionalnymi. |
| `nb-no`                     | Obraz kontenera z `nb-NO` ustawieniami regionalnymi. |
| `nl-nl`                     | Obraz kontenera z `nl-NL` ustawieniami regionalnymi. |
| `pl-pl`                     | Obraz kontenera z `pl-PL` ustawieniami regionalnymi. |
| `pt-br`                     | Obraz kontenera z `pt-BR` ustawieniami regionalnymi. |
| `pt-pt`                     | Obraz kontenera z `pt-PT` ustawieniami regionalnymi. |
| `ru-ru`                     | Obraz kontenera z `ru-RU` ustawieniami regionalnymi. |
| `sv-se`                     | Obraz kontenera z `sv-SE` ustawieniami regionalnymi. |
| `ta-in`                     | Obraz kontenera z `ta-IN` ustawieniami regionalnymi. |
| `te-in`                     | Obraz kontenera z `te-IN` ustawieniami regionalnymi. |
| `th-th`                     | Obraz kontenera z `th-TH` ustawieniami regionalnymi. |
| `tr-tr`                     | Obraz kontenera z `tr-TR` ustawieniami regionalnymi. |
| `zh-cn`                     | Obraz kontenera z `zh-CN` ustawieniami regionalnymi. |
| `zh-hk`                     | Obraz kontenera z `zh-HK` ustawieniami regionalnymi. |
| `zh-tw`                     | Obraz kontenera z `zh-TW` ustawieniami regionalnymi. |

---

## <a name="text-to-speech"></a>Zamiana tekstu na mowę

Obraz kontenera [zamiany tekstu na mowę][sp-tts] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `text-to-speech` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Uwaga dotycząca wersji `1.8.0-amd64-<locale-and-voice>` :

**Funkcja**
* Pełna migracja do programu .NET 3,1

| Tagi obrazu                                  | Uwagi                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaRUS` głosem.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `1.8.0-amd64-en-us-ariarus`.  |


| Ustawienia regionalne dla 1.8.0 v                          | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Obraz kontenera z `ar-EG` ustawieniami regionalnymi i `ar-EG-Hoda` głosem.            |
| `ar-sa-naayf`                               | Obraz kontenera z `ar-SA` ustawieniami regionalnymi i `ar-SA-Naayf` głosem.           |
| `bg-bg-ivan`                                | Obraz kontenera z `bg-BG` ustawieniami regionalnymi i `bg-BG-Ivan` głosem.            |
| `ca-es-herenarus`                           | Obraz kontenera z `ca-ES` ustawieniami regionalnymi i `ca-ES-HerenaRUS` głosem.       |
| `cs-cz-jakub`                               | Obraz kontenera z `cs-CZ` ustawieniami regionalnymi i `cs-CZ-Jakub` głosem.           |
| `da-dk-hellerus`                            | Obraz kontenera z `da-DK` ustawieniami regionalnymi i `da-DK-HelleRUS` głosem.        |
| `de-at-michael`                             | Obraz kontenera z `de-AT` ustawieniami regionalnymi i `de-AT-Michael` głosem.         |
| `de-ch-karsten`                             | Obraz kontenera z `de-CH` ustawieniami regionalnymi i `de-CH-Karsten` głosem.         |
| `de-de-hedda`                               | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Hedda` głosem.           |
| `de-de-heddarus`                            | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Hedda` głosem.           |
| `de-de-stefan-apollo`                       | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Stefan-Apollo` głosem.   |
| `el-gr-stefanos`                            | Obraz kontenera z `el-GR` ustawieniami regionalnymi i `el-GR-Stefanos` głosem.        |
| `en-au-catherine`                           | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-Catherine` głosem.       |
| `en-au-hayleyrus`                           | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-HayleyRUS` głosem.       |
| `en-ca-heatherrus`                          | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-HeatherRUS` głosem.      |
| `en-ca-linda`                               | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-Linda` głosem.           |
| `en-gb-george-apollo`                       | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-George-Apollo` głosem.   |
| `en-gb-hazelrus`                            | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-HazelRUS` głosem.        |
| `en-gb-susan-apollo`                        | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-Susan-Apollo` głosem.    |
| `en-ie-sean`                                | Obraz kontenera z `en-IE` ustawieniami regionalnymi i `en-IE-Sean` głosem.            |
| `en-in-heera-apollo`                        | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-Heera-Apollo` głosem.    |
| `en-in-priyarus`                            | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-PriyaRUS` głosem.        |
| `en-in-ravi-apollo`                         | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-Ravi-Apollo` głosem.     |
| `en-us-benjaminrus`                         | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-BenjaminRUS` głosem.     |
| `en-us-guy24krus`                           | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-Guy24kRUS` głosem.       |
| `en-us-aria24krus`                          | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-Aria24kRUS` głosem.      |
| `en-us-ariarus`                             | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaRUS` głosem.         |
| `en-us-zirarus`                             | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-ZiraRUS` głosem.         |
| `es-es-helenarus`                           | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-HelenaRUS` głosem.       |
| `es-es-laura-apollo`                        | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-Laura-Apollo` głosem.    |
| `es-es-pablo-apollo`                        | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-Pablo-Apollo` głosem.    |
| `es-mx-hildarus`                            | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-HildaRUS` głosem.        |
| `es-mx-raul-apollo`                         | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-Raul-Apollo` głosem.     |
| `fi-fi-heidirus`                            | Obraz kontenera z `fi-FI` ustawieniami regionalnymi i `fi-FI-HeidiRUS` głosem.        |
| `fr-ca-caroline`                            | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-Caroline` głosem.        |
| `fr-ca-harmonierus`                         | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-HarmonieRUS` głosem.     |
| `fr-ch-guillaume`                           | Obraz kontenera z `fr-CH` ustawieniami regionalnymi i `fr-CH-Guillaume` głosem.       |
| `fr-fr-hortenserus`                         | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-HortenseRUS` głosem.     |
| `fr-fr-julie-apollo`                        | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-Julie-Apollo` głosem.    |
| `fr-fr-paul-apollo`                         | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-Paul-Apollo` głosem.     |
| `he-il-asaf`                                | Obraz kontenera z `he-IL` ustawieniami regionalnymi i `he-IL-Asaf` głosem.            |
| `hi-in-hemant`                              | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Hemant` głosem.          |
| `hi-in-kalpana-apollo`                      | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Kalpana-Apollo` głosem.  |
| `hi-in-kalpana`                             | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Kalpana` głosem.         |
| `hr-hr-matej`                               | Obraz kontenera z `hr-HR` ustawieniami regionalnymi i `hr-HR-Matej` głosem.           |
| `hu-hu-szabolcs`                            | Obraz kontenera z `hu-HU` ustawieniami regionalnymi i `hu-HU-Szabolcs` głosem.        |
| `id-id-andika`                              | Obraz kontenera z `id-ID` ustawieniami regionalnymi i `id-ID-Andika` głosem.          |
| `it-it-cosimo-apollo`                       | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-Cosimo-Apollo` głosem.   |
| `it-it-luciarus`                            | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-LuciaRUS` głosem.        |
| `ja-jp-ayumi-apollo`                        | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-Ayumi-Apollo` głosem.    |
| `ja-jp-harukarus`                           | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-HarukaRUS` głosem.       |
| `ja-jp-ichiro-apollo`                       | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-Ichiro-Apollo` głosem.   |
| `ko-kr-heamirus`                            | Obraz kontenera z `ko-KR` ustawieniami regionalnymi i `ko-KR-HeamiRUS` głosem.        |
| `ms-my-rizwan`                              | Obraz kontenera z `ms-MY` ustawieniami regionalnymi i `ms-MY-Rizwan` głosem.          |
| `nb-no-huldarus`                            | Obraz kontenera z `nb-NO` ustawieniami regionalnymi i `nb-NO-HuldaRUS` głosem.        |
| `nl-nl-hannarus`                            | Obraz kontenera z `nl-NL` ustawieniami regionalnymi i `nl-NL-HannaRUS` głosem.        |
| `pl-pl-paulinarus`                          | Obraz kontenera z `pl-PL` ustawieniami regionalnymi i `pl-PL-PaulinaRUS` głosem.      |
| `pt-br-daniel-apollo`                       | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-Daniel-Apollo` głosem.   |
| `pt-br-heloisarus`                          | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-HeloisaRUS` głosem.      |
| `pt-pt-heliarus`                            | Obraz kontenera z `pt-PT` ustawieniami regionalnymi i `pt-PT-HeliaRUS` głosem.        |
| `ro-ro-andrei`                              | Obraz kontenera z `ro-RO` ustawieniami regionalnymi i `ro-RO-Andrei` głosem.          |
| `ru-ru-ekaterinarus`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-EkaterinaRUS` głosem.    |
| `ru-ru-irina-apollo`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-Irina-Apollo` głosem.    |
| `ru-ru-pavel-apollo`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-Pavel-Apollo` głosem.    |
| `sk-sk-filip`                               | Obraz kontenera z `sk-SK` ustawieniami regionalnymi i `sk-SK-Filip` głosem.           |
| `sl-si-lado`                                | Obraz kontenera z `sl-SI` ustawieniami regionalnymi i `sl-SI-Lado` głosem.            |
| `sv-se-hedvigrus`                           | Obraz kontenera z `sv-SE` ustawieniami regionalnymi i `sv-SE-HedvigRUS` głosem.       |
| `ta-in-valluvar`                            | Obraz kontenera z `ta-IN` ustawieniami regionalnymi i `ta-IN-Valluvar` głosem.        |
| `te-in-chitra`                              | Obraz kontenera z `te-IN` ustawieniami regionalnymi i `te-IN-Chitra` głosem.          |
| `th-th-pattara`                             | Obraz kontenera z `th-TH` ustawieniami regionalnymi i `th-TH-Pattara` głosem.         |
| `tr-tr-sedarus`                             | Obraz kontenera z `tr-TR` ustawieniami regionalnymi i `tr-TR-SedaRUS` głosem.         |
| `vi-vn-an`                                  | Obraz kontenera z `vi-VN` ustawieniami regionalnymi i `vi-VN-An` głosem.              |
| `zh-cn-huihuirus`                           | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-HuihuiRUS` głosem.       |
| `zh-cn-kangkang-apollo`                     | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-Kangkang-Apollo` głosem. |
| `zh-cn-yaoyao-apollo`                       | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-Yaoyao-Apollo` głosem.   |
| `zh-hk-danny-apollo`                        | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-Danny-Apollo` głosem.    |
| `zh-hk-tracy-apollo`                        | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-Tracy-Apollo` głosem.    |
| `zh-hk-tracyrus`                            | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-TracyRUS` głosem.        |
| `zh-tw-hanhanrus`                           | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-HanHanRUS` głosem.       |
| `zh-tw-yating-apollo`                       | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-Yating-Apollo` głosem.   |
| `zh-tw-zhiwei-apollo`                       | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-Zhiwei-Apollo` głosem.   |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

Uwaga dotycząca wersji `1.7.0-amd64-<locale-and-voice>` :

**Funkcja**
* Uaktualnione składniki do programu .NET 3,1

| Tagi obrazu                                  | Uwagi                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | Wersja 1. Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `1.7.0-amd64-en-us-ariarus`.  |


| Ustawienia regionalne dla 1.7.0 v                          | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Obraz kontenera z `ar-EG` ustawieniami regionalnymi i `ar-EG-Hoda` głosem.            |
| `ar-sa-naayf`                               | Obraz kontenera z `ar-SA` ustawieniami regionalnymi i `ar-SA-Naayf` głosem.           |
| `bg-bg-ivan`                                | Obraz kontenera z `bg-BG` ustawieniami regionalnymi i `bg-BG-Ivan` głosem.            |
| `ca-es-herenarus`                           | Obraz kontenera z `ca-ES` ustawieniami regionalnymi i `ca-ES-HerenaRUS` głosem.       |
| `cs-cz-jakub`                               | Obraz kontenera z `cs-CZ` ustawieniami regionalnymi i `cs-CZ-Jakub` głosem.           |
| `da-dk-hellerus`                            | Obraz kontenera z `da-DK` ustawieniami regionalnymi i `da-DK-HelleRUS` głosem.        |
| `de-at-michael`                             | Obraz kontenera z `de-AT` ustawieniami regionalnymi i `de-AT-Michael` głosem.         |
| `de-ch-karsten`                             | Obraz kontenera z `de-CH` ustawieniami regionalnymi i `de-CH-Karsten` głosem.         |
| `de-de-hedda`                               | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Hedda` głosem.           |
| `de-de-heddarus`                            | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Hedda` głosem.           |
| `de-de-stefan-apollo`                       | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Stefan-Apollo` głosem.   |
| `el-gr-stefanos`                            | Obraz kontenera z `el-GR` ustawieniami regionalnymi i `el-GR-Stefanos` głosem.        |
| `en-au-catherine`                           | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-Catherine` głosem.       |
| `en-au-hayleyrus`                           | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-HayleyRUS` głosem.       |
| `en-ca-heatherrus`                          | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-HeatherRUS` głosem.      |
| `en-ca-linda`                               | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-Linda` głosem.           |
| `en-gb-george-apollo`                       | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-George-Apollo` głosem.   |
| `en-gb-hazelrus`                            | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-HazelRUS` głosem.        |
| `en-gb-susan-apollo`                        | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-Susan-Apollo` głosem.    |
| `en-ie-sean`                                | Obraz kontenera z `en-IE` ustawieniami regionalnymi i `en-IE-Sean` głosem.            |
| `en-in-heera-apollo`                        | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-Heera-Apollo` głosem.    |
| `en-in-priyarus`                            | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-PriyaRUS` głosem.        |
| `en-in-ravi-apollo`                         | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-Ravi-Apollo` głosem.     |
| `en-us-benjaminrus`                         | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-BenjaminRUS` głosem.     |
| `en-us-guy24krus`                           | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-Guy24kRUS` głosem.       |
| `en-us-aria24krus`                          | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-Aria24kRUS` głosem.      |
| `en-us-ariarus`                             | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaRUS` głosem.         |
| `en-us-zirarus`                             | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-ZiraRUS` głosem.         |
| `es-es-helenarus`                           | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-HelenaRUS` głosem.       |
| `es-es-laura-apollo`                        | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-Laura-Apollo` głosem.    |
| `es-es-pablo-apollo`                        | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-Pablo-Apollo` głosem.    |
| `es-mx-hildarus`                            | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-HildaRUS` głosem.        |
| `es-mx-raul-apollo`                         | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-Raul-Apollo` głosem.     |
| `fi-fi-heidirus`                            | Obraz kontenera z `fi-FI` ustawieniami regionalnymi i `fi-FI-HeidiRUS` głosem.        |
| `fr-ca-caroline`                            | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-Caroline` głosem.        |
| `fr-ca-harmonierus`                         | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-HarmonieRUS` głosem.     |
| `fr-ch-guillaume`                           | Obraz kontenera z `fr-CH` ustawieniami regionalnymi i `fr-CH-Guillaume` głosem.       |
| `fr-fr-hortenserus`                         | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-HortenseRUS` głosem.     |
| `fr-fr-julie-apollo`                        | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-Julie-Apollo` głosem.    |
| `fr-fr-paul-apollo`                         | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-Paul-Apollo` głosem.     |
| `he-il-asaf`                                | Obraz kontenera z `he-IL` ustawieniami regionalnymi i `he-IL-Asaf` głosem.            |
| `hi-in-hemant`                              | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Hemant` głosem.          |
| `hi-in-kalpana-apollo`                      | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Kalpana-Apollo` głosem.  |
| `hi-in-kalpana`                             | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Kalpana` głosem.         |
| `hr-hr-matej`                               | Obraz kontenera z `hr-HR` ustawieniami regionalnymi i `hr-HR-Matej` głosem.           |
| `hu-hu-szabolcs`                            | Obraz kontenera z `hu-HU` ustawieniami regionalnymi i `hu-HU-Szabolcs` głosem.        |
| `id-id-andika`                              | Obraz kontenera z `id-ID` ustawieniami regionalnymi i `id-ID-Andika` głosem.          |
| `it-it-cosimo-apollo`                       | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-Cosimo-Apollo` głosem.   |
| `it-it-luciarus`                            | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-LuciaRUS` głosem.        |
| `ja-jp-ayumi-apollo`                        | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-Ayumi-Apollo` głosem.    |
| `ja-jp-harukarus`                           | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-HarukaRUS` głosem.       |
| `ja-jp-ichiro-apollo`                       | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-Ichiro-Apollo` głosem.   |
| `ko-kr-heamirus`                            | Obraz kontenera z `ko-KR` ustawieniami regionalnymi i `ko-KR-HeamiRUS` głosem.        |
| `ms-my-rizwan`                              | Obraz kontenera z `ms-MY` ustawieniami regionalnymi i `ms-MY-Rizwan` głosem.          |
| `nb-no-huldarus`                            | Obraz kontenera z `nb-NO` ustawieniami regionalnymi i `nb-NO-HuldaRUS` głosem.        |
| `nl-nl-hannarus`                            | Obraz kontenera z `nl-NL` ustawieniami regionalnymi i `nl-NL-HannaRUS` głosem.        |
| `pl-pl-paulinarus`                          | Obraz kontenera z `pl-PL` ustawieniami regionalnymi i `pl-PL-PaulinaRUS` głosem.      |
| `pt-br-daniel-apollo`                       | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-Daniel-Apollo` głosem.   |
| `pt-br-heloisarus`                          | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-HeloisaRUS` głosem.      |
| `pt-pt-heliarus`                            | Obraz kontenera z `pt-PT` ustawieniami regionalnymi i `pt-PT-HeliaRUS` głosem.        |
| `ro-ro-andrei`                              | Obraz kontenera z `ro-RO` ustawieniami regionalnymi i `ro-RO-Andrei` głosem.          |
| `ru-ru-ekaterinarus`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-EkaterinaRUS` głosem.    |
| `ru-ru-irina-apollo`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-Irina-Apollo` głosem.    |
| `ru-ru-pavel-apollo`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-Pavel-Apollo` głosem.    |
| `sk-sk-filip`                               | Obraz kontenera z `sk-SK` ustawieniami regionalnymi i `sk-SK-Filip` głosem.           |
| `sl-si-lado`                                | Obraz kontenera z `sl-SI` ustawieniami regionalnymi i `sl-SI-Lado` głosem.            |
| `sv-se-hedvigrus`                           | Obraz kontenera z `sv-SE` ustawieniami regionalnymi i `sv-SE-HedvigRUS` głosem.       |
| `ta-in-valluvar`                            | Obraz kontenera z `ta-IN` ustawieniami regionalnymi i `ta-IN-Valluvar` głosem.        |
| `te-in-chitra`                              | Obraz kontenera z `te-IN` ustawieniami regionalnymi i `te-IN-Chitra` głosem.          |
| `th-th-pattara`                             | Obraz kontenera z `th-TH` ustawieniami regionalnymi i `th-TH-Pattara` głosem.         |
| `tr-tr-sedarus`                             | Obraz kontenera z `tr-TR` ustawieniami regionalnymi i `tr-TR-SedaRUS` głosem.         |
| `vi-vn-an`                                  | Obraz kontenera z `vi-VN` ustawieniami regionalnymi i `vi-VN-An` głosem.              |
| `zh-cn-huihuirus`                           | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-HuihuiRUS` głosem.       |
| `zh-cn-kangkang-apollo`                     | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-Kangkang-Apollo` głosem. |
| `zh-cn-yaoyao-apollo`                       | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-Yaoyao-Apollo` głosem.   |
| `zh-hk-danny-apollo`                        | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-Danny-Apollo` głosem.    |
| `zh-hk-tracy-apollo`                        | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-Tracy-Apollo` głosem.    |
| `zh-hk-tracyrus`                            | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-TracyRUS` głosem.        |
| `zh-tw-hanhanrus`                           | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-HanHanRUS` głosem.       |
| `zh-tw-yating-apollo`                       | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-Yating-Apollo` głosem.   |
| `zh-tw-zhiwei-apollo`                       | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-Zhiwei-Apollo` głosem.   |

---

## <a name="neural-text-to-speech"></a>Neuronowych Zamiana tekstu na mowę

Obraz kontenera [neuronowych zamiany tekstu na mowę][sp-ntts] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `neural-text-to-speech` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).

| Tagi obrazu                                  | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaNeural` głosem.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `1.2.0-amd64-en-us-arianeural-preview`. |


| v 1.2.0 wersja zapoznawcza — elementy lokalne i głosy           | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaNeural` głosem.      |
| `de-de-katjaneural-preview`                 | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-KatjaNeural` głosem.     |
| `en-au-natashaneural-preview`               | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-NatashaNeural` głosem.   |
| `en-ca-claraneural-preview`                 | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-ClaraNeural` głosem.     |
| `en-gb-libbyneural-preview`                 | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-LibbyNeural` głosem.     |
| `en-gb-mianeural-preview`                   | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-MiaNeural` głosem.       |
| `en-us-arianeural-preview`                  | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaNeural` głosem.      |
| `en-us-guyneural-preview`                   | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-GuyNeural` głosem.       |
| `es-es-elviraneural-preview`                | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-ElviraNeural` głosem.    |
| `es-mx-dalianeural-preview`                 | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-DaliaNeural` głosem.     |
| `fr-ca-sylvieneural-preview`                | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-SylvieNeural` głosem.    |
| `fr-fr-deniseneural-preview`                | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-DeniseNeural` głosem.    |
| `it-it-elsaneural-preview`                  | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-ElsaNeural` głosem.      |
| `ja-jp-nanamineural-preview`                | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-NanamiNeural` głosem.    |
| `ko-kr-sunhineural-preview`                 | Obraz kontenera z `ko-KR` ustawieniami regionalnymi i `ko-KR-SunHiNeural` głosem.     |
| `pt-br-franciscaneural-preview`             | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-FranciscaNeural` głosem. |
| `zh-cn-xiaoxiaoneural-preview`              | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-XiaoxiaoNeural` głosem.  |

## <a name="speech-language-detection"></a>Wykrywanie języka mowy

Obraz kontenera [wykrywania języka mowy][sp-lid] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `language-detection` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Tagi obrazu                                  | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

obraz kontenera można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/textanalytics/` repozytorium i ma nazwę `keyphrase` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)


| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Poprzednie wersje](#tab/previous)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Wykrywanie języka tekstu

Obraz kontenera [wykrywanie języka][ta-la] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/textanalytics/` repozytorium i ma nazwę `language` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Najnowsze wersje](#tab/current)

| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Poprzednie wersje](#tab/previous)


| Tagi obrazu                    | Uwagi |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Analiza tonacji

Obraz kontenera [Analiza tonacji][ta-se] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/textanalytics/` repozytorium i ma nazwę `sentiment` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Ten obraz kontenera ma dostępne następujące Tagi. Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Tagi obrazu | Uwagi                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Analiza tonacji v3 (angielski)               |
| `3.0-es`   | Analiza tonacji v3 (hiszpański)               |
| `3.0-fr`   | Analiza tonacji v3 (francuski)                |
| `3.0-it`   | Analiza tonacji v3 (włoski)               |
| `3.0-de`   | Analiza tonacji v3 (niemiecki)                |
| `3.0-zh`   | Analiza tonacji v3 (chiński uproszczony)  |
| `3.0-zht`  | Analiza tonacji v3 (chiński tradycyjny) |
| `3.0-ja`   | Analiza tonacji v3 (japoński)              |
| `3.0-pt`   | Analiza tonacji v3 (portugalski)            |
| `3.0-nl`   | Analiza tonacji v3 (holenderski)                 |
| `2.1`    | analiza tonacji v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment

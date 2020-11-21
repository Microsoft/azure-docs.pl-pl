---
title: Tagi obrazu kontenera usług Cognitive Services
titleSuffix: Azure Cognitive Services
description: Kompleksowa lista wszystkich tagów obrazu kontenera usługi poznawczej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: 90ac4891dbca34ff2d9bdb9653e27ad2a495b317
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021256"
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

Uwaga dotycząca wersji `2.7.0-amd64` :

**Funkcje**
* Interpunkcja jest ustawiana domyślnie jako włączona.

Zwróć uwagę na to, że ze względu na listy uwzględnionych fraz rozmiar tego obrazu kontenera wzrósł.

| Tagi obrazu                    | Uwagi | Szyfrowane                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |
| `2.7.0-amd64`                 |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)
Uwaga dotycząca wersji `2.6.0-amd64` :

**Funkcje**
* Obsługa PhraseList v2 
* Listy fraz są obsługiwane w następujących ustawieniach regionalnych:
    * EN-au
    * EN-CA
    * pl GB
    * dodatek EN-in
    * en-us
    * zh-cn
* Obsługa pobierania niestandardowego modelu podstawowego. 
    * Używanie `BaseModelLocale=<locale>` z `docker run` poleceniem
* Pełna migracja do programu .NET 3,1

**Prefix**
* Rozwiązano problem polegający na tym, że wynik pewności był zawsze 1 w trybie Diarization
* Przeprowadzono migrację do interfejsu API textanalytics 3,0

Zwróć uwagę na to, że ze względu na listy uwzględnionych fraz rozmiar tego obrazu kontenera wzrósł.

Uwaga dotycząca wersji `2.5.0-amd64` :

**Funkcje**
* Obsługa niestandardowych wymowy w modelach niestandardowych
* Obsługa platformy Azure i chmury dla instytucji rządowych USA

**Prefix**
* Naprawa przebiegu z powodu problemu z niegłównym użytkownikiem w trybie Diarization

| Tagi obrazu                    | Uwagi               |
|-------------------------------|:--------------------|
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   Wersja 1.    |

---

## <a name="custom-text-to-speech"></a>Niestandardowa Zamiana tekstu na mowę

Niestandardowy obraz kontenera [zamiany tekstu na mowę][sp-ctts] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `custom-text-to-speech` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Możesz również znaleźć pełną listę [tagów w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Uwaga dotycząca wersji `1.9.0-amd64` :

Regularne comiesięczne wydanie

| Tagi obrazu                    | Uwagi | Szyfrowane                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |
| `1.9.0-amd64`                 |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)
Uwaga dotycząca wersji `1.8.0-amd64` :

**Funkcje**
* Pełna migracja do programu .NET 3,1

Uwaga dotycząca wersji `1.7.0-amd64` :

**Funkcja**
* Częściowo zmigrowane do programu .NET 3,1

| Tagi obrazu                    | Uwagi               |
|-------------------------------|:--------------------|
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   Wersja 1.    |

---

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Obraz kontenera [zamiany mowy na tekst][sp-stt] można znaleźć w obszarze `mcr.microsoft.com` syndykaty rejestru kontenerów. Znajduje się w `azure-cognitive-services/speechservices/` repozytorium i ma nazwę `speech-to-text` . W pełni kwalifikowana nazwa obrazu kontenera to, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Pełną listę tagów można znaleźć [w MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Ponieważ funkcja zamiany mowy na tekst v 2.5.0, obrazy są obsługiwane w regionie *instytucji rządowych Stanów Zjednoczonych Wirginia* . W przypadku korzystania z tego regionu należy użyć punktu końcowego rozliczeń *Wirginia dla instytucji rządowych USA* i kluczy interfejsu API.

# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Uwaga dotycząca wersji `2.7.0-amd64-<locale>` :

**Funkcje**
* Obsługa następujących nowych ustawień regionalnych:
    * AR-BH, cz-IQ, AR-Jo, AR-lb, AR-OM, AR-sy
    * bg-bg
    * el-gr
    * EN-HK, EN-IE, EN-pH, EN-so, EN-za
    * ES-AR, ES-bo, ES-CL, ES-es-CR, ES-cu, ES-do, ES-EC, ES-gt, ES-PA, ES-PE, ES-PR, ES-PR, es-US, ES-uy, ES-ve
    * et-ee
    * ga — IE
    * hr-hr
    * hu-hu
    * lt — lt
    * lv-lv
    * MT-Mt
    * ro-ro
    * sk-sk
    * SL — SL
* Interpunkcja jest domyślnie włączona.

Zwróć uwagę na to, że ze względu na listy uwzględnionych fraz rozmiar tego obrazu kontenera wzrósł. 

| Tagi obrazu                    | Uwagi                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Obraz kontenera z `en-US` ustawieniami regionalnymi.                                                             |
| `2.7.0-amd64-<locale>`        | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `2.7.0-amd64-en-us`. |

Dla tego kontenera dostępne są następujące ustawienia regionalne.

| Ustawienia regionalne dla programu v 2.7.0           | Uwagi                                    | Szyfrowane                                                                  |
|-----------------------------|:-----------------------------------------|:------------------------------------------------------------------------|
| `ar-ae`                     | Obraz kontenera z `ar-AE` ustawieniami regionalnymi. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | Obraz kontenera z `ar-BH` ustawieniami regionalnymi. | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | Obraz kontenera z `ar-EG` ustawieniami regionalnymi. | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | Obraz kontenera z `ar-IQ` ustawieniami regionalnymi. | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | Obraz kontenera z `ar-JO` ustawieniami regionalnymi. | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | Obraz kontenera z `ar-KW` ustawieniami regionalnymi. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | Obraz kontenera z `ar-LB` ustawieniami regionalnymi. | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | Obraz kontenera z `ar-OM` ustawieniami regionalnymi. | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | Obraz kontenera z `ar-QA` ustawieniami regionalnymi. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | Obraz kontenera z `ar-SA` ustawieniami regionalnymi. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | Obraz kontenera z `ar-SY` ustawieniami regionalnymi. | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | Obraz kontenera z `bg-BG` ustawieniami regionalnymi. | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | Obraz kontenera z `ca-ES` ustawieniami regionalnymi. | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | Obraz kontenera z `cs-CZ` ustawieniami regionalnymi. | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | Obraz kontenera z `da-DK` ustawieniami regionalnymi. | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | Obraz kontenera z `de-DE` ustawieniami regionalnymi. | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | Obraz kontenera z `el-GR` ustawieniami regionalnymi. | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | Obraz kontenera z `en-AU` ustawieniami regionalnymi. | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | Obraz kontenera z `en-CA` ustawieniami regionalnymi. | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | Obraz kontenera z `en-GB` ustawieniami regionalnymi. | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | Obraz kontenera z `en-HK` ustawieniami regionalnymi. | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | Obraz kontenera z `en-IE` ustawieniami regionalnymi. | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | Obraz kontenera z `en-IN` ustawieniami regionalnymi. | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | Obraz kontenera z `en-NZ` ustawieniami regionalnymi. | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | Obraz kontenera z `en-PH` ustawieniami regionalnymi. | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | Obraz kontenera z `en-SG` ustawieniami regionalnymi. | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | Obraz kontenera z `en-US` ustawieniami regionalnymi. | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | Obraz kontenera z `en-ZA` ustawieniami regionalnymi. | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | Obraz kontenera z `es-AR` ustawieniami regionalnymi. | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | Obraz kontenera z `es-BO` ustawieniami regionalnymi. | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | Obraz kontenera z `es-CL` ustawieniami regionalnymi. | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | Obraz kontenera z `es-CO` ustawieniami regionalnymi. | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | Obraz kontenera z `es-CR` ustawieniami regionalnymi. | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | Obraz kontenera z `es-CU` ustawieniami regionalnymi. | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | Obraz kontenera z `es-DO` ustawieniami regionalnymi. | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | Obraz kontenera z `es-EC` ustawieniami regionalnymi. | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | Obraz kontenera z `es-ES` ustawieniami regionalnymi. | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | Obraz kontenera z `es-GT` ustawieniami regionalnymi. | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | Obraz kontenera z `es-HN` ustawieniami regionalnymi. | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | Obraz kontenera z `es-MX` ustawieniami regionalnymi. | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | Obraz kontenera z `es-NI` ustawieniami regionalnymi. | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | Obraz kontenera z `es-PA` ustawieniami regionalnymi. | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | Obraz kontenera z `es-PE` ustawieniami regionalnymi. | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | Obraz kontenera z `es-PR` ustawieniami regionalnymi. | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | Obraz kontenera z `es-PY` ustawieniami regionalnymi. | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | Obraz kontenera z `es-SV` ustawieniami regionalnymi. | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | Obraz kontenera z `es-US` ustawieniami regionalnymi. | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | Obraz kontenera z `es-UY` ustawieniami regionalnymi. | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | Obraz kontenera z `es-VE` ustawieniami regionalnymi. | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | Obraz kontenera z `et-EE` ustawieniami regionalnymi. | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | Obraz kontenera z `fi-FI` ustawieniami regionalnymi. | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | Obraz kontenera z `fr-CA` ustawieniami regionalnymi. | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | Obraz kontenera z `fr-FR` ustawieniami regionalnymi. | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | Obraz kontenera z `ga-IE` ustawieniami regionalnymi. | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | Obraz kontenera z `gu-IN` ustawieniami regionalnymi. | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | Obraz kontenera z `hi-IN` ustawieniami regionalnymi. | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | Obraz kontenera z `hr-HR` ustawieniami regionalnymi. | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | Obraz kontenera z `hu-HU` ustawieniami regionalnymi. | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | Obraz kontenera z `it-IT` ustawieniami regionalnymi. | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | Obraz kontenera z `ja-JP` ustawieniami regionalnymi. | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | Obraz kontenera z `ko-KR` ustawieniami regionalnymi. | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | Obraz kontenera z `lt-LT` ustawieniami regionalnymi. | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | Obraz kontenera z `lv-LV` ustawieniami regionalnymi. | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | Obraz kontenera z `mr-IN` ustawieniami regionalnymi. | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | Obraz kontenera z `mt-MT` ustawieniami regionalnymi. | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | Obraz kontenera z `nb-NO` ustawieniami regionalnymi. | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | Obraz kontenera z `nl-NL` ustawieniami regionalnymi. | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | Obraz kontenera z `pl-PL` ustawieniami regionalnymi. | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | Obraz kontenera z `pt-BR` ustawieniami regionalnymi. | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | Obraz kontenera z `pt-PT` ustawieniami regionalnymi. | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | Obraz kontenera z `ro-RO` ustawieniami regionalnymi. | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | Obraz kontenera z `ru-RU` ustawieniami regionalnymi. | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | Obraz kontenera z `sk-SK` ustawieniami regionalnymi. | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | Obraz kontenera z `sl-SI` ustawieniami regionalnymi. | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | Obraz kontenera z `sv-SE` ustawieniami regionalnymi. | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | Obraz kontenera z `ta-IN` ustawieniami regionalnymi. | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | Obraz kontenera z `te-IN` ustawieniami regionalnymi. | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | Obraz kontenera z `th-TH` ustawieniami regionalnymi. | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | Obraz kontenera z `tr-TR` ustawieniami regionalnymi. | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | Obraz kontenera z `zh-CN` ustawieniami regionalnymi. | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | Obraz kontenera z `zh-HK` ustawieniami regionalnymi. | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | Obraz kontenera z `zh-TW` ustawieniami regionalnymi. | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

Uwaga dotycząca wersji `2.6.0-amd64-<locale>` :

**Funkcje**
* Uaktualniono do najnowszych modeli i w pełni zmigrowane do programu .NET 3,1
* Obsługa PhraseList v2
* Listy fraz są obsługiwane w następujących ustawieniach regionalnych:
    * EN-au
    * EN-CA
    * pl GB
    * dodatek EN-in
    * en-us
    * zh-cn
* Obsługa nowych ustawień regionalnych `cs-CZ` 
    * Wielkie litery i interpunkcja nie są obecnie obsługiwane.

**Prefix**
* Rozwiązuje problem polegający na tym, że wyniki pewności były zawsze 1 w trybie Diarization
* Migracja przy użyciu interfejsu API textanalytics 3,0

Zwróć uwagę na to, że ze względu na listy uwzględnionych fraz rozmiar tego obrazu kontenera wzrósł. 

Uwaga dotycząca wersji `2.5.0-amd64-<locale>` :

**Funkcje**
* Obsługa chmury dla instytucji rządowych USA platformy Azure

**Prefix**
* Rozwiązuje problem z uruchamianiem jako użytkownik spoza katalogu głównego w trybie Diarization

| Tagi obrazu                  | Uwagi                                    |
|-----------------------------|:-----------------------------------------|
| `2.6.0-amd64-<locale>`      | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `2.5.0-amd64-en-us`. |


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

Uwaga dotycząca wersji `1.9.0-amd64-<locale-and-voice>` :

* Regularne comiesięczne wydanie

| Tagi obrazu                                  | Uwagi                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaRUS` głosem.                                            | 
| `1.9.0-amd64-<locale-and-voice>`            | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `1.9.0-amd64-en-us-ariarus`.  |


| Ustawienia regionalne dla 1.9.0 v                          | Uwagi                                                                      | Szyfrowane                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | Obraz kontenera z `ar-EG` ustawieniami regionalnymi i `ar-EG-Hoda` głosem.            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | Obraz kontenera z `ar-SA` ustawieniami regionalnymi i `ar-SA-Naayf` głosem.           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | Obraz kontenera z `bg-BG` ustawieniami regionalnymi i `bg-BG-Ivan` głosem.            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | Obraz kontenera z `ca-ES` ustawieniami regionalnymi i `ca-ES-HerenaRUS` głosem.       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | Obraz kontenera z `cs-CZ` ustawieniami regionalnymi i `cs-CZ-Jakub` głosem.           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | Obraz kontenera z `da-DK` ustawieniami regionalnymi i `da-DK-HelleRUS` głosem.        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | Obraz kontenera z `de-AT` ustawieniami regionalnymi i `de-AT-Michael` głosem.         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | Obraz kontenera z `de-CH` ustawieniami regionalnymi i `de-CH-Karsten` głosem.         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Hedda` głosem.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Hedda` głosem.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-Stefan-Apollo` głosem.   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | Obraz kontenera z `el-GR` ustawieniami regionalnymi i `el-GR-Stefanos` głosem.        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-Catherine` głosem.       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-HayleyRUS` głosem.       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-HeatherRUS` głosem.      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-Linda` głosem.           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-George-Apollo` głosem.   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-HazelRUS` głosem.        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-Susan-Apollo` głosem.    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | Obraz kontenera z `en-IE` ustawieniami regionalnymi i `en-IE-Sean` głosem.            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-Heera-Apollo` głosem.    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-PriyaRUS` głosem.        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | Obraz kontenera z `en-IN` ustawieniami regionalnymi i `en-IN-Ravi-Apollo` głosem.     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-Aria24kRUS` głosem.      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaRUS` głosem.         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-BenjaminRUS` głosem.     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-Guy24kRUS` głosem.       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-ZiraRUS` głosem.         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-HelenaRUS` głosem.       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-Laura-Apollo` głosem.    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-Pablo-Apollo` głosem.    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-HildaRUS` głosem.        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-Raul-Apollo` głosem.     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | Obraz kontenera z `fi-FI` ustawieniami regionalnymi i `fi-FI-HeidiRUS` głosem.        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-Caroline` głosem.        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-HarmonieRUS` głosem.     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | Obraz kontenera z `fr-CH` ustawieniami regionalnymi i `fr-CH-Guillaume` głosem.       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-HortenseRUS` głosem.     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-Julie-Apollo` głosem.    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-Paul-Apollo` głosem.     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | Obraz kontenera z `he-IL` ustawieniami regionalnymi i `he-IL-Asaf` głosem.            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Hemant` głosem.          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Kalpana-Apollo` głosem.  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | Obraz kontenera z `hi-IN` ustawieniami regionalnymi i `hi-IN-Kalpana` głosem.         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | Obraz kontenera z `hr-HR` ustawieniami regionalnymi i `hr-HR-Matej` głosem.           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | Obraz kontenera z `hu-HU` ustawieniami regionalnymi i `hu-HU-Szabolcs` głosem.        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | Obraz kontenera z `id-ID` ustawieniami regionalnymi i `id-ID-Andika` głosem.          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-Cosimo-Apollo` głosem.   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-LuciaRUS` głosem.        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-Ayumi-Apollo` głosem.    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-HarukaRUS` głosem.       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-Ichiro-Apollo` głosem.   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | Obraz kontenera z `ko-KR` ustawieniami regionalnymi i `ko-KR-HeamiRUS` głosem.        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | Obraz kontenera z `ms-MY` ustawieniami regionalnymi i `ms-MY-Rizwan` głosem.          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | Obraz kontenera z `nb-NO` ustawieniami regionalnymi i `nb-NO-HuldaRUS` głosem.        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | Obraz kontenera z `nl-NL` ustawieniami regionalnymi i `nl-NL-HannaRUS` głosem.        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | Obraz kontenera z `pl-PL` ustawieniami regionalnymi i `pl-PL-PaulinaRUS` głosem.      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-Daniel-Apollo` głosem.   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-HeloisaRUS` głosem.      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | Obraz kontenera z `pt-PT` ustawieniami regionalnymi i `pt-PT-HeliaRUS` głosem.        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | Obraz kontenera z `ro-RO` ustawieniami regionalnymi i `ro-RO-Andrei` głosem.          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-EkaterinaRUS` głosem.    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-Irina-Apollo` głosem.    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | Obraz kontenera z `ru-RU` ustawieniami regionalnymi i `ru-RU-Pavel-Apollo` głosem.    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | Obraz kontenera z `sk-SK` ustawieniami regionalnymi i `sk-SK-Filip` głosem.           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | Obraz kontenera z `sl-SI` ustawieniami regionalnymi i `sl-SI-Lado` głosem.            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | Obraz kontenera z `sv-SE` ustawieniami regionalnymi i `sv-SE-HedvigRUS` głosem.       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | Obraz kontenera z `ta-IN` ustawieniami regionalnymi i `ta-IN-Valluvar` głosem.        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | Obraz kontenera z `te-IN` ustawieniami regionalnymi i `te-IN-Chitra` głosem.          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | Obraz kontenera z `th-TH` ustawieniami regionalnymi i `th-TH-Pattara` głosem.         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | Obraz kontenera z `tr-TR` ustawieniami regionalnymi i `tr-TR-SedaRUS` głosem.         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | Obraz kontenera z `vi-VN` ustawieniami regionalnymi i `vi-VN-An` głosem.              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-HuihuiRUS` głosem.       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-Kangkang-Apollo` głosem. | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-Yaoyao-Apollo` głosem.   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-Danny-Apollo` głosem.    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-Tracy-Apollo` głosem.    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | Obraz kontenera z `zh-HK` ustawieniami regionalnymi i `zh-HK-TracyRUS` głosem.        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-HanHanRUS` głosem.       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-Yating-Apollo` głosem.   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | Obraz kontenera z `zh-TW` ustawieniami regionalnymi i `zh-TW-Zhiwei-Apollo` głosem.   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |


# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

Uwaga dotycząca wersji `1.8.0-amd64-<locale-and-voice>` :

**Funkcja**

* Pełna migracja do programu .NET 3,1

Uwaga dotycząca wersji `1.7.0-amd64-<locale-and-voice>` :

**Funkcja**

* Uaktualnione składniki do programu .NET 3,1

| Tagi obrazu                                  | Uwagi                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.8.0-amd64-<locale-and-voice>`            | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | Wersja 1. Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `1.7.0-amd64-en-us-ariarus`.  |


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


# <a name="latest-version"></a>[Najnowsza wersja](#tab/current)

Informacje o wersji dla `v1.3.0` :
* Kontener zamiany tekstu na mowę neuronowych jest teraz ogólnie dostępny. 

| Tagi obrazu                                  | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaNeural` głosem.      |
| `1.3.0-amd64-<locale-and-voice>`    | Zamień `<locale>` na jedną z dostępnych ustawień regionalnych wymienionych poniżej. Na przykład: `1.3.0-amd64-en-us-arianeural`. |


| 1.3.0 i głosy w programie v           | Uwagi                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Obraz kontenera z `de-DE` ustawieniami regionalnymi i `de-DE-KatjaNeural` głosem.     |
| `en-au-natashaneural`               | Obraz kontenera z `en-AU` ustawieniami regionalnymi i `en-AU-NatashaNeural` głosem.   |
| `en-ca-claraneural`                 | Obraz kontenera z `en-CA` ustawieniami regionalnymi i `en-CA-ClaraNeural` głosem.     |
| `en-gb-libbyneural`                 | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-LibbyNeural` głosem.     |
| `en-gb-mianeural`                   | Obraz kontenera z `en-GB` ustawieniami regionalnymi i `en-GB-MiaNeural` głosem.       |
| `en-us-arianeural`                  | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-AriaNeural` głosem.      |
| `en-us-guyneural`                   | Obraz kontenera z `en-US` ustawieniami regionalnymi i `en-US-GuyNeural` głosem.       |
| `es-es-elviraneural`                | Obraz kontenera z `es-ES` ustawieniami regionalnymi i `es-ES-ElviraNeural` głosem.    |
| `es-mx-dalianeural`                 | Obraz kontenera z `es-MX` ustawieniami regionalnymi i `es-MX-DaliaNeural` głosem.     |
| `fr-ca-sylvieneural`                | Obraz kontenera z `fr-CA` ustawieniami regionalnymi i `fr-CA-SylvieNeural` głosem.    |
| `fr-fr-deniseneural`                | Obraz kontenera z `fr-FR` ustawieniami regionalnymi i `fr-FR-DeniseNeural` głosem.    |
| `it-it-elsaneural`                  | Obraz kontenera z `it-IT` ustawieniami regionalnymi i `it-IT-ElsaNeural` głosem.      |
| `ja-jp-nanamineural`                | Obraz kontenera z `ja-JP` ustawieniami regionalnymi i `ja-JP-NanamiNeural` głosem.    |
| `ko-kr-sunhineural`                 | Obraz kontenera z `ko-KR` ustawieniami regionalnymi i `ko-KR-SunHiNeural` głosem.     |
| `pt-br-franciscaneural`             | Obraz kontenera z `pt-BR` ustawieniami regionalnymi i `pt-BR-FranciscaNeural` głosem. |
| `zh-cn-xiaoxiaoneural`              | Obraz kontenera z `zh-CN` ustawieniami regionalnymi i `zh-CN-XiaoxiaoNeural` głosem.  |

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

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

---

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

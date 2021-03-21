---
title: Long audio API — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak długi interfejs API audio jest przeznaczony do asynchronicznej syntezy tekstu na mowę.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618410"
---
# <a name="long-audio-api"></a>Długi interfejs API audio

Długi interfejs API audio jest przeznaczony do asynchronicznej syntezy tekstu na mowę (na przykład: książki audio, artykuły i dokumenty informacyjne). Ten interfejs API nie zwraca danych z syntezy dźwiękowej w czasie rzeczywistym, zamiast tego oczekuje na to, że będziesz sondował o odpowiedzi i zużywać dane wyjściowe, gdy staną się dostępne w usłudze. W przeciwieństwie do interfejsu API zamiany tekstu na mowę, który jest używany przez zestaw mowy SDK, długi interfejs API audio umożliwia tworzenie dźwięków z dźwiękiem dłużej niż 10 minut, dzięki czemu w przypadku wydawców i platform zawartości audio można tworzyć długie zawartości audio, takie jak książki audio w partii.

Dodatkowe korzyści wynikające z długiego interfejsu API audio:

* Za pomocą funkcji syntezy głosu zwróconej przez usługę są stosowane najlepsze głosy neuronowych.
* Nie ma potrzeby wdrażania punktu końcowego głosowego, ponieważ umożliwia on syntezę głosów bez trybu wsadowego w czasie rzeczywistym.

> [!NOTE]
> Długi interfejs API audio obsługuje teraz zarówno [publiczne głosy neuronowych](./language-support.md#neural-voices) , jak i [niestandardowe głosy neuronowych](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Przepływ pracy

Zazwyczaj podczas korzystania z długiego interfejsu API audio można przesłać plik tekstowy lub pliki do wyszukania, przeprowadzić sondowanie stanu, a następnie, jeśli stan zakończy się pomyślnie, można pobrać wyjście audio.

Ten diagram zawiera ogólne omówienie przepływu pracy.

![Diagram przepływu pracy interfejsu API Long audio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Przygotowywanie zawartości do syntezy

Podczas przygotowywania pliku tekstowego upewnij się, że:

* Jest to zwykły tekst (. txt) lub tekst SSML (. txt)
* Jest zakodowany jako [UTF-8 z oznaczeniem kolejności bajtów (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Jest pojedynczym plikiem, a nie plikiem ZIP
* Zawiera więcej niż 400 znaków dla zwykłego tekstu lub 400 [znaków do rozliczenia](./text-to-speech.md#pricing-note) dla tekstu SSML i mniej niż 10 000 akapitów
  * W przypadku zwykłego tekstu każdy akapit jest oddzielony przez naciśnięcie **klawisza ENTER/Return** -View — [przykład wprowadzania tekstu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * W przypadku tekstu SSML każdy element SSML jest traktowany jako akapit. Elementy SSML są oddzielane różnymi akapitami — [przykładem wyświetlania tekstu SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)

## <a name="sample-code"></a>Przykładowy kod
Pozostała część tej strony koncentruje się na języku Python, ale przykładowy kod dla długiego interfejsu API audio jest dostępny w witrynie GitHub dla następujących języków programowania:

* [Przykładowy kod: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Przykładowy kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Przykładowy kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Przykład języka Python

Ta sekcja zawiera przykłady języka Python, które pokazują podstawowe użycie długiego interfejsu API audio. Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do pliku o nazwie `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Te biblioteki są używane do konstruowania żądania HTTP i wywołania interfejsu API REST syntezy tekstu na mowę.

### <a name="get-a-list-of-supported-voices"></a>Pobierz listę obsługiwanych głosów

Aby uzyskać listę obsługiwanych głosów, Wyślij żądanie GET do `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Ten kod umożliwia uzyskanie pełnej listy głosów dla określonego regionu/punktu końcowego, którego można użyć.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Zastąp następujące wartości:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).

Zobaczysz dane wyjściowe, które wyglądają następująco:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Jeśli **Właściwość. publicAvailable** ma **wartość true**, głos jest publicznym głosem neuronowych. W przeciwnym razie jest to niestandardowy głos neuronowych.

### <a name="convert-text-to-speech"></a>Konwertuj tekst na mowę

Przygotuj wejściowy plik tekstowy, w postaci zwykłego tekstu lub SSML tekstu, a następnie Dodaj następujący kod do `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` jest opcjonalnym parametrem. Jeśli ten parametr nie jest ustawiony, wyjście audio zostanie wygenerowane na akapit. Możesz również połączyć dźwięk do 1 wyjściowego przez ustawienie parametru. 
> `outputFormat` jest również opcjonalny. Domyślnie wyjście audio jest ustawione na RIFF-16khz-16bit-mono-PCM. Aby uzyskać więcej informacji na temat obsługiwanych formatów danych wyjściowych audio, zobacz [formaty danych wyjściowych audio](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Zastąp następujące wartości:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<input_file_path>` ścieżkę do pliku tekstowego, który został przygotowany do zamiany tekstu na mowę.
* Zamień na `<locale>` żądane ustawienia regionalne danych wyjściowych. Aby uzyskać więcej informacji, zobacz [Obsługa języków](language-support.md#neural-voices).

Użyj jednego z głosów zwracanego przez poprzednie wywołanie do `/voices` punktu końcowego.

* Jeśli używasz publicznego głosu neuronowych, Zastąp `<voice_name>` wartość odpowiednim głosem wyjściowym.
* Aby użyć niestandardowego głosu neuronowych, Zastąp `voice_identities` zmienną następującą funkcją i Zastąp element `<voice_id>` `id` własnym głosem neuronowych.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Zobaczysz dane wyjściowe, które wyglądają następująco:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Jeśli masz więcej niż 1 pliki wejściowe, musisz przesłać wiele żądań. Istnieją pewne ograniczenia, które należy wziąć pod uwagę.
> * Klient może przesłać do **5** żądań na serwer na sekundę dla każdego konta subskrypcji platformy Azure. W przypadku przekroczenia ograniczenia klient otrzyma kod błędu 429 (zbyt wiele żądań). Zmniejsz liczbę żądań na sekundę.
> * Serwer może działać i kolejkować do **120** żądań dla każdego konta subskrypcji platformy Azure. W przypadku przekroczenia ograniczenia serwer zwróci kod błędu 429 (zbyt wiele żądań). Zaczekaj i unikaj przesyłania nowego żądania, dopóki nie zostaną ukończone pewne żądania.

Adres URL w danych wyjściowych może służyć do uzyskiwania stanu żądania.

### <a name="get-information-of-a-submitted-request"></a>Uzyskaj informacje o przesłanym żądaniu

Aby uzyskać stan przesłanego żądania syntezy, wystarczy wysłać żądanie GET do adresu URL zwróconego przez poprzedni krok.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
Dane wyjściowe będą wyglądać następująco:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

Właściwość from umożliwia `status` odczytanie stanu tego żądania. Żądanie zostanie uruchomione ze `NotStarted` stanu, następnie zmieni się na `Running` , a na koniec `Succeeded` lub `Failed` . Możesz użyć pętli do sondowania tego interfejsu API do momentu, aż stan zmieni się na `Succeeded` .

### <a name="download-audio-result"></a>Pobierz wynik audio

Gdy żądanie syntezy powiedzie się, możesz pobrać wynik audio, wywołując interfejs GET `/files` API.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
Zamień `<request_id>` na identyfikator żądania, dla którego chcesz pobrać wynik. Można go znaleźć w odpowiedzi na poprzedni krok.

Dane wyjściowe będą wyglądać następująco:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
Dane wyjściowe zawierają informacje o 2 plikach. Z tą opcją `"kind": "LongAudioSynthesisScript"` jest wysyłane skrypt wejściowy. Drugim z nich `"kind": "LongAudioSynthesisResult"` jest wynik tego żądania.
Wynik to zip, który zawiera wygenerowane pliki wyjściowe audio wraz z kopią tekstu wejściowego.

Oba pliki można pobrać z adresu URL we `links.contentUrl` właściwości.

### <a name="get-all-synthesis-requests"></a>Pobierz wszystkie żądania syntezy

Możesz uzyskać listę wszystkich przesłanych żądań o następującym kodzie:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

Dane wyjściowe będą wyglądać następująco:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` Właściwość zawiera listę żądań syntezy. Lista jest stronicowana z maksymalnym rozmiarem strony wynoszącym 100. Jeśli istnieje więcej niż 100 żądań, `"@nextLink"` zostanie dostarczona właściwość, aby uzyskać następną stronę listy z podziałem na strony.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Możesz również dostosować rozmiar strony i pominąć liczbę, podając `skip` parametr i `top` w polu adres URL.

### <a name="remove-previous-requests"></a>Usuń poprzednie żądania

Usługa będzie przechowywać do **20 000** żądań dla każdego konta subskrypcji platformy Azure. Jeśli kwota żądania przekracza to ograniczenie, Usuń poprzednie żądania przed wprowadzeniem nowych. Jeśli nie usuniesz istniejących żądań, otrzymasz powiadomienie o błędzie.

Poniższy kod przedstawia sposób usuwania określonego żądania syntezy.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Jeśli żądanie zostanie pomyślnie usunięte, kod stanu odpowiedzi będzie HTTP 204 (brak zawartości).

```console
response.status_code: 204
```

> [!NOTE]
> Żądania ze stanem `NotStarted` lub `Running` nie można usunąć ani usunąć.

Zakończono `long_audio_synthesis_client.py` jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Kody stanu HTTP

W poniższej tabeli przedstawiono szczegóły kodów odpowiedzi HTTP i komunikatów z interfejsu API REST.

| Interfejs API | Kod stanu HTTP | Opis | Rozwiązanie |
|-----|------------------|-------------|----------|
| Utwórz | 400 | Synteza głosowa nie jest włączona w tym regionie. | Zmień klucz subskrypcji mowy z obsługiwanym regionem. |
|        | 400 | Tylko **standardowa** subskrypcja mowy dla tego regionu jest prawidłowa. | Zmień klucz subskrypcji mowy na warstwę cenową "Standardowa". |
|        | 400 | Przekracza limit 20 000 dla konta platformy Azure. Przed przesłaniem nowych żądań Usuń niektóre żądania. | Serwer będzie przechowywać do 20 000 żądań dla każdego konta platformy Azure. Usuń niektóre żądania przed przesłaniem nowych. |
|        | 400 | Nie można użyć tego modelu w syntezie głosowej: {modelID}. | Upewnij się, że stan {modelID} jest poprawny. |
|        | 400 | Region żądania nie jest zgodny z regionem dla modelu: {modelID}. | Upewnij się, że region {modelID} jest zgodny z regionem żądania. |
|        | 400 | Synteza głosowa obsługuje tylko plik tekstowy w kodowaniu UTF-8 ze znacznikiem kolejności bajtów. | Upewnij się, że pliki wejściowe są w kodowaniu UTF-8 przy użyciu znacznika kolejności bajtów. |
|        | 400 | W żądaniu syntezy głosowej są dozwolone tylko prawidłowe dane wejściowe SSML. | Upewnij się, że wejściowe wyrażenia SSML są poprawne. |
|        | 400 | Nie znaleziono nazwy głosu {voicename} w pliku wejściowym. | Nazwa głosu SSML wejściowego nie jest wyrównana do identyfikatora modelu. |
|        | 400 | Liczba akapitów w pliku wejściowym powinna być mniejsza niż 10 000. | Upewnij się, że liczba akapitów w pliku jest mniejsza niż 10 000. |
|        | 400 | Plik wejściowy powinien zawierać więcej niż 400 znaków. | Upewnij się, że plik wejściowy przekracza 400 znaków. |
|        | 404 | Nie można znaleźć modelu zadeklarowanego w definicji syntezy głosu: {modelID}. | Upewnij się, że wartość {modelID} jest poprawna. |
|        | 429 | Przekracza limit aktywnego syntezy głosu. Zaczekaj na zakończenie niektórych żądań. | Serwer może działać i kolejkować do 120 żądań dla każdego konta platformy Azure. Zaczekaj i unikaj przesyłania nowych żądań, dopóki niektóre żądania nie zostaną ukończone. |
| Wszystko       | 429 | Zbyt wiele żądań. | Klient może przesłać do 5 żądań na serwer na sekundę dla każdego konta platformy Azure. Zmniejsz liczbę żądań na sekundę. |
| Usuń    | 400 | Zadanie syntezy głosu jest nadal w użyciu. | Można usuwać tylko żądania, które zostały **ukończone** lub **zakończyły się niepowodzeniem**. |
| GetByID   | 404 | Nie można znaleźć określonej jednostki. | Upewnij się, że identyfikator syntezy jest poprawny. |

## <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Długi interfejs API audio jest dostępny w wielu regionach z unikatowymi punktami końcowymi.

| Region (Region) | Punkt końcowy |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie Środkowe | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Southeast Asia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Południowe Zjednoczone Królestwo | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formaty wyjściowe audio

Obsługujemy elastyczne formaty danych wyjściowych audio. Można generować dane wyjściowe audio w poszczególnych akapitach lub łączyć wyjścia audio w jedno wyjście, ustawiając parametr "concatenateResult". Następujące formaty wyjściowe audio są obsługiwane przez długi interfejs API audio:

> [!NOTE]
> Domyślny format dźwięku to RIFF-16khz-16bit-mono-PCM.

* RIFF-8khz-16bit-mono-PCM
* RIFF-16khz-16bit-mono-PCM
* RIFF-24khz-16bit-mono-PCM
* RIFF-48kHz-16bit-mono-PCM
* audio-16khz-32kbitrate-mono-MP3
* audio-16khz-64kbitrate-mono-MP3
* audio-16khz-128kbitrate-mono-MP3
* audio-24khz-48kbitrate-mono-MP3
* audio-24khz-96kbitrate-mono-MP3
* audio-24khz-160kbitrate-mono-MP3

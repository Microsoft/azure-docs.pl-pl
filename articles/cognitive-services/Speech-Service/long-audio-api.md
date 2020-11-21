---
title: Long audio API (wersja zapoznawcza) — usługa mowy
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
ms.openlocfilehash: 0a538deb3b7da19261e1bc2b7c0d29f35315f786
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015417"
---
# <a name="long-audio-api-preview"></a>Long audio API (wersja zapoznawcza)

Długi interfejs API audio jest przeznaczony do asynchronicznej syntezy tekstu na mowę (na przykład: książki audio, artykuły i dokumenty informacyjne). Ten interfejs API nie zwraca danych z syntezy dźwiękowej w czasie rzeczywistym, zamiast tego oczekuje na to, że będziesz sondował o odpowiedzi i zużywać dane wyjściowe, gdy staną się dostępne w usłudze. W przeciwieństwie do interfejsu API zamiany mowy na mowę, który jest używany przez zestaw mowy SDK, długi interfejs API audio umożliwia tworzenie dźwięków z dźwiękiem dłużej niż 10 minut, dzięki czemu jest idealnym rozwiązaniem dla wydawców i platform zawartości audio.

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
> [!NOTE]
> W języku chińskim (kontynent), chińskim (Hongkong SAR), chińskim (Tajwan), japońskim i koreańskim, jeden wyraz będzie liczony jako dwa znaki. 

## <a name="python-example"></a>Przykład języka Python

Ta sekcja zawiera przykłady języka Python, które pokazują podstawowe użycie długiego interfejsu API audio. Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do pliku o nazwie `voice_synthesis_client.py` .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

Te biblioteki są używane do analizowania argumentów, konstruowania żądania HTTP i wywoływania interfejsu API REST typu "Zamiana tekstu na mowę".

### <a name="get-a-list-of-supported-voices"></a>Pobierz listę obsługiwanych głosów

Ten kod umożliwia uzyskanie pełnej listy głosów dla określonego regionu/punktu końcowego, którego można użyć. Dodaj kod do `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

Uruchom skrypt za pomocą polecenia `python voice_synthesis_client.py --voices -key <your_key> -region <region>` i zastąp następujące wartości:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).

Zobaczysz dane wyjściowe, które wyglądają następująco:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Jeśli parametr **PublicVoice** ma **wartość true**, głos jest publiczny neuronowych głosu. W przeciwnym razie jest to niestandardowy głos neuronowych.

### <a name="convert-text-to-speech"></a>Konwertuj tekst na mowę

Przygotuj wejściowy plik tekstowy, w postaci zwykłego tekstu lub SSML tekstu, a następnie Dodaj następujący kod do `voice_synthesis_client.py` :

> [!NOTE]
> "concatenateResult" jest opcjonalnym parametrem. Jeśli ten parametr nie jest ustawiony, wyjście audio zostanie wygenerowane na akapit. Możesz również połączyć dźwięk do 1 wyjściowego przez ustawienie parametru. Domyślnie wyjście audio jest ustawione na RIFF-16khz-16bit-mono-PCM. Aby uzyskać więcej informacji na temat obsługiwanych wyjść audio, zobacz [formaty danych wyjściowych audio](#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

Uruchom skrypt za pomocą polecenia `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` i zastąp następujące wartości:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<input>` ścieżkę do pliku tekstowego, który został przygotowany do zamiany tekstu na mowę.
* Zamień na `<locale>` żądane ustawienia regionalne danych wyjściowych. Aby uzyskać więcej informacji, zobacz [Obsługa języków](language-support.md#neural-voices).
* Zamień na `<voice_guid>` żądany wyjściowy dźwięk. Użyj jednego z głosów zwracanego przez poprzednie wywołanie do `/voicesynthesis/voices` punktu końcowego.

Zobaczysz dane wyjściowe, które wyglądają następująco:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Wynik zawiera tekst wejściowy i pliki wyjściowe audio, które są generowane przez usługę. Te pliki można pobrać w pliku zip.

> [!NOTE]
> Jeśli masz więcej niż 1 pliki wejściowe, musisz przesłać wiele żądań. Istnieją pewne ograniczenia, które należy wziąć pod uwagę. 
> * Klient może przesłać do **5** żądań na serwer na sekundę dla każdego konta subskrypcji platformy Azure. W przypadku przekroczenia ograniczenia klient otrzyma kod błędu 429 (zbyt wiele żądań). Zmniejsz liczbę żądań na sekundę
> * Serwer może działać i kolejkować do **120** żądań dla każdego konta subskrypcji platformy Azure. W przypadku przekroczenia ograniczenia serwer zwróci kod błędu 429 (zbyt wiele żądań). Zaczekaj i unikaj przesyłania nowego żądania do momentu ukończenia niektórych żądań

### <a name="remove-previous-requests"></a>Usuń poprzednie żądania

Usługa będzie przechowywać do **20 000** żądań dla każdego konta subskrypcji platformy Azure. Jeśli kwota żądania przekracza to ograniczenie, Usuń poprzednie żądania przed wprowadzeniem nowych. Jeśli nie usuniesz istniejących żądań, otrzymasz powiadomienie o błędzie.

Dodaj następujący kod do pliku `voice_synthesis_client.py`:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

Uruchom `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` , aby uzyskać listę utworzonych przez siebie żądań syntezy. Zobaczysz dane wyjściowe podobne do tego:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Aby usunąć żądanie, uruchom `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` i Zamień na `<synthesis_id>` wartość identyfikatora żądania zwróconą z poprzedniego żądania.

> [!NOTE]
> Nie można usunąć ani usunąć żądań ze stanem "Uruchamianie"/"oczekiwanie".

Zakończono `voice_synthesis_client.py` jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

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
| Usuwanie    | 400 | Zadanie syntezy głosu jest nadal w użyciu. | Można usuwać tylko żądania, które zostały **ukończone** lub **zakończyły się niepowodzeniem**. |
| GetByID   | 404 | Nie można znaleźć określonej jednostki. | Upewnij się, że identyfikator syntezy jest poprawny. |

## <a name="regions-and-endpoints"></a>Regiony i punkty końcowe

Długi interfejs API audio jest dostępny w wielu regionach z unikatowymi punktami końcowymi.

| Region (Region) | Punkt końcowy |
|--------|----------|
| Australia Wschodnia | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Kanada Środkowa | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie Środkowe | `https://centralindia.customvoice.api.speech.microsoft.com` |
| South Central US | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Southeast Asia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Południowe Zjednoczone Królestwo | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |
| Zachodnie stany USA 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

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

## <a name="sample-code"></a>Przykładowy kod
Przykładowy kod dla długiego interfejsu API audio jest dostępny w witrynie GitHub.

* [Przykładowy kod: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Przykładowy kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Przykładowy kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
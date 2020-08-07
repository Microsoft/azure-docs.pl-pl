---
title: 'Szybki Start: synteza asynchroniczna dla długich form audio (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Używaj długiego interfejsu API audio, aby asynchronicznie konwertować tekst na mowę i pobrać dane wyjściowe audio z identyfikatora URI dostarczonego przez usługę. Ten interfejs API REST jest idealnym rozwiązaniem dla dostawców zawartości, którzy muszą konwertować pliki tekstowe o więcej niż 10 000 znaków lub 50 akapitów na mowę.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.custom: devx-track-python
ms.openlocfilehash: 330bd3593ea10e4a4a03e430025b8acf1f7ab21a
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87875391"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Szybki Start: synteza asynchroniczna dla długich form audio w języku Python (wersja zapoznawcza)

W tym przewodniku szybki start użyjesz długiego interfejsu API audio do asynchronicznego konwertowania tekstu na mowę i pobrania danych wyjściowych audio z identyfikatora URI dostarczonego przez usługę. Ten interfejs API REST jest idealnym rozwiązaniem dla dostawców zawartości, którzy muszą wyszukiwać dźwięk z tekstu większego niż 5 000 znaków (lub więcej niż 10 minut). Aby uzyskać więcej informacji, zobacz [Long audio API](../../long-audio-api.md).

Asynchronicznej syntezy audio o długim formacie można używać z [publicznymi głosymi neuronowych](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) i [niestandardowymi głosymi neuronowych](../../how-to-custom-voice.md#custom-neural-voices), z których każdy obsługuje określony język i dialekt. 

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* Python 2.7. x lub 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)lub ulubiony Edytor tekstu.
* Subskrypcja platformy Azure i klucz subskrypcji usługi rozpoznawania mowy. [Utwórz konto platformy Azure](../../get-started.md#new-resource) i [Utwórz zasób mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) , aby uzyskać klucz. Podczas tworzenia zasobu mowy upewnij się, że warstwa cenowa jest ustawiona na **S0**, a lokalizacja jest ustawiona na [obsługiwany region](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do pliku o nazwie `voice_synthesis_client.py` .

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

> [!NOTE]
> Jeśli te moduły nie były używane, należy je zainstalować przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests urllib3`.

Te moduły są używane do analizowania argumentów, konstruowania żądania HTTP i wywoływania interfejsu API REST typu "Zamiana tekstu na mowę".

## <a name="get-a-list-of-supported-voices"></a>Pobierz listę obsługiwanych głosów

Ten kod umożliwia uzyskanie pełnej listy głosów dla określonego regionu/punktu końcowego, którego można użyć. Sprawdź [obsługiwany region/punkt końcowy](../../long-audio-api.md). Dodaj kod do `voice_synthesis_client.py` :

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

### <a name="test-your-code"></a>Testowanie kodu

Przetestujmy to, co zostało zrobione dotąd. Musisz zaktualizować kilka elementów w żądaniu poniżej:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).

Uruchom następujące polecenie:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Zobaczysz dane wyjściowe, które wyglądają następująco:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Jeśli parametr **PublicVoice** ma **wartość true**, głos jest publiczny neuronowych głosu. W przeciwnym razie jest to niestandardowy głos neuronowych. 

## <a name="prepare-input-files"></a>Przygotuj pliki wejściowe

Przygotuj wejściowy plik tekstowy. Może to być zwykły tekst lub tekst SSML. Wymagania dotyczące pliku wejściowego znajdują się w temacie How to [Prepare content for syntezy](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Konwertuj tekst na mowę

Po przygotowaniu wejściowego pliku tekstowego Dodaj następujący kod do syntezy mowy, aby `voice_synthesis_client.py` :

> [!NOTE]
> "concatenateResult" jest opcjonalnym parametrem. Jeśli ten parametr nie jest ustawiony, wyjście audio zostanie wygenerowane na akapit. Możesz również połączyć dźwięk do 1 wyjściowego przez ustawienie parametru. Domyślnie wyjście audio jest ustawione na RIFF-16khz-16bit-mono-PCM. Aby uzyskać więcej informacji na temat obsługiwanych wyjść audio, zobacz [formaty danych wyjściowych audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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

### <a name="test-your-code"></a>Testowanie kodu

Utwórzmy żądanie syntezy tekstu przy użyciu pliku wejściowego jako źródła. Musisz zaktualizować kilka elementów w żądaniu poniżej:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<input>` ścieżkę do pliku tekstowego, który został przygotowany do zamiany tekstu na mowę.
* Zamień na `<locale>` żądane ustawienia regionalne danych wyjściowych. Aby uzyskać więcej informacji, zobacz [Obsługa języków](../../language-support.md#neural-voices).
* Zamień na `<voice_guid>` żądany wyjściowy dźwięk. Użyj jednego z głosów zwracanego przez [uzyskanie listy obsługiwanych głosów](#get-a-list-of-supported-voices).

Konwertuj tekst na mowę przy użyciu tego polecenia:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Jeśli masz więcej niż 1 pliki wejściowe, musisz przesłać wiele żądań. Istnieją pewne ograniczenia, które należy wziąć pod uwagę. 
> * Klient może przesłać do **5** żądań na serwer na sekundę dla każdego konta subskrypcji platformy Azure. W przypadku przekroczenia ograniczenia klient otrzyma kod błędu 429 (zbyt wiele żądań). Zmniejsz liczbę żądań na sekundę
> * Serwer może działać i kolejkować do **120** żądań dla każdego konta subskrypcji platformy Azure. W przypadku przekroczenia ograniczenia serwer zwróci kod błędu 429 (zbyt wiele żądań). Zaczekaj i unikaj przesyłania nowego żądania do momentu ukończenia niektórych żądań

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

## <a name="remove-previous-requests"></a>Usuń poprzednie żądania

Serwer będzie przechowywać do **20 000** żądań dla każdego konta subskrypcji platformy Azure. Jeśli kwota żądania przekracza to ograniczenie, Usuń poprzednie żądania przed wprowadzeniem nowych. Jeśli nie usuniesz istniejących żądań, otrzymasz powiadomienie o błędzie.

Dodaj kod do `voice_synthesis_client.py` :

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

### <a name="test-your-code"></a>Testowanie kodu

Teraz sprawdźmy, czy żądania zostały wcześniej przesłane. Przed kontynuowaniem należy zaktualizować kilka rzeczy w ramach tego żądania:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).

Uruchom następujące polecenie:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Spowoduje to zwrócenie listy utworzonych przez siebie żądań syntezy. Zobaczysz dane wyjściowe podobne do tego:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Teraz usuńmy wcześniej przesłane żądanie. Musisz zaktualizować kilka elementów w poniższym kodzie:

* Zamień `<your_key>` na klucz subskrypcji usługi rozpoznawania mowy. Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<region>` region, w którym został utworzony zasób mowy (na przykład: `eastus` lub `westus` ). Te informacje są dostępne na karcie **Przegląd** dla zasobu w [Azure Portal](https://aka.ms/azureportal).
* Zamień na `<synthesis_id>` wartość zwróconą w poprzednim żądaniu.

> [!NOTE]
> Nie można usunąć ani usunąć żądań ze stanem "Uruchamianie"/"oczekiwanie".

Uruchom następujące polecenie:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Zobaczysz dane wyjściowe podobne do tego:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Uzyskaj pełny klient

Ukończono `voice_synthesis_client.py` pobieranie w serwisie [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o długim interfejsie API audio](../../long-audio-api.md)

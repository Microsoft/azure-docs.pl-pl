---
title: Konwertowanie tekstu na mowę, Python — usługa mowy
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak konwertować tekst na mowę przy użyciu języka Python i interfejsu API REST od zamiany tekstu na mowę. Przykładowy tekst zawarty w tym przewodniku ma strukturę języka znaczników syntezy mowy (SSML). Dzięki temu można wybrać głos i język odpowiedzi mowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 171fdb033cba422d8ba580da3ab54db88ca20872
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400829"
---
# <a name="convert-text-to-speech-using-python"></a>Konwertowanie tekstu na mowę przy użyciu języka Python

W tym artykule dowiesz się, jak konwertować tekst na mowę za pomocą języka Python i interfejsu API REST między tekstem na mowę. Treść żądania w tym przewodniku jest skonstruowana jako [język znaczników syntezy mowy (SSML),](speech-synthesis-markup.md)który pozwala wybrać głos i język odpowiedzi.

Ten artykuł wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko Python 2.7.x lub 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>lub ulubiony edytor tekstu
* Klucz subskrypcji platformy Azure dla usługi Mowy

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `tts.py`.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests`.

Moduły te są używane do pisania odpowiedzi mowy do pliku z sygnaturą czasową, konstruowania żądania HTTP i wywołania interfejsu API zamiany tekstu na mowę.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ustawianie klucza subskrypcji i tworzenie monitu dla usługi TTS

W następnych kilku sekcjach utworzysz metody obsługi autoryzacji, wywołasz interfejs API zamiany tekstu na mowę i sprawdź poprawność odpowiedzi. Zacznijmy od dodania kodu, który zapewnia, że ten przykład będzie działać z Python 2.7.x i 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Następnie utwórzmy klasę. W tym miejscu umieścimy nasze metody wymiany tokenów i wywołanie interfejsu API zamiany tekstu na mowę.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

Jest `subscription_key` to unikatowy klucz z witryny Azure portal. `tts`monituje użytkownika o wprowadzenie tekstu, który zostanie przekonwertowany na mowę. To dane wejściowe jest ciągiem literału, więc znaki nie muszą być zmienione. Na koniec `timestr` pobiera bieżący czas, którego użyjemy do nadawanie nazwy pliku.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Interfejs API REST między tekstem na mowę wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. W tym przykładzie wymienia klucz subskrypcji usługi `issueToken` mowy dla tokenu dostępu przy użyciu punktu końcowego.

W tym przykładzie przyjęto założenie, że subskrypcja usługi mowy znajduje się w regionie Zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość dla `fetch_token_url`. Aby uzyskać pełną listę, zobacz [Regiony](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj `TextToSpeech` ten kod do klasy:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [Uwierzytelnianie przy użyciu tokenu dostępu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Złożyć wniosek i zapisać odpowiedź

W tym miejscu masz zamiar utworzyć żądanie i zapisać odpowiedź mowy. Najpierw musisz ustawić `base_url` i `path`. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodniego us. Jeśli zasób jest zarejestrowany w innym regionie, należy zaktualizować plik `base_url`. Aby uzyskać więcej informacji, zobacz [Regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie należy dodać wymagane nagłówki dla żądania. Upewnij się, `User-Agent` że aktualizacja o nazwie zasobu (znajduje się `X-Microsoft-OutputFormat` w witrynie Azure portal) i ustawić preferowane dane wyjściowe audio. Aby uzyskać pełną listę formatów wyjściowych, zobacz [Wyjścia audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Następnie skonstruuj treść żądania przy użyciu języka znaczników syntezy mowy (SSML). Ten przykład definiuje strukturę i `tts` używa danych wejściowych utworzonych wcześniej.

>[!NOTE]
> W tym przykładzie użyto czcionki głosowej. `Guy24kRUS` Aby uzyskać pełną listę głosów/języków dostarczonych przez firmę Microsoft, zobacz [Obsługa języków](language-support.md).
> Jeśli chcesz stworzyć unikalny, rozpoznawalny głos dla swojej marki, zobacz [Tworzenie niestandardowych czcionek głosowych.](how-to-customize-voice-font.md)

Na koniec złożysz wniosek do usługi. Jeśli żądanie zakończy się pomyślnie i zwracany jest kod stanu 200, odpowiedź na mowę jest zapisywana w pliku oznaczonym sygnaturą czasową.

Skopiuj `TextToSpeech` ten kod do klasy:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest tworzenie wystąpienia klasy i wywołanie funkcji.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko, możesz uruchomić przykładową aplikację zamiany tekstu na mowę. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
python tts.py
```

Po wyświetleniu monitu wpisz wszystko, co chcesz przekonwertować z tekstu na mowę. Jeśli powiedzie się, plik mowy znajduje się w folderze projektu. Odtwórz go za pomocą ulubionego odtwarzacza multimedialnego.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Konwertowanie tekstu na mowę za pomocą języka Python i speech SDK](quickstarts/speech-to-text-from-microphone.md)
* [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
* [Nagrywanie próbek głosu w celu utworzenia niestandardowego głosu](record-custom-voice-samples.md)

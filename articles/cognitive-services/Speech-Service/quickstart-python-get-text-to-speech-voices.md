---
title: Lista głosów zamiany tekstu na mowę, Python - Usługa mowy
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak uzyskać pełną listę standardowych i neuronowych głosów dla regionu/punktu końcowego przy użyciu języka Python. Lista jest zwracana jako JSON, a dostępność głosu zależy od regionu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: b388c8d8b61e2fc638ae2bce5bc6d9eeb25ee0d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401013"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Pobierz listę głosów zamiany tekstu na mowę za pomocą języka Python

W tym artykule dowiesz się, jak uzyskać pełną listę standardowych i neuronowych głosów dla regionu/punktu końcowego przy użyciu języka Python. Lista jest zwracana jako JSON, a dostępność głosu zależy od regionu. Aby uzyskać listę obsługiwanych regionów, zobacz [regiony](regions.md).

Ten artykuł wymaga [konta usługi Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z zasobem usługi mowy. Jeśli nie masz konta, możesz użyć [bezpłatnej wersji próbnej](get-started.md), aby uzyskać klucz subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko Python 2.7.x lub 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>lub ulubiony edytor tekstu
* Klucz subskrypcji platformy Azure dla usługi Mowy

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt języka Python przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `pip install requests`.

Żądania są używane dla żądań HTTP do usługi zamiany tekstu na mowę.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Ustawianie klucza subskrypcji i tworzenie monitu dla usługi TTS

W następnych kilku sekcjach utworzysz metody obsługi autoryzacji, wywołasz interfejs API zamiany tekstu na mowę i sprawdź poprawność odpowiedzi. Zacznijmy od utworzenia klasy. W tym miejscu umieścimy nasze metody wymiany tokenów i wywołanie interfejsu API zamiany tekstu na mowę.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Jest `subscription_key` to unikatowy klucz z witryny Azure portal.

## <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Ten punkt końcowy wymaga tokenu dostępu do uwierzytelniania. Aby uzyskać token dostępu, wymagana jest wymiana. W tym przykładzie wymienia klucz subskrypcji usługi `issueToken` mowy dla tokenu dostępu przy użyciu punktu końcowego.

W tym przykładzie przyjęto założenie, że subskrypcja usługi mowy znajduje się w regionie Zachodnie stany USA. Jeśli używasz innego regionu, zaktualizuj wartość dla `fetch_token_url`. Aby uzyskać pełną listę, zobacz [Regiony](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Skopiuj `GetVoices` ten kod do klasy:

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

Tutaj masz zamiar zbudować żądanie i zapisać listę zwróconych głosów. Najpierw musisz ustawić `base_url` i `path`. W tym przykładzie przyjęto założenie, że używasz punktu końcowego zachodniego us. Jeśli zasób jest zarejestrowany w innym regionie, należy zaktualizować plik `base_url`. Aby uzyskać więcej informacji, zobacz [Regiony usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Następnie dodaj wymagane nagłówki dla żądania. Na koniec złożysz wniosek do usługi. Jeśli żądanie zakończy się pomyślnie i zwracany jest kod stanu 200, odpowiedź jest zapisywana w pliku.

Skopiuj `GetVoices` ten kod do klasy:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To już prawie koniec. Ostatnim krokiem jest tworzenie wystąpienia klasy i wywołanie funkcji.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko, jesteś gotowy do uruchomienia próbki. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Poznaj przykłady dla języka Python w serwisie GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Tworzenie niestandardowych czcionek głosowych](how-to-customize-voice-font.md)
* [Nagrywanie próbek głosu w celu utworzenia niestandardowego głosu](record-custom-voice-samples.md)

---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 649af0d3b1503a5607717c4ccbadd8f1d822be32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586893"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `get-languages.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Jeśli nie korzystano z tych modułów, konieczne będzie ich zainstalowanie przed uruchomieniem programu. Aby zainstalować te pakiety, uruchom polecenie `npm install request uuidv4`.

Te moduły są wymagane do utworzenia żądania HTTP i unikatowego identyfikatora dla nagłówka `'X-ClientTraceId'`.

## <a name="set-the-endpoint"></a>Ustawianie punktu końcowego

Ten przykład spróbuje odczytać punkt końcowy usługi Translator ze zmiennej środowiskowej: `TRANSLATOR_TEXT_ENDPOINT` . Jeśli nie chcesz korzystać ze zmiennych środowiskowych, możesz ustawić element `endpoint` jako ciąg i oznaczyć instrukcję warunkową jako komentarz.

```javascript
lorum ipsum
```

## <a name="configure-the-request"></a>Konfigurowanie żądania

Metoda `request()`, udostępniona przez moduł żądania, umożliwia nam przekazanie metody HTTP, parametrów żądania adresu URL, nagłówków i treści w formacie JSON w postaci obiektu `options`. W poniższym fragmencie kodu skonfigurujemy żądanie:

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator 3,0: Języki](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```javascript
let options = {
    method: 'GET',
    baseUrl: endpoint,
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

Jeśli używasz subskrypcji usługi Cognitive Services, musisz także uwzględnić `Ocp-Apim-Subscription-Region` w parametrach żądania. [Dowiedz się więcej o uwierzytelnianiu w ramach subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Wysyłanie żądania i wyświetlanie odpowiedzi

Następnie utworzymy żądanie przy użyciu metody `request()`. Pobiera ona obiekt `options` utworzony w poprzedniej sekcji jako pierwszy argument, a następnie wyświetla ulepszoną odpowiedź w formacie JSON.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> W tym przykładzie definiujemy żądanie HTTP w obiekcie `options`. Jednak moduł żądania obsługuje również metody ułatwiające, takie jak `.post` i `.get`. Aby uzyskać więcej informacji, zobacz [Convenience methods](https://github.com/request/request#convenience-methods) (Metody ułatwiające).

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Jest to również prosty program, który wywoła translator i zwróci odpowiedź JSON. Teraz nadszedł czas, aby uruchomić program:

```console
node get-languages.js
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Przykładowa odpowiedź

Znajdź skrót kraju/regionu na [liście języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Ten przykład został obcięty w celu pokazania fragmentu kodu wyniku:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli klucz subskrypcji umieszczono na stałe w kodzie programu, pamiętaj, aby usunąć ten klucz subskrypcji po zakończeniu pracy z przewodnikiem Szybki start.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API, aby zrozumieć wszystko, co możesz zrobić z translatorem.

> [!div class="nextstepaction"]
> [Odwołanie API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

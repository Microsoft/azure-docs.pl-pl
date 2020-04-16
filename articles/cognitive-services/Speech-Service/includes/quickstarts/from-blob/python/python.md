---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 3ca50a9bad36e0174dc4ee0059c9d01fcc18a5f1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401001"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Pobieranie i instalowanie biblioteki klienta interfejsu API

Aby wykonać przykład, należy wygenerować bibliotekę języka Python dla interfejsu API REST, który jest generowany za pośrednictwem [Swagger](https://swagger.io).

Aby wykonać następujące czynności, należy wykonać następujące czynności:

1. Przejdź do pozycji https://editor.swagger.io (Plik > Nowy > Inny).
1. Kliknij **pozycję Plik**, a następnie kliknij pozycję **Importuj adres URL**.
1. Wprowadź adres URL Swagger, w tym `https://<your-region>.cris.ai/docs/v2.0/swagger`region subskrypcji usługi mowy: .
1. Kliknij **pozycję Generuj klienta** i wybierz pozycję **Python**.
1. Zapisz bibliotekę klienta.
1. Wyodrębnij pobrany python-client-generated.zip gdzieś w systemie plików.
1. Zainstaluj wyodrębniony moduł python-client w środowisku `pip install path/to/package/python-client`Pythona za pomocą pip: .
1. Zainstalowany pakiet ma `swagger_client`nazwę . Można sprawdzić, czy instalacja działała za pomocą polecenia `python -c "import swagger_client"`.

> [!NOTE]
> Ze względu na [znany błąd w autogeneracji Swagger,](https://github.com/swagger-api/swagger-codegen/issues/7541)mogą `swagger_client` wystąpić błędy podczas importowania pakietu.
> Można je naprawić, usuwając linię z zawartością
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> z pliku `swagger_client/models/model.py` i linii z zawartością
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> z pliku `swagger_client/models/inner_error.py` wewnątrz zainstalowanego pakietu. Komunikat o błędzie informuje, gdzie znajdują się te pliki dla instalacji.

## <a name="install-other-dependencies"></a>Instalowanie innych zależności

Przykład używa biblioteki. `requests` Można go zainstalować za pomocą polecenia

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Tworzenie i konfigurowanie klienta http
Pierwszą rzeczą, której potrzebujemy, jest klient http, który ma poprawny podstawowy adres URL i zestaw uwierzytelniania.
Wstaw ten `transcribe` kod do[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generowanie żądania transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten `transcribe` kod do[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Wyślij żądanie i sprawdź jego stan
Teraz publikujemy żądanie w usłudze mowy i sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi po prostu wskaże, czy usługa otrzymała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, która jest lokalizacją, w której będzie przechowywać stan transkrypcji.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Poczekaj na zakończenie transkrypcji
Ponieważ usługa przetwarza transkrypcji asynchronicznie, musimy sondować jego status tak często. Sprawdzamy co 5 sekund.

Będziemy wyliczać wszystkie transkrypcje, że ten zasób usługi mowy jest przetwarzanie i poszukaj jednego utworzyliśmy.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zrobimy to dalej.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Po pomyślnym zakończeniu transkrypcji usługi wyniki będą przechowywane w innym adresie URL, który możemy uzyskać z odpowiedzi na stan.

Tutaj otrzymujemy ten wynik JSON i wyświetlamy go.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco: (Dodaliśmy kilka komentarzy do tej wersji)[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania rozpoznawania mowy za pomocą usługi Mowy.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

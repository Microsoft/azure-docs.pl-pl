---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: d69cb782b3be7035b7571653dcb434e44d22da93
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376857"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Przekazywanie pliku źródłowego do obiektu blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Pobieranie i Instalowanie biblioteki klienta interfejsu API

Aby wykonać przykład potrzebny do wygenerowania biblioteki języka Python dla interfejsu API REST, który jest generowany przez strukturę [Swagger](https://swagger.io).

Wykonaj następujące kroki instalacji:

1. Przejdź do adresu https://editor.swagger.io.
1. Kliknij pozycję **plik**, a następnie kliknij pozycję **Importuj adres URL**.
1. Wprowadź adres URL programu Swagger, w tym Region subskrypcji usługi mowy: `https://<your-region>.cris.ai/docs/v2.0/swagger` .
1. Kliknij przycisk **Generuj klienta** i wybierz język **Python**.
1. Zapisz bibliotekę kliencką.
1. Wyodrębnij pobrane python-client-generated.zip gdzieś w systemie plików.
1. Instalowanie wyodrębnionego modułu Python-Client w środowisku języka Python przy użyciu narzędzia PIP: `pip install path/to/package/python-client` .
1. Zainstalowany pakiet ma nazwę `swagger_client` . Możesz sprawdzić, czy instalacja działała przy użyciu polecenia `python -c "import swagger_client"` .

> [!NOTE]
> Ze względu na [znaną usterkę w autogeneracji struktury Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541)mogą wystąpić błędy podczas importowania `swagger_client` pakietu.
> Można je usunąć, usuwając wiersz z zawartością.
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> z pliku `swagger_client/models/model.py` i wiersza z zawartością
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> z pliku w `swagger_client/models/inner_error.py` zainstalowanym pakiecie. Zostanie wyświetlony komunikat o błędzie z informacją o tym, gdzie znajdują się te pliki na potrzeby instalacji.

## <a name="install-other-dependencies"></a>Instalowanie innych zależności

Przykład używa `requests` biblioteki. Można go zainstalować za pomocą polecenia

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Tworzenie i Konfigurowanie klienta http
Najpierw musimy być klientem http z prawidłowym podstawowym adresem URL i zestawem uwierzytelniania.
Wstaw ten kod w `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generuj żądanie transkrypcji
Następnie wygenerujemy żądanie transkrypcji. Dodaj ten kod do `transcribe`[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Wyślij żądanie i sprawdź jego stan
Teraz wyślemy żądanie do usługi mowy i Sprawdzamy początkowy kod odpowiedzi. Ten kod odpowiedzi będzie po prostu wskazywać, czy usługa odebrała żądanie. Usługa zwróci adres URL w nagłówkach odpowiedzi, które są lokalizacją przechowywania stanu transkrypcji.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Poczekaj na zakończenie transkrypcji
Ponieważ usługa przetwarza proces transkrypcji asynchronicznie, musimy wykonać sondowanie pod kątem jego stanu co do tego często. Sprawdzimy co 5 sekund.

Wyliczmy wszystkie transkrypcje, które są przetwarzane przez ten zasób usługi mowy, i poszukamy utworzonego przez nas.

Oto kod sondowania z wyświetlaniem stanu dla wszystkiego, z wyjątkiem pomyślnego zakończenia, zajmiemy się tym dalej.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Wyświetlanie wyników transkrypcji
Gdy usługa pomyślnie ukończy transkrypcję, wyniki będą przechowywane w innym adresie URL, który można uzyskać z odpowiedzi na stan.

W tym miejscu zostanie pobrany kod JSON wyniku i zostanie on wyświetlony.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Sprawdź swój kod
W tym momencie kod powinien wyglądać następująco: (dodaliśmy Komentarze do tej wersji) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

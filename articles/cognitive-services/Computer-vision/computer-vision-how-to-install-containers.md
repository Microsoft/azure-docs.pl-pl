---
title: Instalowanie kontenerów platformy Docker odczytu OCR z przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Użyj kontenera Read OCR Docker from przetwarzanie obrazów to extract text from images and documents, on-premises (Odczytywanie kontenerów platformy Docker OCR z przetwarzanie obrazów do lokalnego wyodrębniania tekstu z obrazów i dokumentów.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: lokalnie, OCR, Docker, kontener
ms.openlocfilehash: dead48d7d449d1d403359c518eb842b32a54c634
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779097"
---
# <a name="install-read-ocr-docker-containers"></a>Instalowanie kontenerów platformy Docker odczytu ocr

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Kontenery umożliwiają uruchamianie interfejsów API przetwarzania obrazów w Twoim własnym środowisku. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobierać, instalować i uruchamiać kontenery przetwarzanie obrazów kontenerów.

Kontener *odczytu* OCR umożliwia wyodrębnianie tekstu drukowanego i odręcznego z obrazów i dokumentów z obsługą formatów plików JPEG, PNG, BMP, PDF i TIFF. Aby uzyskać więcej informacji, zobacz Read API how-to guide (Przewodnik po [interfejsie API odczytywania).](Vision-API-How-to-Topics/call-read-api.md)

## <a name="read-32-container"></a>Odczytywanie kontenera 3.2

Kontener OCR Read 3.2 zapewnia:
* Nowe modele w celu zwiększenia dokładności.
* Obsługa wielu języków w tym samym dokumencie.
* Obsługa łącznie 73 języków. Zobacz pełną listę języków obsługiwanych [przez OCR.](./language-support.md#optical-character-recognition-ocr)
* Pojedyncza operacja dla dokumentów i obrazów.
* Obsługa większych dokumentów i obrazów.
* Oceny ufności.
* Obsługa dokumentów z tekstem drukowanym i odręcznym.
* Możliwość wyodrębniania tekstu tylko z wybranych stron w dokumencie.
* Wybierz kolejność danych wyjściowych wiersza tekstu z domyślnej na bardziej naturalną kolejność odczytywania tylko dla języków łacińskich.
* Klasyfikacja linii tekstu jako styl odręczny lub nie tylko dla języków łacińskich.

Jeśli obecnie używasz kontenerów read 2.0, zapoznaj się z przewodnikiem migracji, aby dowiedzieć się więcej o zmianach w nowych wersjach. [](read-container-migration-guide.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z kontenerów należy spełnić następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście.](#the-host-computer) Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Platformę Docker należy skonfigurować tak, aby umożliwić kontenerom nawiązywanie połączeń z platformą Azure i wysyłanie danych rozliczeniowych na platformę Azure. <br><br> **W systemie Windows** należy również skonfigurować do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Musisz mieć podstawową wiedzę na temat pojęć dotyczących platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także wiedzę na temat podstawowych `docker` poleceń.| 
|przetwarzanie obrazów zasobów |Aby można było korzystać z kontenera, musisz mieć:<br><br>Zasób usługi Azure **przetwarzanie obrazów** skojarzony klucz interfejsu API i jego klucz URI punktu końcowego. Obie wartości są dostępne na stronach Przegląd i Klucze dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}:** jeden z dwóch dostępnych kluczy zasobów na **stronie** Klucze<br><br>**{ENDPOINT_URI}:** punkt końcowy podany na **stronie Przegląd**|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="request-approval-to-run-the-container"></a>Żądanie zatwierdzenia w celu uruchomienia kontenera

Wypełnij i prześlij formularz [żądania, aby](https://aka.ms/csgate) zażądać zatwierdzenia uruchomienia kontenera. 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer hosta

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Obsługa rozszerzenia Advanced Vector Extension

**Komputer-host** to komputer z uruchomionym kontenerem platformy Docker. Host musi *obsługiwać rozszerzenia* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Aby sprawdzić obsługę AVX2 na hostach z systemem Linux, możesz użyć następującego polecenia:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Komputer hosta jest wymagany *do obsługi* avx2. Kontener nie *będzie działać poprawnie* bez obsługi AVX2.

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenerów

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Dostępne są obrazy kontenerów dla odczytu.

| Kontener | Container Registry / Repozytorium / Nazwa obrazu |
|-----------|------------|
| Przeczytaj 2.0-preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Przeczytaj 3.2 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

Użyj polecenia [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , aby pobrać obraz kontenera.

### <a name="docker-pull-for-the-read-ocr-container"></a>Ściąganie platformy Docker dla kontenera odczytu OCR

# <a name="version-32"></a>[Wersja 3.2](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[Wersja 2.0(wersja zapoznawcza)](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta,](#the-host-computer)użyj następującego procesu, aby pracować z kontenerem.

1. [Uruchom kontener z](#run-the-container-with-docker-run)wymaganymi ustawieniami rozliczeń. [Dostępnych](computer-vision-resource-container-config.md) jest więcej `docker run` przykładów polecenia. 
1. [Odpytuj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [docker run,](https://docs.docker.com/engine/reference/commandline/run/) aby uruchomić kontener. Aby uzyskać [szczegółowe informacje na](#gathering-required-parameters) temat sposobu uzyskania wartości i , zobacz zbieranie `{ENDPOINT_URI}` wymaganych `{API_KEY}` parametrów.

[Dostępne](computer-vision-resource-container-config.md#example-docker-run-commands) są `docker run` przykłady polecenia .

# <a name="version-32"></a>[Wersja 3.2](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener odczytu OCR z obrazu kontenera.
* Przydziela 8 rdzeni procesora CPU i 18 gigabajtów (GB) pamięci.
* Uwidacznia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="version-20-preview"></a>[Wersja 2.0 (wersja zapoznawcza)](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener odczytu OCR z obrazu kontenera.
* Przydziela 8 rdzeni procesora CPU i 16 gigabajtów (GB) pamięci.
* Uwidacznia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po jego zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

---


[Dostępnych](./computer-vision-resource-container-config.md#example-docker-run-commands) jest więcej `docker run` przykładów polecenia. 

> [!IMPORTANT]
> Aby uruchomić kontener, należy określić opcje , i . W przeciwnym razie `Eula` `Billing` kontener nie zostanie `ApiKey` uruchomiony.  Aby uzyskać więcej informacji, zobacz [Rozliczenia](#billing).

Jeśli potrzebujesz wyższej przepływności (na przykład podczas przetwarzania plików wielostronicowych), rozważ wdrożenie wielu kontenerów w klastrze [Kubernetes](deploy-computer-vision-on-premises.md)przy użyciu usług [Azure Storage](../../storage/common/storage-account-create.md) i [Azure Queue.](../../storage/queues/storage-queues-introduction.md)

Jeśli używasz usługi Azure Storage do przechowywania obrazów do przetwarzania, możesz utworzyć parametrów [połączenia](../../storage/common/storage-configure-connection-string.md) do użycia podczas wywoływania kontenera.

Aby znaleźć swoje ciągi połączenia:

1. Przejdź do **konta magazynu** na Azure Portal i znajdź swoje konto.
2. Kliknij pozycję **Klucze dostępu na** liście nawigacji po lewej stronie.
3. Twoje ciągi połączenia będą się znajdowały poniżej **parametrów połączenia**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań. 

# <a name="version-32"></a>[Wersja 3.2](#tab/version-3-2)

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera. Ścieżkę swagger można wyświetlić pod: `http://localhost:5000/swagger/vision-v3.2-read/swagger.json` .

# <a name="version-20-preview"></a>[Wersja 2.0(wersja zapoznawcza)](#tab/version-2)

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera. Ścieżkę swagger można wyświetlić pod: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Odczyt asynchroniczny


# <a name="version-32"></a>[Wersja 3.2](#tab/version-3-2)

Operacje i mogą być używane do asynchronicznego odczytywania obrazu, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich `POST /vision/v3.2/read/analyze` `GET /vision/v3.2/read/operations/{operationId}` operacji REST. Asynchroniczna metoda POST zwróci element , który jest używany jako `operationId` identyfikator żądania HTTP GET.


W interfejsie użytkownika programu Swagger wybierz element , `Analyze` aby rozwinąć go w przeglądarce. Następnie wybierz **pozycję Wypróbuj wybierz**  >  **plik**. W tym przykładzie użyjemy następującego obrazu:

![tabulatory a spacje](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchronicznego żądania POST zwracany jest kod **stanu HTTP 202.** W ramach odpowiedzi znajduje się nagłówek, który `operation-location` zawiera punkt końcowy wyniku żądania.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

To `operation-location` w pełni kwalifikowany adres URL dostępny za pośrednictwem żądania HTTP GET. Oto odpowiedź JSON z wykonania adresu `operation-location` URL z poprzedniego obrazu:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Wersja 2.0(wersja zapoznawcza)](#tab/version-2)

Operacje i mogą być używane do asynchronicznego odczytywania obrazu, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` operacji REST. Asynchroniczna metoda POST zwróci element , który jest używany jako `operationId` identyfikator żądania HTTP GET.

W interfejsie użytkownika programu Swagger wybierz element , `asyncBatchAnalyze` aby rozwinąć go w przeglądarce. Następnie wybierz **pozycję Wypróbuj wybierz**  >  **plik**. W tym przykładzie użyjemy następującego obrazu:

![tabulatory a spacje](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchronicznego żądania POST zwracany jest kod **stanu HTTP 202.** W ramach odpowiedzi znajduje się nagłówek, który `operation-location` zawiera punkt końcowy wyniku żądania.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

To `operation-location` w pełni kwalifikowany adres URL dostępny za pośrednictwem żądania HTTP GET. Oto odpowiedź JSON z wykonania adresu `operation-location` URL z poprzedniego obrazu:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Jeśli wdrażasz wiele kontenerów odczytu OCR za usługą równoważenia obciążenia, na przykład w obszarze Docker Compose lub Kubernetes, musisz mieć zewnętrzną pamięć podręczną. Ponieważ kontener przetwarzania i kontener żądań GET mogą nie być takie same, zewnętrzna pamięć podręczna przechowuje wyniki i udostępnia je między kontenerami. Aby uzyskać szczegółowe informacje na temat ustawień pamięci podręcznej, [zobacz Przetwarzanie obrazów kontenerów platformy Docker.](./computer-vision-resource-container-config.md)

### <a name="synchronous-read"></a>Odczyt synchroniczny

Aby synchronicznie odczytać obraz, można użyć następującej operacji. 

# <a name="version-32"></a>[Wersja 3.2](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Wersja 2.0(wersja zapoznawcza)](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Gdy obraz jest odczytywany w całości, interfejs API zwraca odpowiedź JSON. Jedynym wyjątkiem od tej reguły jest sytuacja, w przypadku wystąpienia błędu. W przypadku wystąpienia błędu jest zwracany następujący kod JSON:

```json
{
    "status": "Failed"
}
```

Obiekt odpowiedzi JSON ma ten sam wykres obiektu co wersja asynchroniczna. Jeśli jesteś użytkownikiem języka JavaScript i chcesz mieć bezpieczeństwo typów, rozważ użycie języka TypeScript do rzutowania odpowiedzi JSON.

Aby uzyskać przykładowy przypadek użycia, zobacz piaskownicę <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">języka TypeScript w</a> tym miejscu i wybierz pozycję **Uruchom,** aby zwizualizować jej łatwość użycia.

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [](./computer-vision-resource-container-config.md#mount-settings) włączoną instalacją wyjściową i włączonym rejestrowaniem, kontener generuje pliki dziennika, które są pomocne w rozwiązywaniu problemów, które mają miejsce podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery Cognitive Services wysyłają informacje rozliczeniowe na platformę Azure przy użyciu odpowiedniego zasobu na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](./computer-vision-resource-container-config.md). 

## <a name="summary"></a>Podsumowanie

W tym artykule o pojęciach i przepływach pracy dotyczących pobierania, instalowania i uruchamiania przetwarzanie obrazów kontenerów. Podsumowanie:

* przetwarzanie obrazów udostępnia kontener systemu Linux dla platformy Docker, hermetyzując odczyt.
* Do uruchomienia obrazu kontenera odczytu jest wymagana aplikacja. 
* Obrazy kontenerów są uruchamiane na platformy Docker.
* Możesz użyć interfejsu API REST lub zestawu SDK do wywołania operacji w kontenerach odczytu OCR, określając adres URI hosta kontenera.
* Informacje rozliczeniowe należy określić podczas wystąpienia kontenera.

> [!IMPORTANT]
> Cognitive Services nie mają licencji na uruchamianie bez połączenia z platformą Azure w celu zmierzania. Klienci muszą włączyć kontenery, aby przez cały czas przekazywać informacje dotyczące rozliczeń z usługą pomiarów. Cognitive Services nie wysyłają danych klienta (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się [z konfigurowaniem kontenerów](computer-vision-resource-container-config.md) dla ustawień konfiguracji
* Przejrzyj omówienie [OCR, aby](overview-ocr.md) dowiedzieć się więcej o rozpoznawaniu tekstu drukowanego i odręcznego
* Zapoznaj się z [interfejsem API odczytu,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) aby uzyskać szczegółowe informacje o metodach obsługiwanych przez kontener.
* Zapoznaj się z [często zadawanymi pytaniami,](FAQ.md) aby rozwiązać problemy związane z przetwarzanie obrazów funkcjonalnością.
* Używanie większej [Cognitive Services kontenerów](../cognitive-services-container-support.md)

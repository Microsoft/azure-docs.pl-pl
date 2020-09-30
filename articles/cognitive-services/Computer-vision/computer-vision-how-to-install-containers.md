---
title: Zainstaluj kontenery Docker OCR z przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Używanie kontenerów platformy Docker OCR z przetwarzanie obrazów do wyodrębniania tekstu z obrazów i douments lokalnych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: lokalna, OCR, Docker, kontener
ms.openlocfilehash: acf6a391965dcba20a2dabc18648076b88c5e7c5
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536379"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Instalowanie kontenerów platformy Docker OCR (wersja zapoznawcza) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Kontenery umożliwiają uruchamianie interfejsów API przetwarzania obrazów w Twoim własnym środowisku. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobierać, instalować i uruchamiać kontenery przetwarzanie obrazów.

Kontener OCR do *odczytu* umożliwia wyodrębnienie tekstu napisanego i odręcznego z obrazów i dokumentów z obsługą formatów plików JPEG, PNG, BMP, PDF i TIFF. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API odczytu](concept-recognizing-text.md#read-api).

## <a name="read-3x-containers"></a>Odczytywanie kontenerów 3. x
Istnieją dwie wersje kontenerów 3. x dostępnych w wersji zapoznawczej. Obie wersje zapewniają dodatkową dokładność i funkcje w poprzednim kontenerze.

Kontener Read 3,0-Preview zawiera następujące informacje:
* Nowe modele na potrzeby ulepszonej dokładności.
* Obsługa wielu języków w tym samym dokumencie
* Obsługa: holenderski, angielski, francuski, niemiecki, włoski, portugalski i hiszpański.
* Pojedyncza operacja dla dokumentów i obrazów.
* Obsługa większych dokumentów i obrazów.
* Wyniki zaufania z przedziału od 0 do 1.
* Obsługa dokumentów z tekstem drukowanym i odręcznym

Kontener Read 3,1-Preview zapewnia te same korzyści jak w przypadku wersji 3.0 — wersja zapoznawcza z dodatkowymi funkcjami:

* Obsługa języka chińskiego uproszczonego i japońskiego.
* wyniki i etykiety dotyczące ufności dla wydrukowanych i odręcznych tekstu. 
* Możliwość wyodrębnienia tekstu z tylko wybranych stron w dokumencie.

Biorąc pod uwagę wersję kontenera, która ma być używana, należy pamiętać, że wersja zapoznawcza programu v 3.1 jest wcześniejsza niż wersja zapoznawcza. Jeśli obecnie używasz kontenerów z Przeczytajm 2,0, zapoznaj się z [przewodnikiem migracji](read-container-migration-guide.md) , aby dowiedzieć się więcej o zmianach w nowych wersjach.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów należy spełnić następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows**program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.| 
|Zasób przetwarzanie obrazów |Aby można było używać kontenera, musisz mieć:<br><br>Zasób usługi Azure **Przetwarzanie obrazów** i skojarzony klucz interfejsu API dla identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach przeglądów i kluczy dla zasobu i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}**: punkt końcowy określony na stronie **Przegląd**|

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="request-approval-to-run-the-container"></a>Żądaj zatwierdzenia do uruchomienia kontenera

Wypełnij i prześlij [formularz żądania](https://aka.ms/csgate) , aby zażądać zatwierdzenia do uruchomienia kontenera. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Obsługa zaawansowanych rozszerzeń wektora

Komputer **hosta** to komputer z uruchomionym kontenerem Docker. Host *musi obsługiwać* [Zaawansowane rozszerzenia wektorów](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Obsługę AVX2 można sprawdzić na hostach z systemem Linux przy użyciu następującego polecenia:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Komputer hosta jest *wymagany* do obsługi AVX2. Kontener *nie będzie* działać poprawnie bez obsługi AVX2.

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Dostępne są obrazy kontenerów do odczytu.

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|-----------|------------|
| Przeczytaj 2,0 — wersja zapoznawcza | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Przeczytaj 3,0 — wersja zapoznawcza | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| Przeczytaj 3,1 — wersja zapoznawcza | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera.

### <a name="docker-pull-for-the-read-container"></a>Wypychanie platformy Docker dla kontenera odczytu

# <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

# <a name="version-20-preview"></a>[Wersja 2,0-Preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](computer-vision-resource-container-config.md) `docker run` polecenia jest dostępnych. 
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat sposobu pobierania `{ENDPOINT_URI}` `{API_KEY}` wartości i.

[Przykłady](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` polecenia są dostępne.

# <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener odczytu z obrazu kontenera.
* Przypisuje 8 rdzeni procesora CPU i 18 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

To polecenie:

* Uruchamia kontener odczytu z obrazu kontenera.
* Przypisuje 8 rdzeni procesora CPU i 18 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

# <a name="version-20-preview"></a>[Wersja 2,0-Preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener odczytu z obrazu kontenera.
* Przypisuje 8 rdzeni procesora i 16 gigabajtów (GB) pamięci.
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

---


Więcej [przykładów](./computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` polecenia jest dostępnych. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

Jeśli potrzebujesz większej przepływności (na przykład podczas przetwarzania plików wielostronicowych), rozważ wdrożenie wielu kontenerów v 3.0 lub v 3.1 [w klastrze Kubernetes](deploy-computer-vision-on-premises.md)przy użyciu [usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create) i [usługi Azure Queue](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction).

Jeśli używasz usługi Azure Storage do przechowywania obrazów do przetworzenia, możesz utworzyć [Parametry połączenia](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) , które będą używane podczas wywoływania kontenera.

Aby znaleźć parametry połączenia:

1. Przejdź do **konta magazynu** w Azure Portal i Znajdź swoje konto.
2. Kliknij pozycję **klucze dostępu** na liście nawigacji po lewej stronie.
3. Parametry połączenia będą znajdować się poniżej **parametrów połączenia**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań. 

# <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-1)

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera. Ścieżkę Swagger można wyświetlić w: `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` .

# <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera. Ścieżkę Swagger można wyświetlić w: `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Wersja 2,0-Preview](#tab/version-2)

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera. Ścieżkę Swagger można wyświetlić w: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Odczyt asynchroniczny


# <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-1)

Możesz użyć `POST /vision/v3.1/read/analyze` `GET /vision/v3.1/read/operations/{operationId}` operacji i w drodze do asynchronicznego odczytywania obrazu, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich operacji Rest. Asynchroniczna Metoda POST zwróci wartość `operationId` , która jest używana jako identyfikator do żądania HTTP GET.


W interfejsie użytkownika programu Swagger wybierz pozycję, `asyncBatchAnalyze` Aby rozwinąć ją w przeglądarce. Następnie wybierz opcję **Wypróbuj**  >  **plik**. W tym przykładzie użyjemy poniższej ilustracji:

![karty vs Spaces](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchronicznego wpisu zwraca kod stanu **HTTP 202** . W ramach odpowiedzi istnieje `operation-location` nagłówek, który zawiera punkt końcowy wyniku żądania.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Jest to w pełni kwalifikowany adres URL i jest dostępny za pośrednictwem protokołu HTTP GET. Oto odpowiedź JSON z wykonania `operation-location` adresu URL z poprzedniego obrazu:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

Możesz użyć `POST /vision/v3.0/read/analyze` `GET /vision/v3.0/read/operations/{operationId}` operacji i w drodze do asynchronicznego odczytywania obrazu, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich operacji Rest. Asynchroniczna Metoda POST zwróci wartość `operationId` , która jest używana jako identyfikator do żądania HTTP GET.

W interfejsie użytkownika programu Swagger wybierz pozycję, `asyncBatchAnalyze` Aby rozwinąć ją w przeglądarce. Następnie wybierz opcję **Wypróbuj**  >  **plik**. W tym przykładzie użyjemy poniższej ilustracji:

![karty vs Spaces](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchronicznego wpisu zwraca kod stanu **HTTP 202** . W ramach odpowiedzi istnieje `operation-location` nagłówek, który zawiera punkt końcowy wyniku żądania.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Jest to w pełni kwalifikowany adres URL i jest dostępny za pośrednictwem protokołu HTTP GET. Oto odpowiedź JSON z wykonania `operation-location` adresu URL z poprzedniego obrazu:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Wersja 2,0-Preview](#tab/version-2)

Możesz użyć `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` operacji i w drodze do asynchronicznego odczytywania obrazu, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich operacji Rest. Asynchroniczna Metoda POST zwróci wartość `operationId` , która jest używana jako identyfikator do żądania HTTP GET.

W interfejsie użytkownika programu Swagger wybierz pozycję, `asyncBatchAnalyze` Aby rozwinąć ją w przeglądarce. Następnie wybierz opcję **Wypróbuj**  >  **plik**. W tym przykładzie użyjemy poniższej ilustracji:

![karty vs Spaces](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchronicznego wpisu zwraca kod stanu **HTTP 202** . W ramach odpowiedzi istnieje `operation-location` nagłówek, który zawiera punkt końcowy wyniku żądania.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Jest to w pełni kwalifikowany adres URL i jest dostępny za pośrednictwem protokołu HTTP GET. Oto odpowiedź JSON z wykonania `operation-location` adresu URL z poprzedniego obrazu:

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
> W przypadku wdrożenia wielu kontenerów odczytu za modułem równoważenia obciążenia, na przykład w obszarze Docker Compose lub Kubernetes, wymagana jest zewnętrzna pamięć podręczna. Ponieważ kontener przetwarzania i kontener żądania GET nie mogą być takie same, zewnętrzna pamięć podręczna przechowuje wyniki i udostępnia je między kontenerami. Aby uzyskać szczegółowe informacje na temat ustawień pamięci podręcznej, zobacz [Configure przetwarzanie obrazów Docker Containers](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).

### <a name="synchronous-read"></a>Odczyt synchroniczne

Aby synchronicznie odczytywać obraz, można użyć następującej operacji. 

# <a name="version-31-preview"></a>[Wersja 3,1-Preview](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

# <a name="version-20-preview"></a>[Wersja 2,0-Preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Gdy obraz jest odczytywany w całości, wówczas i tylko wtedy, gdy interfejs API zwróci odpowiedź JSON. Jedynym wyjątkiem jest to, że wystąpi błąd. Gdy wystąpi błąd, zwracany jest następujący kod JSON:

```json
{
    "status": "Failed"
}
```

Obiekt odpowiedzi JSON ma ten sam Graf obiektów co wersja asynchroniczna. Jeśli jesteś użytkownikiem języka JavaScript i potrzebujesz bezpieczeństwa typu, rozważ użycie języka TypeScript do rzutowania odpowiedzi JSON.

Aby zapoznać się z przykładowym przypadkiem użycia, zobacz <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">piaskownicę TypeScript <span class="docon docon-navigate-external x-hidden-focus"></span> tutaj</a> i wybierz pozycję **Uruchom** , aby wizualizować jej łatwość użytkowania.

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](./computer-vision-resource-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery Cognitive Services wysyłają informacje o rozliczeniach do platformy Azure przy użyciu odpowiedniego zasobu na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów przetwarzanie obrazów. Podsumowanie:

* Przetwarzanie obrazów udostępnia kontener systemu Linux dla platformy Docker, hermetyzując odczyt.
* Obrazy kontenerów są pobierane z rejestru kontenerów "Podgląd kontenera" na platformie Azure.
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Do wywoływania operacji w kontenerach odczytu można użyć interfejsu API REST lub zestawu SDK, określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (na przykład obrazu lub tekstu, który jest analizowany) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](computer-vision-resource-container-config.md) dla ustawień konfiguracji
* Przejrzyj [przegląd przetwarzanie obrazów](overview.md) , aby dowiedzieć się więcej o rozpoznawaniu tekstu napisanego i odręcznego
* Zapoznaj się z [interfejs API przetwarzania obrazów](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , aby uzyskać szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się z [często zadawanymi pytaniami](FAQ.md) , aby rozwiązać problemy związane z funkcjami przetwarzanie obrazów.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)

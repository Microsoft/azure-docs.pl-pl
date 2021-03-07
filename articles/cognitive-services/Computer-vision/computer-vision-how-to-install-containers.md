---
title: Zainstaluj kontenery Docker OCR z przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Używanie kontenerów platformy Docker OCR z przetwarzanie obrazów do wyodrębniania tekstu z obrazów i dokumentów w środowisku lokalnym.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: lokalna, OCR, Docker, kontener
ms.openlocfilehash: 1c9e681e3c02cb65b2a54070cc778051a0e7ac53
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432557"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Instalowanie kontenerów platformy Docker OCR (wersja zapoznawcza) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Kontenery umożliwiają uruchamianie interfejsów API przetwarzania obrazów w Twoim własnym środowisku. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobierać, instalować i uruchamiać kontenery przetwarzanie obrazów.

Kontener OCR do *odczytu* umożliwia wyodrębnienie tekstu napisanego i odręcznego z obrazów i dokumentów z obsługą formatów plików JPEG, PNG, BMP, PDF i TIFF. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API odczytu](concept-recognizing-text.md#read-api).

## <a name="read-32-preview-container"></a>Odczyt kontenera 3,2-Preview

> [!NOTE]
> Kontener Read 3,0-Preview został uznany za przestarzały. 

Kontener OCR Read 3,2-Preview umożliwia:
* Nowe modele na potrzeby ulepszonej dokładności.
* Obsługa wielu języków w tym samym dokumencie.
* Obsługa łącznie 73 języków. Zapoznaj się z pełną listą [języków obsługiwanych przez aparat OCR](./language-support.md#optical-character-recognition-ocr).
* Pojedyncza operacja dla dokumentów i obrazów.
* Obsługa większych dokumentów i obrazów.
* Wyniki pewności.
* Obsługa dokumentów z drukowanym i odręcznym tekstem.
* Możliwość wyodrębnienia tekstu z tylko wybranych stron w dokumencie.
* Wybierz pozycję kolejność wyprowadzania tekstu w wierszu od domyślnego do bardziej naturalnej kolejności odczytywania tylko dla języków łacińskich.
* Klasyfikacja wiersza tekstu jako styl pisma ręcznego lub nie tylko w przypadku języków łacińskich.

Jeśli obecnie używasz kontenerów z Przeczytajm 2,0, zapoznaj się z [przewodnikiem migracji](read-container-migration-guide.md) , aby dowiedzieć się więcej o zmianach w nowych wersjach.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów należy spełnić następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows** program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
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
| Przeczytaj 3,2 — wersja zapoznawcza | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2` |

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera.

### <a name="docker-pull-for-the-read-container"></a>Wypychanie platformy Docker dla kontenera odczytu

# <a name="version-32-preview"></a>[Wersja 3,2-Preview](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2
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

# <a name="version-32-preview"></a>[Wersja 3,2-Preview](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.2 \
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

Jeśli potrzebujesz większej przepływności (na przykład podczas przetwarzania plików wielostronicowych), rozważ wdrożenie wielu kontenerów [w klastrze Kubernetes](deploy-computer-vision-on-premises.md)przy użyciu [usługi Azure Storage](../../storage/common/storage-account-create.md) i [usługi Azure Queue](../../storage/queues/storage-queues-introduction.md).

Jeśli używasz usługi Azure Storage do przechowywania obrazów do przetworzenia, możesz utworzyć [Parametry połączenia](../../storage/common/storage-configure-connection-string.md) , które będą używane podczas wywoływania kontenera.

Aby znaleźć parametry połączenia:

1. Przejdź do **konta magazynu** w Azure Portal i Znajdź swoje konto.
2. Kliknij pozycję **klucze dostępu** na liście nawigacji po lewej stronie.
3. Parametry połączenia będą znajdować się poniżej **parametrów połączenia**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań. 

# <a name="version-32-preview"></a>[Wersja 3,2-Preview](#tab/version-3-2)

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera. Ścieżkę Swagger można wyświetlić w: `http://localhost:5000/swagger/vision-v3.2-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Wersja 2,0-Preview](#tab/version-2)

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera. Ścieżkę Swagger można wyświetlić w: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Odczyt asynchroniczny


# <a name="version-32-preview"></a>[Wersja 3,2-Preview](#tab/version-3-2)

Możesz użyć `POST /vision/v3.2/read/analyze` `GET /vision/v3.2/read/operations/{operationId}` operacji i w drodze do asynchronicznego odczytywania obrazu, podobnie jak usługa przetwarzanie obrazów używa tych odpowiednich operacji Rest. Asynchroniczna Metoda POST zwróci wartość `operationId` , która jest używana jako identyfikator do żądania HTTP GET.


W interfejsie użytkownika programu Swagger wybierz pozycję, `Analyze` Aby rozwinąć ją w przeglądarce. Następnie wybierz opcję **Wypróbuj**  >  **plik**. W tym przykładzie użyjemy poniższej ilustracji:

![karty vs Spaces](media/tabs-vs-spaces.png)

Po pomyślnym uruchomieniu asynchronicznego wpisu zwraca kod stanu **HTTP 202** . W ramach odpowiedzi istnieje `operation-location` nagłówek, który zawiera punkt końcowy wyniku żądania.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`Jest to w pełni kwalifikowany adres URL i jest dostępny za pośrednictwem protokołu HTTP GET. Oto odpowiedź JSON z wykonania `operation-location` adresu URL z poprzedniego obrazu:

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
> W przypadku wdrożenia wielu kontenerów odczytu za modułem równoważenia obciążenia, na przykład w obszarze Docker Compose lub Kubernetes, wymagana jest zewnętrzna pamięć podręczna. Ponieważ kontener przetwarzania i kontener żądania GET nie mogą być takie same, zewnętrzna pamięć podręczna przechowuje wyniki i udostępnia je między kontenerami. Aby uzyskać szczegółowe informacje na temat ustawień pamięci podręcznej, zobacz [Configure przetwarzanie obrazów Docker Containers](./computer-vision-resource-container-config.md).

### <a name="synchronous-read"></a>Odczyt synchroniczne

Aby synchronicznie odczytywać obraz, można użyć następującej operacji. 

# <a name="version-32-preview"></a>[Wersja 3,2-Preview](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

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

Aby zapoznać się z przykładowym przypadkiem użycia, zobacz <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">piaskownicę TypeScript tutaj</a> i wybierz pozycję **Uruchom** , aby wizualizować jej łatwość użytkowania.

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](./computer-vision-resource-container-config.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery Cognitive Services wysyłają informacje o rozliczeniach do platformy Azure przy użyciu odpowiedniego zasobu na koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](./computer-vision-resource-container-config.md). 

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
* Zapoznaj się z [interfejs API przetwarzania obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) , aby uzyskać szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się z [często zadawanymi pytaniami](FAQ.md) , aby rozwiązać problemy związane z funkcjami przetwarzanie obrazów.
* Użyj więcej [kontenerów Cognitive Services](../cognitive-services-container-support.md)

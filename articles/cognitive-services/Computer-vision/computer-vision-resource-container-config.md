---
title: Konfigurowanie kontenerów OCR do odczytu — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób konfigurowania ustawień wymaganych i opcjonalnych dla kontenerów OCR odczytu w przetwarzanie obrazów.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 3e6c4b73e8aeb26c6ac4025ef3c07fb4f8d48eaf
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308646"
---
# <a name="configure-read-ocr-docker-containers"></a>Konfigurowanie kontenerów platformy Docker OCR

Należy skonfigurować środowisko uruchomieniowe kontenera OCR przetwarzanie obrazów Read przy użyciu `docker run` argumentów polecenia. Ten kontener ma kilka wymaganych ustawień oraz kilka opcjonalnych ustawień. Kilka [przykładów](#example-docker-run-commands) polecenia jest dostępnych. Ustawienia dotyczące rozliczeń dotyczą tylko kontenera. 

## <a name="configuration-settings"></a>Ustawienia konfiguracji

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Ustawienia, [`Billing`](#billing-configuration-setting) , i [`Eula`](#eula-setting) są używane razem i należy podać prawidłowe wartości dla wszystkich trzech z nich; w przeciwnym razie kontener nie zostanie uruchomiony. Aby uzyskać więcej informacji na temat tworzenia wystąpienia kontenera przy użyciu tych ustawień konfiguracji, zobacz [rozliczenia](computer-vision-how-to-install-containers.md).

Kontener ma również następujące ustawienia konfiguracji specyficzne dla kontenera:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Nie|ReadEngineConfig:ResultExpirationPeriod| tylko kontenery w wersji 2.0. Okres ważności wyniku w godzinach. Wartość domyślna to 48 godzin. Ustawienie określa, kiedy system powinien czyścić wyniki rozpoznawania. Na przykład, jeśli `resultExpirationPeriod=1` system wyczyści wynik rozpoznawania 1 godzina po zakończeniu procesu. Jeśli `resultExpirationPeriod=0` system czyści wynik rozpoznawania po pobraniu wyniku.|
|Nie|Pamięć podręczna: Redis| tylko kontenery w wersji 2.0. Włącza magazyn Redis do przechowywania wyników. Pamięć podręczna jest *wymagana* , jeśli wiele kontenerów OCR do odczytu jest umieszczonych za modułem równoważenia obciążenia.|
|Nie|Kolejka: RabbitMQ|tylko kontenery w wersji 2.0. Umożliwia RabbitMQ do wysyłania zadań. Ustawienie jest przydatne, gdy wiele kontenerów OCR do odczytu jest umieszczonych za modułem równoważenia obciążenia.|
|Nie|Kolejka: Azure: QueueVisibilityTimeoutInMilliseconds | tylko kontenery v3. x. Godzina, o której komunikat ma być niewidoczny w przypadku przetwarzania przez inny proces roboczy. |
|Nie|Magazyn::D ocumentStore:: MongoDB|tylko kontenery w wersji 2.0. Włącza MongoDB na potrzeby trwałego magazynu wyników. |
|Nie|Storage: ObjectStore: AzureBlob: ConnectionString| tylko kontenery v3. x. Parametry połączenia usługi Azure Blob Storage. |
|Nie|Magazyn: TimeToLiveInDays| tylko kontenery v3. x. Okres ważności wyniku w dniach. Ustawienie określa, kiedy system powinien czyścić wyniki rozpoznawania. Wartość domyślna to 2 dni (48 godzin), co oznacza, że wszystkie wyniki na żywo dłużej niż ten okres nie zostaną pomyślnie pobrane. |
|Nie|Zadanie: MaxRunningTimeSpanInMinutes| tylko kontenery v3. x. Maksymalny czas działania jednego żądania. Wartość domyślna to 60 min. |

## <a name="apikey-configuration-setting"></a>Ustawienie konfiguracji ApiKey

`ApiKey`Ustawienie określa `Cognitive Services` klucz zasobów platformy Azure służący do śledzenia informacji rozliczeniowych dla kontenera. Należy określić wartość ApiKey, a wartość musi być prawidłowym kluczem dla zasobu _Cognitive Services_ określonego dla [`Billing`](#billing-configuration-setting) Ustawienia konfiguracji.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** zarządzania zasobami w obszarze **klucze**

## <a name="applicationinsights-setting"></a>Ustawienie ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Ustawienie konfiguracji rozliczeń

`Billing`Ustawienie określa identyfikator URI punktu końcowego zasobu _Cognitive Services_ na platformie Azure używany do mierzenia informacji rozliczeniowych dla kontenera. Należy określić wartość tego ustawienia konfiguracji, a wartość musi być prawidłowym identyfikatorem URI punktu końcowego dla zasobu _Cognitive Services_ na platformie Azure. Kontener zgłasza użycie co 10 do 15 minut.

To ustawienie można znaleźć w następujących miejscach:

* Azure Portal: **Cognitive Services** Omówienie, etykieta `Endpoint`

Pamiętaj, aby dodać `vision/<version>` routing do identyfikatora URI punktu końcowego, jak pokazano w poniższej tabeli. 

|Wymagane| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Tak| `Billing` | Ciąg | Identyfikator URI punktu końcowego rozliczenia<br><br>Przykład:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v3.2` |

## <a name="eula-setting"></a>Ustawienie umowy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Ustawienia pozostały

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Ustawienia poświadczeń serwera proxy HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Ustawienia logowania
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Ustawienia instalacji

Użyj instalacji powiązań, aby odczytywać i zapisywać dane w kontenerze i z niego. Można określić instalację wejściową lub instalację wyjściową, określając `--mount` opcję w poleceniu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Kontenery przetwarzanie obrazów nie używają instalacji danych wejściowych lub wyjściowych do przechowywania danych szkoleniowych lub usług. 

Dokładna składnia lokalizacji instalacji hosta różni się w zależności od systemu operacyjnego hosta. Ponadto lokalizacja instalacji [komputera hosta](computer-vision-how-to-install-containers.md#the-host-computer)może być niedostępna z powodu konfliktu między uprawnieniami używanymi przez konto usługi platformy Docker i uprawnieniami lokalizacji instalacji hosta. 

|Opcjonalne| Nazwa | Typ danych | Opis |
|-------|------|-----------|-------------|
|Niedozwolone| `Input` | Ciąg | Kontenery przetwarzanie obrazów nie używają tego.|
|Opcjonalne| `Output` | Ciąg | Obiekt docelowy instalacji wyjściowej. Wartość domyślna to `/output`. Jest to lokalizacja dzienników. Dotyczy to również dzienników kontenerów. <br><br>Przykład:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Przykładowe polecenia uruchamiania platformy Docker

W poniższych przykładach użyto ustawień konfiguracji, aby zilustrować sposób pisania i używania `docker run` poleceń.  Po uruchomieniu kontenera kontynuuje działanie, dopóki nie zostanie [zatrzymane](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Znak kontynuacji wiersza**: polecenia platformy Docker w poniższych sekcjach używają ukośnika odwrotnego, `\` jako znaku kontynuacji wiersza. Zastąp lub usuń to w zależności od wymagań systemu operacyjnego hosta. 
* **Kolejność** argumentów: nie zmieniaj kolejności argumentów, o ile nie znasz już kontenerów platformy Docker.

Zastąp ciąg {_argument_name_} własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz punktu końcowego `Computer Vision` zasobu na stronie kluczy platformy Azure `Computer Vision` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Wartość punktu końcowego rozliczenia jest dostępna na `Computer Vision` stronie Przegląd platformy Azure.| Zobacz [zbieranie wymaganych parametrów](computer-vision-how-to-install-containers.md#gathering-required-parameters) dla jawnych przykładów. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](computer-vision-how-to-install-containers.md#billing).
> Wartość ApiKey jest **kluczem** ze `Cognitive Services` strony klucze zasobów platformy Azure.

## <a name="container-docker-examples"></a>Przykłady kontenera Docker

Poniższe przykłady platformy Docker dotyczą kontenera OCR do odczytu.


# <a name="version-32"></a>[Wersja 3,2](#tab/version-3-2)

### <a name="basic-example"></a>Przykład podstawowy

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Przykład rejestrowania 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Wersja 2,0-Preview](#tab/version-2)

### <a name="basic-example"></a>Przykład podstawowy

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>Przykład rejestrowania 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Następne kroki

* Zapoznaj [się z tematem Instalowanie i uruchamianie kontenerów](computer-vision-how-to-install-containers.md).

---
title: Instalowanie i uruchamianie kontenerów platformy Docker dla interfejsu API wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Algorytmy API wykrywania anomalii umożliwiają Znajdowanie anomalii w danych, lokalnie przy użyciu kontenera Docker.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbullwin
ms.custom: cog-serv-seo-aug-2020
keywords: lokalne, Docker, kontener, przesyłanie strumieniowe, algorytmy
ms.openlocfilehash: 70e5950f6577ce2cca2f28be070f3ba372d46a7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97862307"
---
# <a name="install-and-run-docker-containers-for-the-anomaly-detector-api"></a>Instalowanie i uruchamianie kontenerów platformy Docker dla interfejsu API wykrywania anomalii 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Kontenery umożliwiają korzystanie z interfejsu API wykrywania anomalii własnego środowiska. Kontenery doskonale nadają się do określonych wymagań w zakresie zabezpieczeń i zarządzania danymi. W tym artykule dowiesz się, jak pobrać, zainstalować i uruchomić kontener wykrywania anomalii.

Detektor anomalii oferuje pojedynczy kontener platformy Docker służący do korzystania z interfejsu API lokalnie. Użyj kontenera, aby:
* Korzystanie z algorytmów wykrywania anomalii na danych
* Monitoruj dane przesyłane strumieniowo i wykrywaj anomalie w czasie rzeczywistym.
* Wykrywaj anomalie w zestawie danych jako Partia zadań. 
* Wykryj punkty zmian trendu w zestawie danych jako zadanie wsadowe.
* Dostosuj czułość algorytmu wykrywania anomalii, aby lepiej dopasować dane.

Aby uzyskać szczegółowe informacje na temat interfejsu API, zobacz:
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów wykrywania anomalii należy spełnić następujące wymagania wstępne:

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> **W systemie Windows** program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Należy dysponować podstawową wiedzą na temat pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych `docker` poleceń.|
|Zasób wykrywania anomalii |Aby można było korzystać z tych kontenerów, musisz mieć:<br><br>Zasób _wykrywania anomalii_ platformy Azure w celu pobrania skojarzonego klucza interfejsu API i identyfikatora URI punktu końcowego. Obie wartości są dostępne na stronach omówienie i klucze **wykrywania anomalii** Azure Portal i są wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}**: punkt końcowy określony na stronie **Przegląd**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](../../iot-edge/index.yml). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

W poniższej tabeli opisano minimalną i zalecaną liczbę rdzeni procesora CPU oraz ilość pamięci do przydzielenia dla kontenera wykrywania anomalii.

| ZAPYTAŃ (zapytania na sekundę) | Minimum | Zalecane |
|-----------|---------|-------------|
| 10 ZAPYTAŃ | 4 rdzenie, 1 GB pamięci | 8 rdzeni 2 GB pamięci |
| 20 ZAPYTAŃ | 8 rdzeni, 2 GB pamięci | 16 rdzeni 4 GB pamięci |

Każdy rdzeń musi mieć co najmniej 2,6 gigaherca (GHz) lub szybszy.

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Użyj [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) polecenia, aby pobrać obraz kontenera.

| Kontener | Repozytorium |
|-----------|------------|
| poznawcze-usługi-wykrywanie anomalii | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Wypychanie platformy Docker dla kontenera wykrywania anomalii

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Jak używać kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run)z wymaganymi ustawieniami rozliczania. Więcej [przykładów](anomaly-detector-container-configuration.md#example-docker-run-commands) `docker run` polecenia jest dostępnych.
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Uruchom kontener za pomocą `docker run`

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat sposobu pobierania `{ENDPOINT_URI}` `{API_KEY}` wartości i.

[Przykłady](anomaly-detector-container-configuration.md#example-docker-run-commands) `docker run` polecenia są dostępne.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener wykrywania anomalii z obrazu kontenera
* Przypisuje jedne rdzeń procesora CPU i 4 gigabajty (GB) pamięci
* Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener, należy określić opcje, i. w przeciwnym razie kontener nie zostanie uruchomiony.  Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Uruchamianie wielu kontenerów na tym samym hoście

Jeśli zamierzasz uruchomić wiele kontenerów z uwidocznionymi portami, upewnij się, że każdy kontener jest uruchomiony z innym portem. Na przykład Uruchom pierwszy kontener na porcie 5000 i drugi kontener na porcie 5001.

Zastąp `<container-registry>` `<container-name>` wartości i wartościami kontenerów, z których korzystasz. Nie muszą one być tym samym kontenerem. Możliwe jest posiadanie kontenera wykrywania anomalii i kontenera LUIS uruchomionego na HOŚCIE. można też korzystać z wielu kontenerów wykrywania anomalii.

Uruchom pierwszy kontener na porcie 5000.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Uruchom drugi kontener na porcie 5001.


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Każdy kolejny kontener powinien znajdować się na innym porcie.

## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań.

Użyj hosta http://localhost:5000 dla interfejsów API kontenera.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli uruchamiasz kontener z [instalacją wyjściową](anomaly-detector-container-configuration.md#mount-settings) i włączono rejestrowanie, kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery wykrywania anomalii wysyłają informacje o rozliczeniach do platformy Azure przy użyciu zasobu _wykrywania anomalii_ na Twoim koncie platformy Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy służące do pobierania, instalowania i uruchamiania kontenerów wykrywania anomalii. Podsumowanie:

* Detektor anomalii zawiera jeden kontener systemu Linux dla platformy Docker, Hermetyzowanie wykrywania anomalii przy użyciu usługi Batch i przesyłania strumieniowego z oczekiwanym zakresem oraz dostrajania dokładności.
* Obrazy kontenerów są pobierane z prywatnego Azure Container Registry dedykowanego dla kontenerów.
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach wykrywania anomalii przez określenie identyfikatora URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Klienci muszą włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą usługi pomiarowej przez cały czas. Kontenery Cognitive Services nie wysyłają danych klienta (np. dane szeregów czasowych, które są analizowane) do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Przegląd [konfigurowania kontenerów](anomaly-detector-container-configuration.md) dla ustawień konfiguracji
* [Wdróż kontener wykrywania anomalii w Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
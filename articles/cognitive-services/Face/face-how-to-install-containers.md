---
title: Instalowanie i uruchamianie kontenerów platformy Docker dla interfejs API rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: Użyj kontenera Docker, aby uzyskać interfejs API rozpoznawania twarzy do wykrywania i identyfikowania ludzkich twarzy na obrazach.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
keywords: lokalna, Docker, kontener, identyfikacja
ms.openlocfilehash: 64169069bc0a1ccd126d1b79ee89a5666e1caeb1
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861044"
---
# <a name="install-and-run-face-containers-preview"></a>Instalowanie i uruchamianie kontenerów z systemem (wersja zapoznawcza)

> [!IMPORTANT]
> Osiągnięto limit liczby użytkowników kontenera funkcji rozpoznawania twarzy. Obecnie nie akceptujemy nowych aplikacji dla kontenera funkcji rozpoznawania twarzy.

Usługa Azure Cognitive Services interfejs API rozpoznawania twarzy udostępnia kontener platformy Docker systemu Linux, który wykrywa i analizuje ludzkie twarze na obrazach. Identyfikuje także atrybuty, które obejmują dzielnice, takie jak nos i oczy, płeć, wiek i inne funkcje twarzy przewidziane dla maszyn. Oprócz wykrywania, twarz może sprawdzić, czy dwie twarze w tym samym obrazie lub różnych obrazach są takie same, przy użyciu oceny ufności. Twarz może również porównać twarze z bazą danych, aby sprawdzić, czy podobna lub identyczna twarz już istnieje. Może również organizować podobne twarze w grupy przy użyciu współużytkowanych cech wizualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów usługi Front Service należy spełnić następujące wymagania wstępne.

|Wymagane|Przeznaczenie|
|--|--|
|Aparat platformy Docker| Aparat platformy Docker musi być zainstalowany na [komputerze-hoście](#the-host-computer). Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).<br><br> Program Docker musi być skonfigurowany tak, aby umożliwić kontenerom łączenie się z danymi rozliczeń i wysyłanie ich do platformy Azure. <br><br> W systemie Windows program Docker musi być również skonfigurowany do obsługi kontenerów systemu Linux.<br><br>|
|Znajomość platformy Docker | Potrzebna jest podstawowa znajomość pojęć platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów. Trzeba również znać podstawowe `docker` polecenia.| 
|Zasób czołowy |Aby używać kontenera, musisz mieć:<br><br>**Zasób platformy** Azure i skojarzony klucz interfejsu API oraz identyfikator URI punktu końcowego. Obie wartości są dostępne na stronach **Przegląd** i **klucze** dla zasobu. Są one wymagane do uruchomienia kontenera.<br><br>**{API_KEY}**: jeden z dwóch dostępnych kluczy zasobów na stronie **kluczy**<br><br>**{ENDPOINT_URI}**: punkt końcowy określony na stronie **Przegląd**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Komputer-host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Wymagania i zalecenia dotyczące kontenera

W poniższej tabeli opisano minimalną i zalecaną liczbę rdzeni procesora CPU oraz ilość pamięci do przydzielenia dla każdego kontenera usługi.

| Kontener | Minimum | Zalecane | Liczba transakcji na sekundę<br>(Minimum, maksimum)|
|-----------|---------|-------------|--|
|Rozpoznawanie twarzy | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci |10, 20|

* Każdy rdzeń musi mieć co najmniej 2,6 GHz lub szybszy.
* Liczba transakcji na sekundę (TPS).

Rdzeń i pamięć odpowiadają `--cpus` `--memory` ustawieniom i, które są używane jako część `docker run` polecenia.

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą docker pull

Dostępne są obrazy kontenerów dla usługi. 

| Kontener | Repozytorium |
|-----------|------------|
| Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Wypychanie platformy Docker dla kontenera rozpoznawania

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Używanie kontenera

Gdy kontener znajduje się na [komputerze hosta](#the-host-computer), użyj następującego procesu, aby współpracować z kontenerem.

1. [Uruchom kontener](#run-the-container-with-docker-run) z wymaganymi ustawieniami rozliczania. Więcej [przykładów](./face-resource-container-config.md#example-docker-run-commands) `docker run` polecenia jest dostępnych. 
1. [Zbadaj punkt końcowy przewidywania kontenera](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Uruchamianie kontenera przy użyciu uruchomienia platformy Docker

Użyj polecenia [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , aby uruchomić kontener. Zapoznaj się z tematem [zbieranie wymaganych parametrów](#gathering-required-parameters) , aby uzyskać szczegółowe informacje na temat sposobu pobierania `{ENDPOINT_URI}` `{API_KEY}` wartości i.

[Przykłady](face-resource-container-config.md#example-docker-run-commands) `docker run` polecenia są dostępne.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

To polecenie:

* Uruchamia kontener czołowy z obrazu kontenera.
* Przypisuje jedne rdzeń procesora CPU i 4 GB pamięci.
* Udostępnia port TCP 5000 i przypisuje pseudo TTY dla kontenera.
* Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście. 

Więcej [przykładów](./face-resource-container-config.md#example-docker-run-commands) `docker run` polecenia jest dostępnych. 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby można było uruchomić kontener lub nie można uruchomić kontenera, należy określić opcje, i. Aby uzyskać więcej informacji, zobacz [rozliczenia](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Wykonywanie zapytania dotyczącego punktu końcowego przewidywania kontenera

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań. 

Użyj hosta `http://localhost:5000` dla interfejsów API kontenera.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zatrzymywanie kontenera

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W przypadku uruchomienia kontenera z [instalacją wyjściową](./face-resource-container-config.md#mount-settings) i włączeniu rejestrowania kontener generuje pliki dziennika, które są przydatne do rozwiązywania problemów występujących podczas uruchamiania lub uruchamiania kontenera.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Rozliczenia

Kontenery usługi kroju umożliwiają wysyłanie informacji dotyczących rozliczeń na platformę Azure przy użyciu zasobu kroju na koncie platformy Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Konfigurowanie kontenerów](./face-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono koncepcje i przepływ pracy związane z pobieraniem, instalowaniem i uruchamianiem kontenerów usług. Podsumowanie:

* Obrazy kontenerów są pobierane z Azure Container Registry.
* Obrazy kontenerów są uruchamiane w platformie Docker.
* Możesz użyć interfejsu API REST lub zestawu SDK, aby wywoływać operacje w kontenerach usługi "Front Service", określając identyfikator URI hosta kontenera.
* Podczas tworzenia wystąpienia kontenera należy określić informacje o rozliczeniach.

> [!IMPORTANT]
> Kontenery Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z platformą Azure w celu pomiaru. Aby umożliwić klientom przekazywanie informacji dotyczących rozliczeń za pomocą usługi pomiarowej przez kontenery, klienci muszą je włączyć. Kontenery Cognitive Services nie wysyłają danych klienta, takich jak obraz lub tekst, który jest analizowany, do firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać ustawienia konfiguracji, zobacz [Konfigurowanie kontenerów](face-resource-container-config.md).
* Aby dowiedzieć się więcej o wykrywaniu i identyfikowaniu twarzy, zobacz temat [Omówienie funkcji twarz](Overview.md).
* Aby uzyskać informacje na temat metod obsługiwanych przez kontener, zobacz [interfejs API rozpoznawania twarzy](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Aby korzystać z więcej kontenerów Cognitive Services, zobacz [Cognitive Services Containers](../cognitive-services-container-support.md).

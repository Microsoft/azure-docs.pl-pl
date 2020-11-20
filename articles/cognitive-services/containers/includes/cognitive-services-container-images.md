---
title: Repozytoria kontenerów i obrazy
services: cognitive-services
author: aahill
manager: nitinme
description: Dwie tabele przedstawiające rejestry kontenerów, repozytoria i nazwy obrazów dla wszystkich ofert usług poznawczych.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 2c2ebe9b419100163ae55c1be85dd1464904e841
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978427"
---
### <a name="container-repositories-and-images"></a>Repozytoria kontenerów i obrazy

Poniższe tabele stanowią listę dostępnych obrazów kontenerów oferowanych przez usługę Azure Cognitive Services. Aby uzyskać pełną listę wszystkich dostępnych nazw obrazów kontenerów i ich dostępnych tagów, zobacz [Cognitive Services znacznika obrazu kontenera](../container-image-tags.md). 

#### <a name="generally-available"></a>Ogólnie dostępne 

Microsoft Container Registry (MCR) łączy wszystkie ogólnie dostępne kontenery dla Cognitive Services. Kontenery są również dostępne bezpośrednio w usłudze [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Aby uzyskać więcej informacji [, zobacz jak uruchamiać i instalować kontenery Luis](../../LUIS/luis-container-howto.md) .

**Analiza tekstu**

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|
| Analiza tonacji v3 (angielski) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Analiza tonacji v3 (hiszpański) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Analiza tonacji v3 (francuski) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Analiza tonacji v3 (włoski) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Analiza tonacji v3 (niemiecki) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Analiza tonacji v3 (chiński uproszczony) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Analiza tonacji v3 (chiński tradycyjny) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Analiza tonacji v3 (japoński) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Analiza tonacji v3 (portugalski) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Analiza tonacji v3 (holenderski) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Aby uzyskać więcej informacji [, zobacz Jak uruchomić i zainstalować kontenery analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

**Narzędzie do wykrywania anomalii** 

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|
| Wykrywacz anomalii | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Aby uzyskać więcej informacji [, zobacz Jak uruchomić i zainstalować kontenery wykrywania anomalii](../../anomaly-detector/anomaly-detector-container-howto.md) .

**Usługa mowy**

> [!NOTE]
> Aby korzystać z kontenerów mowy, należy wykonać [formularz żądania online](https://aka.ms/csgate).

| Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|
| [Zamiana mowy na tekst](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Custom Speech do tekstu](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Zamiana tekstu na mowę](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"Wersja zapoznawcza" 

Następujące kontenery w wersji zapoznawczej są dostępne publicznie. Microsoft Container Registry (MCR) łączy wszystkie publicznie dostępne kontenery niewarunkowe do Cognitive Services. Kontenery są również dostępne bezpośrednio w usłudze [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>"Gated" (wersja zapoznawcza)

Wcześniej w repozytorium były hostowane kontenery w wersji zapoznawczej `containerpreview.azurecr.io` . Od 22 września 2020 te kontenery (z wyjątkiem analiza tekstu dla kondycji) są hostowane w witrynie Microsoft Container Registry (MCR), a ich pobieranie nie wymaga użycia polecenia Docker login. Aby użyć kontenera, należy wykonać następujące:

1. Wypełnij [formularz żądania](https://aka.ms/csgate) przy użyciu identyfikatora subskrypcji platformy Azure i scenariusza użytkownika. 
2. Po zatwierdzeniu Pobierz kontener z MCR. 
3. Użyj klucza i punktu końcowego z odpowiedniego zasobu platformy Azure do uwierzytelnienia kontenera w czasie wykonywania. 

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Przeczytaj v 2.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Przeczytaj v 3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Przetwarzanie obrazów](../../computer-vision/spatial-analysis-container.md) | Analiza przestrzenna | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=ctts) | Niestandardowa Zamiana tekstu na mowę | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=lid) | Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=ntts) | Neuronowych Zamiana tekstu na mowę | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Analiza tekstu dla opieki zdrowotnej](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Analiza tekstu dla opieki zdrowotnej | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
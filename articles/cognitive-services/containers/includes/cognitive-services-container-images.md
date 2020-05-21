---
title: Repozytoria kontenerów i obrazy
services: cognitive-services
author: aahill
manager: nitinme
description: Dwie tabele przedstawiające rejestry kontenerów, repozytoria i nazwy obrazów dla wszystkich ofert usług poznawczych.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 6d47829d32ed0b7ded545c73748cda1019d94bde
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721409"
---
### <a name="container-repositories-and-images"></a>Repozytoria kontenerów i obrazy

Poniższe tabele stanowią listę dostępnych obrazów kontenerów oferowanych przez usługę Azure Cognitive Services. Aby uzyskać pełną listę wszystkich dostępnych nazw obrazów kontenerów i ich dostępnych tagów, zobacz [Cognitive Services znacznika obrazu kontenera](../container-image-tags.md). 

#### <a name="generally-available"></a>Ogólnie dostępne 

Microsoft Container Registry (MCR) łączy wszystkie ogólnie dostępne kontenery dla Cognitive Services. Kontenery są również dostępne bezpośrednio w usłudze [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| Kontener LUIS | Container Registry/repozytorium/nazwa obrazu |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Aby uzyskać więcej informacji [, zobacz jak uruchamiać i instalować kontenery Luis](../../LUIS/luis-container-howto.md) .

#### <a name="text-analytics"></a>[Analiza tekstu](#tab/text-analytics)

| Kontener analiza tekstu | Container Registry/repozytorium/nazwa obrazu |
|--|--|
| Analiza tonacji v3 (angielski) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Analiza tonacji v3 (hiszpański) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Analiza tonacji v3 (francuski) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Analiza tonacji v3 (włoski) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Analiza tonacji v3 (niemiecki) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Analiza tonacji v3 (chiński uproszczony) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Analiza tonacji v3 (chiński tradycyjny) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Analiza tonacji v3 (japoński) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Analiza tonacji v3 (portugalski) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Analiza tonacji v3 (holenderski) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Aby uzyskać więcej informacji [, zobacz Jak uruchomić i zainstalować kontenery analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Publiczny "w wersji zapoznawczej" (rejestr kontenerów: `mcr.microsoft.com` )

Następujące kontenery w wersji zapoznawczej są dostępne publicznie. Microsoft Container Registry (MCR) łączy wszystkie publicznie dostępne kontenery niewarunkowe do Cognitive Services. Kontenery są również dostępne bezpośrednio w usłudze [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Analiza tekstu](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Wykrywacz anomalii](../../anomaly-detector/anomaly-detector-container-howto.md) | Narzędzie do wykrywania anomalii | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Public "Gated" (wersja zapoznawcza) (rejestr kontenerów: `containerpreview.azurecr.io` )

Poniższe kontenery w wersji zapoznawczej są hostowane w rejestrze w wersji zapoznawczej kontenera i wymagają dostępu do aplikacji. Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące kontenerów.

| Usługa | Kontener | Container Registry/repozytorium/nazwa obrazu |
|--|--|--|
| [Przetwarzanie obrazów](../../Computer-vision/computer-vision-how-to-install-containers.md) | Odczyt | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Rozpoznawanie twarzy](../../face/face-how-to-install-containers.md) | Rozpoznawanie twarzy | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Aparat rozpoznawania formularzy](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Rozpoznawanie formularzy | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=stt) | Zamiana mowy na tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech do tekstu | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=tts) | Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Interfejs API usługi rozpoznawania mowy](../../speech-service/speech-container-howto.md?tab=ctts) | Niestandardowa Zamiana tekstu na mowę | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |

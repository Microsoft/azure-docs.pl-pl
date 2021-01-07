---
title: Niesuwerenne chmury — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak korzystać z suwerennych chmur
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: 539ea6f80604eabdbbe56a3280cf5ddb367c0439
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97966787"
---
# <a name="speech-services-in-sovereign-clouds"></a>Usługi mowy w ramach suwerennych chmur

## <a name="azure-government-united-states"></a>Azure Government (Stany Zjednoczone)

Dostępne tylko dla instytucji rządowych Stanów Zjednoczonych i ich partnerów. Zobacz więcej informacji na temat Azure Government [tutaj](../../azure-government/documentation-government-welcome.md) i [tutaj.](../../azure-government/compare-azure-government-global-azure.md)

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Regionach**
  - US Gov Arizona
  - US Gov Wirginia
- **Dostępne warstwy cenowe:**
  - Free (F0) i Standard (S0). Więcej szczegółów można znaleźć [tutaj](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Obsługiwane funkcje:**
  - Zamiana mowy na tekst
    - Niestandardowa Zamiana mowy (model akustyczny (AM) i model języka (LM)
      - [Speech Studio](https://speech.azure.us/)
  - Zamiana tekstu na mowę
  - Translator mowy
- **Nieobsługiwane funkcje:**
  - Neuronowych
  - Niestandardowy głos
- **Obsługiwane języki:**
  - Zobacz listę obsługiwanych języków [tutaj](language-support.md)

### <a name="endpoint-information"></a>Informacje o punkcie końcowym

Ta sekcja zawiera informacje o punkcie końcowym usługi mowy dotyczące użycia z [zestawem SDK](speech-sdk.md), [interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md)oraz [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Interfejs API REST usługi Speech Services

Punkty końcowe interfejsu API REST usługi Speech w Azure Government mają następujący format:

|  Typ/operacja interfejsu API REST | Format punktu końcowego |
|--|--|
| Token dostępu | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Zamiana mowy na tekst interfejsu API REST v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Zamień na `<REGION_IDENTIFIER>` Identyfikator pasujący do regionu subskrypcji z tej tabeli:

|                     | Identyfikator regionu |
|--|--|
| **US Gov Arizona**  | `usgovarizona` |
| **US Gov Wirginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W przypadku zestawu Speech SDK w ramach suwerennych chmur należy użyć "z" hosta " `SpeechConfig` klasy lub `--host` opcji [interfejsu wiersza polecenia mowy](spx-overview.md). (Można również użyć wystąpienia "z punktu końcowego" i `--endpoint` Opcja interfejsu wiersza polecenia mowy).

`SpeechConfig` należy utworzyć wystąpienie klasy w następujący sposób:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Interfejs wiersza polecenia rozpoznawania mowy powinien być używany w ten sposób (należy pamiętać o `--host` opcji):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Zamień `subscriptionKey` na klucz zasobu mowy. Zamień na `usGovHost` wyrażenie pasujące do wymaganej oferty usługi i regionu subskrypcji z tej tabeli:

|  Oferta regionu/usługi | Wyrażenie hosta |
|--|--|
| **US Gov Arizona** | |
| Zamiana mowy na tekst | `wss://usgovarizona.stt.speech.azure.us` |
| Zamiana tekstu na mowę | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov Wirginia** | |
| Zamiana mowy na tekst | `wss://usgovvirginia.stt.speech.azure.us` |
| Zamiana tekstu na mowę | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure China

Dostępne dla organizacji z obecnością biznesową w Chinach. Więcej informacji na temat platformy Azure można znaleźć [tutaj.](/azure/china/overview-operations) 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Regionach**
  - Chiny Wschodnie 2
- **Dostępne warstwy cenowe:**
  - Free (F0) i Standard (S0). Więcej szczegółów można znaleźć [tutaj](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Obsługiwane funkcje:**
  - Zamiana mowy na tekst
    - Niestandardowa Zamiana mowy (model akustyczny (AM) i model języka (LM)
      - [Speech Studio](https://speech.azure.cn/)
  - Zamiana tekstu na mowę
  - Translator mowy
- **Nieobsługiwane funkcje:**
  - Neuronowych
  - Niestandardowy głos
- **Obsługiwane języki:**
  - Arabski (AR-*)
  - Chiński (ZH-*)
  - Angielski (EN-*)
  - Francuski (fr-*)
  - Niemiecki (de-*)
  - Hindi (Hi-IN)
  - Koreański (ko-KR)
  - Rosyjski (ru-RU)
  - Hiszpański (ES-*)

### <a name="endpoint-information"></a>Informacje o punkcie końcowym

Ta sekcja zawiera informacje o punkcie końcowym usługi mowy dotyczące użycia z [zestawem SDK](speech-sdk.md), [interfejs API REST zamiany mowy na tekst](rest-speech-to-text.md)oraz [interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Interfejs API REST usługi Speech Services

Punkty końcowe interfejsu API REST usługi Speech w Chinach platformy Azure mają następujący format:

|  Typ/operacja interfejsu API REST | Format punktu końcowego |
|--|--|
| Token dostępu | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Zamiana mowy na tekst interfejsu API REST v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [Interfejs API REST zamiany mowy na tekst dla krótkiego dźwięku](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Interfejs API REST zamiany tekstu na mowę](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Zamień na `<REGION_IDENTIFIER>` Identyfikator pasujący do regionu subskrypcji z tej tabeli:

|                     | Identyfikator regionu |
|--|--|
| **Chiny Wschodnie 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Zestaw SDK rozpoznawania mowy

W przypadku zestawu Speech SDK w ramach suwerennych chmur należy użyć "z" hosta " `SpeechConfig` klasy lub `--host` opcji [interfejsu wiersza polecenia mowy](spx-overview.md). (Można również użyć wystąpienia "z punktu końcowego" i `--endpoint` Opcja interfejsu wiersza polecenia mowy).

`SpeechConfig` należy utworzyć wystąpienie klasy w następujący sposób:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Interfejs wiersza polecenia rozpoznawania mowy powinien być używany w ten sposób (należy pamiętać o `--host` opcji):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Zamień `subscriptionKey` na klucz zasobu mowy. Zamień na `azCnHost` wyrażenie pasujące do wymaganej oferty usługi i regionu subskrypcji z tej tabeli:

|  Oferta regionu/usługi | Wyrażenie hosta |
|--|--|
| **Chiny Wschodnie 2** | |
| Zamiana mowy na tekst | `wss://chinaeast2.stt.speech.azure.cn` |
| Zamiana tekstu na mowę | `https://chinaeast2.tts.speech.azure.cn` |
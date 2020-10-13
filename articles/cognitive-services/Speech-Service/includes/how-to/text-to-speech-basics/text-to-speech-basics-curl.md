---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940751"
---
W tym przewodniku szybki start dowiesz się, jak konwertować tekst na mowę przy użyciu usługi mowy i zapełniania.

Aby zapoznać się z pojęciami dotyczącymi zamiany tekstu na mowę, zobacz artykuł z [omówieniem](../../../text-to-speech.md) .

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Konwertuj tekst na mowę

W wierszu polecenia Uruchom następujące polecenie. Należy umieścić następujące wartości w poleceniu.
- Klucz subskrypcji usługi rozpoznawania mowy.
- Region usługi mowy.

Możesz również zmienić następujące wartości.
- `X-Microsoft-OutputFormat`Wartość nagłówka, która kontroluje format danych wyjściowych audio. Listę obsługiwanych formatów danych wyjściowych audio można znaleźć w [dokumentacji interfejsu API REST zamiany tekstu na mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs).
- Wyjściowy dźwięk. Aby uzyskać listę głosów dostępnych dla punktu końcowego mowy, zobacz następną sekcję.
- Plik wyjściowy. W tym przykładzie kierujemy odpowiedź z serwera do pliku o nazwie `output.wav` .

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Wyświetl listę dostępnych głosów dla punktu końcowego mowy

Aby wyświetlić listę dostępnych głosów dla punktu końcowego mowy, uruchom następujące polecenie.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Powinna zostać wyświetlona odpowiedź taka jak następująca.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::

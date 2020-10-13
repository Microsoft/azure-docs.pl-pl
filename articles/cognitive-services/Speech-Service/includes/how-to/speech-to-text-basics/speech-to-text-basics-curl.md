---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 1ec0281145609f9ae06ad07e1ad2cfd2b7f9aba8
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940812"
---
W tym przewodniku szybki start dowiesz się, jak skonwertować mowę na tekst przy użyciu usługi mowy i jego zwinięcia.

Aby zapoznać się z pojęciami dotyczącymi zamiany mowy na tekst, zobacz artykuł z [omówieniem](../../../speech-to-text.md) .

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Konwertuj tekst na mowę

W wierszu polecenia Uruchom następujące polecenie. Należy umieścić następujące wartości w poleceniu.
- Klucz subskrypcji usługi rozpoznawania mowy.
- Region usługi mowy.
- Ścieżka wejściowego pliku audio. Pliki audio można generować przy użyciu funkcji [zamiany tekstu na mowę](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Powinna zostać wyświetlona odpowiedź taka jak następująca.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Aby uzyskać więcej informacji, zobacz [Informacje o interfejsie API REST zamiany mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).

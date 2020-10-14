---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 36d12b29054f736b65af5ac411adbc26d870b982
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014153"
---
W tym przewodniku szybki start dowiesz się, jak skonwertować mowę na tekst przy użyciu usługi mowy i jego zwinięcia.

Aby zapoznać się z pojęciami dotyczącymi zamiany mowy na tekst, zobacz artykuł z [omówieniem](../../../speech-to-text.md) .

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz konto platformy Azure i subskrypcję usługi mowy. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Konwersja mowy na tekst

W wierszu polecenia Uruchom następujące polecenie. Należy umieścić następujące wartości w poleceniu.
- Klucz subskrypcji usługi rozpoznawania mowy.
- Region usługi mowy.
- Ścieżka wejściowego pliku audio. Pliki audio można generować przy użyciu funkcji [zamiany tekstu na mowę](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Powinna zostać wyświetlona odpowiedź taka jak następująca.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Aby uzyskać więcej informacji, zobacz [Informacje o interfejsie API REST zamiany mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).

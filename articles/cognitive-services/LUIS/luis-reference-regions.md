---
title: Publikowanie regionów & punkty końcowe — LUIS
description: Region określony w Azure Portal jest taki sam, gdzie zostanie opublikowana aplikacja LUIS, a dla tego samego regionu zostanie wygenerowany adres URL punktu końcowego.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 04/07/2021
ms.custom: references_regions
ms.openlocfilehash: fd02bf5c3291569b71416392b651967e0da701d9
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226671"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Tworzenie i publikowanie regionów oraz skojarzonych kluczy

Regiony tworzenia LUIS są obsługiwane przez portal LUIS. Aby opublikować aplikację usługi LUIS w więcej niż jednym regionie, musisz mieć co najmniej jeden klucz na region.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUISe regiony tworzenia

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS ma jeden portal, którego można użyć niezależnie od regionu, [www.Luis.AI](https://www.luis.ai). Nadal musisz tworzyć i publikować dane w tym samym regionie.

Regiony tworzenia mają [sparowane regiony awaryjne](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikowanie regionów i zasobów platformy Azure

Aplikacja zostanie opublikowana we wszystkich regionach skojarzonych z zasobami usługi LUIS dodanymi w portalu usługi LUIS. Na przykład w przypadku aplikacji utworzonej w witrynie [www.Luis.AI][www.luis.ai], jeśli utworzysz zasób usługi Luis lub poznawczej w formie **zachodniej** i [dodasz go do aplikacji jako zasób](luis-how-to-azure-subscription.md), aplikacja zostanie opublikowana w tym regionie.

## <a name="public-apps"></a>Aplikacje publiczne
Publiczna aplikacja jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobów LUIS opartym na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z ich kluczem zasobów.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiony publikowania są powiązane z regionami tworzenia

Aplikację region tworzenia można opublikować tylko w odpowiednim regionie publikacji. Jeśli Twoja aplikacja znajduje się w niewłaściwym regionie tworzenia, wyeksportuj aplikację i zaimportuj ją do prawidłowego regionu tworzenia dla regionu publikacji.

> [!NOTE]
> Aplikacje LUIS utworzone w systemie https://www.luis.ai można teraz publikować we wszystkich punktach końcowych, w tym regionach [Europy](#publishing-to-europe) i [Australii](#publishing-to-australia) .

## <a name="publishing-to-europe"></a>Publikowanie w Europie

 Region globalny | Region tworzenia interfejsu API | Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| Europa | `westeurope`| Francja Środkowa<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa Północna<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| West Europe<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Południowe Zjednoczone Królestwo<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Szwajcaria Północna<br>`switzerlandnorth`    |  `https://switzerlandnorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publikowanie w Australii

 Region globalny | Region tworzenia interfejsu API | Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| Australia | `australiaeast` | Australia Wschodnia<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Inne regiony publikowania

 Region globalny | Region tworzenia interfejsu API | Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| Afryka | `westus`<br>[www.luis.ai][www.luis.ai]| Północna Republika Południowej Afryki<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Indie Środkowe<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Azja Wschodnia<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japan East<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japonia Zachodnia<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Korea Środkowa<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Southeast Asia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Północny Zjednoczone Emiraty Arabskie<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada Środkowa<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Central US<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Wschodnie stany USA 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Północno-środkowe stany USA<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | South Central US<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Zachodnio-środkowe stany USA<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Ameryka Południowa | `westus`<br>[www.luis.ai][www.luis.ai] | Brazylia Południowa<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Punkty końcowe

Dowiedz się więcej o [punktach końcowych tworzenia i przewidywania](developer-reference-resource.md).

## <a name="failover-regions"></a>Regiony trybu failover

Każdy region ma region pomocniczy do przełączenia w tryb failover. Europa działa w trybie failover w Europie i Australia przechodzi w tryb failover w Australii.

Regiony tworzenia mają [sparowane regiony trybu failover](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołania do wstępnie utworzonych jednostek](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
---
title: Publikowanie regionów & punkty końcowe — LUIS
description: Region określony w Azure Portal jest taki sam, gdzie zostanie opublikowana aplikacja LUIS, a dla tego samego regionu zostanie wygenerowany adres URL punktu końcowego.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80292084"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Tworzenie i publikowanie regionów oraz skojarzonych kluczy

Trzy regiony tworzenia są obsługiwane przez odpowiednie portale LUIS. Aby opublikować aplikację LUIS w więcej niż jednym regionie, musisz mieć co najmniej jeden klucz na region.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUISe regiony tworzenia
Istnieją trzy portale tworzenia LUIS na podstawie regionu. Tworzyć i publikować należy w tym samym regionie.

|LUIS|Region tworzenia|Nazwa regionu platformy Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|U.S.<br>nie Europa<br>nie Australia| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Australia| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

Regiony tworzenia mają [sparowane regiony trybu failover](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publikowanie regionów i zasobów platformy Azure
Aplikacja zostanie opublikowana we wszystkich regionach skojarzonych z zasobami LUIS dodanymi w portalu LUIS. Na przykład w przypadku aplikacji utworzonej w witrynie [www.Luis.AI][www.luis.ai], jeśli utworzysz zasób usługi Luis lub poznawczej w formie **zachodniej** i [dodasz go do aplikacji jako zasób](luis-how-to-azure-subscription.md), aplikacja zostanie opublikowana w tym regionie.

## <a name="public-apps"></a>Aplikacje publiczne
Publiczna aplikacja jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobów LUIS opartym na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z ich kluczem zasobów.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Regiony publikowania są powiązane z regionami tworzenia

Aplikację region tworzenia można opublikować tylko w odpowiednim regionie publikacji. Jeśli Twoja aplikacja znajduje się w niewłaściwym regionie tworzenia, wyeksportuj aplikację i zaimportuj ją do poprawnego regionu tworzenia w regionie publikacji.

Aplikacje LUIS utworzone w https://www.luis.ai systemie można publikować we wszystkich punktach końcowych z wyjątkiem regionów [Europy](#publishing-to-europe) i [Australii](#publishing-to-australia) .

## <a name="publishing-to-europe"></a>Publikowanie w Europie

Do opublikowania w regionach europejskich tworzone są https://eu.luis.ai tylko aplikacje Luis. Jeśli spróbujesz opublikować gdziekolwiek w innym miejscu przy użyciu klucza w regionie Europa, LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy https://eu.luis.aiużyć. Aplikacje LUIS utworzone w [https://eu.luis.ai][eu.luis.ai] programie nie są automatycznie migrowane do innych regionów. Aby przeprowadzić migrację, wyeksportuj i zaimportuj aplikację LUIS.

## <a name="europe-publishing-regions"></a>Regiony publikowania w Europie

 Region globalny | Tworzenie regionu interfejsu API & tworzenia witryny sieci Web| Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Francja Środkowa<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Północna<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Zachodnia<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Południowe Zjednoczone Królestwo<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publikowanie w Australii

W celu opublikowania w regionach australijskich tworzysz aplikacje https://au.luis.ai Luis. Jeśli spróbujesz opublikować gdziekolwiek w innym miejscu przy użyciu klucza w regionie australijski, LUIS wyświetla komunikat ostrzegawczy. Zamiast tego należy https://au.luis.aiużyć. Aplikacje LUIS utworzone w [https://au.luis.ai][au.luis.ai] programie nie są automatycznie migrowane do innych regionów. Aby przeprowadzić migrację, wyeksportuj i zaimportuj aplikację LUIS.

## <a name="australia-publishing-regions"></a>Regiony publikacji Australii

 Region globalny | Tworzenie regionu interfejsu API & tworzenia witryny sieci Web| Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australia Wschodnia<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publikowanie w innych regionach

W celu opublikowania w innych regionach tworzone są aplikacje [https://www.luis.ai](https://www.luis.ai) Luis.

## <a name="other-publishing-regions"></a>Inne regiony publikowania

 Region globalny | Tworzenie regionu interfejsu API & tworzenia witryny sieci Web| Publikowanie & regionie przeszukiwania<br>`API region name`   |  Format adresu URL punktu końcowego   |
|-----|------|------|------|
| Afryka | `westus`<br>[www.luis.ai][www.luis.ai]| Północna Republika Południowej Afryki<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Indie Środkowe<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Azja Wschodnia<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japonia Wschodnia<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Japonia Zachodnia<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Korea Środkowa<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azja | `westus`<br>[www.luis.ai][www.luis.ai]| Azja Południowo-Wschodnia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada Środkowa<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Środkowe stany USA<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Wschodnie stany USA<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Wschodnie stany USA 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Północno-środkowe stany USA<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Południowo-środkowe stany USA<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Zachodnio-środkowe stany USA<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Ameryka Północna | `westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Ameryka Północna |`westus`<br>[www.luis.ai][www.luis.ai] | Zachodnie stany USA 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Ameryka Południowa | `westus`<br>[www.luis.ai][www.luis.ai] | Brazylia Południowa<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Punkty końcowe

Dowiedz się więcej o [punktach końcowych tworzenia i przewidywania](developer-reference-resource.md).

## <a name="failover-regions"></a>Regiony trybu failover

Każdy region ma region pomocniczy do przełączenia w tryb failover. Europa działa w trybie failover w Europie i Australia przechodzi w tryb failover w Australii.

Regiony tworzenia mają [sparowane regiony trybu failover](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołania do wstępnie utworzonych jednostek](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai

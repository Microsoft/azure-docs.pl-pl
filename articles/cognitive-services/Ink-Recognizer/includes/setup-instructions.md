---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71996863"
---
>[!NOTE]
> Punkty końcowe dla zasobów utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla aparatu rozpoznawania pisma odręcznego przy użyciu [Azure Portal](../../cognitive-services-apis-create-account.md). 

* [Klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) można także uzyskać bezpłatnie przez siedem dni. Po zarejestrowaniu się w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/)będzie dostępny punkt końcowy.

Po utworzeniu zasobu Pobierz punkt końcowy i klucz, otwierając zasób na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), a następnie klikając pozycję **Szybki Start**.

Utwórz dwie [zmienne środowiskowe](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`— Punkt końcowy zasobu. Będzie wyglądać następująco: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`— Klucz subskrypcji służący do uwierzytelniania żądań.   

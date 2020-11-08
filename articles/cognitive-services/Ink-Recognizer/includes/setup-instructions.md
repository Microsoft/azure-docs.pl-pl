---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369085"
---
>[!NOTE]
> Punkty końcowe dla zasobów utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](../../cognitive-services-custom-subdomains.md). 

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla aparatu rozpoznawania pisma odręcznego przy użyciu [Azure Portal](../../cognitive-services-apis-create-account.md).

Po utworzeniu zasobu Pobierz punkt końcowy i klucz, otwierając zasób na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), a następnie klikając pozycję **Szybki Start**.

Utwórz dwie [zmienne środowiskowe](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` — Klucz subskrypcji służący do uwierzytelniania żądań. 

* `INK_RECOGNITION_ENDPOINT` — Punkt końcowy zasobu. Będzie to wyglądać następująco: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`
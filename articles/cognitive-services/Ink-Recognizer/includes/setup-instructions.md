---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: ede1fb4bd2a9a6e6536959053e3ca4d8e4a82f87
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327367"
---
>[!NOTE]
> Punkty końcowe dla zasobów utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla aparatu rozpoznawania pisma odręcznego przy użyciu [Azure Portal](../../cognitive-services-apis-create-account.md).

Po utworzeniu zasobu Pobierz punkt końcowy i klucz, otwierając zasób na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), a następnie klikając pozycję **Szybki Start**.

Utwórz dwie [zmienne środowiskowe](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` — Klucz subskrypcji służący do uwierzytelniania żądań. 

* `INK_RECOGNITION_ENDPOINT` — Punkt końcowy zasobu. Będzie to wyglądać następująco: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   

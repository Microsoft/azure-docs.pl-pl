---
title: Wdrażanie kontenera LUIS w usłudze Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Wdróż kontener LUIS w usłudze Azure Container instance i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879218"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Wdrażanie kontenera Language Understanding (LUIS) w usłudze Azure Container Instances

Dowiedz się, jak wdrożyć kontener Cognitive Services [Luis](luis-container-howto.md) do usługi Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Ta procedura pokazuje tworzenie zasobu wykrywania anomalii. Następnie omówiono ściąganie skojarzonego obrazu kontenera. Na koniec wyróżnimy możliwość wykonywania aranżacji tych dwóch z przeglądarki. Korzystanie z kontenerów umożliwia przechodzenie przez deweloperów do zarządzania infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Kontener LUIS wymaga `.gz` pliku modelu, który jest pobierany w czasie wykonywania. Kontener musi być w stanie uzyskać dostęp do tego pliku modelu za pośrednictwem instalacji woluminu z wystąpienia kontenera. Aby uzyskać informacje na temat tworzenia udziału plików platformy Azure, zobacz [Tworzenie udziału plików](../../storage/files/storage-how-to-create-file-share.md). Zanotuj nazwę konta usługi Azure Storage, klucz i nazwę udziału plików, ponieważ będą potrzebne później.

### <a name="export-and-upload-packaged-luis-app"></a>Eksportuj i przekazuj spakowane aplikacje LUIS

Aby można było przekazać model LUIS (spakowaną aplikację) do udziału plików platformy Azure, należy <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">najpierw <span class="docon docon-navigate-external x-hidden-focus"></span> wyeksportować go z portalu usługi Luis </a>. W Azure Portal przejdź do strony **Przegląd** zasobu konto magazynu i wybierz pozycję **udziały plików**. Wybierz ostatnio utworzoną nazwę udziału plików, a następnie wybierz przycisk **Przekaż** .

> [!div class="mx-imgBorder"]
> ![Przekaż do udziału plików](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Przekaż plik modelu LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]

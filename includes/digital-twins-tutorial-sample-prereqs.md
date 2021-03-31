---
author: baanders
description: Dołączanie pliku dla samouczków Digital bliźniaczych reprezentacji systemu Azure — wymagania wstępne dla przykładowego projektu
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622357"
---
## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, należy najpierw wykonać poniższe wymagania wstępne. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem **Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

### <a name="get-required-resources"></a>Pobierz wymagane zasoby

Aby ukończyć ten samouczek, **Zainstaluj [program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), wersję 16,5 lub nowszą** na komputerze deweloperskim. Jeśli masz już zainstalowaną starszą wersję, możesz otworzyć aplikację *Instalator programu Visual Studio* na maszynie i postępować zgodnie z monitami, aby zaktualizować instalację.

>[!NOTE]
> Upewnij się, że instalacja programu Visual Studio 2019 obejmuje **[obciążenie Programowanie na platformie Azure](/dotnet/azure/configure-visual-studio)**. To obciążenie umożliwia aplikacji publikowanie usługi Azure Functions i wykonywanie innych zadań programistycznych platformy Azure.

Samouczek jest oparty na przykładowym projekcie pisanym w języku C#. Przykład znajduje się w tym miejscu: [przykłady kompleksowej usługi Azure Digital bliźniaczych reprezentacji](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Pobierz przykładowy projekt** na swoją maszynę, przechodząc do linku przykładowego i wybierając przycisk *Przeglądaj kod* pod tytułem. Spowoduje to przejście do repozytorium GitHub dla przykładów, które można pobrać jako *. Plik ZIP* , wybierając przycisk *Code (kod* ) i Pobierz plik *zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Widok repozytorium Digital-bliźniaczych reprezentacji-Samples w witrynie GitHub. Wybrano przycisk kod, tworząc małe okno dialogowe, w którym jest wyróżniony przycisk Pobierz plik ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Spowoduje to pobranie *. Folder ZIP* na maszynę jako **digital-twins-samples-master.zip**. Rozpakuj folder i Wyodrębnij pliki.

### <a name="prepare-an-azure-digital-twins-instance"></a>Przygotowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

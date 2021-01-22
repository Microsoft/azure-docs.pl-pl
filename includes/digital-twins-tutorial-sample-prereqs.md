---
author: baanders
description: Dołączanie pliku dla samouczków Digital bliźniaczych reprezentacji systemu Azure — wymagania wstępne dla przykładowego projektu
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660525"
---
## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem **Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** .

Przed rozpoczęciem Zainstaluj program **[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), wersję 16,5 lub nowszą** na komputerze deweloperskim. Jeśli masz już zainstalowaną starszą wersję, możesz otworzyć aplikację *Instalator programu Visual Studio* na maszynie i postępować zgodnie z monitami, aby zaktualizować instalację.

Samouczek jest oparty na przykładowym projekcie pisanym w języku C#. Przykład znajduje się w tym miejscu: [przykłady kompleksowej usługi Azure Digital bliźniaczych reprezentacji](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Pobierz przykładowy projekt** na swoją maszynę, przechodząc do linku przykładowego i wybierając przycisk *Przeglądaj kod* pod tytułem. Spowoduje to przejście do repozytorium GitHub dla przykładów, które można pobrać jako *. Plik ZIP* , wybierając przycisk *Code (kod* ) i Pobierz plik *zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Widok repozytorium Digital-bliźniaczych reprezentacji-Samples w witrynie GitHub. Wybrano przycisk kod, tworząc małe okno dialogowe, w którym jest wyróżniony przycisk Pobierz plik ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Spowoduje to pobranie *. Folder ZIP* na maszynę jako **digital-twins-samples-master.zip**. Rozpakuj folder i Wyodrębnij pliki.

### <a name="prepare-an-azure-digital-twins-instance"></a>Przygotowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]

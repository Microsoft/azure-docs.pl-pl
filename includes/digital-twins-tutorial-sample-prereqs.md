---
author: baanders
description: plik dołączania Azure Digital Twins samouczków — wymagania wstępne dotyczące przykładowego projektu
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 00d584690d37f1dcc47b785ef533abe888befec3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512567"
---
## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, należy najpierw spełnić następujące wymagania wstępne. 

Jeśli nie masz subskrypcji platformy **Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** przed rozpoczęciem utwórz bezpłatne konto.

### <a name="get-required-resources"></a>Uzyskiwanie wymaganych zasobów

Aby ukończyć ten samouczek, **[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)r. w wersji 16.5** lub nowszej na komputerze dewelopera. Jeśli masz już zainstalowaną starszą wersję, możesz otworzyć aplikację *Instalator programu Visual Studio* na maszynie i postępować zgodnie z monitami, aby zaktualizować instalację.

>[!NOTE]
> Upewnij się, że instalacja programu Visual Studio 2019 obejmuje obciążenie **[Tworzenie aplikacji na platformie Azure.](/dotnet/azure/configure-visual-studio)** To obciążenie umożliwia aplikacji publikowanie funkcji platformy Azure i wykonywanie innych zadań programistyki na platformie Azure.

Samouczek jest oparty na przykładowym projekcie napisanym w języku C#. Przykład znajduje się tutaj: [Azure Digital Twins przykłady typu end-to-end.](/samples/azure-samples/digital-twins-samples/digital-twins-samples) **Pobierz przykładowy projekt** na swojej maszynie, przechodząc do linku przykładowego i wybierając przycisk *Przeglądaj* kod pod tytułem. Spowoduje to pobranie przykładów do repozytorium GitHub, które można pobrać jako *plik . Zip* (Plik ZIP), wybierając *przycisk Code (Kod)* i Download *ZIP (Pobierz plik ZIP).*

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Zrzut ekranu przedstawiający repozytorium digital-twins-samples w witrynie GitHub. Po wybraniu przycisku Kod zostanie wyświetlone małe okno dialogowe, w którym wyróżniony jest przycisk Pobierz plik ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Spowoduje to pobranie pliku *. Zip* folder to your machine as **digital-twins-samples-master.zip**. Rozpakować folder i wyodrębnić pliki.

### <a name="prepare-an-azure-digital-twins-instance"></a>Przygotowywanie Azure Digital Twins danych

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

---
title: 'Szybki Start: Tworzenie aplikacji HoloLens przy użyciu aparatu Unity'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację aparatu HoloLens Unity przy użyciu kotwic obiektów.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 4f85a258042430d58690ef578db6d21a6c831d50
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044809"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Szybki Start: Tworzenie aplikacji HoloLens przy użyciu kotwic obiektów platformy Azure w środowisku Unity

W tym przewodniku szybki start utworzysz aplikację HoloLens Unity, która używa [kotwic obiektów platformy Azure](../overview.md). Kotwice obiektów platformy Azure to zarządzana usługa w chmurze, która konwertuje zasoby 3D na modele AI, które umożliwiają korzystanie z rzeczywistości mieszanej opartej na obiektach dla urządzenia HoloLens. Po zakończeniu będziesz mieć aplikację HoloLens utworzoną przy użyciu aparatu Unity, która może wykrywać obiekty w świecie fizycznym.

Omawiane tematy:

> [!div class="checklist"]
> * Przygotuj ustawienia kompilacji aparatu Unity.
> * Wyeksportuj projekt HoloLens programu Visual Studio.
> * Wdróż aplikację i uruchom ją na urządzeniu HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

W aparacie Unity Otwórz `quickstarts/apps/unity/basic` projekt.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

Włącz urządzenie, wybierz pozycję **wszystkie aplikacje**, a następnie Znajdź i uruchom aplikację. Po ekranie powitalnym aparatu Unity zobaczysz komunikat informujący o tym, że obiekt obserwatora został zainicjowany. Należy jednak dodać model do aplikacji.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

Aplikacja szuka obiektów w bieżącym polu widoku, a następnie śledzi je po wykryciu. Wystąpienie zostanie usunięte, gdy będzie 6 metrów od lokalizacji użytkownika. Tekst debugowania pokazuje szczegóły dotyczące wystąpienia, takie jak identyfikator, zaktualizowana sygnatura czasowa i współczynnik pokrycia powierzchni.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Unity HoloLens z MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Pojęcia: Omówienie zestawu SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Często zadawane pytania](../faq.md)

---
title: 'Samouczek: udostępnianie kotwic między sesjami i urządzeniami'
description: W tym samouczku dowiesz się, jak udostępnić identyfikatory kotwic Azure w środowisku z systemem Android/iOS w środowisku Unity przy użyciu usługi zaplecza.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: b29873e161b64c52abbfdf3f2611714f6b012361
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097290"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Samouczek: udostępnianie kotwic przestrzenny między sesjami i urządzeniami

Kotwice przestrzenne platformy Azure to wieloplatformowa usługa programistyczna, za pomocą której można tworzyć środowiska o rzeczywistości mieszanej przy użyciu obiektów, które utrzymują swoją lokalizację na różnych urządzeniach w miarę upływu czasu. 

W tym samouczku użyjesz [kotwic przestrzennych platformy Azure](../overview.md) do tworzenia kotwic podczas jednej sesji, a następnie znajdowania ich na tym samym urządzeniu lub innym. Te same kotwice mogą również znajdować się na wielu urządzeniach w tym samym miejscu i w tym samym czasie.

![Animacja pokazująca kotwice przestrzenne, które są tworzone za pomocą urządzenia przenośnego i używane z innym urządzeniem w ciągu kilku dni.](./media/persistence.gif)


Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Wdróż ASP.NET Core aplikację sieci Web na platformie Azure, której możesz użyć do udostępniania kotwic i przechowywania kotwic w pamięci przez określony czas.
> * Skonfiguruj scenę AzureSpatialAnchorsLocalSharedDemo w ramach przykładu aparatu Unity z naszego przewodnika Szybki Start, aby skorzystać z aplikacji sieci Web do udostępniania.
> * Wdróż i uruchom kotwice na jednym lub większej liczbie urządzeń.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> W tym samouczku będziesz używać aplikacji sieci Web w technologii Unity i ASP.NET Core, ale podejście tutaj dotyczy tylko przykładowego sposobu udostępniania identyfikatorów kotwice przestrzenne platformy Azure na innych urządzeniach. Możesz użyć innych języków i technologii zaplecza, aby osiągnąć ten sam cel.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Pobierz przykładowy projekt

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Wdrażanie usługi zakotwiczenia udostępniania

## <a name="visual-studio"></a>[Program Visual Studio](#tab/VS)

Otwórz program Visual Studio, a następnie otwórz projekt w folderze *Sharing\SharingServiceSample* .

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Przed wdrożeniem usługi w programie Visual Studio Code należy utworzyć grupę zasobów i plan App Service.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Przejdź do <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>, a następnie zaloguj się do swojej subskrypcji platformy Azure.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Obok pozycji **Grupa zasobów** wybierz pozycję **Nowa**.

Nadaj **nazwę grupie zasobów**, a następnie wybierz przycisk **OK**.

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Obok pozycji **Plan hostingu** wybierz pozycję **Nowy**.

W okienku **Konfigurowanie planu hostingu** Użyj następujących ustawień:

| Ustawienie | Sugerowana wartość | Opis |
|-|-|-|
|Plan usługi App Service| MySharingServicePlan | Nazwa planu App Service |
| Lokalizacja | Zachodnie stany USA | Centrum danych, w którym jest hostowana aplikacja sieci Web |
| Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , która określa funkcje hostingu |

Wybierz przycisk **OK**.

Otwórz Visual Studio Code, a następnie otwórz projekt w folderze *Sharing\SharingServiceSample* . 

Aby wdrożyć usługę udostępniania za pomocą Visual Studio Code, postępuj zgodnie z instrukcjami zawartymi w temacie <a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu Visual Studio Code</a>. Zacznij od sekcji "Otwieranie za pomocą Visual Studio Code". Nie twórz innego projektu ASP.NET, jak wyjaśniono w poprzednim kroku, ponieważ masz już projekt do wdrożenia i opublikowania: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Wdrażanie przykładowej aplikacji

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono ASP.NET Core aplikację sieci Web na platformie Azure i skonfigurowano i wdrożono aplikację Unity. Utworzono kotwice przestrzenne z aplikacją i udostępniono je innym urządzeniom za pomocą aplikacji sieci Web ASP.NET Core.

Możesz ulepszyć ASP.NET Core aplikację sieci Web, tak aby używała Azure Cosmos DB do utrwalania magazynu udostępnionych identyfikatorów kotwic przestrzennych. Dzięki obsłudze Azure Cosmos DB można w aplikacji internetowej ASP.NET Core utworzyć kotwicę dzisiaj. Następnie przy użyciu identyfikatora zakotwiczenia, który jest przechowywany w aplikacji sieci Web, możesz później wrócić do aplikacji, aby ponownie zlokalizować zakotwiczenie.

> [!div class="nextstepaction"]
> [Używanie Azure Cosmos DB do przechowywania kotwic](./tutorial-use-cosmos-db-to-store-anchors.md)
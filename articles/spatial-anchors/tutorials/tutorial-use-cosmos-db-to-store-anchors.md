---
title: 'Samouczek: udostępnianie kotwic z Azure Cosmos DB'
description: W tym samouczku dowiesz się, jak udostępnić identyfikatory kotwic usługi Azure przestrzenny na urządzeniach z systemem Android/iOS w środowisku Unity przy użyciu usługi zaplecza i Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77615151"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Samouczek: udostępnianie kotwic przestrzennych platformy Azure między sesjami i urządzeniami przy użyciu zaplecza Azure Cosmos DB

Ten samouczek to kontynuacja [udostępniania zakotwiczenia przestrzennego platformy Azure między sesjami i urządzeniami.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Przeprowadzimy Cię przez proces dodawania kilku możliwości, aby Azure Cosmos DB działać jako magazyn zaplecza, jednocześnie udostępniając kotwice przestrzenne platformy Azure między sesjami i urządzeniami.

![Obraz przedstawiający trwałość obiektu GIF](./media/persistence.gif)

Warto zauważyć, że mimo że będziesz używać aparatu Unity i Azure Cosmos DB w tym samouczku, wystarczy udostępnić przykład sposobu udostępniania identyfikatorów kotwic przestrzennych między urządzeniami. Do osiągniecia tego celu można użyć innych języków i technologii zaplecza. Ponadto aplikacja sieci Web ASP.NET Core używana w tym samouczku wymaga zestawu .NET Core 2,2 SDK. Działa on prawidłowo na Web Apps dla systemu Windows, ale nie jest on obecnie uruchamiany w Web Apps for Linux.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Dodaj bazę danych usługi Azure Cosmos do utworzonej wcześniej grupy zasobów.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Skopiuj plik `Connection String` , ponieważ będzie on potrzebny.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Wprowadzanie drobnych zmian w plikach SharingService

W **Eksplorator rozwiązań**Otwórz `SharingService\Startup.cs`program.

Znajdź `#define INMEMORY_DEMO` w górnej części pliku i komentarz, który znajduje się w wierszu. Zapisz plik.

W **Eksplorator rozwiązań**Otwórz `SharingService\appsettings.json`program.

Znajdź `StorageConnectionString` Właściwość i ustaw wartość tak, aby była taka sama jak `Connection String` wartość skopiowana w [kroku Tworzenie konta bazy danych](#create-a-database-account). Zapisz plik.

Możesz ponownie opublikować usługę udostępniania i uruchomić przykładową aplikację.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unity-20193"></a>Unity 2019,3

Ze względu na istotne zmiany, środowisko Unity 2019,3 nie jest obecnie obsługiwane. Użyj aparatu Unity 2019,1 lub 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto usługi Azure Cosmos DB do udostępnienia identyfikatorów kotwic między urządzeniami. Aby dowiedzieć się więcej na temat korzystania z kotwic przestrzennych platformy Azure w nowej aplikacji HoloLens Unity, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Uruchamianie nowej aplikacji HoloLens Unity](./tutorial-new-unity-hololens-app.md)

---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d06a6ecd8af16da3e6df21e984fbf6a727fbc27e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104417"
---
### <a name="upload-your-model"></a>Przekaż model

Jeśli nie masz jeszcze modelu kotwic obiektów, postępuj zgodnie z instrukcjami w temacie [Tworzenie modelu](../articles/object-anchors/quickstarts/get-started-model-conversion.md) , aby go utworzyć. Następnie wróć tutaj.

Za pomocą usługi HoloLens połączonej z portalem urządzeń z systemem Windows wykonaj następujące kroki, aby przekazać model używany przez aplikację:

1. W portalu urządzeń z systemem Windows przejdź do pozycji **System > plików explorer > LocalAppData**. Na tej stronie powinna zostać wyświetlona aplikacja na liście aplikacji.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="Eksplorator plików":::

2. Otwórz aplikację i kliknij `LocalState` folder.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Otwórz folder LocalState":::

3. Przekaż plik modelu do `LocalState` folderu.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="przekazywanie modelu w portalu":::

    Uruchom ponownie aplikację z programu HoloLens. Teraz można wykryć obiekty zgodne z modelem.
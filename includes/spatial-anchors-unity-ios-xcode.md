---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 81d2804d99896200ea6f68592ea168112e172c20
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762617"
---
Wybierz pozycję **kompilacja**. W otwartym okienku wybierz folder, do którego chcesz wyeksportować projekt Xcode.

   Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt Xcode.

   > [!NOTE]
   > Jeśli zostanie wyświetlone okno z komunikatem z pytaniem, czy chcesz zamienić lub dołączyć, zalecamy wybranie opcji **Dołącz**, ponieważ jest ona szybsza. Należy zaznaczyć opcję **Zamień** tylko wtedy, gdy zmieniasz elementy zawartości w scenie. Można na przykład dodać, usunąć lub zmienić relacje nadrzędny/podrzędny lub dodać, usunąć lub zmienić właściwości. Jeśli wprowadzasz tylko zmiany w kodzie źródłowym, **dołączenie** powinno być wystarczające.

## <a name="open-the-xcode-project"></a>Otwieranie projektu Xcode

Teraz możesz otworzyć `Unity-iPhone.xcodeproj` projekt w Xcode. 

Można uruchomić Xcode i otworzyć wyeksportowany `Unity-iPhone.xcodeproj` projekt lub uruchomić projekt w programie Xcode, uruchamiając następujące polecenie w lokalizacji, w której został wyeksportowany projekt:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Wybierz węzeł główny **Unity-iPhone** , aby wyświetlić ustawienia projektu, a następnie wybierz kartę **Ogólne** .

W obszarze **Informacje o wdrożeniu** upewnij się, że element docelowy wdrożenia ma ustawioną wartość **iOS 11,0**.

Wybierz kartę **możliwości podpisywania &** i upewnij się, że włączono **Automatyczne zarządzanie podpisywaniem** . Jeśli tak nie jest, włącz ją, a następnie Zresetuj ustawienia kompilacji, wybierając opcję **Włącz automatyczne** w wyświetlonym okienku.

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzeniu z systemem iOS.

   ![Zrzut ekranu przedstawiający przycisk My iPhone służący do wybierania urządzenia.](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

   ![Zrzut ekranu przedstawiający przycisk strzałki "wdróż i uruchom".](./media/spatial-anchors-unity/deploy-run.png)

---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89536271"
---
Wybierz pozycję **kompilacja**. W otwartym oknie dialogowym Wybierz folder, do którego chcesz wyeksportować projekt Xcode.

Po zakończeniu eksportowania zostanie wyświetlony folder zawierający wyeksportowany projekt Xcode.

> [!NOTE]
> Jeśli zostanie wyświetlone okno z pytaniem, czy chcesz zamienić lub dołączyć, zalecamy wybranie opcji **Dołącz** , ponieważ jest ona szybsza. Należy wybrać opcję **Zamień** tylko w przypadku zmiany elementów zawartości w scenie. (Na przykład w przypadku dodawania, usuwania lub zmieniania relacji nadrzędny/podrzędny lub dodawania, usuwania lub zmieniania właściwości). Jeśli wprowadzasz tylko zmiany w kodzie źródłowym, **dołączenie** powinno być wystarczające.

## <a name="open-the-xcode-project"></a>Otwieranie projektu Xcode

Teraz możesz otworzyć `Unity-iPhone.xcodeproj` w Xcode. Można uruchomić Xcode i otworzyć wyeksportowany `Unity-iPhone.xcodeproj` projekt lub uruchomić projekt w programie Xcode, uruchamiając następujące polecenie w lokalizacji, w której został wyeksportowany projekt:

```bash
open ./Unity-iPhone.xcodeproj
```

Wybierz węzeł główny **Unity-iPhone** , aby wyświetlić ustawienia projektu, a następnie wybierz kartę **Ogólne** .

W obszarze **podpisywanie**upewnij się, że opcja **automatycznie Zarządzaj podpisywaniem** jest włączona. Jeśli tak nie jest, włącz ją, a następnie wybierz opcję **Włącz automatyczne** w oknie dialogowym, które pojawia się, aby zresetować ustawienia kompilacji.

W obszarze **Deployment Info (Informacje o wdrożeniu)** upewnij się, że w polu **Deployment Target (Cel wdrożenia)** ustawiono wartość `11.0`.

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/spatial-anchors-unity/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

![Wdrażanie i uruchamianie](./media/spatial-anchors-unity/deploy-run.png)

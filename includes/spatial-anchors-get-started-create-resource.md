---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "83638984"
---
## <a name="create-a-spatial-anchors-resource"></a>Tworzenie zasobu usługi Spatial Anchors

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

W okienku nawigacji po lewej stronie Azure Portal wybierz pozycję **Utwórz zasób**.

Użyj pola wyszukiwania, aby wyszukać **kotwice przestrzenne**.

   ![Wyszukiwanie usługi Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Wybierz **kotwice przestrzenne**. W oknie dialogowym wybierz pozycję **Utwórz**.

W oknie dialogowym **konto kotwicowe** :

- Wprowadź unikatową nazwę zasobu, używając zwykłych znaków alfanumerycznych.
- Wybierz subskrypcję, do której chcesz dołączyć zasób.
- Utwórz grupę zasobów, wybierając pozycję **Utwórz nową**. Nadaj jej **nazwę, a** następnie wybierz **przycisk OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Wybierz lokalizację (region), w której ma zostać umieszczony zasób.
- Wybierz pozycję **Nowy**, aby rozpocząć tworzenie zasobu.

   ![Tworzenie zasobu](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Po utworzeniu zasobu w witrynie Azure Portal zostanie wyświetlona wartość ukończenie wdrożenia. Kliknij opcję **Przejdź do zasobu**.

![Zakończono wdrożenie](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Następnie można wyświetlić właściwości zasobów. Skopiuj wartość **identyfikatora konta** zasobu do edytora tekstu, ponieważ będzie ona potrzebna później.

   ![Właściwości zasobu](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Skopiuj również wartość **domeny konta** zasobu do edytora tekstu, ponieważ będzie ona potrzebna później.

   ![Domena konta](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

W obszarze **Ustawienia**wybierz pozycję **klucz**. Skopiuj wartość **klucza podstawowego** do edytora tekstu. Ta wartość to `Account Key` . Będzie on potrzebny później.

   ![Klucz konta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

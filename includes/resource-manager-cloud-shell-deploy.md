---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74451634"
---
## <a name="deploy-template-from-cloud-shell"></a>Wdrażanie szablonu za pomocą usługi Cloud Shell

Do wdrożenia swojego szablonu możesz użyć usługi [Cloud Shell](../articles/cloud-shell/overview.md). Aby wdrożyć szablon zewnętrzny, podaj identyfikator URI szablonu dokładnie tak, jak w przypadku dowolnego wdrożenia zewnętrznego. Aby wdrożyć szablon lokalny, należy najpierw załadować szablon do konta magazynu dla Cloud Shell. W tej sekcji opisano sposób ładowania szablonu do konta usługi Cloud Shell i wdrażania go jako pliku lokalnego. Jeśli Cloud Shell nie zostały użyte, zobacz [omówienie Azure Cloud Shell](../articles/cloud-shell/overview.md) , aby uzyskać informacje o konfigurowaniu IT.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz swoją grupę zasobów usługi Cloud Shell. Wzorzec nazwy to `cloud-shell-storage-<region>`.

   ![Wybieranie grupy zasobów](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Wybierz konto magazynu dla usługi Cloud Shell.

   ![Wybieranie konta magazynu](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Wybierz **obiekty blob**.

   ![Wybieranie pozycji Obiekty blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Wybierz pozycję **+ Kontener**.

   ![Dodawanie kontenera](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Nadaj kontenerowi nazwę i poziom dostępu. Przykładowy szablon w tym artykule nie zawiera poufnych informacji, więc Zezwalaj na anonimowy dostęp do odczytu. Wybierz przycisk **OK**.

   ![Podaj wartości kontenera](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Wybierz utworzony wcześniej kontener.

   ![Wybierz nowy kontener](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Wybierz pozycję **Przekaż**.

   ![Przekaż obiekt BLOB](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Znajdź i przekaż swój szablon.

   ![Przekazywanie pliku](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po przekazaniu wybierz szablon.

   ![Wybierz nowy szablon](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Skopiuj adres URL.

   ![Kopiowanie adresu URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otwórz wiersz polecenia.

   ![Otwieranie usługi Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)

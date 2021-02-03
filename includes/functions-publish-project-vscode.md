---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 2517f132578b5de6b062b38ce94581f118327a13
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493598"
---
## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

W tej sekcji utworzysz aplikację funkcji i powiązane zasoby w ramach subskrypcji platformy Azure, a następnie wdrożono swój kod.

> [!IMPORTANT]
> Publikowanie do istniejącej aplikacji funkcji spowoduje zastąpienie zawartości tej aplikacji na platformie Azure.


1. Wybierz ikonę platformy Azure na pasku działania, a następnie w obszarze **Azure: Functions** wybierz przycisk **Wdróż do aplikacji funkcji...** .

    ![Publikowanie projektu na platformie Azure](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Podaj następujące informacje po wyświetleniu monitów:

    - **Wybierz folder**: Wybierz folder z obszaru roboczego lub przejdź do folderu, który zawiera aplikację funkcji. Nie zobaczysz tego, jeśli masz już otwartą prawidłową aplikację funkcji.

    - **Wybierz subskrypcję**: wybierz subskrypcję do użycia. Nie zobaczysz tego, jeśli masz tylko jedną subskrypcję.

    - **Wybierz aplikację funkcji na platformie Azure**: Wybierz plik `- Create new Function App`. (Nie należy wybierać `Advanced` opcji, która nie jest objęta tym artykułem).
      
    - **Wprowadź globalnie unikatową nazwę aplikacji funkcji**: wpisz nazwę, która jest prawidłowa w ścieżce URL. Wpisana nazwa zostanie sprawdzona, aby upewnić się, że jest ona unikatowa w Azure Functions.
    
    - **Wybierz lokalizację dla nowych zasobów**: Aby uzyskać lepszą wydajność, wybierz [region](https://azure.microsoft.com/regions/) w sąsiedztwie. 
    
    Rozszerzenie pokazuje stan poszczególnych zasobów w miarę ich tworzenia na platformie Azure w obszarze powiadomień.

    :::image type="content" source="media/functions-publish-project-vscode/resource-notification.png" alt-text="Powiadomienie o tworzeniu zasobów platformy Azure":::
    
1.  Po zakończeniu następujące zasoby platformy Azure są tworzone w ramach subskrypcji przy użyciu nazw opartych na nazwie aplikacji funkcji:
    
    [!INCLUDE [functions-vs-code-created-resources](functions-vs-code-created-resources.md)]

    Po utworzeniu aplikacji funkcji i zastosowaniu pakietu wdrożeniowego zostanie wyświetlone powiadomienie. 

    [!INCLUDE [functions-vs-code-create-tip](functions-vs-code-create-tip.md)]

4. Wybierz pozycję **Wyświetl dane wyjściowe** w tym powiadomieniu, aby wyświetlić wyniki tworzenia i wdrażania, w tym utworzone zasoby platformy Azure. W przypadku odrzucenia powiadomienia wybierz ikonę dzwonka w prawym dolnym rogu, aby zobaczyć ją ponownie.

    ![Utwórz pełne powiadomienie](media/functions-publish-project-vscode/function-create-notifications.png)

---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 34848b638ff0c7f7b9d1a2f3e5894339f8310ccc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013378"
---
## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Potok automatycznie tworzy folder wyjściowy w kontenerze obiektów blob adftutorial. Następnie kopiuje plik emp.txt z folderu wejściowego do folderu wyjściowego. 

1. Na Azure Portal na stronie kontenera **adftutorial** wybierz pozycję **Odśwież** , aby wyświetlić folder danych wyjściowych. 
    
    ![Zrzut ekranu przedstawia stronę kontenera, na której można odświeżyć stronę.](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. Wybierz pozycję **dane wyjściowe** na liście folderów. 

3. Upewnij się, że plik **emp.txt** jest kopiowany do folderu wyjściowego. 

    ![Zrzut ekranu przedstawia zawartość folderu wyjściowego.](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby, które zostały utworzone w ramach tego przewodnika Szybki start, możesz wyczyścić na dwa sposoby. Możesz usunąć [grupę zasobów platformy Azure](../articles/azure-resource-manager/management/overview.md) zawierającą wszystkie zasoby w tej grupie. Jeśli chcesz zachować inne zasoby bez zmian, usuń tylko fabrykę danych utworzoną w tym samouczku.

Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów łącznie z fabrykami danych w nich zawartymi. Uruchom poniższe polecenie, aby usunąć całą grupę zasobów: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> Porzucenie grupy zasobów może zająć trochę czasu. Prosimy o cierpliwość

Jeśli chcesz usunąć tylko fabrykę danych, a nie całą grupę zasobów, uruchom następujące polecenie: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```
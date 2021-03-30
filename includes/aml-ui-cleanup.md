---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "75659880"
---
>[!IMPORTANT]
>Utworzone zasoby możesz wykorzystać na potrzeby wymagań wstępnych innych samouczków i artykułów dotyczących usługi Azure Machine Learning.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie planujesz korzystać z utworzonych elementów, Usuń całą grupę zasobów, aby nie naliczać żadnych opłat.

1. W Azure Portal wybierz pozycję **grupy zasobów** po lewej stronie okna.
 
   ![Usuwanie grupy zasobów w witrynie Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. Z listy wybierz utworzoną grupę zasobów.

1. Wybierz pozycję **Usuń grupę zasobów**.

Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów utworzonych w projektancie. 

### <a name="delete-individual-assets"></a>Usuwanie pojedynczych zasobów

W projektancie, w którym został utworzony eksperyment, Usuń pojedyncze zasoby, zaznaczając je, a następnie wybierając przycisk **Usuń** .

Obiekt docelowy obliczeń, który został utworzony w tym miejscu, *automatycznie przeskaluje* się do zerowych węzłów, gdy nie jest używany. To działanie jest podejmowane w celu zminimalizowania opłat. Jeśli chcesz usunąć element docelowy obliczeń, wykonaj następujące czynności:

![Usuń zasoby](./media/aml-ui-cleanup/delete-asset.png)

Zestawy danych można wyrejestrować z obszaru roboczego, zaznaczając każdy z nich i wybierając pozycję **Wyrejestruj**.

![Wyrejestruj zestaw danych](./media/aml-ui-cleanup/unregister-dataset1225.png)

Aby usunąć zestaw danych, przejdź do konta magazynu przy użyciu Azure Portal lub Eksplorator usługi Azure Storage i ręcznie usuń te zasoby.



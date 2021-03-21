---
title: Utwórz chmurę prywatną rozwiązania Azure VMware
description: Procedura tworzenia chmury prywatnej rozwiązania VMware platformy Azure przy użyciu Azure Portal.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: da79881e609f982960468a8f26c98178f972ad43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725408"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz nowy zasób**. W polu tekstowym **Wyszukaj w witrynie Marketplace** `Azure VMware Solution` , a następnie wybierz pozycję **Azure VMware Solution** z listy. W oknie **rozwiązanie platformy Azure VMware** wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** wpisz wartości pól. W poniższej tabeli wymieniono właściwości pól.

   | Pole   | Wartość  |
   | ---| --- |
   | **Subskrypcja** | Subskrypcja, której planujesz użyć do wdrożenia.|
   | **Grupa zasobów** | Grupa zasobów dla zasobów w chmurze prywatnej. |
   | **Lokalizacja** | Wybierz lokalizację, na przykład **Wschodnie stany USA**.|
   | **Nazwa zasobu** | Nazwa chmury prywatnej rozwiązania Azure VMware. |
   | **SKU** | Wybierz następującą wartość jednostki SKU: AV36 |
   | **Hosts** | Liczba hostów do dodania do klastra chmury prywatnej. Wartość domyślna to 3, która może zostać podniesiona lub obniżona po wdrożeniu.  |
   | **Blok adresów** | Wprowadź blok adresów IP dla sieci CIDR dla chmury prywatnej, na przykład 10.175.0.0/22. |
   | **Virtual Network** | Wybierz Virtual Network lub Utwórz nowy dla chmury prywatnej rozwiązania Azure VMware.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Na karcie podstawowe wpisz wartości pól." border="true":::

1. Po zakończeniu wybierz pozycję **Przegląd + Utwórz**. Na następnym ekranie Sprawdź wprowadzone informacje. Jeśli wszystkie informacje są poprawne, wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Ten krok zajmuje mniej niż dwie godziny. 

1. Sprawdź, czy wdrożenie zakończyło się pomyślnie. Przejdź do utworzonej grupy zasobów i wybierz chmurę prywatną.  Po zakończeniu wdrażania zostanie wyświetlony stan **powodzenie** . 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Sprawdź, czy wdrożenie zakończyło się pomyślnie." border="true":::
---
title: Wdróż rozwiązanie VMware dla platformy Azure
description: Procedura wdrażania rozwiązania VMware dla platformy Azure przy użyciu Azure Portal.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 4fa2925cf284871c4a168d2b69d4fbd3a81de878
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254646"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz nowy zasób**. W polu tekstowym **Wyszukaj w witrynie Marketplace** `Azure VMware Solution` , a następnie wybierz pozycję **Azure VMware Solution** z listy. W oknie **rozwiązanie platformy Azure VMware** wybierz pozycję **Utwórz** .

1. Na karcie **podstawowe** wpisz wartości pól. W poniższej tabeli wymieniono właściwości pól.

   | Pole   | Wartość  |
   | ---| --- |
   | **Subskrypcja** | Subskrypcja, której planujesz użyć do wdrożenia.|
   | **Grupa zasobów** | Grupa zasobów dla zasobów w chmurze prywatnej. |
   | **Lokalizacja** | Wybierz lokalizację, na przykład **Wschodnie stany USA**.|
   | **Nazwa zasobu** | Nazwa chmury prywatnej rozwiązania Azure VMware. |
   | **SKU** | Wybierz następującą wartość jednostki SKU: AV36 |
   | **Hosts** | Liczba hostów do dodania do klastra chmury prywatnej. Wartość domyślna to 3, która może zostać podniesiona lub obniżona po wdrożeniu.  |
   | **hasło administratora vCenter** | Wprowadź hasło administratora chmury. |
   | **Hasło Menedżera NSX-T** | Wprowadź hasło administratora NSX-T. |
   | **Blok adresów** | Wprowadź blok adresów IP dla sieci CIDR dla chmury prywatnej, na przykład 10.175.0.0/22. |
   | **Virtual Network** | Wybierz Virtual Network lub Utwórz nowy dla chmury prywatnej rozwiązania Azure VMware.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Na karcie podstawowe wpisz wartości pól." border="true":::

1. Po zakończeniu wybierz pozycję **Przegląd + Utwórz**. Na następnym ekranie Sprawdź wprowadzone informacje. Jeśli wszystkie informacje są poprawne, wybierz pozycję **Utwórz**.

   > [!NOTE]
   > Ten krok zajmuje mniej niż dwie godziny. 

1. Sprawdź, czy wdrożenie zakończyło się pomyślnie. Przejdź do utworzonej grupy zasobów i wybierz chmurę prywatną.  Po zakończeniu wdrażania zostanie wyświetlony stan **powodzenie** . 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Sprawdź, czy wdrożenie zakończyło się pomyślnie." border="true":::
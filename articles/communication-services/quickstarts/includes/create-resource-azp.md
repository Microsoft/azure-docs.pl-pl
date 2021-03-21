---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: ab30b19160294b334c8c5abdfbcc1dc0da370f48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495899"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Tworzenie zasobu komunikacyjnego platformy Azure

Aby utworzyć zasób usługi Azure Communications Services, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com). W lewym górnym rogu strony wybierz pozycję **+ Utwórz zasób**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Zrzut ekranu z wyróżnionym przyciskiem Utwórz zasób w Azure Portal.":::

Wprowadź wartość **komunikacja** na stronie **Wyszukaj w witrynie Marketplace** lub na pasku wyszukiwania w górnej części portalu.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie usług komunikacyjnych na pasku wyszukiwania.":::

W wynikach wybierz pozycję **usługi komunikacyjne** , a następnie wybierz pozycję **Dodaj**.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Zrzut ekranu przedstawiający panel platformy Azure z wyróżnionym przyciskiem Dodaj.":::

Teraz możesz skonfigurować zasób usług komunikacyjnych. Na pierwszej stronie w procesie tworzenia zostanie wyświetlony monit o określenie:

* Subskrypcja
* Grupa zasobów (można utworzyć nową grupę zasobów lub wybrać istniejącą)
* Nazwa zasobu usług komunikacyjnych
* Geografia, z którą zostanie skojarzony zasób

W następnym kroku można przypisać Tagi do zasobu. Tagi mogą służyć do organizowania zasobów platformy Azure. Aby uzyskać więcej informacji na temat tagów, zobacz [dokumentację tagowania zasobów](../../../azure-resource-manager/management/tag-resources.md) .

Na koniec możesz przejrzeć konfigurację i **utworzyć** zasób. Należy pamiętać, że wdrożenie może potrwać kilka minut.

## <a name="manage-your-communication-services-resource"></a>Zarządzanie zasobem usług komunikacyjnych

Aby zarządzać zasobem usług komunikacyjnych, przejdź do [Azure Portal](https://portal.azure.com)i Wyszukaj i wybierz pozycję **Azure Communication Services**.

Na stronie **usługi komunikacyjne** wybierz nazwę zasobu.

Strona **Przegląd** dla zasobu zawiera opcje podstawowego zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Więcej opcji konfiguracji można znaleźć w menu po lewej stronie zasobów.
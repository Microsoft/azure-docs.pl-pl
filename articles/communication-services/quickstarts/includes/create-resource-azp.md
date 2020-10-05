---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 11b9c553573d9e6188ba634b4cb966d6a9b850b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947660"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Tworzenie zasobu komunikacyjnego platformy Azure

Aby utworzyć zasób usługi Azure Communications Services, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com). W lewym górnym rogu strony wybierz pozycję **+ Utwórz zasób**. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Zrzut ekranu z wyróżnionym przyciskiem Utwórz zasób w Azure Portal.":::

Wprowadź wartość **komunikacja** na stronie **Wyszukaj w witrynie Marketplace** lub na pasku wyszukiwania w górnej części portalu.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Zrzut ekranu z wyróżnionym przyciskiem Utwórz zasób w Azure Portal.":::

W wynikach wybierz pozycję **usługi komunikacyjne** , a następnie wybierz pozycję **Dodaj**.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Zrzut ekranu z wyróżnionym przyciskiem Utwórz zasób w Azure Portal.":::

Teraz możesz skonfigurować zasób usług komunikacyjnych. Na pierwszej stronie w procesie tworzenia zostanie wyświetlony monit o określenie:

* Subskrypcja
* Grupa zasobów (można utworzyć nową grupę zasobów lub wybrać istniejącą)
* Nazwa zasobu usług komunikacyjnych
* Geografia, z którą zostanie skojarzony zasób

W następnym kroku można przypisać Tagi do zasobu. Tagi mogą służyć do organizowania zasobów platformy Azure. Aby uzyskać więcej informacji na temat tagów, zobacz [dokumentację tagowania zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources) .

Na koniec możesz przejrzeć konfigurację i **utworzyć** zasób. Należy pamiętać, że wdrożenie może potrwać kilka minut.

## <a name="manage-your-communication-services-resource"></a>Zarządzanie zasobem usług komunikacyjnych

Aby zarządzać zasobem usług komunikacyjnych, przejdź do [Azure Portal](https://portal.azure.com)i Wyszukaj i wybierz pozycję **Azure Communication Services**.

Na stronie **usługi komunikacyjne** wybierz nazwę zasobu.

Strona **Przegląd** dla zasobu zawiera opcje podstawowego zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Więcej opcji konfiguracji można znaleźć w menu po lewej stronie zasobów.
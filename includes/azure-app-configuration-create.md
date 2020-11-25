---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 48bf5d5df3c54e8b2894ad28fa08553d5add0437
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001483"
---
1. Aby utworzyć nowy magazyn konfiguracji aplikacji, zaloguj się do [Azure Portal](https://portal.azure.com). W lewym górnym rogu strony głównej wybierz pozycję **Utwórz zasób**. W polu **Wyszukaj w witrynie Marketplace** wprowadź wartość *Konfiguracja aplikacji* , a następnie wybierz pozycję <kbd>wprowadź</kbd>.

    ![Wyszukiwanie usługi App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Wybierz pozycję **Konfiguracja aplikacji** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

    ![Wybieranie pozycji Utwórz](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. W okienku **Tworzenie konfiguracji aplikacji** wprowadź następujące ustawienia:

    | Ustawienie | Sugerowana wartość | Opis |
    |---|---|---|
    | **Subskrypcja** | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć do testowania usługi App Configuration. Jeśli konto ma tylko jedną subskrypcję, zostanie automatycznie zaznaczone, a lista **subskrypcji** nie jest wyświetlana. |
    | **Grupa zasobów** | *AppConfigTestResources* | Wybierz lub Utwórz grupę zasobów dla zasobu magazynu konfiguracji aplikacji. Ta grupa jest przydatna do organizowania wielu zasobów, które mogą zostać usunięte w tym samym czasie przez usunięcie grupy zasobów. Aby uzyskać więcej informacji, zobacz temat [Używanie grup zasobów do zarządzania zasobami platformy Azure](../articles/azure-resource-manager/management/overview.md). |
    | **Nazwa zasobu** | Nazwa unikatowa w skali globalnej | Wprowadź unikatową nazwę zasobu, która ma być używana dla zasobu magazynu konfiguracji aplikacji. Nazwa musi być ciągiem od 5 do 50 znaków i zawierać tylko cyfry, litery i `-` znaki. Nazwa nie może rozpoczynać się ani kończyć `-` znakiem. |
    | **Lokalizacja** | *Środkowe stany USA* | Użyj opcji **Lokalizacja**, aby określić lokalizację geograficzną, w której będzie hostowany magazyn konfiguracji aplikacji. Aby uzyskać najlepszą wydajność, należy utworzyć zasób w tym samym regionie, w którym są inne składniki aplikacji. |
    | **Warstwa cenowa** | *Bezpłatna* | Wybierz odpowiednią warstwę cenową. Aby uzyskać więcej informacji, zobacz [stronę cennika konfiguracji aplikacji](https://azure.microsoft.com/pricing/details/app-configuration). |

1. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność ustawień.

1. Wybierz pozycję **Utwórz**. Wdrożenie może potrwać kilka minut.

1. Po zakończeniu wdrażania przejdź do zasobu konfiguracja aplikacji. Wybierz pozycje **Ustawienia** > **Klucze dostępu**. Zanotuj podstawowe parametry połączenia klucza tylko do odczytu. Te parametry połączenia będą używane później, aby skonfigurować aplikację do komunikacji z utworzonym magazynem konfiguracji aplikacji.
---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76021183"
---
1. Zaloguj się do [Azure portal][Azure portal].
1. Wybierz pozycję **Utwórz zasób**. Następnie wybierz pozycję **Integration**  >  **Relay**. Jeśli nie widzisz pozycji **Relay** na liście, wybierz pozycję **Zobacz wszystko** w prawym górnym rogu.
1. Wybierz pozycję **Utwórz**, a następnie wprowadź nazwę przestrzeni nazw w polu **Nazwa** . Azure Portal sprawdza, czy nazwa jest dostępna.
1. Wybierz subskrypcję platformy Azure, w której chcesz utworzyć przestrzeń nazw.
1. W obszarze [Grupa zasobów](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)wybierz istniejącą grupę zasobów, w której ma zostać umieszczona przestrzeń nazw, lub Utwórz nową.  
1. Wybierz kraj lub region, w którym powinna być hostowana przestrzeń nazw.

    ![Create namespace][create-namespace]

1. Wybierz przycisk **Utwórz**. Azure Portal tworzy przestrzeń nazw i włącza ją. Po kilku minutach system aprowizuje zasoby dla Twojego konta.

### <a name="get-management-credentials"></a>Uzyskiwanie poświadczeń zarządzania

1. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz nowo utworzoną nazwę przestrzeni nazw.
1. Wybierz pozycję **zasady dostępu współdzielonego**.  
1. W obszarze **Zasady dostępu współdzielonego** wybierz pozycję **RootManageSharedAccessKey**.
1. W obszarze **zasady sygnatury dostępu współdzielonego: RootManageSharedAccessKey**wybierz przycisk **Kopiuj** obok pozycji **podstawowe parametry połączenia**. Ta akcja powoduje skopiowanie parametrów połączenia do Schowka w celu późniejszego użycia. Wklej tę wartość do Notatnika lub innej tymczasowej lokalizacji.
1. Powtórz poprzedni krok w celu skopiowania i wklejenia wartości pozycji **Klucz podstawowy** w lokalizacji tymczasowej do późniejszego użycia.  

    ![połączenie — parametry][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com

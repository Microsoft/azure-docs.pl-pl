---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 3ca8d631110f8b175e7dc68d61cc6da4ac87d375
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789526"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [użytkowników pakietu Office 365](https://office365.com)  

Aby można było używać konta użytkowników pakietu Office 365 w aplikacji logiki, należy autoryzować aplikację logiki do łączenia się z kontem użytkowników programu Office 365. Na szczęście można to łatwo zrobić w aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono procedurę autoryzacji aplikacji logiki w celu nawiązania połączenia z kontem użytkowników pakietu Office 365:  

1. Aby utworzyć połączenie z użytkownikami pakietu Office 365, w Projektancie aplikacji logiki zaznacz opcję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź w polu wyszukiwania pozycję *Użytkownicy z pakietem Office 365* . Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![Krok tworzenia połączenia użytkowników pakietu Office 365](./media/connectors-create-api-office365users/office365users-1.png)  
2. Jeśli wcześniej nie utworzono żadnych połączeń z użytkownikami pakietu Office 365, zostanie wyświetlony monit o podanie poświadczeń użytkowników pakietu Office 365. Te poświadczenia będą używane do autoryzacji aplikacji logiki do nawiązywania połączenia z danymi konta użytkowników pakietu Office 365 i uzyskiwania do nich dostępu:  
   ![Krok tworzenia połączenia użytkowników pakietu Office 365](./media/connectors-create-api-office365users/office365users-2.png)  
3. Podaj nazwę użytkownika i hasło dla użytkowników pakietu Office 365, aby autoryzować aplikację logiki:  
   ![Krok tworzenia połączenia użytkowników pakietu Office 365](./media/connectors-create-api-office365users/office365users-3.png)  
4. Zwróć uwagę, że połączenie zostało utworzone i teraz możesz kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![Krok tworzenia połączenia użytkowników pakietu Office 365](./media/connectors-create-api-office365users/office365users-4.png)  


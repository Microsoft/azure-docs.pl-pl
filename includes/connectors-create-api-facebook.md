---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c94e7d1fb5c42a0246b38c88eb097c75ec8ca4e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789859"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto w usłudze [Facebook](https://www.facebook.com/) 

Aby można było używać swojego konta w usłudze Facebook w aplikacji logiki, należy autoryzować aplikację logiki do łączenia się z kontem w serwisie Facebook. Na szczęście można to łatwo zrobić w aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono procedurę autoryzacji aplikacji logiki w celu nawiązania połączenia z kontem w usłudze Facebook:

1. Aby utworzyć połączenie z usługą Facebook, w Projektancie aplikacji logiki wybierz pozycję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź wartość *Facebook* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![serwis Facebook — krok 1](./media/connectors-create-api-facebook/facebook-1.png)
2. Jeśli wcześniej nie utworzono żadnych połączeń z usługą Facebook, zostanie wyświetlony monit o podanie poświadczeń w serwisie Facebook. Te poświadczenia będą używane do autoryzacji aplikacji logiki do nawiązywania połączenia z usługą i uzyskiwania dostępu do danych konta w serwisie Facebook:  
   ![serwis Facebook — krok 2](./media/connectors-create-api-facebook/facebook-2.png)
3. Podaj nazwę użytkownika i hasło do usługi Facebook, aby autoryzować aplikację logiki:  
   ![Facebook — krok 3](./media/connectors-create-api-facebook/facebook-3.png)   
4. Zwróć uwagę, że połączenie zostało utworzone i teraz możesz kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![Krok 4 w serwisie Facebook](./media/connectors-create-api-facebook/facebook-4.png)   


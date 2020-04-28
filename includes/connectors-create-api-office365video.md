---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: c0dcba2dd003dfebdd9ce67bf4d78082c32824ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789493"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [wideo pakietu Office 365](https://support.office.com/article/Meet-Office-365-Video-ca1cc1a9-a615-46e1-b6a3-40dbd99939a6)  

Aby można było korzystać z konta wideo pakietu Office 365 w aplikacji logiki, należy autoryzować aplikację logiki do łączenia się z kontem wideo pakietu Office 365. Na szczęście można to łatwo zrobić w aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono procedurę autoryzacji aplikacji logiki w celu nawiązania połączenia z kontem wideo pakietu Office 365:  

1. Aby utworzyć połączenie z pakietem wideo pakietu Office 365, w Projektancie aplikacji logiki zaznacz opcję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź w polu wyszukiwania pozycję *wideo pakietu Office 365* . Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![Krok tworzenia połączenia wideo z pakietem Office 365](./media/connectors-create-api-office365video/office365video-1.png)  
2. Jeśli wcześniej nie utworzono żadnych połączeń z pakietem wideo pakietu Office 365, zostanie wyświetlony monit o podanie poświadczeń wideo pakietu Office 365. Te poświadczenia będą używane do autoryzacji aplikacji logiki do nawiązywania połączenia i uzyskiwania dostępu do danych konta wideo pakietu Office 365:  
   ![Krok tworzenia połączenia wideo z pakietem Office 365](./media/connectors-create-api-office365video/office365video-2.png)  
3. Podaj swoje poświadczenia, aby połączyć się z wideo pakietu Office 365:  
   ![Krok tworzenia połączenia wideo z pakietem Office 365](./media/connectors-create-api-office365video/office365video-3.png)  
4. Zwróć uwagę, że połączenie zostało utworzone i teraz możesz kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![Krok tworzenia połączenia wideo z pakietem Office 365](./media/connectors-create-api-office365video/office365video-4.png)  


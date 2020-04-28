---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789658"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* Konto w [usłudze OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Aby móc korzystać z konta usługi OneDrive w aplikacji logiki, Autoryzuj aplikację logiki w celu nawiązania połączenia z kontem w usłudze OneDrive.  Można to łatwo zrobić w aplikacji logiki na Azure Portal. 

Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem w usłudze OneDrive, wykonując następujące czynności:

1. Utwórz aplikację logiki. W projektancie Logic Apps wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie w polu wyszukiwania wprowadź ciąg "OneDrive". Wybierz jeden z wyzwalaczy lub akcji:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Jeśli nie utworzono wcześniej żadnych połączeń z usługą OneDrive, zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń usługi OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Wybierz pozycję **Zaloguj**, a następnie wprowadź nazwę użytkownika i hasło. Wybierz pozycję **Zaloguj się**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Te poświadczenia służą do autoryzacji aplikacji logiki do nawiązywania połączenia z usługą i uzyskiwania dostępu do danych na koncie usługi OneDrive. 
4. Wybierz pozycję **tak** , aby autoryzować aplikację logiki do korzystania z Twojego konta w usłudze OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Zwróć uwagę na to, że połączenie zostało utworzone. Teraz wykonaj inne czynności w aplikacji logiki:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)


---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87102879"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [usługi OneDrive dla firm](https://OneDrive.com) 

Aby można było korzystać z konta usługi OneDrive dla firm z usługą Logic Apps, należy autoryzować Logic Apps do nawiązania połączenia z kontem usługi OneDrive dla firm w Azure Portal.

Wykonaj następujące kroki, aby autoryzować Logic Apps do nawiązywania połączenia z kontem usługi OneDrive dla firm:  

1. Zaloguj się w witrynie Azure Portal. 

1. W obszarze **usługi platformy Azure** wybierz pozycję **Logic Apps**. Następnie wybierz z listy nazwę aplikacji logiki.

1. W menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki** w obszarze **Narzędzia programistyczne**.

1. W projektancie Logic Apps zaznacz opcję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie w polu wyszukiwania wprowadź wartość *OneDrive dla firm* . Wybierz wyzwalacz lub akcję do użycia:  

   ![Zrzut ekranu projektanta Logic Apps, pokazujący wyzwalacz cyklu z akcjami interfejsu API usługi OneDrive dla firm.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Jeśli wcześniej nie utworzono żadnych połączeń z usługą OneDrive dla firm, postępuj zgodnie z monitem o podanie poświadczeń usługi OneDrive dla firm. Te poświadczenia służą do autoryzowania aplikacji logiki do uzyskiwania dostępu do danych konta usługi OneDrive dla firm:  

   ![Zrzut ekranu programu Logic Apps Designer z wyświetlonym monitem logowania dla usługi OneDrive dla firm.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Podaj nazwę użytkownika i hasło do usługi OneDrive dla firm, aby autoryzować aplikację logiki:  

   ![Zrzut ekranu strony logowania do usługi OneDrive dla firm, w której wyświetlany jest monit logowania.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. Połączenie jest teraz wymienione w kroku. Wybierz pozycję Zapisz, a następnie Kontynuuj tworzenie aplikacji logiki. 

   ![Zrzut ekranu programu Logic Apps Designer z wyświetlonym wyzwalaczem z połączeniem z usługą OneDrive dla firm.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87040228"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto w [usłudze OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Aby móc korzystać z konta usługi OneDrive w Logic Apps, musisz autoryzować Logic Apps do łączenia się z kontem w usłudze OneDrive w Azure Portal.

Wykonaj następujące kroki, aby autoryzować Logic Apps do nawiązywania połączenia z kontem w usłudze OneDrive:  

1. Zaloguj się do Portalu Azure. 

1. W obszarze **usługi platformy Azure**wybierz pozycję **Logic Apps**. Następnie wybierz z listy nazwę aplikacji logiki.

1. W menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki** w obszarze **Narzędzia programistyczne**.

1. W projektancie Logic Apps zaznacz opcję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź w polu wyszukiwania pozycję *OneDrive* . Wybierz wyzwalacz lub akcję do użycia:

   ![Zrzut ekranu programu Logic Apps Designer pokazujący listę akcji interfejsu API usługi OneDrive do dodania.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Jeśli nie utworzono wcześniej żadnych połączeń z usługą OneDrive, postępuj zgodnie z monitem, aby zalogować się przy użyciu poświadczeń usługi OneDrive:  

   ![Zrzut ekranu przedstawiający projektanta Logic Apps, pokazujący monit logowania dla interfejsu API usługi OneDrive.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Wybierz pozycję **Zaloguj**, a następnie wprowadź nazwę użytkownika i hasło. Wybierz pozycję **Zaloguj się**: 

   ![Zrzut ekranu przedstawiający stronę logowania konto Microsoft na potrzeby autoryzacji interfejsu API usługi OneDrive.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Te poświadczenia służą do autoryzowania aplikacji logiki do uzyskiwania dostępu do danych na koncie w usłudze OneDrive. 

4. Wybierz pozycję **tak** , aby autoryzować aplikację logiki do korzystania z Twojego konta w usłudze OneDrive:  

   ![Zrzut ekranu przedstawiający konto Microsoft autoryzację dla Logic Apps, z uwzględnieniem dozwolonych akcji.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. Połączenie jest teraz wymienione w kroku. Wybierz pozycję Zapisz, a następnie Kontynuuj tworzenie aplikacji logiki. 

   ![Zrzut ekranu programu Logic Apps Designer, który pokazuje Edytor akcji z połączeniem interfejsu API usługi OneDrive.](./media/connectors-create-api-onedrive/onedrive-5.png)

---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: a7290e46a0c5e228689dd1f1a0dc7f9a1182ad60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74789650"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [MailChimp](https://www.MailChimp.com/) 

Aby można było używać konta MailChimp w aplikacji logiki, należy autoryzować aplikację logiki do łączenia się z kontem MailChimp. Na szczęście można to łatwo zrobić w aplikacji logiki w witrynie Azure Portal. 

Poniżej przedstawiono procedurę autoryzacji aplikacji logiki w celu nawiązania połączenia z kontem MailChimp:

1. Aby utworzyć połączenie z usługą MailChimp, w Projektancie aplikacji logiki wybierz pozycję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź *MailChimp* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![MailChimp krok 1](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Jeśli nie utworzono żadnych połączeń z MailChimp przed, otrzymasz monit o podanie poświadczeń MailChimp. Te poświadczenia będą używane do autoryzacji aplikacji logiki do nawiązywania połączenia z usługą i uzyskiwania dostępu do danych konta MailChimp:  
   ![MailChimp krok 2](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. Podaj nazwę użytkownika MailChimp i hasło, aby autoryzować aplikację logiki:  
   ![MailChimp krok 3](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Zwróć uwagę, że połączenie zostało utworzone i teraz możesz kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![MailChimp krok 4](./media/connectors-create-api-mailchimp/mailchimp-4.png)


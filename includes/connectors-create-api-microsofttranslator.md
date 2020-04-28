---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 94f18b20d84a1929368053c44b4f07793e385b38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789566"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto [MicrosoftTranslator](https://www.microsoft.com/translator)  

Aby można było używać konta MicrosoftTranslator w aplikacji logiki, należy autoryzować aplikację logiki do łączenia się z kontem MicrosoftTranslator. Na szczęście można to łatwo zrobić w aplikacji logiki w witrynie Azure Portal.  

Poniżej przedstawiono procedurę autoryzacji aplikacji logiki w celu nawiązania połączenia z kontem MicrosoftTranslator:  

1. Aby utworzyć połączenie z usługą MicrosoftTranslator, w Projektancie aplikacji logiki wybierz pozycję **Pokaż interfejsy API zarządzane przez firmę Microsoft** na liście rozwijanej, a następnie wprowadź *MicrosoftTranslator* w polu wyszukiwania. Wybierz wyzwalacz lub akcję, której chcesz użyć:  
   ![Krok tworzenia połączenia MicrosoftTranslator](./media/connectors-create-api-microsofttranslator/microsofttranslator-1.png)  
2. Jeśli nie utworzono żadnych połączeń z MicrosoftTranslator przed, otrzymasz monit o podanie poświadczeń MicrosoftTranslator. Te poświadczenia będą używane do autoryzacji aplikacji logiki do nawiązywania połączenia z usługą i uzyskiwania dostępu do danych konta MicrosoftTranslator:  
   ![Krok tworzenia połączenia MicrosoftTranslator](./media/connectors-create-api-microsofttranslator/microsofttranslator-2.png)  
3. Zwróć uwagę, że połączenie zostało utworzone i teraz możesz kontynuować wykonywanie innych czynności w aplikacji logiki:  
   ![Krok tworzenia połączenia MicrosoftTranslator](./media/connectors-create-api-microsofttranslator/microsofttranslator-3.png)  


---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999550"
---
1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki. 

2. W projektancie Logic Apps wprowadź "GitHub" jako filtr. 

3. Wybierz łącznik usługi GitHub i wyzwalacz, którego chcesz użyć.

   ![Wybieranie łącznika usługi GitHub i wyzwalacza](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Wszystkie przepływy pracy aplikacji logiki muszą zaczynać się od wyzwalacza. Akcje można wybrać tylko wtedy, gdy przepływ pracy logiki zaczyna się już od wyzwalacza. 

4. Jeśli połączenie nie zostało wcześniej utworzone, wybierz pozycję **Zaloguj** , aby po wyświetleniu monitu podać poświadczenia usługi GitHub.  

   ![Zaloguj się przy użyciu swoich poświadczeń usługi GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Aplikacja logiki używa tych poświadczeń do autoryzacji łączenia i uzyskiwania dostępu do danych konta usługi GitHub. 

5. Podaj nazwę użytkownika i hasło usługi GitHub, a następnie potwierdź autoryzację.

   ![Podaj poświadczenia i Potwierdź autoryzację](./media/connectors-create-api-github/github-connector-authorize.png)   

   Połączenie jest teraz tworzone w Azure Portal i jest gotowe do użycia.

6. Kontynuuj definiowanie przepływu pracy aplikacji logiki.

   ![Dodawanie kolejnych akcji do przepływu pracy aplikacji logiki](./media/connectors-create-api-github/github-connector-logic-app.png)


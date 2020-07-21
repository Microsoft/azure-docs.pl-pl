---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524215"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* Konto w [usłudze OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Aby móc korzystać z konta usługi OneDrive w aplikacji logiki, Autoryzuj aplikację logiki w celu nawiązania połączenia z kontem w usłudze OneDrive.  Można to łatwo zrobić w aplikacji logiki na Azure Portal. 

Autoryzuj aplikację logiki, aby nawiązać połączenie z kontem w usłudze OneDrive, wykonując następujące czynności:

1. Utwórz aplikację logiki. W projektancie Logic Apps wybierz pozycję **Pokaż zarządzane interfejsy API firmy Microsoft** na liście rozwijanej, a następnie w polu wyszukiwania wprowadź ciąg "OneDrive". Wybierz jeden z wyzwalaczy lub akcji:  
   ![Okno dialogowe zatytułowane "Pokaż zarządzane interfejsy API firmy Microsoft" ma pole wyszukiwania, które zawiera wartość "OneDrive". Poniżej znajduje się lista czterech wyzwalaczy. Pierwszy na liście to "OneDrive — po utworzeniu pliku, który jest zaznaczony.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Jeśli nie utworzono wcześniej żadnych połączeń z usługą OneDrive, zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń usługi OneDrive:  
   ![Okno dialogowe zatytułowane "OneDrive — po utworzeniu pliku" ma przycisk "Zaloguj".](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Wybierz pozycję **Zaloguj**, a następnie wprowadź nazwę użytkownika i hasło. Wybierz pozycję **Zaloguj się**:  
   ![Okno dialogowe zatytułowane "Logowanie" oznacza "Korzystanie z konto Microsoft". Ma dwa pola tekstowe o nazwie "email lub Phone" i "Password" zawierają również pole wyboru z etykietą "nie wylogowuj mnie" i przycisk "Zaloguj".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Te poświadczenia służą do autoryzacji aplikacji logiki do nawiązywania połączenia z usługą i uzyskiwania dostępu do danych na koncie usługi OneDrive. 
4. Wybierz pozycję **tak** , aby autoryzować aplikację logiki do korzystania z Twojego konta w usłudze OneDrive:  
   ![Okno dialogowe zatytułowane "Zezwól tej aplikacji na dostęp do informacji?" zostanie wyświetlony monit o zezwolenie na wykonanie następujących czterech czynności: 1) "Logowanie automatyczne", 2) "dostęp do Twoich adresów e-mail", 3) "dostęp do informacji w dowolnym momencie" i 4) "dostęp do plików w usłudze OneDrive". Istnieje przycisk "tak", aby nadać uprawnienie, a przycisk "nie", aby go odmówić. Istnieje link pozwalający zmienić te uprawnienia aplikacji.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Zwróć uwagę na to, że połączenie zostało utworzone. Teraz wykonaj inne czynności w aplikacji logiki:  
   ![Okno dialogowe zatytułowane "gdy tworzony jest plik" ma pole tekstowe zatytułowane "FOLDER" ze skojarzonym przyciskiem przeglądania.](./media/connectors-create-api-onedrive/onedrive-5.png)


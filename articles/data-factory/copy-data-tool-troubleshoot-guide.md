---
title: Rozwiązywanie problemów z narzędziami Kopiowanie danych w programie Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z narzędziami Kopiowanie danych w programie Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92550674"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Rozwiązywanie problemów z narzędziami Kopiowanie danych w programie Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów Kopiowanie danych narzędzia w programie Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Kod błędu: nie można przeprowadzić walidacji w narzędziu Kopiowanie danych

- **Objawy** : w pierwszym kroku narzędzia kopiowanie danych zostanie wyświetlony komunikat ostrzegawczy "nie można sprawdzić poprawności".
- **Przyczyny** : może to być spowodowane wyłączeniem wszystkich plików cookie innych firm.
- **Rozwiązanie** : 
    - Użyj programu Internet Explorer lub przeglądarki Microsoft Edge.
    - Jeśli używasz przeglądarki Chrome, postępuj zgodnie z poniższymi instrukcjami, aby dodać wyjątek dla plików cookie dla *microsoftonline.com* i *Windows.NET* .
        1.  Otwórz przeglądarkę Chrome.
        2.  Kliknij klucz lub trzy wiersze po prawej stronie (Dostosowywanie i kontrolowanie Google Chrome).
        3.  Kliknij polecenie **Ustawienia** .
        4.  Wyszukaj **pliki cookie** lub przejdź do obszaru **Ochrona prywatności** w obszarze Ustawienia zaawansowane.
        5.  Wybierz pozycję **ustawienia zawartości** .    
        6.  Pliki cookie powinny być ustawione tak, aby **zezwalały na ustawianie danych lokalnych (zalecane)** .
        7.  Kliknij pozycję **Zarządzaj wyjątkami** . W obszarze **wzorzec nazwy hosta** wprowadź następujące polecenie i upewnij się, że opcja **Zezwalaj** jest ustawionym zachowaniem.
            - login.microsoftonline.com
            - login.windows.net
        8.  Zamknij przeglądarkę i uruchom ponownie.
    - Jeśli używasz przeglądarki Firefox, postępuj zgodnie z poniższymi instrukcjami, aby dodać wyjątek dla plików cookie.
        1. W menu Firefox przejdź do pozycji **Narzędzia**  >  **Opcje** .
        2. W **Privacy** obszarze  >  **historia** prywatności może być widoczne, że bieżące ustawienie **używa ustawień niestandardowych dla historii** .
        3. W obszarze **Zaakceptuj pliki cookie innych firm** bieżące ustawienie może nie być **nigdy** , a następnie kliknij pozycję **wyjątki** po prawej stronie, aby dodać poniższe lokacje.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Zamknij przeglądarkę i uruchom ponownie. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Kod błędu: nie można otworzyć strony logowania i wprowadzić hasła

- **Objawy** : Narzędzie kopiowanie danych przekieruje Cię do strony logowania, ale strona logowania nie jest wyświetlana pomyślnie.
- **Przyczyny** : ten problem może wystąpić, Jeśli zmieniono środowisko sieciowe z sieci biurowej na sieć domową. W przeglądarkach znajdują się niektóre pamięci podręczne. 
- **Rozwiązanie** : 
    1.  Zamknij przeglądarkę i spróbuj ponownie. Jeśli problem nadal istnieje, przejdź do następnego kroku.   
    2.  Jeśli używasz przeglądarki Internet Explorer, spróbuj otworzyć ją w trybie prywatnym (naciśnij klawisze "Ctrl" + "Shift" + "P"). Jeśli używasz przeglądarki Chrome, spróbuj otworzyć ją w trybie incognito (naciśnij klawisze "Ctrl" + "Shift" + "N"). Jeśli problem nadal istnieje, przejdź do następnego kroku. 
    3.  Spróbuj użyć innej przeglądarki. 


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik dotyczący wydajności przepływów danych dla mapowania ADF](concepts-data-flow-performance.md)

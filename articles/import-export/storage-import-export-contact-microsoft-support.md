---
title: Utwórz bilet pomocy technicznej lub przypadek dla zadania importu/eksportu platformy Azure | Microsoft Docs
description: Dowiedz się, jak rejestrować żądania obsługi dla problemów związanych z zadaniem importu/eksportu.
services: storsimple
author: alkohli
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 03d953bd534595e47702642403626a05b7f67aba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706921"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>Otwórz bilet pomocy technicznej dla zadania importu/eksportu

Jeśli wystąpią problemy z usługą Import/Export, można utworzyć żądanie obsługi dla pomocy technicznej. W tym artykule przedstawiono następujące instrukcje:

* Jak utworzyć żądanie obsługi.
* Jak zarządzać cyklem życia żądania pomocy technicznej z poziomu portalu.

## <a name="create-a-support-request"></a>Tworzenie żądania obsługi

Wykonaj następujące kroki, aby utworzyć żądanie obsługi:

1. Przejdź do zadania importowania/eksportowania. Przejdź do sekcji **Obsługa i rozwiązywanie problemów** , a następnie wybierz pozycję **nowe żądanie obsługi**.
     
    ![Podstawy](./media/storage-import-export-contact-microsoft-support/import-export-support1.png)
   
2. W obszarze **nowe żądanie obsługi** wybierz pozycję **podstawowe**. W obszarze **podstawowe** wykonaj następujące czynności:
    
    1. Z listy rozwijanej **typ problemu** wybierz pozycję **techniczne**.
    2. Wybierz **subskrypcję**.
    3. W obszarze **Usługa** Sprawdź pozycję **Moje usługi**. Z listy rozwijanej można wybrać jedną z opcji — **Zarządzanie kontem magazynu**, **obiekt BLOB** lub **plik**. 
        - W przypadku wybrania opcji **Zarządzanie kontem magazynu** wybierz pozycję **zasób** i **Plan pomocy technicznej**.
            ![Wybierz pozycję Zarządzanie kontem magazynu](./media/storage-import-export-contact-microsoft-support/import-export-support3.png)
        - W przypadku wybrania **obiektu BLOB** wybierz pozycję **zasób**, **nazwy kontenerów** (opcjonalnie) i **Plan pomocy technicznej**.
            ![Wybierz obiekt BLOB](./media/storage-import-export-contact-microsoft-support/import-export-support2.png)
        - W przypadku wybrania opcji **plik** wybierz pozycję **zasób**, **nazwy udziałów plików** (opcjonalnie), a następnie wybierz pozycję plik **planu pomocy technicznej** . ![](./media/storage-import-export-contact-microsoft-support/import-export-support4.png)
    4. Wybierz opcję **Dalej**.

3. W obszarze **nowe żądanie obsługi** wybierz pozycję **krok 2**. W obszarze **problem** wykonaj następujące czynności:
    
    1. Wybierz **wagę** jako **wpływ na C-minimalny**. W razie konieczności dział pomocy technicznej zaktualizuje ją.
    2. Wybierz **typ problemu** jako **migrację danych**.
    3. Wybierz **kategorię** jako **Import-Export**.
    4. Podaj **tytuł** problemu i więcej **szczegółów**.
    5. Podaj datę i godzinę rozpoczęcia problemu.
    6. W polu **przekazywanie pliku** wybierz ikonę folderu, aby przeglądać wszystkie inne pliki, które chcesz przekazać.
    7. Sprawdź **udostępnianie informacji diagnostycznych**.
    8. Wybierz opcję **Dalej**.

       ![Problem](./media/storage-import-export-contact-microsoft-support/import-export-support5.png)

4. W obszarze **nowe żądanie obsługi** wybierz pozycję **krok 3. informacje kontaktowe**. W oknie **informacje kontaktowe** wykonaj następujące czynności:

   1. W obszarze **Opcje kontaktu** Podaj preferowaną metodę kontaktu (telefon lub adres e-mail) i język. Czas odpowiedzi jest wybierany automatycznie na podstawie planu subskrypcji.
   2. W informacjach kontaktowych Podaj swoją nazwę, adres e-mail, kontakt opcjonalny, kraj/region. Zaznacz pole wyboru **Zapisz zmiany kontaktu w przyszłych żądaniach pomocy technicznej** .
   3. Wybierz przycisk **Utwórz**.
   
       ![Informacje kontaktowe](./media/storage-import-export-contact-microsoft-support/import-export-support7.png)   

      Pomoc techniczna firmy Microsoft będzie używać tych informacji w celu uzyskania dalszych informacji, diagnozowania i rozwiązywania problemów.
      Po przesłaniu żądania inżynier pomocy technicznej skontaktuje się z Tobą w celu kontynuowania żądania.

## <a name="manage-a-support-request"></a>Zarządzanie żądaniem obsługi

Po utworzeniu biletu pomocy technicznej możesz zarządzać jego cyklem życia z poziomu portalu.

#### <a name="to-manage-your-support-requests"></a>Aby zarządzać żądaniami pomocy technicznej

1. Aby przejść do strony pomoc i obsługa techniczna, przejdź do okna **przeglądaj > pomoc i obsługa techniczna**.

    ![Zrzut ekranu przedstawia okno dialogowe pomoc.](./media/storage-import-export-contact-microsoft-support/manage-support-ticket2.png)   

2. W **oknie Pomoc i obsługa techniczna** jest wyświetlana tabelaryczna lista **najnowszych żądań obsługi** .

    ![Zrzut ekranu przedstawia stronę pomocy i obsługi technicznej w stanie otwartym.](./media/storage-import-export-contact-microsoft-support/manage-support-ticket1.png) 

3. Wybierz i kliknij żądanie pomocy technicznej. Możesz wyświetlić stan i szczegóły tego żądania. Wybierz pozycję **+ Nowy komunikat** , jeśli chcesz wykonać kolejne czynności w ramach tego żądania.

    ![Zrzut ekranu przedstawia nowy komunikat wybrany dla tego żądania.](./media/storage-import-export-contact-microsoft-support/manage-support-ticket3.png) 


## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak transferować dane do i z usługi Azure Storage za pomocą usługi Azure Import/Export](storage-import-export-service.md).

---
title: Samouczek do dostarczania Azure Data Box z powrotem w kolejności eksportu | Microsoft Docs
description: Dowiedz się, jak dostarczyć Azure Data Box do firmy Microsoft po zakończeniu zamówienia eksportu
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6c2eed9aac614b0765582d5cf373caf50323ef7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537360"
---
# <a name="tutorial-return-azure-data-box"></a>Samouczek: zwracanie Azure Data Box

W tym samouczku opisano, jak zwrócić Azure Data Box, a dane są wymazywane po odebraniu urządzenia do danych platformy Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Wymazywanie danych z urządzenia Data Box

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

* Samouczek został ukończony [: Kopiuj dane z Azure Data Box](data-box-deploy-export-copy-data.md).
* Zadania kopiowania zostały wykonane. Przygotowanie do wysłania nie będzie działać, jeśli zadania kopiowania są w toku.

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

Następne czynności są zależne od lokalizacji, z której zwracasz urządzenie.

## <a name="ship-data-box-back"></a>Wysyłka zwrotna urządzenia Data Box

Upewnij się, że kopia danych z urządzenia została ukończona, a **przygotowanie do wysłania** uruchomienie zakończyło się pomyślnie. W zależności od regionu, w którym jest wysyłane urządzenie, procedura jest inna.

## <a name="us-canada-europe"></a>[W Stanach Zjednoczonych, Kanadzie, Europie](#tab/in-us-canada-europe)

W przypadku zwrotu urządzenia w Stanach Zjednoczonych, Kanadzie lub Europie wykonaj następujące czynności.

1. Upewnij się, że wyłączono zasilanie urządzenia, a kable zostały odłączone. 
2. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Upewnij się, że etykieta wysyłkowa jest wyświetlana na ekranie E-Ink, i Zaplanuj odbiór z użyciem operatora. Jeśli etykieta jest uszkodzona lub zgubiona lub nie jest wyświetlana na ekranie E-Ink, skontaktuj się z pomoc techniczna firmy Microsoft. Jeśli otrzymasz takie instrukcje od pomocy technicznej, możesz przejść na stronę **Przegląd > Pobierz etykietę wysyłkową** w witrynie Azure Portal. Pobierz etykietę wysyłkową i zamocować ją na urządzeniu. 
4. Jeśli zwracasz urządzenie, zamów odbiór paczki przez firmę UPS. Aby zaplanować odbiór, wykonaj następujące czynności:

   - Zadzwoń do lokalnego oddziału firmy UPS (numer bezpłatny dla danego kraju/regionu).
   - Podczas rozmowy podaj numer śledzenia przesyłki zwrotnej widoczny na wyświetlaczu E-ink lub na wydrukowanej etykiecie. Jeśli nie podasz numeru śledzenia, firma UPS będzie wymagała dodatkowej opłaty podczas odbioru.
   - Jeśli wystąpią jakiekolwiek problemy podczas planowania odbioru lub pojawi się prośba o uiszczenie dodatkowych opłat, skontaktuj się z zespołem operacyjnym usługi Azure Data Box. Wyślij wiadomość e-mail do [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Zamiast planowania odbioru możesz także dostarczyć urządzenie Data Box do najbliższego punktu UPS.
4. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

## <a name="australia"></a>[Australia](#tab/in-australia)

W przypadku centrów danych w Australii obowiązuje dodatkowe zabezpieczenie. Zabezpieczenie to polega na powiadamianiu z wyprzedzeniem o wszystkich przesyłkach przychodzących. Wykonaj poniższe kroki w przypadku wysyłki w Australii.

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a **przygotowanie do wysłania** zakończyło się pomyślnie.
3. Wyłącz urządzenie i odłącz kable.
4. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
5. Zarezerwuj odbiór online na stronie [Link do firmy DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="japan"></a>[Japonia](#tab/in-japan)

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Wyłącz urządzenie i odłącz kable.
3. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
4. Na liście przewozowym wpisz nazwę i adres swojej firmy jako dane nadawcy.
5. Wyślij wiadomość e-mail do firmy Quantium Solutions, korzystając z następującego szablonu.

    * Jeśli list przewozowy firmy Japan Post Chakubarai nie został dołączony lub został utracony, poinformuj o tym w wiadomości e-mail. Firma Quantium Solutions Japan zleci firmie Japan Post dostarczenie listu przewozowego po odebraniu przesyłki.
    * Jeśli masz wiele zamówień, wyślij stosowną wiadomość e-mail, aby zapewnić indywidualny odbiór.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. Po zarezerwowaniu odbioru otrzymasz od firmy Quantium Solutions wiadomość e-mail z potwierdzeniem. Wiadomość e-mail z potwierdzeniem zawiera również informacje o liście przewozowym Chakubarai.

W razie konieczności możesz skontaktować się z pomocą techniczną firmy Quantium Solutions (język japoński), korzystając z poniższych informacji: 

* Adres e-mail: Customerservice.JP@quantiumsolutions.com
* Numer telefonu：03-5755-0150

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Zanotuj numer śledzenia (widoczny jako numer referencyjny na stronie przygotowywania do wysyłki w lokalnym internetowym interfejsie użytkownika urządzenia Data Box). Ta wartość jest dostępna po pomyślnym ukończeniu kroku przygotowywania do wysyłki. Pobierz etykietę wysyłkową z tej strony i naklej ją na opakowaniu.
3. Wyłącz urządzenie i odłącz kable.
4. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia. 
5. Wyślij wiadomość e-mail do działu obsługi klienta firmy SingPost przy użyciu następującego szablonu wiadomości e-mail, podając numer śledzenia.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > W przypadku żądań rezerwacji odebranych w dniu roboczym:
   >
   > * Przed 15:00 — odbiór odbędzie się następnego dnia roboczego między 9:00 a 13:00.
   > * Po 15:00 — odbiór odbędzie się następnego dnia roboczego między 14:00 a 18:00.  

## <a name="south-africa"></a>[Republika Południowej Afryki](#tab/in-sa)

1. Zachowaj oryginalne pole używane do pakowania urządzenia na potrzeby wysyłki zwrotnej.
2. Zanotuj numer odwołania (numer listów) widoczny w lokalnym interfejsie użytkownika sieci Web urządzenia. Ta liczba jest wyświetlana po pomyślnym **uruchomieniu przygotowanie do wysłania** .
3. Pobierz i wydrukuj etykietę wysyłkową, która jest dostępna w lokalnym interfejsie użytkownika sieci Web urządzenia i umieścić ją w pakiecie dostarczania.
4. Aby zaksięgować pobranie z DHL, wybierz jedną z następujących opcji:

    * Wywołaj centrum kontaktowe działu obsługi klienta przed 2:00 PM w dniu **+ 27 (0) 11 9213600**, wybierz opcję 1, a następnie określ numer telefonu.
    * Wyślij wiadomość e-mail na adres [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com), korzystając z następującego szablonu:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Zamiast tego można usunąć pakiet z najbliższego punktu usługi DHL.

5. Jeśli występują problemy, Wyślij wiadomość e-mail [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) ze szczegółowymi informacjami o wykrytych problemach i umieść numer list wierszy w wierszu subject:. Możesz również zadzwonić pod numer +27(0)119213902.

## <a name="hong-kong"></a>[SRA Hongkong](#tab/in-hk)

1. Na potrzeby wysyłki zwrotnej zapakuj urządzenie w oryginalne pudełko.
2. Zanotuj numer odwołania (numer śledzenia dla wysyłki odwrotnej) widoczny w lokalnym interfejsie użytkownika sieci Web urządzenia. Ta liczba jest wyświetlana po pomyślnym **uruchomieniu przygotowanie do wysłania** .
3. Pobierz i wydrukuj etykietę wysyłkową, która jest dostępna w lokalnym interfejsie użytkownika sieci Web urządzenia i umieścić ją w pakiecie dostarczania.
4. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
5. Zadzwoń na infolinię firmy **Quantium Solutions** pod numer **(852) 2318 1213** w godzinach jej pracy (od 9:00 do 18:00, od poniedziałku do piątku).  
6. Powołaj się na odbiór przesyłki w ramach platformy Microsoft Azure i podaj numer referencyjny oraz numer śledzenia (powyższy kod kreskowy) na zwrotnej etykiecie wysyłkowej w celu zorganizowania odbioru urządzenia.
7. Otrzymasz słowne potwierdzenie terminu odbioru. Jeśli Kurier nie dotarł do kolekcji, wywołaj metodę Quantium Solutions, aby zapoznać się z alternatywnymi rozwiązaniami.
8. Po zarezerwowaniu odbioru za pośrednictwem firmy Quantium udostępnij potwierdzenie działowi [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com), korzystając z następującego szablonu:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

W przypadku wystąpienia jakichkolwiek problemów w wiadomościach e-mail urządzenie Data Box operacje Azji, [adbo@microsoft.com](mailto:adbo@microsoft.com) podając nazwę zadania w nagłówku tematu i napotkany problem.

## <a name="united-arab-emirates"></a>[Zjednoczone Emiraty Arabskie](#tab/in-uae)

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a krok **przygotowanie do wysłania** zakończył się pomyślnie.
3. Zwróć uwagę na numer odwołania na stronie **przygotowanie do wysłania** lokalnego interfejsu użytkownika sieci Web urządzenia.
4. Wyłącz zasilanie urządzenia i Usuń kable. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
6. Na potrzeby wysyłki zwrotnej zapakuj urządzenie w oryginalne pudełko.
7. [Azure Data Box operacji](mailto:adbops@microsoft.com) w celu uzyskania identyfikatora, który zostanie użyty do zidentyfikowania pakietu po nadejściu go do centrum danych.
8. Zapisz ten identyfikator na drukowanej etykiecie wysyłkowej obok adresu zwrotnego, aby był widoczny.  
9. Zarezerwuj przebiór w tryb online, przechodząc do [DHL ekspresowego](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab)  >  **Zaplanuj odbiór**.
   - Wprowadź numer referencyjny ze strony **przygotowanie do wysłania** lokalnego interfejsu użytkownika sieci Web urządzenia w polu Numer list rzeczy.
   - Rezerwacje są akceptowane od 9:00 AM – 2:00 PM sześć dni w tygodniu (z wyjątkiem pt i dni wolnych od pracy).
   - Żądania pobrania należy umieścić co najmniej 90 minut przed godziną zamknięcia klienta.
10. Jeśli masz problemy z narzędziem do rezerwacji DHL, możesz skontaktować się z DHL przy użyciu dowolnej z następujących metod:
    - Wywołanie 04-2924545.
    - Wyślij wiadomość e-mail [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com) ze szczegółowymi informacjami o problemach i umieść numer list kontrolnych w wierszu subject:.
    - Zadzwoń do działu obsługi klienta DHL o 600 567567.

## <a name="self-managed"></a>[Samodzielne zarządzanie](#tab/in-selfmanaged)

Jeśli używasz urządzenie Data Box w Japonii, Singapur, Korea, Indie, Republika Południowej Afryki, Zjednoczone Królestwo, Europa Zachodnia lub Australia i wybrano opcję wysyłki samozarządzanej podczas tworzenia zamówienia, postępuj zgodnie z tymi instrukcjami.

1. Zanotuj kod autoryzacji widoczny na stronie przygotowywania do wysyłki w lokalnym internetowym interfejsie użytkownika urządzenia Data Box po pomyślnym ukończeniu tego kroku.
2. Wyłącz urządzenie i odłącz kable. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Wyślij wiadomość e-mail do zespołu operacji Azure Data Box, korzystając z poniższego szablonu, gdy wszystko będzie gotowe do zwrócenia urządzenia.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box

Gdy urządzenie osiągnie centrum danych platformy Azure, urządzenie Data Box wymazuje dane z jego dysków zgodnie z [zaleceniami dotyczącymi wersji 1 programu NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje na temat tematów, takich jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Wymazywanie danych z urządzenia Data Box

Przejdź do następnego artykułu, aby dowiedzieć się, jak zarządzać urządzenie Data Box.

> [!div class="nextstepaction"]
> [Zarządzanie urządzenie Data Box za pośrednictwem Azure Portal](./data-box-portal-admin.md)

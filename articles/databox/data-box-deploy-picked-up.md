---
title: Samouczek dotyczący odsyłania z powrotem urządzenia Azure Data Box | Microsoft Docs
description: W tym samouczku dowiesz się, jak zwracać urządzenie Azure Data Box. Opisano tu m.in. przygotowanie do wysyłki i odsyłanie urządzenia Data Box, weryfikowanie przekazania danych i wymazywanie danych z urządzenia Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 267094ea6a7295a65b93fb7700c97e4280da341d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539115"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Samouczek: wysyłka zwrotna urządzenia Azure Data Box i weryfikowanie przekazania danych na platformę Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>wysyłka zwrotna urządzenia Data Box i weryfikowanie przekazania danych na platformę Azure

::: zone-end

::: zone target="docs"

W tym samouczku opisano sposób zwrotu urządzenia Azure Data Box i weryfikacji danych przekazanych na platformę Azure.

Ten samouczek zawiera informacje na następujące tematy:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Przygotowanie do wysłania
> * Wysyłanie urządzenia Data Box do firmy Microsoft
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

* Ukończenie tematu [Samouczek: kopiowanie danych na urządzenie Azure Data Box i ich weryfikacja](data-box-deploy-copy-data.md).
* Zadania kopiowania są ukończone, a na stronie **Połącz i skopiuj** nie ma żadnych błędów. Nie można uruchomić **przygotowania do wysłania**, jeśli zadania kopiowania są w toku lub występują błędy na stronie **Połącz i skopiuj**.

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Po zakończeniu kopiowania danych należy przygotować i dostarczyć urządzenie. Gdy urządzenie dotrze do centrum danych platformy Azure, dane zostaną automatycznie przekazane na platformę Azure.

## <a name="prepare-to-ship"></a>Przygotowanie do wysłania

Przed przygotowaniem do wysłania upewnij się, że zadania kopiowania zostały ukończone.

1. Przejdź do strony **Prepare to ship** (Przygotowanie do wysłania) w lokalnym internetowym interfejsie użytkownika i rozpocznij przygotowanie do wysłania.
2. Wyłącz urządzenie za pomocą lokalnego internetowego interfejsu użytkownika. Odłącz przewody od urządzenia.

Następne czynności są zależne od lokalizacji, z której zwracasz urządzenie.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Wysyłka zwrotna urządzenia Data Box

Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a **przygotowanie do wysłania** zakończyło się pomyślnie. W zależności od regionu, w którym jest wysyłane urządzenie, procedura jest inna.

::: zone-end

## <a name="us-canada-europe"></a>[W Stanach Zjednoczonych, Kanadzie, Europie](#tab/in-us-canada-europe)

W przypadku zwrotu urządzenia w Stanach Zjednoczonych, Kanadzie lub Europie wykonaj następujące czynności.

1. Upewnij się, że wyłączono zasilanie urządzenia, a kable zostały odłączone.
2. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Upewnij się, że na wyświetlaczu E-ink jest widoczna etykieta wysyłkowa, i umów się na odbiór przesyłki z przewoźnikiem. Jeśli etykieta jest uszkodzona, nie ma jej lub nie jest widoczna na wyświetlaczu E-ink, skontaktuj się z pomocą techniczną firmy Microsoft. Jeśli otrzymasz takie instrukcje od pomocy technicznej, możesz przejść na stronę **Przegląd > Pobierz etykietę wysyłkową** w witrynie Azure Portal. Pobierz etykietę wysyłkową i dołącz ją do urządzenia. 
4. Jeśli zwracasz urządzenie, zamów odbiór paczki przez firmę UPS. Aby zaplanować odbiór, wykonaj następujące czynności:

    * Zadzwoń do lokalnego oddziału firmy UPS (numer bezpłatny dla danego kraju/regionu).
    * Podczas rozmowy podaj numer śledzenia przesyłki zwrotnej widoczny na wyświetlaczu E-ink lub na wydrukowanej etykiecie. Jeśli nie podasz numeru śledzenia, firma UPS będzie wymagała dodatkowej opłaty podczas odbioru.
    * Jeśli wystąpią jakiekolwiek problemy podczas planowania odbioru lub pojawi się prośba o uiszczenie dodatkowych opłat, skontaktuj się z zespołem operacyjnym usługi Azure Data Box. Wyślij wiadomość e-mail do [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Zamiast planowania odbioru możesz także dostarczyć urządzenie Data Box do najbliższego punktu UPS.
4. Po odebraniu urządzenia Data Box i zeskanowaniu go przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box

Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="australia"></a>[Australia](#tab/in-australia)

W przypadku centrów danych w Australii obowiązuje dodatkowe zabezpieczenie. Zabezpieczenie to polega na powiadamianiu z wyprzedzeniem o wszystkich przesyłkach przychodzących. Wykonaj poniższe kroki w przypadku wysyłki w Australii.

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Upewnij się, że kopiowanie danych na urządzenie zostało ukończone, a **przygotowanie do wysłania** zakończyło się pomyślnie.
3. Wyłącz urządzenie i odłącz kable.
4. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
5. Zarezerwuj odbiór online na stronie [Link do firmy DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box

Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="japan"></a>[Japonia](#tab/in-japan)

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Wyłącz urządzenie i odłącz kable.
3. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
4. Na liście przewozowym wpisz nazwę i adres swojej firmy jako dane nadawcy.
5. Wyślij wiadomość e-mail do firmy Quantium Solutions, korzystając z poniższego szablonu.

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="singapore"></a>[Singapur](#tab/in-singapore)

1. Zachowaj oryginalne pudełko użyte do wysłania urządzenia na potrzeby wysyłki zwrotnej.
2. Zanotuj numer śledzenia (widoczny jako numer referencyjny na stronie **przygotowywania do wysyłki** w lokalnym internetowym interfejsie użytkownika urządzenia Data Box). Numer śledzenia jest dostępny po pomyślnym ukończeniu kroku **Przygotowanie do wysłania**. Pobierz etykietę wysyłkową z tej strony i naklej ją na opakowaniu.
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
   > * Przed 15:00 — odbiór odbędzie się następnego dnia roboczego między 9:00 a 13:00.
   > * Po 15:00 — odbiór odbędzie się następnego dnia roboczego między 14:00 a 18:00.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box

Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="south-africa"></a>[Republika Południowej Afryki](#tab/in-sa)

1. Na potrzeby wysyłki zwrotnej zapakuj urządzenie w oryginalne pudełko.
2. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Zanotuj numer śledzenia (widoczny jako numer referencyjny na stronie **Przygotowanie do wysłania** w lokalnym internetowym interfejsie użytkownika urządzenia Data Box). Numer śledzenia jest dostępny po pomyślnym ukończeniu kroku „Przygotowanie do wysłania”. Pobierz etykietę wysyłkową z tej strony i naklej ją na opakowaniu.
4. Zażądaj kodu zwrotu od zespołu operacyjnego usługi Azure Data Box. Kod zwrotu jest wymagany do dostarczenia paczki z powrotem do centrum danych. Wyślij wiadomość e-mail do [adbops@microsoft.com](mailto:adbops@microsoft.com). Zapisz ten kod na etykiecie wysyłkowej obok adresu zwrotnego, gdzie będzie dobrze widoczny.
5. Zarezerwuj odbiór przez firmę DHL przy użyciu jednej z następujących metod:
 
   * Zarezerwuj odbiór online, przechodząc do witryny internetowej firmy [DHL Express dla regionu Republika Południowej Afryki, do pozycji **Zaplanuj odbiór**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference).
   * Wyślij wiadomość e-mail na adres [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com), korzystając z następującego szablonu:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Dostarcz paczkę do najbliższego punktu obsługującego usługę firmy DHL.

6. W przypadku wystąpienia jakichkolwiek problemów wyślij wiadomość e-mail na adres [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) ze szczegółowymi informacjami o problemach, a w wierszu tematu podaj numer listu przewozowego. Możesz również zadzwonić pod numer +27(0)119213902.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box

Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="hong-kong"></a>[SRA Hongkong](#tab/in-hk)

1. Na potrzeby wysyłki zwrotnej zapakuj urządzenie w oryginalne pudełko.
2. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Zadzwoń na infolinię firmy **Quantium Solutions** pod numer **(852) 2318 1213** w godzinach jej pracy (od 9:00 do 18:00, od poniedziałku do piątku).  
4. Powołaj się na odbiór przesyłki w ramach platformy Microsoft Azure i podaj numer referencyjny oraz numer śledzenia (powyższy kod kreskowy) na zwrotnej etykiecie wysyłkowej w celu zorganizowania odbioru urządzenia.
5. Otrzymasz słowne potwierdzenie terminu odbioru. Jeśli kurier nie zjawi się po odbiór przesyłki, zadzwoń na infolinię firmy Quantium Solutions, aby zaplanować odbiór alternatywny.
6. Po zarezerwowaniu odbioru za pośrednictwem firmy Quantium Solutions udostępnij potwierdzenie działowi [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com), korzystając z następującego szablonu:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

W przypadku wystąpienia jakichkolwiek problemów wyślij wiadomość e-mail do działu Data Box Operations Asia na adres [adbo@microsoft.com](mailto:adbo@microsoft.com) ze szczegółowymi informacjami o problemach, a w wierszu tematu podaj nazwę zadania.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 

::: zone-end

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Samodzielne zarządzanie](#tab/in-selfmanaged)

Jeśli używasz urządzenie Data Box w instytucji rządowych Stanów Zjednoczonych, Japonia, Singapur, Korea, Indie, RPA, Zjednoczone Królestwo, Europa Zachodnia lub Australia, a wybrano opcję wysyłki samozarządzanej podczas tworzenia zamówienia, postępuj zgodnie z tymi instrukcjami.

1. Zanotuj kod autoryzacji, który jest widoczny na stronie **przygotowywania do wysyłki** w lokalnym internetowym interfejsie użytkownika urządzenia Data Box po pomyślnym ukończeniu tego kroku.
2. Wyłącz urządzenie i odłącz kable. Zwiń przewód zasilający dostarczony wraz z urządzeniem i umieść go bezpiecznie z tyłu urządzenia.
3. Gdy zechcesz odesłać urządzenie, wyślij wiadomość e-mail do zespołu operacyjnego usługi Azure Data Box, korzystając z poniższego szablonu.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Wymazywanie danych z urządzenia Data Box
 
Po zakończeniu przekazywania danych na platformę Azure dane z dysków urządzenia Data Box są bezpiecznie wymazywane zgodnie z wytycznymi [NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end


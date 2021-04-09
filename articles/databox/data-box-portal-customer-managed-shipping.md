---
title: Microsoft Azure urządzenie Data Box samozarządzanej wysyłki | Microsoft Docs danych
description: Opisuje przepływ pracy do samozarządzanej wysyłki dla urządzeń Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 07529b18191c71776a9a36edbfa4cfd8ded5af4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524553"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Użyj samozarządzanej wysyłki dla Azure Data Box w Azure Portal

W tym artykule opisano zadania wysyłki samozarządzane, które umożliwiają zamówienie, pobranie i porzucenie urządzenia Azure Data Box. Możesz zarządzać urządzeniem urządzenie Data Box przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Samoobsługowa wysyłka jest dostępna jako opcja w przypadku [zamówienia Azure Data Box](data-box-deploy-ordered.md). Samoobsługowa wysyłka jest dostępna tylko w następujących regionach:

* US Government
* Zjednoczone Królestwo
* Europa Zachodnia
* Japonia
* Singapur
* Korea Południowa
* Indie
* Republika Południowej Afryki
* Australia

## <a name="use-self-managed-shipping"></a>Korzystanie z samodzielnej wysyłki

Po umieszczeniu zamówienia urządzenie Data Box można wybrać opcję wysyłki samozarządzanej.

1. W kolejności Azure Data Box w obszarze **szczegóły kontaktu** wybierz pozycję **+ Dodaj adres wysyłkowy**.
 
   ![Samoobsługowe zarządzanie wysyłką, Dodawanie adresu wysyłkowego](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Po wybraniu typu wysyłki wybierz opcję **wysyłka samodzielna** . Ta opcja jest dostępna tylko wtedy, gdy jesteś w obsługiwanym regionie, zgodnie z opisem w sekcji wymagania wstępne.

3. Po podaniu adresu wysyłkowego musisz sprawdzić jego poprawność i zakończyć zamówienie.

   ![Samozarządzane wysyłanie, weryfikowanie i Dodawanie adresu](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Gdy urządzenie zostanie przygotowane i otrzymasz na niego powiadomienie e-mail, możesz zaplanować odbiór.

   W kolejności Azure Data Box przejdź do pozycji **Przegląd** , a następnie wybierz pozycję **Zaplanuj pobranie**.

   ![Urządzenie Data Box kolejność, Zaplanuj opcję pobrania](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Postępuj zgodnie z instrukcjami podanymi w temacie **Planowanie pobrania dla platformy Azure**.

   Aby można było uzyskać kod autoryzacji, należy wysłać wiadomość e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) w celu zaplanowania odbioru urządzenia z centrum danych w regionie.

   ![Zaplanuj pobranie instrukcji platformy Azure](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Po zaplanowaniu odbioru urządzenia można wyświetlić kod autoryzacji urządzenia w okienku **Planowanie pobrania dla platformy Azure** .

   ![Wyświetlanie kodu autoryzacji urządzenia](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Zanotuj ten **kod autoryzacji**. Zgodnie z wymaganiami dotyczącymi zabezpieczeń, podczas planowania pobrania należy przedstawić nazwę osoby, która dotarła do pobrania.

   Należy również podać szczegółowe informacje o tym, kto zostanie przejściu do centrum danych w celu pobrania. Użytkownik lub punkt kontaktu musi posiadać zweryfikowany identyfikator fotograficzny dla instytucji rządowych, który zostanie sprawdzony w centrum danych.

   Ponadto osoba, która wybiera urządzenie, musi mieć **kod autoryzacji**. Kod autoryzacji jest weryfikowany w czasie odbioru centrum danych.

7. Zamówienie jest automatycznie przenoszone do stanu **pobrania** , gdy urządzenie zostało pobrane z centrum danych.

    ![Zamówienie w stanie pobrania](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Po wybraniu urządzenia skopiuj dane do urządzenie Data Box w swojej lokacji. Po zakończeniu kopiowania danych możesz przygotować się do wysłania urządzenie Data Box. Aby uzyskać więcej informacji, zobacz [przygotowanie do wysłania](data-box-deploy-picked-up.md#prepare-to-ship).

   Krok **przygotowanie do wysłania** musi zakończyć się bez błędów krytycznych. W przeciwnym razie musisz ponownie uruchomić ten krok po wprowadzeniu niezbędnych poprawek. Po pomyślnym ukończeniu kroku **przygotowanie do wysłania** można wyświetlić kod autoryzacji dla listy rozwijanej dla lokalnego interfejsu użytkownika urządzenia.

   > [!NOTE]
   > Nie udostępniaj kodu autoryzacji za pośrednictwem poczty e-mail. Jest to możliwe tylko do zweryfikowania w centrum danych podczas upuszczania.

9. Jeśli otrzymałeś termin do odwinięcia, zamówienie powinno być **gotowe do odebrania** w Azure Portal stanu centrum danych platformy Azure. Postępuj zgodnie z instrukcjami w obszarze **Zaplanuj** , aby zwrócić urządzenie.

   ![Instrukcje dotyczące usuwania urządzeń](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Po zweryfikowaniu identyfikatora i kodu autoryzacji i usunięciu urządzenia w centrum danych należy **otrzymać** stan zamówienia.

    ![Zamówienie o stanie otrzymane](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Po odebraniu urządzenia kopia danych będzie kontynuowana. Po zakończeniu kopiowania kolejność jest ukończona.

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z usługą Azure Data Box](data-box-quickstart-portal.md)

---
title: Microsoft Azure Data Box Disk samozarządzanej wysyłki | Microsoft Docs danych
description: Opisuje przepływ pracy do samozarządzanej wysyłki dla urządzeń Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/20/2020
ms.author: alkohli
ms.openlocfilehash: c4b3479e5728c32e66bc40f950bc948bf61dce42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575167"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Użyj samozarządzanej wysyłki dla Azure Data Box Disk w Azure Portal

W tym artykule opisano zadania wysyłki samozarządzane w celu uporządkowania, pobierania i wyrzucania Azure Data Box Disk. Data Box Disk można zarządzać przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Samoobsługowa wysyłka jest dostępna jako opcja w przypadku [zamówienia Azure Data Box Disk](data-box-disk-deploy-ordered.md). Samoobsługowa wysyłka jest dostępna tylko w następujących regionach:

* US Government
* Europa Zachodnia
* Japonia
* Singapur
* Korea Południowa
* Republika Południowej Afryki
* Indie (wersja zapoznawcza)

## <a name="use-self-managed-shipping"></a>Korzystanie z samodzielnej wysyłki

Po umieszczeniu zamówienia Data Box Disk można wybrać opcję samodzielna wysyłka.

1. W kolejności Azure Data Box Disk w obszarze **szczegóły kontaktu**wybierz pozycję **+ Dodaj adres wysyłkowy**.

   ![Zrzut ekranu przedstawiający kreatora zamówień z etapem szczegóły kontaktu z opcją Dodaj adres wysyłkowy.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Po wybraniu opcji Typ wysyłki wybierz opcję **wysyłka samozarządzana** . Ta opcja jest dostępna tylko wtedy, gdy jesteś w obsługiwanym regionie, zgodnie z opisem w sekcji wymagania wstępne.

3. Po podaniu adresu wysyłkowego musisz sprawdzić jego poprawność i zakończyć zamówienie.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie adresu wysyłkowego z opcjami Wyślij przy użyciu i Dodaj adres wysyłkowy.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Gdy urządzenie zostanie przygotowane i otrzymasz powiadomienie e-mail, możesz zaplanować odbiór. W kolejności Azure Data Box Disk przejdź do pozycji **Przegląd** , a następnie wybierz pozycję **Zaplanuj pobranie**.

   ![Porządkowanie urządzenia urządzenie Data Box do pobrania](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Postępuj zgodnie z instrukcjami podanymi w temacie **Planowanie pobrania dla platformy Azure**. Aby można było uzyskać kod autoryzacji, należy wysłać wiadomość e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) w celu zaplanowania odbioru urządzenia z centrum danych w regionie.

   ![Zamówienie kuriera](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Po zaplanowaniu odbioru urządzenia będzie można wyświetlić kod autoryzacji w ramach  **zaplanowanego pobrania platformy Azure**.

   ![Zrzut ekranu przedstawiający okno dialogowe Zaplanuj pobieranie na platformę Azure z kodem autoryzacji dla odbioru pola tekstowego o nazwie.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Zanotuj ten **kod autoryzacji**.

   Zgodnie z wymaganiami dotyczącymi zabezpieczeń, w chwili pobrania harmonogramu, konieczne jest zaprezentowanie nazwiska osoby, która będzie docierać do pobrania.

   Należy również podać szczegółowe informacje o tym, kto zostanie przejściu do centrum danych w celu pobrania. Użytkownik lub punkt kontaktu musi posiadać zweryfikowany identyfikator fotograficzny dla instytucji rządowych, który zostanie sprawdzony w centrum danych.

   Ponadto osoba, która wybiera urządzenie, musi mieć **kod autoryzacji**. Kod autoryzacji jest unikatowy dla odbioru lub odrzucania i jest sprawdzany w centrum danych.

7. Zamówienie jest automatycznie przenoszone do stanu **pobrania** , gdy urządzenie zostało pobrane z centrum danych.

   ![Pobrane](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Po wybraniu urządzenia można skopiować dane do Data Box Disk w danej lokacji. Po zakończeniu kopiowania danych możesz przygotować się do wysłania Data Box Disk.

   Po zakończeniu kopiowania danych należy skontaktować się z operacjami w celu zaplanowania terminu odrzucania. Należy udostępnić szczegóły osoby przechodzącej do centrum danych w celu usunięcia dysków. Centrum danych będzie również wymagało zweryfikowania kodu autoryzacji w momencie jego rozwinięcia. Kod autoryzacji do odrzucania będzie dostępny w Azure Portal w obszarze **Porzuć harmonogram**.

   > [!NOTE]
   > Nie udostępniaj kodu autoryzacji za pośrednictwem poczty e-mail. Jest to możliwe tylko do zweryfikowania w centrum danych podczas upuszczania.

9. Jeśli otrzymasz termin, aby porzucić zamówienie, powinno teraz znajdować się w stanie **gotowy do odebrania w centrum danych platformy Azure** w Azure Portal.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie adresu wysyłkowego z opcją Wyślij przy użyciu opcji wychodzące i Dodaj adres wysyłkowy.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Po zweryfikowaniu identyfikatora i kodu autoryzacji i usunięciu urządzenia w centrum danych należy **otrzymać**stan zamówienia.

    ![Odebrano ukończone](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Po odebraniu urządzenia kopia danych będzie kontynuowana. Po zakończeniu kopiowania kolejność jest ukończona.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wdrażanie Azure Data Box Disk przy użyciu Azure Portal](data-box-disk-quickstart-portal.md)

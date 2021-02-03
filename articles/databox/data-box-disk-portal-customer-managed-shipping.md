---
title: Microsoft Azure Data Box Disk samozarządzanej wysyłki | Microsoft Docs danych
description: Opisuje przepływ pracy do samozarządzanej wysyłki dla urządzeń Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526334"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>Użyj samozarządzanej wysyłki dla Azure Data Box Disk w Azure Portal

W tym artykule opisano zadania wysyłki samozarządzane w celu uporządkowania, pobierania i wyrzucania Azure Data Box Disk. Data Box Disk można zarządzać przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Samoobsługowa wysyłka jest dostępna jako opcja w przypadku [zamówienia Azure Data Box Disk](data-box-disk-deploy-ordered.md). Samoobsługowa wysyłka jest dostępna tylko w następujących regionach:

* US Government
* Zjednoczone Królestwo
* Europa Zachodnia
* Australia
* Japonia
* Singapur
* Korea Południowa
* Republika Południowej Afryki
* Indie (wersja zapoznawcza)

## <a name="use-self-managed-shipping"></a>Korzystanie z samodzielnej wysyłki

Po umieszczeniu zamówienia Data Box Disk można wybrać opcję samodzielna wysyłka.

1. W kolejności Azure Data Box Disk w obszarze **szczegóły kontaktu** wybierz pozycję **+ Dodaj adres wysyłkowy**.

   ![Zrzut ekranu przedstawiający kreatora zamówień z etapem szczegóły kontaktu z opcją Dodaj adres wysyłkowy.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Po wybraniu opcji Typ wysyłki wybierz opcję **wysyłka samozarządzana** . Ta opcja jest dostępna tylko wtedy, gdy jesteś w obsługiwanym regionie, zgodnie z opisem w sekcji wymagania wstępne.

3. Po podaniu adresu wysyłkowego musisz sprawdzić jego poprawność i zakończyć zamówienie.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie adresu wysyłkowego z opcjami Wyślij przy użyciu i Dodaj adres wysyłkowy.](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Gdy urządzenie zostanie przygotowane i otrzymasz powiadomienie e-mail, możesz zaplanować odbiór. W kolejności Azure Data Box Disk przejdź do pozycji **Przegląd** , a następnie wybierz pozycję **Zaplanuj pobranie**.

   ![Porządkowanie urządzenia urządzenie Data Box do pobrania](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. Postępuj zgodnie z instrukcjami podanymi w temacie **Planowanie pobrania dla platformy Azure**. Aby można było uzyskać kod autoryzacji, należy wysłać wiadomość e-mail [adbops@microsoft.com](mailto:adbops@microsoft.com) w celu zaplanowania odbioru urządzenia z centrum danych w regionie.

   ![Zamówienie kuriera](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. Po zaplanowaniu odbioru urządzenia można wyświetlić swój kod autoryzacji w temacie **Planowanie pobrania na platformie Azure**.

   ![Zrzut ekranu przedstawiający okno dialogowe Zaplanuj pobieranie na platformę Azure z kodem autoryzacji dla odbioru pola tekstowego o nazwie.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   Zanotuj ten kod autoryzacji.

   Zgodnie z wymaganiami dotyczącymi zabezpieczeń, podczas planowania pobrania należy zaprezentować nazwę osoby, która będzie mogła uzyskać do pobrania.

   Należy również podać szczegółowe informacje o tym, kto przejdzie do centrum danych w celu pobrania. Użytkownik lub punkt kontaktu musi posiadać identyfikator zdjęć zatwierdzony przez rząd, który zostanie sprawdzony w centrum danych.

   Osoba, która wybiera urządzenie, musi również mieć kod autoryzacji. Kod autoryzacji jest unikatowy dla pobrania lub odrzucania i jest sprawdzany w centrum danych.

7. Zamówienie jest automatycznie przenoszone do stanu **pobrania** po pobraniu urządzenia z centrum danych.

   ![Pobrane](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. Po wybraniu urządzenia można skopiować dane do Data Box Disk w danej lokacji. Po zakończeniu kopiowania danych możesz przygotować się do wysłania Data Box Disk.

   Po zakończeniu kopiowania danych skontaktuj się z operacji w celu zaplanowania terminu dla elementu docelowego. Musisz udostępnić szczegóły osoby przechodzącej do centrum danych w celu usunięcia dysków. Centrum danych będzie również wymagało zweryfikowania kodu autoryzacji w momencie jego rozwinięcia. Kod autoryzacji do odrzucania można znaleźć w Azure Portal w obszarze nie **wyrzucaj harmonogramu**.

   > [!NOTE]
   > Nie udostępniaj kodu autoryzacji za pośrednictwem poczty e-mail. Jest to możliwe tylko do zweryfikowania w centrum danych podczas upuszczania.

9. Po otrzymaniu terminu do odwinięcia, zamówienie powinno być w stanie **gotowe do odebrania w centrum danych platformy Azure** w Azure Portal.

   ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie adresu wysyłkowego z opcją Wyślij przy użyciu opcji wychodzące i Dodaj adres wysyłkowy.](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. Po zweryfikowaniu identyfikatora i kodu autoryzacji i usunięciu urządzenia w centrum danych należy **otrzymać** stan zamówienia.

    ![Odebrano ukończone](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. Po odebraniu urządzenia kopia danych będzie kontynuowana. Po zakończeniu kopiowania kolejność jest ukończona.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: wdrażanie usługi Azure Data Box Disk za pomocą witryny Azure Portal](data-box-disk-quickstart-portal.md)

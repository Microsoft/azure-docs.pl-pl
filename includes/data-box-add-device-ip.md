---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553228"
---
1. Zaloguj się do urządzenia Data Box. Upewnij się, że jest odblokowane.

    ![Zrzut ekranu przedstawia pulpit nawigacyjny z urządzeniem wyświetlanym jako odblokowany.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Przejdź do pozycji **Ustaw interfejsy sieciowe**. Zanotuj adres IP urządzenia dla interfejsu sieciowego używanego do łączenia z klientem.

    ![Zrzut ekranu przedstawia ustawienia sieci, w których można zobaczyć adres I.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Przejdź do pozycji **Połącz i Kopiuj** , a następnie kliknij pozycję **rest**.

    ![Zrzut ekranu przedstawia okienko łączenie i kopiowanie, w którym można wybrać opcję REST jako ustawienie dostępu.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. W oknie dialogowym **Uzyskaj dostęp do konta magazynu i przekaż dane** skopiuj **Punkt końcowy usługi blob**.

    ![Zrzut ekranu przedstawia okno dialogowe dostęp do konta magazynu i przekazywanie danych, w którym można skopiować punkt końcowy usługi BLOB Service.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Uruchom program **Notatnik** jako administrator, a następnie otwórz plik **hosts** znajdujący się w folderze `C:\Windows\System32\Drivers\etc`.
6. Dodaj do pliku **hosts** następujący wpis: `<device IP address> <Blob service endpoint>`
7. Powinno to wyglądać podobnie jak na poniższej ilustracji. Zapisz plik **hosts**.

    ![Zrzut ekranu przedstawia dokument notatnika z dodanym adresem i punktem końcowym usługi BLOB Service.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)

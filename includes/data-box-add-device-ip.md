---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183896"
---
1. Zaloguj się do urządzenia Data Box. Upewnij się, że jest odblokowane.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Przejdź do pozycji **Ustaw interfejsy sieciowe**. Zanotuj adres IP urządzenia dla interfejsu sieciowego używanego do łączenia z klientem.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Przejdź do pozycji **Połącz i Kopiuj** , a następnie kliknij pozycję **rest**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. W oknie dialogowym **Uzyskaj dostęp do konta magazynu i przekaż dane** skopiuj **Punkt końcowy usługi blob**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Uruchom program **Notatnik** jako administrator, a następnie otwórz plik **hosts** znajdujący się w folderze `C:\Windows\System32\Drivers\etc`.
6. Dodaj do pliku **hosts** następujący wpis: `<device IP address> <Blob service endpoint>`
7. Powinno to wyglądać podobnie jak na poniższej ilustracji. Zapisz plik **hosts**.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)

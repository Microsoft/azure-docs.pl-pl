---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 6b716640bc8117ccc2d473b337ee3e555e8d9b0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375990"
---
1. Zaloguj się do urządzenia Data Box. Upewnij się, że jest odblokowane.

    ![Pulpit nawigacyjny urządzenia Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Przejdź do pozycji **Ustaw interfejsy sieciowe**. Zanotuj adres IP urządzenia dla interfejsu sieciowego używanego do łączenia z klientem.

    ![Pulpit nawigacyjny urządzenie Data Box 2](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Przejdź do pozycji **Połącz i Kopiuj** , a następnie kliknij pozycję **rest**.

    ![Pulpit nawigacyjny urządzenie Data Box 3](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. W oknie dialogowym **Uzyskaj dostęp do konta magazynu i przekaż dane** skopiuj **Punkt końcowy usługi blob**.

    ![Pulpit nawigacyjny urządzenie Data Box 4](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Uruchom program **Notatnik** jako administrator, a następnie otwórz plik **hosts** znajdujący się w folderze `C:\Windows\System32\Drivers\etc`.
6. Dodaj do pliku **hosts** następujący wpis: `<device IP address> <Blob service endpoint>`
7. Powinno to wyglądać podobnie jak na poniższej ilustracji. Zapisz plik **hosts**.

    ![Pulpit nawigacyjny urządzenie Data Box 5](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)

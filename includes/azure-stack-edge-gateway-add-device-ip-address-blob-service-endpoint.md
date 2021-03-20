---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185547"
---
1. Przejdź do lokalnego interfejsu użytkownika sieci Web urządzenia i zaloguj się na swoim urządzeniu. Upewnij się, że urządzenie jest odblokowane.

2. Przejdź do strony **Ustawienia sieci** . Zanotuj adres IP urządzenia dla interfejsu sieciowego używanego do łączenia z klientem.

3. Jeśli pracujesz ze zdalnym klientem systemu Windows, uruchom program **Notepad** jako administrator, a następnie otwórz plik hosts znajdujący się w lokalizacji `C:\Windows\System32\Drivers\etc` .

4. Dodaj do pliku hosts następujący wpis: `<Device IP address> <Blob service endpoint>`

    Uzyskano punkt końcowy usługi BLOB z konta magazynu Edge utworzonego w Azure Portal. Będziesz używać tylko sufiksu punktu końcowego usługi BLOB.

    Powinno to wyglądać podobnie jak na poniższej ilustracji. Zapisz plik `hosts`.

    ![Modyfikuj plik hosts w kliencie systemu Windows](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)
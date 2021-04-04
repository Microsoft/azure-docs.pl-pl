---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995436"
---
#### <a name="to-connect-through-the-serial-console"></a>Aby nawiązać połączenie za pośrednictwem konsoli szeregowej
1. Podłącz kabel szeregowy do urządzenia (bezpośrednio lub za pośrednictwem adaptera szeregowego USB).
2. Otwórz **Panel sterowania**, a następnie otwórz okno **Menedżer urządzeń**.
3. Zidentyfikuj port COM w sposób pokazany na poniższym rysunku.
   
     ![Łączenie za pośrednictwem konsoli szeregowej](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Uruchom program PuTTY. 
5. W okienku po prawej stronie zmień wartość pola **Typ połączenia** na **Szeregowe**.
6. W okienku po prawej stronie wpisz odpowiedni port COM. Upewnij się, że parametry konfiguracji portu zostały ustawione w następujący sposób:
   
   * Szybkość: 115 200
   * Bity danych: 8
   * Bity stopu: 1
   * Parzystość: Brak
   * Sterowanie przepływem: Brak
     
     Te ustawienia zostały pokazane na poniższej ilustracji.
     
     ![Ustawienia programu PuTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Jeśli domyślne ustawienie sterowania przepływem nie działa, spróbuj ustawić je na XON/XOFF.
     > 
     > 
7. Kliknij pozycję **Otwórz**, aby rozpocząć sesję szeregową.


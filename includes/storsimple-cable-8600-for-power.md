---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183267"
---
#### <a name="to-cable-your-device-for-power"></a>Aby podłączyć urządzenie do urządzenia
> [!NOTE]
> Obie obudowy na urządzeniu StorSimple obejmują nadmiarowe PCMs. W przypadku każdej obudowy PCMs musi być zainstalowany i podłączony do różnych źródeł, aby zapewnić wysoką dostępność.
> 
> 

1. Upewnij się, że przełączniki zasilające na wszystkich PCMsach są wyłączone.
2. W obudowie podstawowej Podłącz przewody zasilające do obu PCMs. Przewody zasilające są oznaczone kolorem czerwonym na diagramie okablowania prądu.
3. Upewnij się, że dwie PCMs na podstawowej obudowie używają oddzielnych źródeł.
4. Podłącz przewody zasilające do potęgi jednostek rozkładu stojaków, jak pokazano na diagramie okablowania.
5. Powtórz kroki od 2 do 4 dla obudowy EBOD.
6. Włącz obudowę EBOD, przełączając przełącznik zasilany z poszczególnych PCM do pozycji włączone.
7. Sprawdź, czy obudowa EBOD jest włączona, sprawdzając, czy są włączone zielone diody LED z tyłu kontrolera EBOD.
8. Włącz obudowę podstawową, przełączając każdy przełącznik PCM do pozycji włączone.
9. Sprawdź, czy system jest włączony, upewniając się, że diody LED kontrolera urządzenia zostały włączone.
10. Upewnij się, że połączenie między kontrolerem EBOD i kontrolerem urządzenia jest aktywne, sprawdzając, czy cztery diody LED obok portu sygnatury dostępu współdzielonego na kontrolerze EBOD są zielone.
    
    > [!IMPORTANT]
    > Aby zapewnić wysoką dostępność systemu, zalecamy ścisłe przestrzeganie schematu okablowania sieciowego pokazanego na poniższym diagramie.
    > 
    > 
    
    ![Podłączanie kabla do urządzenia 4U](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Okablowanie sieciowe**
    
    | Etykieta | Opis |
    |:--- |:--- |
    | 1 |Obudowa podstawowa |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Kontroler 0 |
    | 5 |Kontroler 1 |
    | 6 |Kontroler EBOD 0 |
    | 7 |Kontroler EBOD 1 |
    | 8 |Obudowa EBOD |
    | 9 |Jednostki |


---
title: Zastępowanie kontrolera StorSimple 8600 EBOD | Microsoft Docs
description: Wyjaśnia sposób usuwania i zastępowania jednego lub obu kontrolerów EBOD na urządzeniu z systemem StorSimple 8600.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: bdafb4288bf818928aba43d09f15d4dd75092b24
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014843"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Zastępowanie kontrolera EBOD na urządzeniu StorSimple

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak zastąpić uszkodzony moduł kontrolera EBOD na urządzeniu Microsoft Azure StorSimple. Aby zastąpić moduł EBOD Controller, należy:

* Usuń wadliwy kontroler EBOD
* Zainstaluj nowy kontroler EBOD

Przed rozpoczęciem należy wziąć pod uwagę następujące informacje:

* Puste moduły EBOD muszą zostać wstawione do wszystkich nieużywanych gniazd. Obudowa nie zostanie poprawnie chłodna, jeśli gniazdo zostanie otwarte.
* Kontroler EBOD jest wymienialny i można go usunąć. Nie usuwaj modułu zakończonego niepowodzeniem, dopóki nie zostanie zamieniony. Po zainicjowaniu procesu wymiany należy go zakończyć w ciągu 10 minut.

> [!IMPORTANT]
> Przed podjęciem próby usunięcia lub zastąpienia dowolnego składnika StorSimple upewnij się, że zawarto przegląd [Konwencji ikon bezpieczeństwa](storsimple-8000-safety.md#safety-icon-conventions) i innych [środków bezpieczeństwa](storsimple-8000-safety.md).

## <a name="remove-an-ebod-controller"></a>Usuwanie kontrolera EBOD
Przed zastępowaniem modułu niepowodzenia kontrolera EBOD na urządzeniu StorSimple upewnij się, że inny moduł kontrolera EBOD jest aktywny i uruchomiony. Poniższa procedura i tabela wyjaśniają, jak usunąć moduł kontrolera EBOD.

#### <a name="to-remove-an-ebod-module"></a>Aby usunąć moduł EBOD
1. Otwórz witrynę Azure Portal.
2. Przejdź do urządzenia i przejdź do pozycji **Ustawienia**  >  **kondycja sprzętu** i sprawdź, czy stan diody LED aktywnego modułu kontrolera EBOD jest zielony, a dioda LED dla niepowodzenia modułu kontrolera EBOD to Red.
3. Znajdź moduł niepowodzenia kontrolera EBOD w tylnej części urządzenia.
4. Usuń kable, które łączą moduł kontrolera EBOD z kontrolerem przed przełączeniem modułu EBOD do systemu.
5. Zanotuj dokładny port SAS modułu EBOD Controller, który został podłączony do kontrolera. Po zazastąpieniu modułu EBOD należy przywrócić system do tej konfiguracji.
   
   > [!NOTE]
   > Zwykle jest to port A, który jest oznaczony jako **host w** programie na poniższym diagramie.
   
    ![Plan dla kontrolera EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Rysunek 1** Tył modułu EBOD
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Dioda LED awarii |
   | 2 |Dioda LED |
   | 3 |Łączniki SAS |
   | 4 |Diody LED SAS |
   | 5 |Porty szeregowe tylko do użytku fabrycznego |
   | 6 |Port A (host w) |
   | 7 |Port B (host out) |
   | 8 |Port C (tylko użycie fabryki) |

## <a name="install-a-new-ebod-controller"></a>Zainstaluj nowy kontroler EBOD
Poniższa procedura i tabela wyjaśniają, jak zainstalować moduł kontrolera EBOD na urządzeniu StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Aby zainstalować kontroler EBOD
1. Sprawdź, czy urządzenie EBOD jest uszkodzone, szczególnie w odniesieniu do łącznika interfejsu. Nie należy instalować nowego kontrolera EBOD w przypadku wygiętania jakichkolwiek numerów PIN.
2. Za pomocą zamków w pozycji otwartej przesuń moduł do obudowy do momentu, aż zamkzy zaangażują się.
   
    ![Instalowanie kontrolera EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Rysunek 2**  Instalowanie modułu kontrolera EBOD
3. Zamknij zatrzaśnięcie. Należy usłyszeć kliknięcie, gdy zachodzi zamknięcie.
   
    ![Zwalnianie zamka EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Rysunek 3**  Zamykanie zamka modułu EBOD
4. Podłącz kable ponownie. Użyj dokładnej konfiguracji, która była obecna przed zastąpieniem. Aby uzyskać szczegółowe informacje na temat sposobu łączenia kabli, zobacz poniższy diagram i tabela.
   
    ![Podłączanie kabla do urządzenia 4U](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Rysunek 4**. Ponowne łączenie kabli
   
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
   | 9 |Jednostki dystrybucji |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).


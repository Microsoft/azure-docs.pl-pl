---
title: Zastępowanie dysku na urządzeniu z serii StorSimple 8000 | Microsoft Docs
description: Wyjaśnia, jak zastąpić dysk w podstawowej obudowie StorSimple lub w obudowie EBOD.
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
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014877"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Wymiana dysku w urządzeniu StorSimple z serii 8000

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak usunąć i zastąpić wadliwe lub uszkodzone dyski twarde na urządzeniu Microsoft Azure StorSimple. Aby zastąpić dysk, należy:

* Odłączanie blokady przed naruszeniem
* Usuń dysk
* Zainstaluj zamienny dysk

> [!IMPORTANT]
> Przed usunięciem i zastępowaniem stacji dysków zapoznaj się z informacjami o bezpieczeństwie [zastępowanie składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Odłączanie blokady przed naruszeniem
W tej procedurze wyjaśniono, w jaki sposób można włączać lub wyStorSimpleać blokady przed naruszeniem na urządzeniu z systemem, gdy zastąpisz stacje dysków. Blokady przed naruszeniem są instalowane w uchwytach nośnej stacji, a dostęp do nich uzyskuje się za pomocą małego szczeliny w sekcji zatrzaśnięcie uchwytu. Dyski są dostarczane z blokadami ustawionymi na położenie zablokowane.

#### <a name="to-unlock-the-antitamper-lock"></a>Aby odblokować blokadę przed naruszeniem
1. Starannie Wstaw klucz blokady ("Tamperproof" T10, który został dostarczony przez firmę Microsoft) do otworu w uchwycie i w jego gnieździe. 
   
   Jeśli blokada naruszy jest aktywowana, czerwony wskaźnik jest widoczny w szczelinie.
  
    ![Zablokowana stacja dysków](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Rysunek 1** Blokada chroniąca przed naruszeniem
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Otwór wskaźnika |
   | 2 |Blokada antynaruszenie |
2. Obróć klucz w kierunku przeciwnie do czasu, aż czerwony wskaźnik nie będzie widoczny w szczelinie nad kluczem.
3. Usuń klucz.
   
    ![Odblokowano dysk](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Rysunek 2** Odblokowano dysk
4. Dysk można teraz usunąć.

Wykonaj kroki opisane w sekcji wstecz, aby włączyć blokadę.

## <a name="remove-the-disk-drive"></a>Usuń dysk
Urządzenie StorSimple obsługuje konfigurację funkcji miejsca do magazynowania w macierzy RAID 10. Oznacza to, że może działać normalnie z jednym uszkodzonym dyskiem, dyskiem SSD lub dyskiem twardym.

> [!IMPORTANT]
> * Jeśli system ma więcej niż jeden dysk, który uległ awarii, nie należy usuwać więcej niż jednego SSD lub dysku twardego z systemu w dowolnym momencie. Wykonanie tej operacji może spowodować utratę danych.
> * Upewnij się, że zamienny dysk SSD został umieszczony w gnieździe, które wcześniej zawierało dysk SSD. Podobnie Umieść zamienny dysk twardy w gnieździe, które wcześniej zawierało dysk twardy.
> * W Azure Portal gniazda są numerowane od 0 do 11. W związku z tym, jeśli w portalu widać, że dysk w gnieździe 2 zakończył się niepowodzeniem, na urządzeniu poszukaj uszkodzonego dysku z lewej strony.
> 
> 

Dyski można usuwać i wymieniać, gdy system działa.

#### <a name="to-remove-a-drive"></a>Aby usunąć dysk
1. Aby zidentyfikować uszkodzony dysk, w Azure Portal przejdź do **ustawień urządzenia > kondycja sprzętu**. Ponieważ dysk może kończyć się niepowodzeniem w obudowie podstawowej i/lub w obudowie EBOD (Jeśli korzystasz z modelu 8600), sprawdź stan dysków w obszarze **składniki udostępnione** i w obszarze **EBOD składniki współużytkowane**. Uszkodzony dysk w jednej z obudów zostanie wyświetlony z czerwonym stanem.
2. Znajdź dyski z przodu podstawowej obudowy lub obudowy EBOD. 
3. Jeśli dysk jest odblokowany, przejdź do następnego kroku. Jeśli dysk jest zablokowany, Odblokuj go, wykonując procedurę z odłączania [blokady przed naruszeniem](#disengage-the-antitamper-lock).
4. Naciśnij czarny zamka w module nośnej dysków i wyciągnij obsługę operatora napędowego i z przodu obudowy.
   
    ![Zwalnianie dojścia dysku](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Rysunek 3** Zwalnianie dojścia dysku
5. Gdy dojście do nośnej stacji dysków jest w pełni rozszerzone, przesuń nośnik dysku z obudowy. 
   
    ![Przesuwanie dysku z dysku](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Rysunek 4** Przesuwanie stacji dysków z nośnej

## <a name="install-the-replacement-disk-drive"></a>Zainstaluj zamienny dysk
Po awarii dysku na urządzeniu StorSimple i usunięciu go, wykonaj poniższą procedurę, aby zastąpić go nowym dyskiem.

#### <a name="to-insert-a-drive"></a>Aby wstawić dysk
1. Upewnij się, że dojście do nośnej stacji jest w pełni rozszerzone, jak pokazano na poniższej ilustracji.
   
    ![Stacja dysków z rozszerzoną obsługą](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Rysunek 5** Dysk z rozszerzonym dojściem
2. Przesuń nośnik napędu do obudowy.
   
    ![Przesuwanie dysku do nośnika stacji dysków](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Rysunek 6**  .  Przesuwanie nośnej stacji dysków do obudowy
3. Po włożeniu nośnika stacji Zamknij uchwyt nośnej stacji, gdy kontynuuje wypychanie nośnej stacji dysków do obudowy, aż do momentu przyłączenia uchwytu nośnego do zablokowanego położenia.
4. Użyj klawisza blokady dostarczonego przez firmę Microsoft (Tamperproof TORX), aby zabezpieczyć dojście do miejsca, przełączając blokadę w prawo.
5. Sprawdź, czy zastąpienie zakończyło się pomyślnie, a dysk działa. Uzyskaj dostęp do Azure Portal i przejdź do **ustawień urządzenia**  >  **kondycja sprzętu**. W obszarze **składniki udostępnione** lub **EBOD udostępnione składniki** stan dysku powinien być zielony, co oznacza, że jest w dobrej kondycji.

   
   > [!NOTE]
   > Zmiana stanu dysku na zielony po zastąpieniu może potrwać kilka godzin.
  
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).


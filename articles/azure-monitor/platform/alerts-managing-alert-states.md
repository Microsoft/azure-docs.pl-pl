---
title: Zarządzanie stanami alertów i grup inteligentnych
description: Zarządzanie Stanami alertu i wystąpieniami grup inteligentnych
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: b91c3d59a5a37457d67fdfbc646334fccb331df5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104805"
---
# <a name="manage-alert-and-smart-group-states"></a>Zarządzanie stanami alertów i grup inteligentnych

Alerty w Azure Monitor mają teraz [stan alertu i stan monitora](./alerts-overview.md) , a podobnie grupy inteligentne mają [stan grupy inteligentnej](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json). Zmiany stanu są teraz przechwytywane w historii skojarzonej z odpowiednim alertem lub grupą inteligentną. Ten artykuł przeprowadzi Cię przez proces zmiany stanu dla alertu i grupy inteligentnej.

## <a name="change-the-state-of-an-alert"></a>Zmiana stanu alertu

1. Stan alertu można zmienić na następujące różne sposoby: 
    * Na stronie wszystkie alerty kliknij pole wyboru obok alertów, które chcesz zmienić, a następnie kliknij przycisk Zmień stan.   
    ![Zrzut ekranu przedstawia stronę wszystkie alerty z wybranym stanem zmiany.](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stronie Szczegóły alertu dla określonego wystąpienia alertu można kliknąć pozycję Zmień stan.   
    ![Zrzut ekranu przedstawia stronę szczegóły alertu z wybranym stanem alertu Zmień.](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na stronie Szczegóły alertu dla określonego wystąpienia alertu w okienku grupa inteligentna kliknij pole wyboru obok alertów, które chcesz    
    ![Zrzut ekranu przedstawia stronę szczegóły alertu dla alertu pulsu z niektórymi wystąpieniami znacznikami wyboru.](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stronie Szczegóły grupy inteligentnej, na liście alertów członków, kliknij pole wyboru obok alertów, które chcesz zmienić, a następnie kliknij pozycję Zmień Stateto zmienić stan i kliknij przycisk Zmień stan.   
    ![Zrzut ekranu przedstawia stronę szczegółów grupy inteligentnej, na której można wybrać alerty, dla których ma zostać zmieniona zmiana stanu.](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Po kliknięciu przycisku Zmień stan zostanie otwarte okno podręczne, które umożliwi wybranie stanu (nowy/potwierdzony/zamknięty) i w razie potrzeby wprowadź komentarz.   
![Zrzut ekranu przedstawia okno dialogowe alertu o zmianie szczegółów.](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Po wykonaniu tej czynności zmiana stanu zostanie zarejestrowana w historii odpowiedniego alertu. Można to wyświetlić, otwierając odpowiednią stronę szczegółów i sprawdzając sekcję Historia.    
![Zrzut ekranu przedstawia historię zmian stanu.](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Zmiana stanu grupy inteligentnej
1. Stan grupy inteligentnej można zmienić na następujące różne sposoby:
    1. Na stronie Lista grup inteligentnych możesz kliknąć pole wyboru obok grup inteligentnych, które chcesz zmienić, a następnie kliknij przycisk Zmień stan.  
    ![Zrzut ekranu przedstawia stronę zmiana stanu dla grup inteligentnych.](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stronie Szczegóły grupy inteligentnej można kliknąć pozycję Zmień stan.        
    ![Zrzut ekranu przedstawia stronę szczegółów grupy inteligentnej z wybranym stanem Zmień grupę inteligentną.](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Po kliknięciu przycisku Zmień stan zostanie otwarte okno podręczne, które umożliwi wybranie stanu (nowy/potwierdzony/zamknięty) i w razie potrzeby wprowadź komentarz. 
![Zrzut ekranu przedstawia okno dialogowe zmiana stanu dla grupy inteligentnej.](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Zmiana stanu grupy inteligentnej nie powoduje zmiany stanu alertów poszczególnych członków.

1. Po wykonaniu tej czynności zmiana stanu zostanie zarejestrowana w historii odpowiedniej grupy inteligentnej. Można to wyświetlić, otwierając odpowiednią stronę szczegółów i sprawdzając sekcję Historia.     
![Zrzut ekranu przedstawia historię zmian dla grupy inteligentnej.](./media/alerts-managing-alert-states/state-sg-history.jpg)
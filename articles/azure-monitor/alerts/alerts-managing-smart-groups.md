---
title: Zarządzanie grupami inteligentnymi
description: Zarządzanie grupami inteligentnymi utworzonymi w wystąpieniach alertów
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3b736fb7e4002f44ddb5d9a34b0ae3c19e21e35a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042661"
---
# <a name="manage-smart-groups"></a>Zarządzanie grupami inteligentnymi

[Grupy inteligentne](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) używają algorytmów uczenia maszynowego do grupowania alertów na podstawie współistnienia lub podobieństwa, dzięki czemu użytkownik może teraz zarządzać grupami inteligentnymi, a nie zarządzać indywidualnymi alertami. W tym artykule przedstawiono sposób uzyskiwania dostępu do inteligentnych grup i używania ich w programie Azure Monitor.

1. Aby wyświetlić grupy inteligentne utworzone dla wystąpień alertów, można wykonać jedną z:

     1. Kliknij pozycję **grupy inteligentne** na stronie **Podsumowanie alertów** .    
    ![Zrzut ekranu przedstawia stronę Podsumowanie alertów z wyróżnionymi grupami inteligentnymi.](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Kliknij pozycję alerty według grup inteligentnych na stronie wszystkie alerty.   
     ![Zrzut ekranu przedstawia stronę wszystkie alerty z wyróżnioną opcją alert według grupy inteligentnej.](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. Spowoduje to przejście do widoku listy dla wszystkich grup inteligentnych utworzonych w wystąpieniach alertów. Zamiast przesiania przez wiele alertów, możesz teraz zaradzić sobie z tymi grupami inteligentnymi.   
![Zrzut ekranu przedstawia stronę wszystkie alerty.](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Kliknięcie dowolnej grupy inteligentnej powoduje otwarcie strony szczegółów, w której można zobaczyć przyczynę grupowania wraz z alertami elementu członkowskiego. Ta agregacja pozwala na korzystanie z pojedynczej grupy inteligentnej zamiast przechodzenia przez wiele alertów.   
![Zrzut ekranu przedstawia stronę szczegółów.](./media/alerts-managing-smart-groups/sg-details.jpg)
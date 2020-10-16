---
title: Zarządzanie grupami inteligentnymi
description: Zarządzanie grupami inteligentnymi utworzonymi w wystąpieniach alertów
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: 763bfefcf71b0be43722b99f31641015a5991607
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105842"
---
# <a name="manage-smart-groups"></a>Zarządzanie grupami inteligentnymi

[Grupy inteligentne](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json) używają algorytmów uczenia maszynowego do grupowania alertów na podstawie współistnienia lub podobieństwa, dzięki czemu użytkownik może teraz zarządzać grupami inteligentnymi, a nie zarządzać indywidualnymi alertami. W tym artykule przedstawiono sposób uzyskiwania dostępu do inteligentnych grup i używania ich w programie Azure Monitor.

1. Aby wyświetlić grupy inteligentne utworzone dla wystąpień alertów, można wykonać jedną z:

     1. Kliknij pozycję **grupy inteligentne** na stronie **Podsumowanie alertów** .    
    ![Zrzut ekranu przedstawia stronę Podsumowanie alertów z wyróżnionymi grupami inteligentnymi.](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Kliknij pozycję alerty według grup inteligentnych na stronie wszystkie alerty.   
     ![Zrzut ekranu przedstawia stronę wszystkie alerty z wyróżnioną opcją alert według grupy inteligentnej.](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. Spowoduje to przejście do widoku listy dla wszystkich grup inteligentnych utworzonych w wystąpieniach alertów. Zamiast przesiania przez wiele alertów, możesz teraz zaradzić sobie z tymi grupami inteligentnymi.   
![Zrzut ekranu przedstawia stronę wszystkie alerty.](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Kliknięcie dowolnej grupy inteligentnej powoduje otwarcie strony szczegółów, w której można zobaczyć przyczynę grupowania wraz z alertami elementu członkowskiego. Ta agregacja pozwala na korzystanie z pojedynczej grupy inteligentnej zamiast przechodzenia przez wiele alertów.   
![Zrzut ekranu przedstawia stronę szczegółów.](./media/alerts-managing-smart-groups/sg-details.jpg)
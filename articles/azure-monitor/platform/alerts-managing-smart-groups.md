---
title: Zarządzanie grupami inteligentnymi
description: Zarządzanie grupami inteligentnymi utworzonymi w wystąpieniach alertów
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: a84e1ed514bce0f4dda496a5304cbfa0665c7c51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983069"
---
# <a name="manage-smart-groups"></a>Zarządzanie grupami inteligentnymi

[Grupy inteligentne](https://aka.ms/smart-groups) używają algorytmów uczenia maszynowego do grupowania alertów na podstawie współistnienia lub podobieństwa, dzięki czemu użytkownik może teraz zarządzać grupami inteligentnymi, a nie zarządzać indywidualnymi alertami. W tym artykule przedstawiono sposób uzyskiwania dostępu do inteligentnych grup i używania ich w programie Azure Monitor.

1. Aby wyświetlić grupy inteligentne utworzone dla wystąpień alertów, można wykonać jedną z:

     1. Kliknij pozycję **grupy inteligentne** na stronie **Podsumowanie alertów** .    
    ![Zrzut ekranu przedstawia stronę Podsumowanie alertów z wyróżnionymi grupami inteligentnymi.](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Kliknij pozycję alerty według grup inteligentnych na stronie wszystkie alerty.   
     ![Zrzut ekranu przedstawia stronę wszystkie alerty z wyróżnioną opcją alert według grupy inteligentnej.](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. Spowoduje to przejście do widoku listy dla wszystkich grup inteligentnych utworzonych w wystąpieniach alertów. Zamiast przesiania przez wiele alertów, możesz teraz zaradzić sobie z tymi grupami inteligentnymi.   
![Zrzut ekranu przedstawia stronę wszystkie alerty.](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Kliknięcie dowolnej grupy inteligentnej powoduje otwarcie strony szczegółów, w której można zobaczyć przyczynę grupowania wraz z alertami elementu członkowskiego. Ta agregacja pozwala na korzystanie z pojedynczej grupy inteligentnej zamiast przechodzenia przez wiele alertów.   
![Zrzut ekranu przedstawia stronę szczegółów.](./media/alerts-managing-smart-groups/sg-details.jpg)

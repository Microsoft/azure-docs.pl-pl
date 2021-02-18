---
title: Zarządzanie grupami inteligentnymi
description: Zarządzanie grupami inteligentnymi utworzonymi w wystąpieniach alertów
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: c36162f35c152fd5b9898d92b57e2e12044635a7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615440"
---
# <a name="manage-smart-groups"></a>Zarządzanie grupami inteligentnymi

[Grupy inteligentne](../platform/alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) używają algorytmów uczenia maszynowego do grupowania alertów na podstawie współistnienia lub podobieństwa, dzięki czemu użytkownik może teraz zarządzać grupami inteligentnymi, a nie zarządzać indywidualnymi alertami. W tym artykule przedstawiono sposób uzyskiwania dostępu do inteligentnych grup i używania ich w programie Azure Monitor.

1. Aby wyświetlić grupy inteligentne utworzone dla wystąpień alertów, można wykonać jedną z:

     1. Kliknij pozycję **grupy inteligentne** na stronie **Podsumowanie alertów** .    
    ![Zrzut ekranu przedstawia stronę Podsumowanie alertów z wyróżnionymi grupami inteligentnymi.](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Kliknij pozycję alerty według grup inteligentnych na stronie wszystkie alerty.   
     ![Zrzut ekranu przedstawia stronę wszystkie alerty z wyróżnioną opcją alert według grupy inteligentnej.](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. Spowoduje to przejście do widoku listy dla wszystkich grup inteligentnych utworzonych w wystąpieniach alertów. Zamiast przesiania przez wiele alertów, możesz teraz zaradzić sobie z tymi grupami inteligentnymi.   
![Zrzut ekranu przedstawia stronę wszystkie alerty.](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Kliknięcie dowolnej grupy inteligentnej powoduje otwarcie strony szczegółów, w której można zobaczyć przyczynę grupowania wraz z alertami elementu członkowskiego. Ta agregacja pozwala na korzystanie z pojedynczej grupy inteligentnej zamiast przechodzenia przez wiele alertów.   
![Zrzut ekranu przedstawia stronę szczegółów.](./media/alerts-managing-smart-groups/sg-details.jpg)
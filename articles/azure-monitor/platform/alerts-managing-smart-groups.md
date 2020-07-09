---
title: Zarządzanie grupami inteligentnymi
description: Zarządzanie grupami inteligentnymi utworzonymi w wystąpieniach alertów
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: d7077e51282864f1208080838a1bb94ddd773b7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77667469"
---
# <a name="manage-smart-groups"></a>Zarządzanie grupami inteligentnymi

[Grupy inteligentne](https://aka.ms/smart-groups) używają algorytmów uczenia maszynowego do grupowania alertów na podstawie współistnienia lub podobieństwa, dzięki czemu użytkownik może teraz zarządzać grupami inteligentnymi, a nie zarządzać indywidualnymi alertami. W tym artykule przedstawiono sposób uzyskiwania dostępu do inteligentnych grup i używania ich w programie Azure Monitor.

1. Aby wyświetlić grupy inteligentne utworzone dla wystąpień alertów, możesz

     1. Kliknij pozycję **grupy inteligentne** na stronie **Podsumowanie alertów** .    
    ![Monitorowanie](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. Kliknij pozycję alerty według grup inteligentnych na stronie wszystkie alerty.   
     ![Monitorowanie](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. Spowoduje to przejście do widoku listy dla wszystkich grup inteligentnych utworzonych w wystąpieniach alertów. Zamiast przesiania przez wiele alertów, możesz teraz zaradzić sobie z tymi grupami inteligentnymi.   
![Monitorowanie](./media/alerts-managing-smart-groups/sg-list.jpg)

3. Kliknięcie dowolnej grupy inteligentnej powoduje otwarcie strony szczegółów, w której można zobaczyć przyczynę grupowania wraz z alertami elementu członkowskiego. Ta agregacja pozwala na korzystanie z pojedynczej grupy inteligentnej zamiast przechodzenia przez wiele alertów.   
![Monitorowanie](./media/alerts-managing-smart-groups/sg-details.jpg)

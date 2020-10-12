---
title: plik dołączany
description: plik dołączany
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694193"
---
## <a name="lifecycle"></a>Cykl życia

Na karcie **cykl życia** Określ, kiedy wygasa przypisanie użytkownika do pakietu dostępu. Można również określić, czy użytkownicy mogą rozciągnąć swoje przypisania.

1. W sekcji **wygaśnięcie** Ustaw **przypisania pakietów dostępu wygasają** na **dzień**, **liczbę dni**lub **nigdy**.

    Na **dzień**wybierz datę wygaśnięcia w przyszłości.

    Dla **liczby dni**Określ liczbę z zakresu od 0 do 3660 dni.

    W zależności od dokonanego wyboru przypisanie użytkownika do pakietu dostępu wygasa w określonym dniu, przez określoną liczbę dni po zatwierdzeniu lub nigdy.

1. Kliknij pozycję **Pokaż zaawansowane ustawienia wygasania** , aby wyświetlić dodatkowe ustawienia.

    ![Dostęp do ustawień wygasania cyklu życia pakietu](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Aby zezwolić użytkownikowi na rozszeranie swoich przypisań, ustaw opcję **zezwól użytkownikom na rozszerzone dostęp** na **wartość tak**.

    Jeśli w zasadach są dozwolone rozszerzenia, użytkownik otrzyma wiadomość e-mail 14 dni, a także 1 dzień przed przypisaniem pakietu dostępu zostanie ustawiony na wygaśnięcie monitu o rozszerzenie przypisania. Jeśli użytkownik prześle żądanie przedłużenia dostępu, Data rozszerzenia musi być równa lub wcześniejsza od przypisań zgodnie z definicją w zasadach, które zostały użyte do udzielenia użytkownikowi dostępu do pakietu dostępu. Na przykład jeśli zasady wskazują, że dla przypisań ustawiono wygasanie 30 czerwca, Maksymalna długość żądania przez użytkownika to 30 czerwca.

    Jeśli dostęp użytkownika zostanie rozszerzony, nie będzie można zażądać pakietu dostępu po określonej dacie rozszerzenia (Data ustawiona w strefie czasowej użytkownika, który utworzył zasady).

1. Aby wymagać zatwierdzenia do udzielenia rozszerzenia, ustaw opcję **Wymagaj zatwierdzenia, aby przyznać rozszerzenie** **wartość tak**.

    Zostaną użyte te same ustawienia zatwierdzania, które zostały określone na karcie **żądania** .

1. Kliknij przycisk **dalej** lub **zaktualizuj**.

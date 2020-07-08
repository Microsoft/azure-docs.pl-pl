---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183177"
---
W tym kroku utworzysz regułę zapory, aby otworzyć port sondy dla punktu końcowego ze zrównoważonym obciążeniem (59999, jak określono wcześniej) i inną regułę, aby otworzyć port odbiornika grupy dostępności. Ze względu na to, że został utworzony punkt końcowy ze zrównoważonym obciążeniem na maszynach wirtualnych, które zawierają repliki grup dostępności, należy otworzyć port sondy i port odbiornika na odpowiednich maszynach wirtualnych.

1. Na maszynach wirtualnych, które obsługują repliki, uruchom **zaporę systemu Windows z zabezpieczeniami zaawansowanymi**.

2. Kliknij prawym przyciskiem myszy **reguły ruchu przychodzącego**, a następnie kliknij przycisk **nową regułę**.

3. Na stronie **Typ reguły** wybierz pozycję **port**, a następnie kliknij przycisk **dalej**.

4. Na stronie **Protokół i porty** wybierz pozycję **TCP**, wpisz **59999** w polu **określone porty lokalne** , a następnie kliknij przycisk **dalej**.

5. Na stronie **Akcja** pozostaw wybrane **połączenie** , a następnie kliknij przycisk **dalej**.

6. Na stronie **profil** zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **dalej**.

7. Na stronie **Nazwa** w polu tekstowym **Nazwa** Określ nazwę reguły, taką jak **zawsze włączony port sondy odbiornika**, a następnie kliknij przycisk **Zakończ**.

8. Powtórz powyższe kroki dla portu odbiornika grupy dostępności (jak określono wcześniej w $EndpointPort parametr skryptu), a następnie określ odpowiednią nazwę reguły, taką jak **zawsze włączony port odbiornika**.


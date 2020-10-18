---
title: Automatyczne skalowanie usługi w chmurze w portalu | Microsoft Docs
description: Dowiedz się, jak za pomocą portalu skonfigurować reguły automatycznego skalowania dla roli sieci Web lub roli procesu roboczego usługi w chmurze na platformie Azure.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 06a0209c2bbd0982054d33c199685d016f405b0c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165489"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>Jak skonfigurować Skalowanie automatyczne dla usługi w chmurze w portalu

Warunki można ustawić dla roli proces roboczy usługi w chmurze, która wyzwala operację skalowania w poziomie lub out. Warunki roli mogą opierać się na PROCESORAch, dyskach lub obciążeniach sieciowych roli. Możesz również ustawić warunek na podstawie kolejki komunikatów lub metryki innego zasobu platformy Azure skojarzonego z subskrypcją.

> [!NOTE]
> Ten artykuł koncentruje się na rolach sieci Web i procesu roboczego usługi w chmurze. Gdy tworzysz maszynę wirtualną (klasyczną) bezpośrednio, jest ona hostowana w usłudze w chmurze. Można skalować standardową maszynę wirtualną, kojarząc ją z [zestawem dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) i ręcznie ją włączać lub wyłączać.

## <a name="considerations"></a>Zagadnienia do rozważenia
Przed skonfigurowaniem skalowania aplikacji należy wziąć pod uwagę następujące informacje:

* Na skalowanie ma wpływ podstawowe użycie.

    Większe wystąpienia roli używają większej liczby rdzeni. Aplikację można skalować tylko w ramach limitu rdzeni dla subskrypcji. Załóżmy na przykład, że subskrypcja ma limit 20 rdzeni. W przypadku uruchamiania aplikacji z dwoma średnimi usługami w chmurze (łącznie z 4 rdzeniami) można skalować inne wdrożenia usług w chmurze w ramach subskrypcji przez pozostałe 16 rdzeni. Aby uzyskać więcej informacji na temat rozmiarów, zobacz [rozmiary usługi w chmurze](cloud-services-sizes-specs.md).

* Możesz skalować na podstawie progu komunikatów w kolejce. Aby uzyskać więcej informacji o sposobach korzystania z kolejek, zobacz [jak korzystać z usługi queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Możesz również skalować inne zasoby skojarzone z subskrypcją.

* Aby włączyć wysoką dostępność aplikacji, należy się upewnić, że jest ona wdrażana z co najmniej dwoma wystąpieniami roli. Aby uzyskać więcej informacji, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/).

* Automatyczne skalowanie jest wykonywane tylko wtedy, gdy wszystkie role są w stanie **gotowe** .  


## <a name="where-scale-is-located"></a>Gdzie znajduje się skala
Po wybraniu usługi w chmurze powinien być widoczny blok usługi w chmurze.

1. W bloku usługa w chmurze na kafelku **role i wystąpienia** wybierz nazwę usługi w chmurze.   
   **Ważne**: Pamiętaj, aby kliknąć rolę usługi w chmurze, a nie wystąpienia roli znajdującego się poniżej roli.

    ![Zrzut ekranu przedstawiający kafelek Role i wystąpienia z rolą proces roboczy z kolejką S B z opcją zakreśloną w kolorze czerwonym.](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Wybierz kafelek **Skala** .

    ![Zrzut ekranu strony operacje z kafelkiem sprzedaż przedstawionym na czerwono.](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatyczne skalowanie
Ustawienia skalowania dla roli można skonfigurować przy użyciu obu trybów **ręcznych** lub **automatycznych**. Ręczne jest tak jak oczekiwano, ustawiasz bezwzględną liczbę wystąpień. Funkcja automatycznego zezwala jednak na Określanie reguł, które określają, jak i w jakim stopniu należy skalować.

Ustaw opcję **skalowanie przez** , aby **zaplanować i reguły wydajności**.

![Zrzut ekranu pokazujący opcję harmonogram i reguły wydajności.](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Istniejący profil.
2. Dodaj regułę dla profilu nadrzędnego.
3. Dodaj inny profil.

Wybierz pozycję **Dodaj profil**. Profil określa tryb, który ma być używany dla skali: **zawsze**, **cykl**, **stała Data**.

Po skonfigurowaniu profilu i reguł Wybierz ikonę **Zapisz** u góry.

#### <a name="profile"></a>Profil
Profil ustawia minimalne i maksymalne wystąpienia skali, a także wtedy, gdy ten zakres skali jest aktywny.

* **Always** (Zawsze)

    Zawsze zachowuj dostęp do tego zakresu wystąpień.  

    ![Usługa w chmurze, która zawsze jest skalowana](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Cykl**

    Wybierz zestaw dni tygodnia do skalowania.

    ![Skalowanie usługi w chmurze z harmonogramem cyklu](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Stała Data**

    Stały zakres dat służący do skalowania roli.

    ![Skalowanie usługi w chmurze ze stałą datą](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Po skonfigurowaniu profilu wybierz przycisk **OK** w dolnej części bloku profil.

#### <a name="rule"></a>Reguła
Reguły są dodawane do profilu i reprezentują Warunek wyzwalający skalę.

Wyzwalacz reguły jest oparty na metryce usługi w chmurze (użycie procesora, aktywność dysku lub aktywność sieci), do której można dodać wartość warunkową. Ponadto możesz mieć wyzwalacz oparty na kolejce komunikatów lub w przypadku niektórych innych zasobów platformy Azure skojarzonych z Twoją subskrypcją.

![Zrzut ekranu przedstawiający okno dialogowe reguła z opcją Nazwa metryki zakreśloną na czerwono.](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Po skonfigurowaniu reguły wybierz przycisk **OK** w dolnej części bloku reguła.

## <a name="back-to-manual-scale"></a>Powrót do ręcznego skalowania
Przejdź do [ustawień skalowania](#where-scale-is-located) i dla opcji **Skaluj według** ustaw wartość **Liczba wystąpień wprowadzanych ręcznie**.

![Ustawienia skalowania usług Cloud Services z profilem i regułą](./media/cloud-services-how-to-scale-portal/manual-basics.png)

To ustawienie powoduje usunięcie automatycznego skalowania z roli, a następnie można ustawić liczbę wystąpień bezpośrednio.

1. Opcja Skala (ręczna lub automatyczna).
2. Suwak wystąpienia roli, w którym można ustawić wystąpienia do skalowania.
3. Wystąpienia roli do skalowania.

Po skonfigurowaniu ustawień skalowania wybierz ikonę **Zapisz** u góry.
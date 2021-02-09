---
title: Automatyczne skalowanie usługi w chmurze (klasycznej) w portalu | Microsoft Docs
description: Dowiedz się, jak za pomocą portalu skonfigurować reguły automatycznego skalowania dla ról usługi w chmurze (klasycznych) na platformie Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ba7b2279969acab93ac96c42e0033e76e9d0542d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980834"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-classic-in-the-portal"></a>Jak skonfigurować automatyczne skalowanie dla usługi w chmurze (klasycznej) w portalu

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Warunki można ustawić dla roli proces roboczy usługi w chmurze, która wyzwala operację skalowania w poziomie lub out. Warunki roli mogą opierać się na PROCESORAch, dyskach lub obciążeniach sieciowych roli. Możesz również ustawić warunek na podstawie kolejki komunikatów lub metryki innego zasobu platformy Azure skojarzonego z subskrypcją.

> [!NOTE]
> Ten artykuł koncentruje się na usłudze w chmurze (klasycznej). Gdy tworzysz maszynę wirtualną (klasyczną) bezpośrednio, jest ona hostowana w usłudze w chmurze. Można skalować standardową maszynę wirtualną, kojarząc ją z [zestawem dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) i ręcznie ją włączać lub wyłączać.

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

![ustawienia skalowania usług obrazów w chmurze z profilem i regułą](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

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

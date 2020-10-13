---
title: Jak skonfigurować rozsyłanie na koncentratorze wirtualnym
titleSuffix: Azure Virtual WAN
description: W tym artykule opisano sposób konfigurowania routingu koncentratora wirtualnego
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313692"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Jak skonfigurować rozsyłanie na koncentratorze wirtualnym

Koncentrator wirtualny może zawierać wiele bram, takich jak Brama sieci VPN typu lokacja-lokacja, Brama ExpressRoutea, Brama połączeń punkt-lokacja i Zapora platformy Azure. Możliwości routingu w koncentratorze wirtualnym są udostępniane przez router zarządzający całą trasą, w tym Routing tranzytowy, między bramami korzystającymi z Border Gateway Protocol (BGP). Ten router zapewnia również łączność między sieciami wirtualnymi, które łączą się z koncentratorem wirtualnym i obsługują zagregowaną przepływność wynoszącą 50 GB/s. Te możliwości routingu dotyczą standardowych wirtualnych klientów sieci WAN.

Aby uzyskać więcej informacji, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="create-a-route-table"></a><a name="create-table"></a>Tworzenie tabeli tras

1. W Azure Portal przejdź do koncentratora wirtualnego.
2. W obszarze **łączność**wybierz pozycję **Routing**. Na stronie Routing są wyświetlane tabele tras **domyślnych** i **Brak** .

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Strona routingu":::
3. Wybierz pozycję **+ Utwórz tabelę tras** , aby otworzyć stronę **Tworzenie tabeli tras** .
4. Na karcie Tworzenie tabeli tras **podstawowe** wypełnij następujące pola.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Strona routingu":::

   * **Nazwa**
   * **Trasy**
   * **Nazwa trasy**
   * **Typ docelowy**
   * **Prefiks lokalizacji docelowej**: można agregować prefiksy. Na przykład: Sieć wirtualna 1:10.1.0.0/24 i Sieć wirtualna 2:10.1.1.0/24 można agregować jako 10.1.0.0/16. Trasy **gałęzi** dotyczą wszystkich połączonych witryn sieci VPN, obwodów usługi ExpressRoute i połączeń sieci VPN użytkowników.
   * **Następny przeskok**: Lista połączeń sieci wirtualnej lub Zapora platformy Azure.

     W przypadku wybrania połączenia sieci wirtualnej zobaczysz pozycję **Konfiguruj trasy statyczne**. Jest to opcjonalne ustawienie konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie tras statycznych](about-virtual-hub-routing.md#static).

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Strona routingu":::

5. Wybierz kartę **etykiety** , aby skonfigurować nazwy etykiet. Etykiety zapewniają mechanizm logicznego grupowania tabel tras.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Strona routingu":::

6. Wybierz kartę **skojarzenia** , aby skojarzyć połączenia z tabelą tras.
Zobaczysz **gałęzie**, **sieci wirtualne**i **bieżące ustawienia** połączeń.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Strona routingu":::

7. Wybierz kartę **propagacje** , aby propagować trasy z połączeń do tabeli tras.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Strona routingu":::

8. Wybierz pozycję **Utwórz** , aby utworzyć tabelę tras.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Aby edytować tabelę tras

W Azure Portal zlokalizuj tabelę tras koncentratora wirtualnego. Wybierz tabelę tras, aby edytować wszystkie informacje.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Aby usunąć tabelę tras

W Azure Portal zlokalizuj tabelę tras koncentratora wirtualnego. Nie można usunąć domyślnej tabeli tras ani żadnej z nich. Można jednak usunąć wszystkie niestandardowe tabele tras. Kliknij pozycję **"..."**, a następnie wybierz pozycję **Usuń**.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Aby wyświetlić obowiązujące trasy

W Azure Portal zlokalizuj tabelę tras koncentratora wirtualnego. Kliknij przycisk **"..."** i wybierz **efektywne trasy** , aby wyświetlić trasy, które są uzyskiwane przez wybraną tabelę tras. Trasy propagowane z połączenia z tabelą tras są automatycznie wypełniane przy użyciu **efektywnych tras** tabeli tras. Aby uzyskać więcej informacji, zobacz [Informacje o obowiązujących trasach](effective-routes-virtual-hub.md).

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Strona routingu" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Aby skonfigurować konfigurację routingu dla połączenia sieci wirtualnej

1. W Azure Portal przejdź do wirtualnej sieci WAN, a w obszarze **łączność**wybierz pozycję **połączenia Virtual Network**.
1. Wybierz pozycję **+ Dodaj połączenie**.
1. Wybierz sieć wirtualną z listy rozwijanej.
1. Skonfiguruj konfigurację routingu do skojarzenia z tabelą tras. W polu **Skojarz tabelę tras**wybierz tabelę tras z listy rozwijanej.
1. Skonfiguruj konfigurację routingu do propagowania do jednej lub wielu tabel tras. W polu **Propaguj do trasy**wybierz pozycję z listy rozwijanej.
1. W przypadku **tras statycznych**skonfiguruj trasy statyczne dla wirtualnego urządzenia sieciowego (jeśli ma to zastosowanie). Wirtualna sieć WAN obsługuje pojedynczy adres IP następnego przeskoku dla trasy statycznej w połączeniu sieci wirtualnej. Jeśli na przykład masz oddzielne urządzenie wirtualne dla przepływów ruchu przychodzącego i wychodzącego, najlepszym rozwiązaniem jest posiadanie urządzeń wirtualnych w oddzielnych sieci wirtualnych i dołączenie sieci wirtualnych do koncentratora wirtualnego.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Strona routingu" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).

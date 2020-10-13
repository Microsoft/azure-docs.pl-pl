---
title: Jak utworzyć serwer DHCP i zarządzać nim
description: W tym artykule opisano sposób zarządzania serwerem DHCP w rozwiązaniu VMware platformy Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 2c059918f57b7f01058a031f1bf281b243855661
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332835"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Tworzenie i zarządzanie serwerem DHCP w rozwiązaniu VMWare platformy Azure

NSX-T zapewnia możliwość konfigurowania protokołu DHCP dla chmury prywatnej. Jeśli planujesz używać NSX-T do hostowania serwera DHCP, zobacz [Tworzenie serwera DHCP](#create-dhcp-server). W przeciwnym razie, jeśli w sieci istnieje zewnętrzny serwer DHCP innej firmy i chcesz przekazać żądania do tego serwera DHCP, zobacz [Tworzenie usługi przekaźnika DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Utwórz serwer DHCP

Wykonaj następujące kroki, aby skonfigurować serwer DHCP na NSX-T.

W programie NSX Manager przejdź do karty **Sieć** , a następnie wybierz pozycję **DHCP** w obszarze **Zarządzanie IP**. Wybierz przycisk **Dodaj serwer** . Podaj nazwę serwera i adres IP serwera. Po zakończeniu wybierz pozycję **Zapisz**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Dodaj serwer DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Połącz serwer DHCP z bramą warstwy 1.

1. Wybierz pozycję **bramy warstwy 1**, bramę, a następnie wybierz pozycję **Edytuj** .

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Dodaj podsieć, wybierając pozycję **Brak zestawu alokacji adresów IP**

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Na następnym ekranie wybierz pozycję **serwer lokalny DHCP** z listy rozwijanej **Typ** . W obszarze **serwer DHCP**wybierz opcję **domyślny serwer DHCP** , a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="Dodaj serwer DHCP" border="true":::

1. W oknie **brama warstwy 1** wybierz pozycję **Zapisz**. Na następnym ekranie zobaczysz **zmiany zapisane**, wybierz pozycję **Zamknij edytowanie** , aby zakończyć.

### <a name="add-a-network-segment"></a>Dodawanie segmentu sieci

Po utworzeniu serwera DHCP należy dodać do niego segmenty sieci.

1. W NSX-T wybierz kartę **Sieć** i wybierz **segmenty** w obszarze **łączność**. Wybierz pozycję **Dodaj segment**. Nazwij segment i połączenie z bramą warstwy 1. Następnie wybierz pozycję **Ustaw podsieci** , aby skonfigurować nową podsieć. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Dodaj serwer DHCP" border="true":::

1. W oknie **Ustawianie podsieci** wybierz pozycję **Dodaj podsieć**. Wprowadź adres IP bramy i zakres DHCP, a następnie wybierz pozycję **Dodaj** , a następnie **Zastosuj**

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Po zakończeniu wybierz pozycję **Zapisz** , aby zakończyć dodawanie segmentu sieci.

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="Dodaj serwer DHCP" border="true":::

## <a name="create-dhcp-relay-service"></a>Tworzenie usługi przekaźnika DHCP

1. W oknie NXT-T wybierz kartę **Sieć** , a następnie w obszarze **Zarządzanie IP**wybierz pozycję **DHCP**. Wybierz pozycję **Dodaj serwer**. W polu **Typ serwera** wybierz opcję przekaźnik DHCP, a następnie wprowadź nazwę serwera i adres IP serwera przekazywania. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz pozycję **bramy warstwy 1** w obszarze **łączność**. Wybierz wielokropek pionowy w bramie warstwy 1 i wybierz pozycję **Edytuj**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz pozycję **Brak zestawu alokacji adresów IP** , aby zdefiniować alokację adresów IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Dodaj serwer DHCP" border="true":::

1. W oknie dialogowym, w polu **Typ**wybierz opcję **serwer przekaźnika DHCP**. Na liście rozwijanej **przekaźnik DHCP** wybierz serwer przekaźnika DHCP. Po zakończeniu wybierz pozycję **Zapisz** .

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="Dodaj serwer DHCP" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>Określ adres IP zakresu DHCP dla segmentu

> [!NOTE]
> Ta konfiguracja jest wymagana do realizacji funkcji przekazywania DHCP w segmencie klienta DHCP. 

1. W obszarze **łączność**wybierz pozycję **segmenty**. Wybierz wielokropek pionowy i wybierz pozycję **Edytuj**. Zamiast tego, jeśli chcesz dodać nowy segment, możesz wybrać pozycję **Dodaj segment** , aby utworzyć nowy segment.

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Dodaj szczegóły dotyczące segmentu. Wybierz wartość w obszarze **podsieci** lub **Ustaw podsieci** , aby dodać lub zmodyfikować podsieć.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz wielokropek pionowy i wybierz pozycję **Edytuj**. Jeśli musisz utworzyć nową podsieć, wybierz pozycję **Dodaj podsieć** , aby utworzyć bramę i skonfigurować zakres DHCP. Podaj zakres puli adresów IP i wybierz pozycję **Zastosuj**, a następnie wybierz pozycję **Zapisz** .

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Teraz Pula serwerów DHCP jest przypisana do segmentu.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Dodaj serwer DHCP" border="true":::

---
title: Jak utworzyć serwer DHCP i zarządzać nim
description: W tym artykule opisano sposób zarządzania serwerem DHCP w rozwiązaniu VMware platformy Azure.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb74ed4474cc14081e59142f2f60915fccd47559
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461060"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Tworzenie i zarządzanie serwerem DHCP w rozwiązaniu VMware platformy Azure

NSX-T zapewnia możliwość konfigurowania protokołu DHCP dla chmury prywatnej. Jeśli używasz NSX-T do hostowania serwera DHCP, zobacz [Tworzenie serwera DHCP](#create-dhcp-server). W przeciwnym razie, jeśli w sieci istnieje zewnętrzny serwer DHCP innej firmy, zobacz [Tworzenie usługi przekaźnika DHCP](#create-dhcp-relay-service).

## <a name="create-dhcp-server"></a>Utwórz serwer DHCP

Wykonaj następujące kroki, aby skonfigurować serwer DHCP na NSX-T.

1. W programie NSX Manager przejdź do karty **Sieć** , a następnie wybierz pozycję **DHCP**. 
1. Wybierz pozycję **Dodaj serwer** , a następnie podaj nazwę serwera i adres IP. 
1. Wybierz pozycję **Zapisz**.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Dodaj serwer DHCP" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>Połącz serwer DHCP z bramą warstwy 1.

1. Wybierz pozycję **bramy warstwy 1**, bramę z listy, a następnie wybierz pozycję **Edytuj**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz pozycję **Brak zestawu alokacji adresów IP** , aby dodać podsieć.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz opcję **serwer lokalny DHCP** dla tego **typu**. 
1. Wybierz opcję **domyślny** serwer DHCP dla **serwera DHCP** , a następnie wybierz pozycję **Zapisz**.


1. W oknie **brama warstwy 1** wybierz pozycję **Zapisz**. 
1. Wybierz pozycję **Zamknij edycję** , aby zakończyć.

### <a name="add-a-network-segment"></a>Dodawanie segmentu sieci

Po utworzeniu serwera DHCP należy dodać do niego segmenty sieci.

1. W NSX-T wybierz kartę **Sieć** i wybierz **segmenty** w obszarze **łączność**. 
1. Wybierz pozycję **Dodaj segment** i nazwij segment i połączenie z bramą warstwy 1. 
1. Wybierz pozycję **Ustaw podsieci** , aby skonfigurować nową podsieć. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="Dodaj serwer DHCP" border="true":::

1. W oknie **Ustawianie podsieci** wybierz pozycję **Dodaj podsieć**. 
1. Wprowadź adres IP bramy i zakres DHCP, a następnie wybierz pozycję **Dodaj** , a następnie **Zastosuj**

1. Wybierz pozycję **Zapisz** , aby dodać nowy segment sieci.

## <a name="create-dhcp-relay-service"></a>Tworzenie usługi przekaźnika DHCP

1. Wybierz kartę **Sieć** i w obszarze **Zarządzanie IP**wybierz pozycję **DHCP**. 
1. Wybierz pozycję **Dodaj serwer**. 
1. W polu **Typ serwera** wybierz opcję przekaźnik DHCP, a następnie wprowadź nazwę serwera i adres IP serwera przekazywania. 
1. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz pozycję **bramy warstwy 1** w obszarze **łączność**. 
1. Wybierz wielokropek pionowy w bramie warstwy 1 i wybierz pozycję **Edytuj**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz pozycję **Brak zestawu alokacji adresów IP** , aby zdefiniować alokację adresów IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz **serwer przekazywania DHCP** dla **typu**.
1. Wybierz serwer przekazywania DHCP do **przekaźnika DHCP**. 
1. Wybierz pozycję **Zapisz**.


## <a name="specify-a-dhcp-range-ip-on-a-segment"></a>Określ adres IP zakresu DHCP dla segmentu

> [!NOTE]
> Ta konfiguracja jest wymagana do realizacji funkcji przekazywania DHCP w segmencie klienta DHCP. 

1. W obszarze **łączność**wybierz pozycję **segmenty**. 
1. Wybierz wielokropek pionowy i wybierz pozycję **Edytuj**. 

   >[!TIP]
   >Jeśli chcesz dodać nowy segment, wybierz pozycję **Dodaj segment**.

1. Dodaj szczegóły dotyczące segmentu. 
1. Wybierz wartość w obszarze **Ustaw podsieci** , aby dodać lub zmodyfikować podsieć.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz wielokropek pionowy i wybierz pozycję **Edytuj**. Jeśli musisz utworzyć nową podsieć, wybierz pozycję **Dodaj podsieć** , aby utworzyć bramę i skonfigurować zakres DHCP. 
1. Podaj zakres puli adresów IP i wybierz pozycję **Zastosuj**, a następnie wybierz pozycję **Zapisz** .

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Dodaj serwer DHCP" border="true":::

   Pula serwerów DHCP jest przypisana do segmentu.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Dodaj serwer DHCP" border="true":::

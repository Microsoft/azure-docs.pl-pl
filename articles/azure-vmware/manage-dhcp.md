---
title: Zarządzanie protokołem DHCP dla rozwiązania VMware platformy Azure
description: Dowiedz się, jak utworzyć protokół DHCP i zarządzać nim w chmurze prywatnej rozwiązania Azure VMware.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708555"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Zarządzanie protokołem DHCP dla rozwiązania VMware platformy Azure

Aplikacje i obciążenia działające w środowisku chmury prywatnej wymagają usług DHCP do przypisywania adresów IP.  W tym artykule opisano sposób tworzenia i zarządzania usługą DHCP w rozwiązaniu Azure VMware na dwa sposoby:

- Jeśli używasz NSX-T do hostowania serwera DHCP, musisz [utworzyć serwer DHCP](#create-a-dhcp-server) i [przekazać go do tego serwera](#create-dhcp-relay-service). Podczas tworzenia serwera DHCP należy również dodać segment sieci i określić zakres adresów IP protokołu DHCP.   

- Jeśli używasz zewnętrznego serwera DHCP innej firmy w sieci, musisz [utworzyć usługę przekaźnika DHCP](#create-dhcp-relay-service). Podczas tworzenia przekaźnika na serwerze DHCP, przy użyciu NSX-T lub innej firmy do hostowania serwera DHCP, należy określić zakres adresów IP protokołu DHCP.

>[!IMPORTANT]
>Usługa DHCP nie działa w przypadku maszyn wirtualnych w sieci rozciąganej programu VMware HCX L2, gdy serwer DHCP znajduje się w lokalnym centrum danych.  NSX domyślnie blokuje wszystkie żądania DHCP od przechodzenia do rozciągnięcia L2. W przypadku rozwiązania Zobacz procedurę [Wyślij żądania DHCP do lokalnego serwera DHCP](#send-dhcp-requests-to-the-on-premises-dhcp-server) .


## <a name="create-a-dhcp-server"></a>Tworzenie serwera DHCP

Jeśli chcesz używać NSX-T do hostowania serwera DHCP, utworzysz serwer DHCP. Następnie dodasz segment sieci i określisz zakres adresów IP protokołu DHCP.

1. W Menedżerze NSX-T wybierz pozycję **Sieć**  >  **DHCP**, a następnie wybierz pozycję **Dodaj serwer**.

1. W polu **Typ serwera** wybierz pozycję **DHCP** , podaj nazwę serwera i adres IP, a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="Dodaj serwer DHCP" border="true":::

1. Wybierz pozycję **bramy warstwy 1**, wybierz wielokropek pionowy w bramie warstwy 1, a następnie wybierz pozycję **Edytuj**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="Wybierz bramę do użycia" border="true":::

1. Wybierz pozycję **Brak zestawu alokacji adresów IP** , aby dodać podsieć.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="Dodawanie podsieci" border="true":::

1. W obszarze **Typ** wybierz opcję **serwer lokalny DHCP**. 
   
1. W polu **serwer DHCP** wybierz opcję **domyślny serwer DHCP**, a następnie wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Zapisz** ponownie, a następnie wybierz pozycję **Zamknij edytowanie**.

### <a name="add-a-network-segment"></a>Dodawanie segmentu sieci

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>Tworzenie usługi przekaźnika DHCP

Jeśli chcesz użyć zewnętrznego serwera DHCP innej firmy, musisz utworzyć usługę przekaźnika DHCP. Należy również określić zakres adresów IP protokołu DHCP w Menedżerze NSX-T. 

1. W Menedżerze NSX-T wybierz pozycję **Sieć**  >  **DHCP**, a następnie wybierz pozycję **Dodaj serwer**.

1. Wybierz opcję **przekaźnik DHCP** dla **typu serwera**, podaj nazwę serwera i adres IP, a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="Tworzenie usługi przekaźnika DHCP" border="true":::

1. Wybierz pozycję **bramy warstwy 1**, wybierz wielokropek pionowy w bramie warstwy 1, a następnie wybierz pozycję **Edytuj**.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Edytuj bramę warstwy 1" border="true":::

1. Wybierz pozycję **Brak zestawu alokacji adresów IP** , aby zdefiniować alokację adresów IP.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="Edytuj alokację adresów IP" border="true":::

1. W obszarze **Typ** wybierz opcję **serwer DHCP**. 
   
1. W polu **serwer DHCP** wybierz opcję **przekaźnik DHCP**, a następnie wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Zapisz** ponownie, a następnie wybierz pozycję **Zamknij edytowanie**.


## <a name="specify-the-dhcp-ip-address-range"></a>Określ zakres adresów IP protokołu DHCP

1. W Menedżerze NSX-T wybierz pozycję   >  **segmenty** sieci. 
   
1. Wybierz wielokropek pionowy w nazwie segmentu i wybierz pozycję **Edytuj**.
   
1. Wybierz pozycję **Ustaw podsieci** , aby określić adres IP protokołu DHCP dla podsieci. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="segmenty sieci" border="true":::
      
1. W razie konieczności zmodyfikuj adres IP bramy, a następnie wprowadź wartość adres IP zakresu DHCP. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="Edytowanie podsieci" border="true":::
      
1. Wybierz pozycję **Zastosuj**, a następnie **Zapisz**. Do segmentu przypisano pulę serwerów DHCP.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Pula serwerów DHCP przypisana do segmentu" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>Wysyłanie żądań DHCP do lokalnego serwera DHCP

Jeśli chcesz wysyłać żądania DHCP z maszyn wirtualnych rozwiązań VMware platformy Azure w rozszerzonym segmencie L2 do lokalnego serwera DHCP, utworzysz profil segmentu zabezpieczeń. 

1. Zaloguj się do lokalnego programu vCenter i w obszarze **Strona główna** wybierz pozycję **HCX**.

1. Wybierz pozycję **rozszerzenie sieci** w obszarze **usługi**.

1. Wybierz rozszerzenie sieci, dla którego chcesz obsługiwać żądania DHCP z rozwiązań VMware platformy Azure w środowisku lokalnym. 

1. Zanotuj nazwę sieci docelowej.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="Zrzut ekranu przedstawiający rozszerzenie sieci w kliencie VMware vSphere" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. W Menedżerze rozwiązań Azure VMware NSX-T wybierz kolejno pozycje **Sieć**  >  **segmenty**  >  **segmentu profile**. 

1. Wybierz kolejno pozycje **Dodaj profil segmentu** i **zabezpieczenia segmentu**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania profilu segmentu w NSX-T" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Podaj nazwę i tag, a następnie ustaw przełącznik **filtru BPDU** na wartość włączone, a wszystkie przełączniki DHCP są wyłączone.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="Zrzut ekranu przedstawiający filtr BPDU włączony i przełączania DHCP" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Usuń wszystkie adresy MAC, jeśli istnieją, na **liście dozwolonych filtrów BPDU**.  Następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="Zrzut ekranu przedstawiający adresy MAC na liście dozwolonych filtrów BPDU":::

1. W obszarze segmenty **sieci**  >    >  **segmenty**, w obszarze wyszukiwania wprowadź nazwę sieci definicji.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Zrzut ekranu przedstawiający pole filtru segmentów > sieci":::

1. Wybierz wielokropek pionowy w nazwie segmentu i wybierz pozycję **Edytuj**.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Zrzut ekranu przycisku Edytuj dla segmentu" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. Zmień **zabezpieczenia segmentu** na utworzony wcześniej profil segmentu.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Zrzut ekranu przedstawiający pole zabezpieczeń segmentu" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [konserwacji hosta i zarządzania cyklem życia](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).
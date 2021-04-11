---
title: Konfigurowanie składników sieciowych NSX w rozwiązaniu VMware platformy Azure
description: Informacje na temat konfigurowania segmentów sieci NSX-T przy użyciu konsoli rozwiązań VMware platformy Azure.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a4702efe8ea26234d074f421d2e6eab871eba5f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733142"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Konfigurowanie składników sieciowych NSX w rozwiązaniu VMware platformy Azure

Chmura prywatna rozwiązania Azure VMware ma domyślnie NSX-T jako sieć zdefiniowaną przez program (SDDC). Jest ona wstępnie zainicjowana za pomocą bramy NSX-T w trybie **aktywny/aktywny** i domyślnej bramy NSX-t w trybie aktywny/wstrzymanie.  Te bramy pozwalają połączyć segmenty (przełączniki logiczne) i zapewnić East-West i North-South łączność. 

Po wdrożeniu chmury prywatnej rozwiązania VMware platformy Azure można skonfigurować niezbędne obiekty NSX-T z poziomu konsoli rozwiązań VMware platformy Azure w obszarze **obciążenie sieci**.  W konsoli programu jest wyświetlany uproszczony widok operacji NSX-T, których administrator oprogramowania VMware potrzebuje codziennie i który jest przeznaczony dla użytkowników, którzy nie znają NSX-T.  

Dostępne są cztery opcje konfigurowania składników NSX-T w konsoli rozwiązania Azure VMware:
- **Segmenty** — Twórz segmenty, które są wyświetlane w Menedżerze NSX-T i vCenter.
- **DHCP** — Utwórz serwer DHCP lub przekaźnik DHCP, jeśli planujesz korzystanie z protokołu DHCP.
- **Dublowanie portów** — Tworzenie funkcji dublowania portów w celu ułatwienia rozwiązywania problemów z siecią.
- **DNS** — Utwórz usługę przesyłania dalej DNS w celu wysyłania żądań DNS do wyszukanego serwera DNS w celu rozwiązania tego problemu.  

>[!NOTE]
>Nadal będziesz mieć dostęp do konsoli Menedżera NSX-T, w której można korzystać z wymienionych ustawień zaawansowanych i innych funkcji NSX-T. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Zrzut ekranu przedstawiający cztery opcje w konsoli rozwiązań VMware platformy Azure na potrzeby konfigurowania NSX-T.":::

## <a name="prerequisites"></a>Wymagania wstępne
Maszyny wirtualne utworzone lub zmigrowane do chmury prywatnej rozwiązania Azure VMware powinny być dołączone do segmentu. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Utwórz segment NSX-T w Azure Portal
Można utworzyć i skonfigurować segment NSX-T z poziomu konsoli rozwiązań VMware platformy Azure w Azure Portal.  Te segmenty są połączone z domyślną bramą warstwy 1, a obciążenia tych segmentów uzyskują East-West i North-South łączności. Po utworzeniu segmentu zostanie on wyświetlony w Menedżerze NSX-T i vCenter.

>[!NOTE]
>Jeśli planujesz korzystanie z protokołu DHCP, musisz [skonfigurować serwer DHCP lub przekaźnik DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) , aby można było utworzyć i skonfigurować segment NSX-T.

1. W chmurze prywatnej rozwiązania Azure VMware w obszarze **obciążenie sieci** wybierz pozycję **segmenty**  >  **Dodaj**. Podaj szczegóły nowego segmentu logicznego, a następnie wybierz **przycisk OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania nowego segmentu.":::
   
   - **Nazwa segmentu** — nazwa przełącznika logicznego, który jest widoczny w programie vCenter.
   - **Brama podsieci** — adres IP bramy dla podsieci przełącznika logicznego z maską podsieci. Maszyny wirtualne są podłączone do przełącznika logicznego, a wszystkie maszyny wirtualne łączące się z tym przełącznikiem należą do tej samej podsieci.  Ponadto wszystkie maszyny wirtualne dołączone do tego segmentu logicznego muszą mieć adres IP z tego samego segmentu.
   - **DHCP** (opcjonalnie) — zakresy DHCP dla segmentu logicznego. [Serwer DHCP lub przekaźnik DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) musi być skonfigurowany do korzystania z protokołu DHCP na segmentach.  
   - **Połączona Brama**  -  *Wybrane domyślnie i tylko do odczytu.*  Brama warstwy 1 i typ informacji o segmencie. 
      - **T1** — nazwa bramy warstwy 1 w Menedżerze NSX-T. Chmura prywatna rozwiązania Azure VMware jest dostępna z bramą warstwy NSX-T w trybie aktywny/aktywny i domyślną bramą NSX-T w trybie aktywny/wstrzymania.  Segmenty utworzone za pośrednictwem konsoli rozwiązań VMware platformy Azure łączą się tylko z domyślną bramą warstwy 1, a obciążenia tych segmentów uzyskują East-West i North-South łączności. Więcej bram warstwy 1 można utworzyć tylko za poorednictwem Menedżera NSX-T. Bramy warstwy 1 utworzone za pomocą konsoli Menedżera NSX-T nie są widoczne w konsoli rozwiązania VMware platformy Azure. 
      - Segment nakładki **typu** obsługiwany przez rozwiązanie VMware platformy Azure.

   Segment jest teraz widoczny w konsoli rozwiązania platformy Azure VMware, NSX-T Manager i vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Utwórz serwer DHCP lub przekaźnik DHCP w Azure Portal
Serwer DHCP lub przekaźnik można utworzyć bezpośrednio z poziomu konsoli rozwiązań VMware platformy Azure w Azure Portal. Serwer DHCP lub przekaźnik nawiązuje połączenie z bramą warstwy 1, która jest tworzona podczas wdrażania rozwiązania Azure VMware. Wszystkie segmenty, w których wydała się zakresy DHCP, będą częścią tego protokołu DHCP.  Po utworzeniu serwera DHCP lub przekaźnika DHCP należy zdefiniować podsieć lub zakres na poziomie segmentu, aby go użyć.

1. W chmurze prywatnej rozwiązania Azure VMware w obszarze **obciążenie sieci** wybierz pozycję **DHCP**  >  **Dodaj**.

2. Wybierz **serwer DHCP** lub **przekaźnik DHCP** , a następnie podaj nazwę serwera lub przekaźnika oraz trzy adresy IP. 

   >[!NOTE]
   >W przypadku przekazywania DHCP tylko jeden adres IP jest wymagany do pomyślnej konfiguracji.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania serwera DHCP lub przekaźnika DHCP w rozwiązaniach VMware platformy Azure.":::

4. Ukończ konfigurację DHCP, [dostarczając zakresy DHCP w segmentach logicznych](#create-an-nsx-t-segment-in-the-azure-portal) , a następnie wybierz przycisk **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Konfigurowanie funkcji dublowania portów w Azure Portal
Funkcję dublowania portów można skonfigurować do monitorowania ruchu sieciowego, który obejmuje przekazywanie kopii poszczególnych pakietów z jednego portu przełącznika sieci na inny. Ta opcja powoduje umieszczenie analizatora protokołów na porcie, który odbiera zdublowane dane. Analizuje ruch ze źródła, maszyny wirtualnej lub grupy maszyn wirtualnych, a następnie wysyła do zdefiniowanego miejsca docelowego. 

Aby skonfigurować funkcję dublowania portów w konsoli rozwiązania Azure VMware, możesz:

* [Krok 1. Utwórz źródłową i docelową maszynę wirtualną lub grupy maszyn](#step-1-create-source-and-destination-vms-or-vm-groups) wirtualnych — Grupa źródłowa zawiera jedną maszynę wirtualną lub wiele maszyn wirtualnych, na których jest duplikowany ruch.

* [Krok 2. Utwórz profil dublowania portów](#step-2-create-a-port-mirroring-profile) — Zdefiniuj kierunek ruchu dla źródłowej i docelowej grupy maszyn wirtualnych.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Krok 1. Utwórz źródłową i docelową maszynę wirtualną lub grupy maszyn wirtualnych

W tym kroku utworzysz źródłową grupę maszyn wirtualnych i docelową grupę maszyn wirtualnych.

1. W chmurze prywatnej rozwiązania Azure VMware w obszarze **obciążenie sieci** wybierz kolejno pozycje maszyny wirtualne **dublowanie portów**  >    >  **Dodaj**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia grupy maszyn wirtualnych na potrzeby dublowania portów.":::

1. Podaj nazwę nowej grupy maszyn wirtualnych, wybierz z listy żądane maszyny wirtualne, a następnie kliknij przycisk **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Zrzut ekranu przedstawiający listę maszyn wirtualnych, które mają zostać dodane do grupy maszyn wirtualnych.":::

1. Powtórz te kroki, aby utworzyć docelową grupę maszyn wirtualnych.

### <a name="step-2-create-a-port-mirroring-profile"></a>Krok 2. Tworzenie profilu dublowania portów

W tym kroku zdefiniujesz profil dla kierunku ruchu źródłowego i docelowego grupy maszyn wirtualnych.

>[!NOTE]
>Upewnij się, że utworzono źródłową i docelową grupę maszyn wirtualnych.

1. Wybierz pozycję Dodaj **dublowanie portów**  >   , a następnie podaj:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Zrzut ekranu przedstawiający informacje wymagane dla profilu dublowania portów.":::

   - **Nazwa funkcji dublowania portów** — opisowa nazwa profilu.
   - **Kierunek** — wybieranie z ruchu przychodzącego, wychodzącego lub dwukierunkowego.
   - **Źródło** — wybierz źródłową grupę maszyn wirtualnych.
   - **Miejsce docelowe** — wybierz docelową grupę maszyn wirtualnych.
   - **Opis** — wprowadź opis funkcji dublowania portów.

1. Wybierz **przycisk OK** , aby zakończyć profil. 

   Profile i grupy maszyn wirtualnych są widoczne w konsoli rozwiązania Azure VMware.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Konfigurowanie usługi przesyłania dalej DNS w Azure Portal
Skonfiguruj usługę przesyłania dalej DNS, w której określone żądania DNS zostaną przekazane do wyszukanego serwera DNS w celu rozwiązania tego problemu.  Usługa przesyłania dalej DNS jest skojarzona z **domyślną strefą DNS** i maksymalnie trzema **strefami FQDN**.

>[!TIP]
>[Aby skonfigurować usługę przesyłania dalej DNS, można także użyć konsoli Menedżera NSX-T](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Aby skonfigurować usługę przesyłania dalej DNS w konsoli rozwiązania Azure VMware, możesz:

* [Krok 1. Konfigurowanie domyślnej strefy DNS i strefy FQDN](#step-1-configure-a-default-dns-zone-and-fqdn-zone) — po odebraniu kwerendy DNS usługa przesyłania dalej DNS porównuje nazwę domeny z nazwami domen w strefie DNS FQDN. 

* [Krok 2. Konfigurowanie usługi DNS](#step-2-configure-dns-service) — skonfigurujesz usługę przesyłania dalej DNS.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Krok 1. Konfigurowanie domyślnej strefy DNS i strefy FQDN
Skonfigurujesz domyślną strefę DNS i strefę FQDN do wysyłania zapytań DNS do serwera nadrzędnego.  Po odebraniu zapytania DNS usługa przesyłania dalej DNS porównuje nazwę domeny w zapytaniu z nazwami domen DNS strefy FQDN. W przypadku znalezienia dopasowania zapytanie jest przekazywane do serwerów DNS określonych w strefie DNS FQDN. Jeśli żadne dopasowanie nie zostanie znalezione, zapytanie jest przekazywane do serwerów DNS określonych w domyślnej strefie DNS.

>[!NOTE]
>Przed skonfigurowaniem strefy FQDN należy zdefiniować domyślną strefę DNS. 

1. W chmurze prywatnej rozwiązania Azure VMware w obszarze **obciążenie sieci** wybierz pozycję   >  **strefy DNS** systemu DNS  >  **Dodaj**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania stref DNS i usługi DNS.":::

1. Wybierz **domyślną strefę DNS** i podaj:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania domyślnej strefy DNS.":::

   1. Nazwa strefy DNS.

   1. Maksymalnie trzy adresy IP serwerów DNS w formacie **8.8.8.8**.

1. Wybierz **strefę FQDN** i podaj:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania strefy FQDN. ":::

   1. Nazwa strefy DNS.

   1. Domena FQDN.

   1. Maksymalnie trzy adresy IP serwerów DNS w formacie **8.8.8.8**.

1. Wybierz **przycisk OK** , aby zakończyć dodawanie domyślnej strefy DNS i usługi DNS.

### <a name="step-2-configure-dns-service"></a>Krok 2. Konfigurowanie usługi DNS

1. Wybierz kartę **Usługa DNS** , a następnie wybierz pozycję **Dodaj**. Podaj szczegóły i wybierz **przycisk OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Zrzut ekranu przedstawiający informacje wymagane dla usługi DNS.":::

   >[!TIP]
   >**Brama warstwy 1** jest domyślnie zaznaczona i odzwierciedla bramę utworzoną podczas wdrażania rozwiązania Azure VMware.

   Usługa DNS została dodana pomyślnie.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Zrzut ekranu przedstawiający pomyślne dodanie usługi DNS.":::


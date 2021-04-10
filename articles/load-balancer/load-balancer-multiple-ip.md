---
title: Równoważenie obciążenia dla wielu konfiguracji adresów IP — Azure Portal
titleSuffix: Azure Load Balancer
description: Ten artykuł zawiera informacje na temat równoważenia obciążenia w podstawowych i dodatkowych konfiguracjach IP przy użyciu Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3d28946aad263af635a0139e68d424a77a1eab25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417828"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Równoważenie obciążenia dla wielu konfiguracji adresów IP przy użyciu Azure Portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Program PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Interfejs wiersza polecenia](load-balancer-multiple-ip-cli.md)

W tym artykule przedstawiono sposób użycia Azure Load Balancer z wieloma adresami IP na dodatkowym kontrolerze sieci (NIC). Na poniższym diagramie przedstawiono nasz scenariusz:

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

W naszym scenariuszu używana jest następująca konfiguracja:

- Dwie maszyny wirtualne z systemem Windows.
- Każda maszyna wirtualna ma podstawową i pomocniczą kartę sieciową.
- Każda pomocnicza karta sieciowa ma dwie konfiguracje IP.
- Każda maszyna wirtualna obsługuje dwie witryny sieci Web: contoso.com i fabrikam.com.
- Każda witryna sieci Web jest powiązana z konfiguracją adresów IP na pomocniczej karcie sieciowej.
- Azure Load Balancer służy do uwidocznienia dwóch adresów IP frontonu, po jednym dla każdej witryny sieci Web. Adresy frontonu są używane do dystrybucji ruchu do odpowiedniej konfiguracji protokołu IP dla każdej witryny sieci Web.
- Ten sam numer portu jest używany zarówno dla adresów IP frontonu, jak i adresów IP puli zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

W naszym przykładzie scenariusza założono, że masz grupę zasobów o nazwie **contosofabrikam** , która jest skonfigurowana w następujący sposób:

- Grupa zasobów zawiera sieć wirtualną o nazwie **myVNet**.
- Sieć **myVNet** obejmuje dwie maszyny wirtualne o nazwach **VM1** i **VM2**.
- VM1 i VM2 znajdują się w tym samym zestawie dostępności o nazwie **myAvailset**. 
- VM1 i VM2 mają odpowiednio podstawową kartę sieciową o nazwie **VM1NIC1** i **VM2NIC1**. 
- VM1 i VM2 każdy z nich ma pomocniczą kartę sieciową o nazwie **VM1NIC2** i **VM2NIC2**.

Aby uzyskać więcej informacji na temat tworzenia maszyn wirtualnych z wieloma kartami sieciowymi, zobacz [Tworzenie maszyny wirtualnej z wieloma](../virtual-machines/windows/multiple-nics.md)kartami sieciowymi przy użyciu programu PowerShell.

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Wykonywanie równoważenia obciążenia dla wielu konfiguracji adresów IP

Wykonaj następujące kroki, aby osiągnąć scenariusz opisany w tym artykule.

### <a name="step-1-configure-the-secondary-nics"></a>Krok 1. Konfigurowanie pomocniczych kart sieciowych

Dla każdej maszyny wirtualnej w sieci wirtualnej Dodaj konfigurację protokołu IP dla pomocniczej karty sieciowej:  

1. Przejdź do Azure Portal: https://portal.azure.com . Zaloguj się przy użyciu konta platformy Azure.

2. W lewym górnym rogu ekranu wybierz ikonę **grupy zasobów** . Następnie wybierz grupę zasobów, w której znajdują się maszyny wirtualne (na przykład **contosofabrikam**). W okienku **grupy zasobów** zostaną wyświetlone wszystkie zasoby i karty sieciowe dla maszyn wirtualnych.

3. W przypadku pomocniczej karty sieciowej dla każdej maszyny wirtualnej Dodaj konfigurację adresu IP:

    1. Wybierz pomocniczą kartę sieciową, którą chcesz skonfigurować.
    
    2. Wybierz pozycję **konfiguracje adresów IP**. W następnym okienku w górnej części strony wybierz pozycję **Dodaj**.

    3. W obszarze **Dodawanie konfiguracji adresów IP** Dodaj drugą konfigurację adresu IP do karty sieciowej: 

        1. Wprowadź nazwę pomocniczej konfiguracji adresu IP. (Na przykład w przypadku VM1 i VM2 należy odpowiednio nazwać konfigurację protokołu IP **VM1NIC2-ipconfig2** i **VM2NIC2-ipconfig2**).

        2. W polu **prywatny adres IP**, ustawienie **alokacji** wybierz pozycję **statyczny**.

        3. Wybierz przycisk **OK**.

Po zakończeniu drugiej konfiguracji protokołu IP dla pomocniczej karty sieciowej zostanie ona wyświetlona w obszarze Ustawienia **konfiguracji protokołu IP** dla danej karty sieciowej.

### <a name="step-2-create-the-load-balancer"></a>Krok 2. Tworzenie modułu równoważenia obciążenia

Utwórz moduł równoważenia obciążenia dla konfiguracji:

1. Przejdź do Azure Portal: https://portal.azure.com . Zaloguj się przy użyciu konta platformy Azure.

2. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów**  >    >  **Load Balancer**. Następnie wybierz pozycję **Utwórz**.

3. W obszarze **Tworzenie modułu równoważenia obciążenia** wpisz nazwę modułu równoważenia obciążenia. W tym scenariuszu używamy nazwy **mylb**.

4. W obszarze **publiczny adres IP** Utwórz nowy publiczny adres IP o nazwie **PublicIP1**.

5. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów dla maszyn wirtualnych (na przykład **contosofabrikam**). Wybierz lokalizację, w której ma zostać wdrożony moduł równoważenia obciążenia, a następnie wybierz przycisk **OK**.

Moduł równoważenia obciążenia rozpocznie wdrażanie. Wdrożenie może potrwać kilka minut. Po zakończeniu wdrażania moduł równoważenia obciążenia jest wyświetlany jako zasób w grupie zasobów.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Krok 3. Konfigurowanie puli adresów IP frontonu

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) skonfiguruj pulę adresów IP frontonu dla modułu równoważenia obciążenia:

1. W portalu wybierz pozycję **więcej usług**. W polu Filtr wpisz **publiczny adres IP** , a następnie wybierz pozycję **publiczne adresy IP**. W następnym okienku w górnej części strony wybierz pozycję **Dodaj**.

2. Skonfiguruj dwa publiczne adresy IP (**PublicIP1** i **PublicIP2**) dla obu witryn sieci web (contoso.com i fabrikam.com):

   1. Wpisz nazwę dla adresu IP frontonu.

   2. W obszarze **Grupa zasobów** wybierz istniejącą grupę zasobów dla swoich maszyn wirtualnych (na przykład **contosofabrikam**).

   3. W polu **Lokalizacja** wybierz tę samą lokalizację co maszyny wirtualne.

   4. Wybierz przycisk **OK**.

      Po utworzeniu publicznych adresów IP są one wyświetlane pod **publicznymi** adresami IP.

3. <a name="step3-3"></a>W portalu wybierz pozycję **więcej usług**. W polu Filtr wpisz **moduł równoważenia obciążenia** , a następnie wybierz pozycję **Load Balancer**. 

4. Wybierz moduł równoważenia obciążenia (**mylb**), do którego chcesz dodać pulę adresów IP frontonu.

5. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja adresu IP frontonu**. W następnym okienku w górnej części strony wybierz pozycję **Dodaj**.

6. Wpisz nazwę dla adresu IP frontonu (na przykład **contosofe** lub **fabrikamfe**).

7. <a name="step3-7"></a>Wybierz pozycję **adres IP**. W obszarze **Wybierz publiczny adres IP** wybierz adresy IP frontonu (**PublicIP1** lub **PublicIP2**).

8. Utwórz drugi adres IP frontonu, powtarzając <a href="#step3-3">krok 3</a> w <a href="#step3-7">kroku 7</a> w tej sekcji.

Po skonfigurowaniu puli frontonu adresy IP są wyświetlane w ustawieniach **konfiguracji adresu IP frontonu** modułu równoważenia obciążenia. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Krok 4. Konfigurowanie puli zaplecza

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) skonfiguruj pulę adresów zaplecza w module równoważenia obciążenia:
        
1. W portalu wybierz pozycję **więcej usług**. W polu Filtr wpisz **moduł równoważenia obciążenia** , a następnie wybierz pozycję **Load Balancer**.

2. Wybierz moduł równoważenia obciążenia (**mylb**), do którego chcesz dodać pulę zaplecza.

3. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**. Wpisz nazwę puli zaplecza (na przykład **contosopool** lub **fabrikampool**). W następnym okienku w górnej części strony wybierz pozycję **Dodaj**. 

4. W obszarze **skojarzone z** wybierz pozycję **zestaw dostępności**.

5. W obszarze **zestaw dostępności** wybierz pozycję **myAvailset**.

6. Dodaj docelowe konfiguracje IP sieci dla obu maszyn wirtualnych: 

    ![Konfigurowanie pul zaplecza dla modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Dla **docelowej maszyny wirtualnej** wybierz maszynę wirtualną, którą chcesz dodać do puli zaplecza (na przykład **VM1** lub **VM2**).

    2. W obszarze **Konfiguracja protokołu IP sieci** wybierz konfigurację IP pomocniczej karty sieciowej dla maszyny wirtualnej wybranej w poprzednim kroku (na przykład **VM1NIC2-ipconfig2** lub **VM2NIC2-ipconfig2**).

7. Wybierz przycisk **OK**.

Po skonfigurowaniu puli zaplecza adresy są wyświetlane w obszarze Ustawienia **puli zaplecza** modułu równoważenia obciążenia.

### <a name="step-5-configure-the-health-probe"></a>Krok 5. Konfigurowanie sondy kondycji

Konfigurowanie sondy kondycji dla modułu równoważenia obciążenia:

1. W portalu wybierz pozycję **więcej usług**. W polu Filtr wpisz **moduł równoważenia obciążenia** , a następnie wybierz pozycję **Load Balancer**.

2. Wybierz moduł równoważenia obciążenia (**mylb**), do którego chcesz dodać sondę kondycji.

3. W obszarze **Ustawienia** wybierz pozycję **sonda kondycji**. W następnym okienku w górnej części strony wybierz pozycję **Dodaj**. 

4. Wpisz nazwę sondy kondycji (na przykład **http**). Wybierz przycisk **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Krok 6. Konfigurowanie reguł równoważenia obciążenia

Dla każdej witryny sieci Web (contoso.com i fabrikam.com) Skonfiguruj reguły równoważenia obciążenia:
    
1. <a name="step6-1"></a>W obszarze **Ustawienia** wybierz pozycję **reguły równoważenia obciążenia**. W następnym okienku w górnej części strony wybierz pozycję **Dodaj**. 

2. W polu **Nazwa** wpisz nazwę reguły równoważenia obciążenia (na przykład **HTTPc** for contoso.com lub **HTTPf** for fabrikam.com).

3. W polu **adres IP frontonu** wybierz utworzony wcześniej adres IP frontonu (na przykład **contosofe** lub **fabrikamfe**).

4. W polu **port** i **port zaplecza** pozostaw wartość domyślną **80**.

5. Dla **zmiennoprzecinkowego adresu IP (bezpośredni zwrot serwera)** wybierz pozycję **wyłączone**.

6. <a name="step6-6"></a>Wybierz **przycisk OK**.

7. Utwórz drugą regułę modułu równoważenia obciążenia, powtarzając <a href="#step6-1">krok 1</a> przez <a href="#step6-6">krok 6</a> w tej sekcji.

Po skonfigurowaniu reguł zostaną one wyświetlone w obszarze Ustawienia **reguł równoważenia obciążenia** usługi równoważenia obciążenia.

### <a name="step-7-configure-dns-records"></a>Krok 7. Konfigurowanie rekordów DNS

Ostatnim krokiem jest skonfigurowanie rekordów zasobów DNS w taki sposób, aby wskazywały odpowiednie adresy IP frontonu dla modułu równoważenia obciążenia. Domeny można hostować w Azure DNS. Aby uzyskać więcej informacji o korzystaniu z Azure DNS z Load Balancer, zobacz [używanie Azure DNS z innymi usługami platformy Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat sposobu łączenia usług równoważenia obciążenia na platformie Azure [przy użyciu usług równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak za pomocą różnych typów dzienników zarządzać usługą równoważenia obciążenia i rozwiązywać problemy w [Azure monitor dziennikach Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).

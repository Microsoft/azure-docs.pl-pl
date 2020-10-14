---
title: Jak korzystać z funkcji publicznego adresu IP w wirtualnej sieci WAN
description: W tym artykule wyjaśniono, jak używać funkcji publicznego adresu IP w wirtualnej sieci WAN platformy Azure.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 61ed6487bc000a35fd25cabde2b562b6eb08da46
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048308"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-virtual-wan"></a>Jak korzystać z funkcji publicznego adresu IP w wirtualnej sieci WAN platformy Azure

Publiczny adres IP jest nową funkcją łączności rozwiązań VMware platformy Azure, a klienci mogą włączyć publiczny dostęp do Internetu na dwa sposoby. 

- Aplikacje mogą być hostowane i opublikowane w Application Gateway module równoważenia obciążenia dla ruchu HTTP/HTTPS.
- Opublikowany za pomocą funkcji publicznych adresów IP w wirtualnej sieci WAN platformy Azure.

W ramach wdrożenia chmury prywatnej platformy Azure, po włączeniu funkcji publicznego adresu IP, wymagane składniki z automatyzacją są tworzone i włączane automatycznie:

-  Virtual WAN

-  Wirtualne Centrum sieci WAN z łącznością ExpressRoute

-  Usługi zapory platformy Azure z publicznym adresem IP

W tym artykule szczegółowo opisano, jak używać funkcji publicznego adresu IP w wirtualnej sieci WAN do tworzenia zasobów, takich jak serwery sieci Web, maszyny wirtualne i hosty, dostępne za pośrednictwem sieci publicznej.

## <a name="prerequisites"></a>Wymagania wstępne

- Środowisko rozwiązań VMware platformy Azure
- Serwer sieci webdziałający w środowisku rozwiązań VMware platformy Azure.
- Nowy, nienakładający się zakres adresów IP dla wdrożenia wirtualnego koncentratora sieci WAN, zazwyczaj a `/24` .

## <a name="reference-architecture"></a>Architektura referencyjna

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagram architektury publicznego adresu IP" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Diagram architektury przedstawia serwer sieci webklienta hostowany w środowisku rozwiązań VMware platformy Azure i skonfigurowany z RFC1918 prywatnymi adresami IP.  Ta usługa sieci Web jest dostępna w Internecie za pomocą funkcji publicznego adresu IP sieci wirtualnej.  Publiczny adres IP to zazwyczaj docelowy translator adresów sieciowych przetłumaczony w zaporze platformy Azure. W przypadku reguł DNAT zasady zapory tłumaczą publiczne żądania adresów IP na adres prywatny (WebServer) z portem.

Żądania użytkownika trafią zaporę w publicznym adresie IP, który z kolei jest tłumaczony na prywatny adres IP przy użyciu reguł DNAT w zaporze platformy Azure. Zapora sprawdza tabelę NAT, a jeśli żądanie jest zgodne z wpisem, przekazuje ruch do przetłumaczonego adresu i portu w środowisku rozwiązań VMware platformy Azure.

Serwer sieci Web otrzymuje żądanie i odpowiedzi z żądanymi informacjami lub stroną zapory, a następnie Zapora przekazuje informacje do użytkownika na publicznym adresie IP.

## <a name="test-case"></a>Przypadek testowy
W tym scenariuszu należy opublikować serwer sieci webiis w Internecie. Aby opublikować witrynę sieci Web na publicznym adresie IP, użyj funkcji Public IP w rozwiązaniu VMware platformy Azure.  Skonfigurujemy reguły NAT w zaporze i dostęp do zasobu rozwiązania Azure VMware (maszyn wirtualnych z serwerem WebServer) przy użyciu publicznego adresu IP.

## <a name="deploy-virtual-wan"></a>Wdrożyć usługę Virtual WAN

1. Zaloguj się do Azure Portal, a następnie wyszukaj i wybierz pozycję **Azure VMware rozwiązanie**.

1. Wybierz chmurę prywatną rozwiązania VMware platformy Azure.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. W obszarze **Zarządzaj**wybierz pozycję **łączność**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Wybierz kartę **publiczny adres IP** , a następnie wybierz pozycję **Konfiguruj**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Zaakceptuj wartości domyślne lub zmień je, a następnie wybierz pozycję **Utwórz**.

   - Grupa zasobów wirtualnej sieci rozległej

   - Nazwa wirtualnej sieci rozległej

   - Blok adresów koncentratora wirtualnego (przy użyciu nowego nienakładających się zakresów adresów IP)

   - Liczba publicznych adresów IP (1-100)

Wdrożenie wszystkich składników trwa około godzinę. To wdrożenie musi wystąpić tylko raz, aby obsługiwać wszystkie przyszłe publiczne adresy IP dla tego środowiska rozwiązania VMware platformy Azure.  

>[!TIP]
>Stan można monitorować w obszarze **powiadomień** . 

## <a name="view-and-add-public-ip-addresses"></a>Wyświetlanie i Dodawanie publicznych adresów IP

Możemy sprawdzić i dodać więcej publicznych adresów IP, wykonując poniższe kroki.

1. W Azure Portal Wyszukaj i wybierz opcję **Zapora**.

1. Wybierz wdrożoną zaporę, a następnie wybierz pozycję **odwiedź Menedżera zapory platformy Azure, aby skonfigurować tę zaporę i zarządzać nią**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Wybierz pozycję **zabezpieczone centra wirtualne** , a następnie z listy wybierz koncentrator wirtualny.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Diagram architektury publicznego adresu IP" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Na stronie koncentrator wirtualny wybierz pozycję **Konfiguracja publicznego adresu IP**i Dodaj więcej publicznych adresów IP, a następnie wybierz pozycję **Dodaj**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Podaj liczbę wymaganych adresów IP i wybierz pozycję **Dodaj**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Diagram architektury publicznego adresu IP" border="true":::


## <a name="create-firewall-policies"></a>Tworzenie zasad zapory

Po wdrożeniu wszystkich składników można je wyświetlić w dodanej grupie zasobów. Następnym krokiem jest dodanie zasad zapory.

1. W Azure Portal Wyszukaj i wybierz opcję **Zapora**.

1. Wybierz wdrożoną zaporę, a następnie wybierz pozycję **odwiedź Menedżera zapory platformy Azure, aby skonfigurować tę zaporę i zarządzać nią**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Wybierz pozycję **zasady zapory platformy Azure** , a następnie wybierz pozycję **Utwórz zasady zapory platformy Azure**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Na karcie **podstawy** podaj wymagane szczegóły i wybierz pozycję **Dalej: ustawienia DNS**. 

1. Na karcie **DNS** wybierz pozycję **Disable (Wyłącz**), a następnie wybierz pozycję Next ( **Dalej): Rules**.

1. Wybierz pozycję **Dodaj kolekcję reguł**, podaj poniżej szczegóły i wybierz pozycję **Dodaj** , a następnie wybierz pozycję **Dalej: analiza zagrożeń**.

   -  Nazwa
   -  Typ kolekcji reguł — DNAT
   -  Priorytet
   -  Akcja zbierania reguł — Zezwalaj
   -  Nazwa reguły
   -  Typ źródła — **adres IP**
   -  Zewnętrz **\***
   -  Protokół — **TCP**
   -  Port docelowy — **80**
   -  Typ docelowy — **adres IP**
   -  Miejsce docelowe — **publiczny adres IP**
   -  Przetłumaczony adres — **prywatny adres IP serwera sieci Web rozwiązania Azure VMware**
   -  Przetłumaczony port — **port serwera sieci Web rozwiązania VMware platformy Azure**

1. Pozostaw wartość domyślną, a następnie wybierz pozycję **Next (dalej): Hubs**.

1. Wybierz pozycję **Skojarz koncentrator wirtualny**.

   :::image type="content" source="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png":::

1. Wybierz centrum z listy i wybierz pozycję **Dodaj**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Diagram architektury publicznego adresu IP" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Wybierz pozycję **Dalej: Tagi**. 

1. Obowiązkowe Utwórz pary nazwa/wartość, aby sklasyfikować zasoby. 

1. Wybierz pozycję **Dalej: przegląd + Utwórz** , a następnie wybierz pozycję **Utwórz**.

## <a name="limitations"></a>Ograniczenia

Możesz mieć 100 publicznych adresów IP na SDDCs.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o korzystaniu z publicznych adresów IP przy użyciu [wirtualnej sieci WAN platformy Azure](../virtual-wan/virtual-wan-about.md).


---
title: Jak korzystać z funkcji publicznego adresu IP w rozwiązaniu Azure VMware
description: W tym artykule wyjaśniono, jak używać funkcji publicznego adresu IP w wirtualnej sieci WAN platformy Azure.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 794e24e531d464adf58d5a06b5a411ada18c4a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023660"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>Jak korzystać z funkcji publicznego adresu IP w rozwiązaniu Azure VMware

Publiczny adres IP to nowa funkcja łączności z rozwiązaniem VMware platformy Azure. Udostępnia ona zasoby, takie jak serwery sieci Web, maszyny wirtualne i hosty dostępne za pośrednictwem sieci publicznej. 

Publiczny dostęp do Internetu można włączyć na dwa sposoby. 

- Aplikacje mogą być hostowane i opublikowane w Application Gateway module równoważenia obciążenia dla ruchu HTTP/HTTPS.
- Opublikowany za pomocą funkcji publicznych adresów IP w wirtualnej sieci WAN platformy Azure.

W ramach wdrożenia chmury prywatnej na platformie Azure, po włączeniu funkcji publicznego adresu IP, wymagane składniki z automatyzacją i są włączone:

-  Wirtualna sieć WAN

-  Wirtualne Centrum sieci WAN z łącznością ExpressRoute

-  Usługi zapory platformy Azure z publicznym adresem IP

W tym artykule szczegółowo przedstawiono sposób korzystania z funkcji publicznego adresu IP w wirtualnej sieci WAN.

## <a name="prerequisites"></a>Wymagania wstępne

- Środowisko rozwiązań VMware platformy Azure
- Serwer sieci webdziałający w środowisku rozwiązań VMware platformy Azure.
- Nowy, nienakładający się zakres adresów IP dla wdrożenia wirtualnego koncentratora sieci WAN, zazwyczaj a `/24` .

## <a name="reference-architecture"></a>Architektura referencyjna

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Diagram architektury publicznego adresu IP" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Diagram architektury przedstawia serwer sieci Web hostowany w środowisku rozwiązań VMware platformy Azure i skonfigurowany z RFC1918 prywatnymi adresami IP.  Usługa sieci Web jest dostępna w Internecie za pomocą funkcji publicznych adresów IP w sieci wirtualnej.  Publiczny adres IP to zazwyczaj docelowy translator adresów sieciowych przetłumaczony w zaporze platformy Azure. W przypadku reguł DNAT zasady zapory tłumaczą publiczne żądania adresów IP na adres prywatny (WebServer) z portem.

Żądania użytkownika trafią zaporę w publicznym adresie IP, który z kolei jest tłumaczony na prywatny adres IP przy użyciu reguł DNAT w zaporze platformy Azure. Zapora sprawdza tabelę NAT, a jeśli żądanie jest zgodne z wpisem, przekazuje ruch do przetłumaczonego adresu i portu w środowisku rozwiązań VMware platformy Azure.

Serwer sieci Web otrzymuje żądanie i odpowiedzi z żądanymi informacjami lub stroną zapory, a następnie Zapora przekazuje informacje do użytkownika na publicznym adresie IP.

## <a name="test-case"></a>Przypadek testowy
W tym scenariuszu opublikujesz serwer sieci webiis w Internecie. Aby opublikować witrynę sieci Web na publicznym adresie IP, użyj funkcji Public IP w rozwiązaniu VMware platformy Azure.  Należy również skonfigurować reguły NAT w zaporze i uzyskać dostęp do zasobu rozwiązania Azure VMware (maszyn wirtualnych z serwerem sieci Web) za pomocą publicznego adresu IP.

>[!TIP]
>Aby włączyć ruch wychodzący, należy ustawić konfigurację zabezpieczeń > ruchu internetowego do **zapory platformy Azure**.

## <a name="deploy-virtual-wan"></a>Wdrożyć usługę Virtual WAN

1. Zaloguj się do Azure Portal, a następnie wyszukaj i wybierz pozycję **Azure VMware rozwiązanie**.

1. Wybierz chmurę prywatną rozwiązania VMware platformy Azure.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Zrzut ekranu przedstawiający chmurę prywatną rozwiązania Azure VMware." border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. W obszarze **Zarządzaj** wybierz pozycję **łączność**.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Zrzut ekranu przedstawiający sekcję łączność." border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Wybierz kartę **publiczny adres IP** , a następnie wybierz pozycję **Konfiguruj**.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Zrzut ekranu pokazujący, gdzie rozpocząć konfigurowanie publicznego adresu IP" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. Zaakceptuj wartości domyślne lub zmień je, a następnie wybierz pozycję **Utwórz**.

   - Grupa zasobów wirtualnej sieci WAN

   - Nazwa wirtualnej sieci WAN

   - Blok adresów koncentratora wirtualnego (przy użyciu nowego nienakładających się zakresów adresów IP)

   - Liczba publicznych adresów IP (1-100)

Wdrożenie wszystkich składników trwa około godzinę. To wdrożenie musi wystąpić tylko raz, aby obsługiwać wszystkie przyszłe publiczne adresy IP dla tego środowiska rozwiązania VMware platformy Azure.  

>[!TIP]
>Stan można monitorować w obszarze **powiadomień** . 

## <a name="view-and-add-public-ip-addresses"></a>Wyświetlanie i Dodawanie publicznych adresów IP

Możemy sprawdzić i dodać więcej publicznych adresów IP, wykonując poniższe kroki.

1. W Azure Portal Wyszukaj i wybierz opcję **Zapora**.

1. Wybierz wdrożoną zaporę, a następnie wybierz pozycję **odwiedź Menedżera zapory platformy Azure, aby skonfigurować tę zaporę i zarządzać nią**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Zrzut ekranu przedstawiający opcję konfigurowania zapory i zarządzania nią" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Wybierz pozycję **zabezpieczone centra wirtualne** , a następnie z listy wybierz koncentrator wirtualny.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Zrzut ekranu Menedżera zapory" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. Na stronie koncentrator wirtualny wybierz pozycję **Konfiguracja publicznego adresu IP** i Dodaj więcej publicznych adresów IP, a następnie wybierz pozycję **Dodaj**. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania konfiguracji publicznego adresu IP w Menedżerze zapory" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Podaj liczbę wymaganych adresów IP i wybierz pozycję **Dodaj**.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Zrzut ekranu, aby dodać określoną liczbę konfiguracji publicznych adresów IP" border="true":::


## <a name="create-firewall-policies"></a>Tworzenie zasad zapory

Po wdrożeniu wszystkich składników można je wyświetlić w dodanej grupie zasobów. Następnym krokiem jest dodanie zasad zapory.

1. W Azure Portal Wyszukaj i wybierz opcję **Zapora**.

1. Wybierz wdrożoną zaporę, a następnie wybierz pozycję **odwiedź Menedżera zapory platformy Azure, aby skonfigurować tę zaporę i zarządzać nią**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Zrzut ekranu przedstawiający opcję konfigurowania zapory i zarządzania nią" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Wybierz pozycję **zasady zapory platformy Azure** , a następnie wybierz pozycję **Utwórz zasady zapory platformy Azure**.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia zasad zapory w Menedżerze zapory" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Na karcie **podstawy** podaj wymagane szczegóły i wybierz pozycję **Dalej: ustawienia DNS**. 

1. Na karcie **DNS** wybierz pozycję **Disable (Wyłącz**), a następnie wybierz pozycję Next ( **Dalej): Rules**.

1. Wybierz pozycję **Dodaj kolekcję reguł**, podaj poniżej szczegóły, a następnie wybierz pozycję **Dodaj** , a następnie wybierz pozycję **Dalej: analiza zagrożeń**.

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

1. Wybierz centrum z listy i wybierz pozycję **Dodaj**.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Zrzut ekranu pokazujący wybrane centra, które zostaną przekonwertowane na zabezpieczone centra wirtualne." border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Wybierz pozycję **Dalej: Tagi**. 

1. Obowiązkowe Utwórz pary nazw i wartości, aby klasyfikować zasoby. 

1. Wybierz pozycję **Dalej: przegląd + Utwórz** , a następnie wybierz pozycję **Utwórz**.

## <a name="limitations"></a>Ograniczenia

Możesz mieć 100 publicznych adresów IP na chmurę prywatną.

## <a name="next-steps"></a>Następne kroki

Teraz, w jaki sposób można korzystać z funkcji publicznego adresu IP w rozwiązaniu Azure VMware, warto zapoznać się z tematem:

- Używanie publicznych adresów IP z [wirtualną siecią WAN platformy Azure](../virtual-wan/virtual-wan-about.md).
- [Tworzenie tunelu IPSec w rozwiązaniu VMware platformy Azure](create-ipsec-tunnel.md).

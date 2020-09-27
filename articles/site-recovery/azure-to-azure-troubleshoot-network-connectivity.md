---
title: Rozwiązywanie problemów z łącznością z platformą Azure do odzyskiwania po awarii platformy Azure z Azure Site Recovery
description: Rozwiązywanie problemów z łącznością w przypadku odzyskiwania po awarii maszyny wirtualnej platformy Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 59bbca9461ff174ebe2451a6c01d84dee404cf56
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398310"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Rozwiązywanie problemów z łącznością sieciową na platformie Azure na platformie Azure

W tym artykule opisano typowe problemy związane z łącznością sieciową w przypadku replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego. Więcej informacji o wymaganiach dotyczących sieci znajduje się w temacie [wymagania dotyczące łączności w przypadku replikacji maszyn wirtualnych platformy Azure](azure-to-azure-about-networking.md).

Aby replikacja Site Recovery działała, do maszyny wirtualnej wymagane jest połączenie wychodzące z określonymi adresami URL lub zakresami adresów IP. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) do kontrolowania łączności wychodzącej, może to być przyczyną jednego z tych problemów.

| **Nazwa**                  | **Commercial**                               | **Instytucje rządowe**                                 | **Opis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Wymagane, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z poziomu maszyny wirtualnej. Jeśli znasz wszystkie konta magazynu pamięci podręcznej dla maszyn wirtualnych, możesz użyć listy dozwolonych adresów URL dla określonych kont magazynu. Na przykład, `cache1.blob.core.windows.net` a `cache2.blob.core.windows.net` nie `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Wymagany do autoryzacji i uwierzytelniania do adresów URL usługi Site Recovery. |
| Replikacja               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Wymagane, aby komunikacja z usługą Site Recovery mogła się odbywać z poziomu maszyny wirtualnej. Można użyć odpowiedniego _adresu IP Site Recovery_ , jeśli serwer proxy zapory obsługuje adresy IP. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Wymagane, aby dane dotyczące monitorowania i diagnostyki Site Recovery mogły być zapisywane z poziomu maszyny wirtualnej. W przypadku, gdy serwer proxy zapory obsługuje adresy IP, można użyć odpowiedniego _Site Recovery monitorowania_ . |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Łączność wychodząca dla adresów URL Site Recovery lub zakresów adresów IP (kod błędu 151037 lub 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problem 1: nie można zarejestrować maszyny wirtualnej platformy Azure w Site Recovery (151195)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można nawiązać połączenia z Site Recovery punktami końcowymi z powodu błędu rozpoznawania systemu nazw domen (DNS). Ten problem jest bardziej typowy w przypadku przełączenia w tryb failover maszyny wirtualnej, ale serwer DNS nie jest dostępny z regionu odzyskiwania po awarii (DR).

#### <a name="resolution"></a>Rozwiązanie

W przypadku korzystania z niestandardowej usługi DNS upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii.

Aby sprawdzić, czy maszyna wirtualna używa niestandardowego ustawienia DNS:

1. Otwórz **maszyn wirtualnych** i wybierz maszynę wirtualną.
1. Przejdź do **ustawień** maszyny wirtualne i wybierz pozycję **Sieć**.
1. W obszarze **Sieć wirtualna/podsieć**wybierz link, aby otworzyć stronę zasobów sieci wirtualnej.
1. Przejdź do pozycji **Ustawienia** i wybierz pozycję **serwery DNS**.

Spróbuj uzyskać dostęp do serwera DNS z maszyny wirtualnej. Jeśli serwer DNS nie jest dostępny, udostępnij go przez przechodzenie przez serwer DNS w tryb failover lub Tworzenie linii lokacji między siecią DR i systemem DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com — błąd":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja Site Recovery nie powiodła się (151196)

> [!NOTE]
> Jeśli maszyny wirtualne znajdują się za **standardowym** wewnętrznym modułem równoważenia obciążenia, domyślnie nie będą miały dostępu do Microsoft 365 adresów IP, takich jak `login.microsoftonline.com` . Zmień go na **podstawowy** typ wewnętrznego modułu równoważenia obciążenia lub Utwórz dostęp wychodzący, jak wspomniano w artykule [Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu interfejsu wiersza polecenia platformy Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration).

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można nawiązać połączenia w celu Microsoft 365nia punktów końcowych uwierzytelniania i tożsamości.

#### <a name="resolution"></a>Rozwiązanie

- Azure Site Recovery wymaga dostępu do Microsoft 365 zakresów adresów IP na potrzeby uwierzytelniania.
- Jeśli używasz zasad grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń) platformy Azure/serwera proxy zapory do kontrolowania łączności sieciowej wychodzącej na maszynie wirtualnej, upewnij się, że zezwalasz na komunikację z Microsoft 365 zakresami adresów IP. Azure Active Directory utwórz sieciowej grupy zabezpieczeń regułę opartą na [tagu usług (Azure AD)](../virtual-network/security-overview.md#service-tags) , która umożliwia dostęp do wszystkich adresów IP odpowiadających usłudze Azure AD.
- Jeśli nowe adresy są dodawane do usługi Azure AD w przyszłości, należy utworzyć nowe reguły sieciowej grupy zabezpieczeń.

### <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

Ten przykład pokazuje, jak skonfigurować reguły sieciowej grupy zabezpieczeń dla maszyny wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, użyj opcji Zezwalaj na używanie reguł **wychodzących https** do portu 443 dla wszystkich wymaganych zakresów adresów IP.
- Przykład zakłada, że lokalizacja źródłowa maszyny wirtualnej to **Wschodnie stany USA** , a lokalizacja docelowa to **środkowe stany USA**.

#### <a name="nsg-rules---east-us"></a>Reguły sieciowej grupy zabezpieczeń — Wschodnie stany USA

1. Utwórz regułę protokołu HTTPS dla ruchu wychodzącego dla sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu. W tym przykładzie jest użyty **tag usługi docelowej**: _Magazyn. Wschodnie_ i **docelowe zakresy portów**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Zrzut ekranu przedstawia okienko Dodawanie reguły zabezpieczeń dla ruchu wychodzącego dla reguły zabezpieczeń dla usługi Storage w regionach wschód U S.":::

1. Utwórz regułę protokołu HTTPS dla ruchu wychodzącego dla sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu. W tym przykładzie używa **znacznika usługi docelowej**: _usługi azureactivedirectory_ i **docelowy zakres portów**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="Zrzut ekranu przedstawia okienko Dodawanie reguły zabezpieczeń dla ruchu wychodzącego dla reguły zabezpieczeń dla Azure Active Directory.":::

1. Podobnie jak w przypadku powyższych reguł zabezpieczeń, Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "EventHub. środkowe" w sieciowej grupy zabezpieczeń, który odpowiada lokalizacji docelowej. Pozwala to na dostęp do monitorowania Site Recovery.
1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "AzureSiteRecovery" w sieciowej grupy zabezpieczeń. Pozwala to na dostęp do usługi Site Recovery w dowolnym regionie.

#### <a name="nsg-rules---central-us"></a>Reguły sieciowej grupy zabezpieczeń — środkowe stany USA

Na potrzeby tego przykładu reguły sieciowej grupy zabezpieczeń są wymagane, aby można było włączyć replikację z regionu docelowego do trybu failover w regionie źródłowym:

1. Utwórz regułę protokołu HTTPS dla ruchu wychodzącego dla _magazynu. środkowe_:

   - **Tag usługi docelowej**: _Storage. środkowe_
   - **Docelowe zakresy portów**: _443_

1. Utwórz regułę protokołu HTTPS dla ruchu wychodzącego dla _usługi azureactivedirectory_.

   - **Tag usługi docelowej**: _usługi azureactivedirectory_
   - **Docelowe zakresy portów**: _443_

1. Podobnie jak powyżej reguły zabezpieczeń, Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "EventHub. Wschód" w sieciowej grupy zabezpieczeń, który odpowiada lokalizacji źródłowej. Pozwala to na dostęp do monitorowania Site Recovery.
1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "AzureSiteRecovery" w sieciowej grupy zabezpieczeń. Pozwala to na dostęp do usługi Site Recovery w dowolnym regionie.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja Site Recovery nie powiodła się (151197)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można nawiązać połączenia, aby Azure Site Recovery punkty końcowe usługi.

#### <a name="resolution"></a>Rozwiązanie

Jeśli używasz reguły/serwera proxy zapory sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) platformy Azure do sterowania łącznością sieci wychodzącej na maszynie, istnieje kilka tagów usługi, które muszą być dozwolone. [Dowiedz się więcej](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: replikacja z platformy Azure do platformy Azure nie powiodła się, gdy ruch sieciowy przechodzi przez lokalny serwer proxy (151072)

#### <a name="possible-cause"></a>Możliwa przyczyna

Niestandardowe ustawienia serwera proxy są nieprawidłowe, a Agent usługi mobilności Azure Site Recovery nie wykrył automatycznie ustawień serwera proxy w programie Internet Explorer (IE).

#### <a name="resolution"></a>Rozwiązanie

1. Agent usługi mobilności wykrywa ustawienia serwera proxy z programu IE w systemach Windows i `/etc/environment` Linux.
1. Jeśli wolisz ustawić serwer proxy tylko dla usługi mobilności Azure Site Recovery, możesz podać szczegóły serwera proxy w _ProxyInfo. conf_ znajdującym się w:

   - System **Linux**:`/usr/local/InMage/config/`
   - **System Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo. conf_ powinna mieć ustawienia serwera proxy w następującym formacie _ini_ :

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Agent usługi mobilności Azure Site Recovery obsługuje tylko **nieuwierzytelnione serwery proxy**.

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Aby zezwolić na [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags), wykonaj kroki opisane w [dokumencie wskazówki dotyczące sieci](./azure-to-azure-about-networking.md).

## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure](azure-to-azure-how-to-enable-replication.md)
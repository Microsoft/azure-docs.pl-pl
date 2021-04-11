---
title: Używanie zapory platformy Azure do ochrony pulpitu wirtualnego systemu Windows
description: Dowiedz się, jak używać zapory platformy Azure do ochrony wdrożeń pulpitów wirtualnych systemu Windows
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: d5320f44aa5d922cea852ab09e5141fad277e2b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566030"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Używanie usługi Azure Firewall do chronienia wdrożeń pulpitów wirtualnych systemu Windows

Windows Virtual Desktop to usługa wirtualizacji pulpitu i aplikacji działająca na platformie Azure. Gdy użytkownik końcowy nawiązuje połączenie ze środowiskiem pulpitu wirtualnego systemu Windows, jego sesja jest uruchamiana przez pulę hostów. Pula hostów jest kolekcją maszyn wirtualnych platformy Azure, które są rejestrowane na pulpicie wirtualnym systemu Windows jako hosty sesji. Te maszyny wirtualne działają w sieci wirtualnej i podlegają kontrolkom zabezpieczeń sieci wirtualnej. Potrzebują one wychodzącego dostępu do Internetu do usługi pulpitów wirtualnych systemu Windows, aby działać prawidłowo i mogą również potrzebować wychodzącego dostępu do Internetu dla użytkowników końcowych. Zapora platformy Azure może pomóc w zablokowaniu środowiska i przefiltrowania ruchu wychodzącego.

[![Architektura ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) pulpitu wirtualnego systemu Windows](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Postępuj zgodnie z wytycznymi w tym artykule, aby zapewnić dodatkową ochronę puli hostów usług pulpitu wirtualnego systemu Windows przy użyciu zapory platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne


 - Wdrożenie środowiska pulpitu wirtualnego systemu Windows i puli hostów.

   Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) i [Tworzenie puli hostów przy użyciu szablonu Azure Resource Manager](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md).

Aby dowiedzieć się więcej na temat środowisk pulpitu wirtualnego systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Dostęp wychodzący puli hostów do pulpitu wirtualnego systemu Windows

Maszyny wirtualne platformy Azure tworzone dla pulpitu wirtualnego systemu Windows muszą mieć dostęp do kilku w pełni kwalifikowanych nazw domen (FQDN), aby działać prawidłowo. Zapora platformy Azure udostępnia tag nazwy FQDN pulpitu wirtualnego systemu Windows, aby uprościć tę konfigurację. Wykonaj następujące kroki, aby zezwolić na ruch wychodzący platformy pulpitu wirtualnego systemu Windows:

- Wdróż zaporę platformy Azure i skonfiguruj pulę hostów z systemem Windows Virtual Machine Routing (UDR), aby skierować cały ruch za pośrednictwem zapory platformy Azure. Domyślna trasa wskazuje teraz na zaporę.
- Utwórz kolekcję reguł aplikacji i Dodaj regułę, aby włączyć tag *WindowsVirtualDesktop* FQDN. Zakresem źródłowych adresów IP jest sieć wirtualna puli hostów, protokół to **https**, a lokalizacja docelowa to **WindowsVirtualDesktop**.

- Zestaw wymaganych kont magazynu i usługi Service Bus dla puli hostów usług pulpitu wirtualnego systemu Windows jest specyficzny dla wdrożenia, dlatego nie jest jeszcze przechwytywany w tagu WindowsVirtualDesktop FQDN. Można to rozwiązać w jeden z następujących sposobów:

   - Zezwalaj na dostęp HTTPS z podsieci puli hostów do * xt.blob.core.windows.net, * eh.servicebus.windows.net i * xt.table.core.windows.net. Te symbole wieloznaczne umożliwiają dostęp, ale są mniej restrykcyjne.
   - Użyj poniższego zapytania usługi log Analytics, aby wyświetlić dokładnie wymagane nazwy FQDN, a następnie jawnie je włączyć w regułach aplikacji zapory:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Utwórz kolekcję reguł sieci Dodaj następujące reguły:

   - Zezwalaj na system DNS — Zezwalanie na ruch z dodanego prywatnego adresu IP do * dla portów TCP i UDP 53.
   - Zezwalaj na usługę KMS — Zezwalanie na ruch z maszyn wirtualnych z systemem Windows Virtual Machines do usługi aktywacji systemu Windows portu TCP 1688. Aby uzyskać więcej informacji na temat docelowych adresów IP, zobacz [Aktywacja systemu Windows kończy się niepowodzeniem w scenariuszu wymuszonego tunelowania](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution).

> [!NOTE]
> Niektóre wdrożenia mogą nie potrzebować reguł DNS, na przykład Azure Active Directory kontrolery domeny przekazują zapytania DNS do Azure DNS w 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Dostęp wychodzący puli hostów do Internetu

W zależności od potrzeb organizacji warto włączyć bezpieczny wychodzący dostęp do Internetu dla użytkowników końcowych. W przypadkach, gdy lista dozwolonych miejsc docelowych jest dobrze zdefiniowana (na przykład [Microsoft 365 Access](/microsoft-365/enterprise/microsoft-365-ip-web-service)), w celu skonfigurowania wymaganego dostępu można użyć aplikacji zapory platformy Azure i reguł sieci. To kieruje ruch użytkowników końcowych bezpośrednio do Internetu w celu uzyskania najlepszej wydajności.

Jeśli chcesz odfiltrować ruch wychodzący z Internetu użytkownika przy użyciu istniejącej lokalnej bezpiecznej bramy sieci Web, możesz skonfigurować przeglądarki sieci Web lub inne aplikacje działające w puli hostów usług pulpitu wirtualnego systemu Windows z jawną konfiguracją serwera proxy. Na przykład zapoznaj się z tematem [jak korzystać z opcji wiersza polecenia przeglądarki Microsoft Edge, aby skonfigurować ustawienia serwera proxy](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Te ustawienia serwera proxy mają wpływ tylko na dostęp do Internetu użytkowników końcowych, co umożliwia wychodzący ruch platformy pulpitu wirtualnego systemu Windows bezpośrednio za pośrednictwem zapory platformy Azure.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Może być konieczne skonfigurowanie dodatkowych reguł zapory, w zależności od wymagań:

- Dostęp do serwera NTP

   Domyślnie maszyny wirtualne z systemem Windows łączą się z time.windows.com za pośrednictwem portu UDP 123 na potrzeby synchronizacji czasu. Utwórz regułę sieci, aby zezwolić na dostęp, lub na serwer czasu używany w danym środowisku.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat pulpitu wirtualnego systemu Windows: [co to jest pulpit wirtualny systemu Windows?](../virtual-desktop/overview.md)
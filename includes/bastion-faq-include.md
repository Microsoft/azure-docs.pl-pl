---
title: plik dołączany
description: plik dołączany
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: efb32631c5ee1eedece6d2a06b94702b602ed418
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86276122"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Które regiony są dostępne?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Czy na mojej maszynie wirtualnej jest potrzebny publiczny adres IP?

Po nawiązaniu połączenia z maszyną wirtualną za pomocą usługi Azure bastionu nie potrzebujesz publicznego adresu IP na maszynie wirtualnej platformy Azure, z którą nawiązujesz połączenie. Usługa bastionu otworzy sesję/połączenie RDP/SSH z maszyną wirtualną za pośrednictwem prywatnego adresu IP maszyny wirtualnej w sieci wirtualnej.

### <a name="is-ipv6-supported"></a>Czy jest obsługiwany protokół IPv6?

W tej chwili protokół IPv6 nie jest obsługiwany. Usługa Azure bastionu obsługuje tylko protokół IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Czy potrzebuję klienta RDP lub SSH?

Nie jest potrzebny klient RDP lub SSH do uzyskiwania dostępu do maszyny wirtualnej platformy Azure w Azure Portal. Użyj [Azure Portal](https://portal.azure.com) , aby zezwolić na dostęp do maszyny wirtualnej za pośrednictwem protokołu RDP/SSH bezpośrednio w przeglądarce.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Czy potrzebuję agenta uruchomionego na maszynie wirtualnej platformy Azure?

Nie musisz instalować agenta ani żadnego oprogramowania w przeglądarce ani na maszynie wirtualnej platformy Azure. Usługa bastionu jest bez agentów i nie wymaga dodatkowego oprogramowania dla protokołu RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Ile współbieżnych sesji protokołu RDP i SSH wykonuje każda pomoc techniczna platformy Azure bastionu?

Protokoły RDP i SSH są protokołem opartym na użyciu. Wysokie użycie sesji spowoduje, że host bastionu będzie obsługiwał niższą łączną liczbę sesji. Poniższe wartości liczbowe zakładają normalne dzienne przepływy pracy.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Jakie funkcje są obsługiwane w sesji protokołu RDP?

Obecnie obsługiwane jest tylko kopiowanie/wklejanie tekstu. Funkcje takie jak kopiowanie plików nie są obsługiwane. Możesz przekazać nam swoją opinię na temat nowych funkcji na [stronie opinii o usłudze Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Które przeglądarki są obsługiwane?

Użyj przeglądarki Microsoft Edge lub Google Chrome w systemie Windows. W przypadku komputerów Mac firmy Apple należy użyć przeglądarki Google Chrome. Program Microsoft Edge chrom jest również obsługiwany odpowiednio w systemach Windows i Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Gdzie usługa Azure bastionu przechowuje dane klienta?

Usługa Azure bastionu nie przenosi ani nie zapisuje danych klienta z regionu, w którym jest wdrożony.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Czy wszystkie role są wymagane do uzyskania dostępu do maszyny wirtualnej?

Aby można było nawiązać połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika w zasobie Azure bastionu

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Jakie są ceny?

Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Czy usługa Azure bastionu wymaga RDS CAL do celów administracyjnych na maszynach wirtualnych hostowanych na platformie Azure?
Nie, dostęp do maszyn wirtualnych z systemem Windows Server za pomocą usługi Azure bastionu nie wymaga [RDS CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) , gdy jest używany wyłącznie do celów administracyjnych.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Jakie układy klawiatury są obsługiwane podczas sesji zdalnej bastionu?

Usługa Azure bastionu obecnie obsługuje układ klawiatury en-us-QWERTY wewnątrz maszyny wirtualnej.  Obsługa innych ustawień regionalnych układu klawiatury jest w toku.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Czy zdefiniowane przez użytkownika Routing (UDR) jest obsługiwany w podsieci usługi Azure bastionu?

Nie. UDR nie jest obsługiwana w podsieci bastionu platformy Azure.
W przypadku scenariuszy obejmujących zarówno usługę Azure bastionu, jak i zaporę sieci wirtualnej (urządzenie WUS) w tej samej sieci wirtualnej, nie trzeba wymuszać ruchu z podsieci bastionu platformy Azure do zapory platformy Azure, ponieważ komunikacja między usługą Azure bastionu i maszynami wirtualnymi jest prywatna. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do maszyn wirtualnych za zaporą platformy Azure za pomocą bastionu](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Dlaczego otrzymuję komunikat o błędzie "sesja wygasła" przed rozpoczęciem sesji bastionu?

Sesja powinna zostać zainicjowana tylko z Azure Portal. Zaloguj się do Azure Portal i ponownie Rozpocznij sesję. Jeśli przejdziesz do adresu URL bezpośrednio z innej sesji lub karty przeglądarki, ten błąd jest oczekiwany. Gwarantuje to, że sesja jest bezpieczniejsza i dostęp do sesji można uzyskać tylko za pomocą Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Jak mogę obsługiwać błędy wdrażania?

Przejrzyj wszystkie komunikaty o błędach i [Zgłoś żądanie pomocy technicznej w Azure Portal zgodnie z](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) wymaganiami. Awarie wdrożenia mogą wynikać z [limitów subskrypcji platformy Azure, przydziałów i ograniczeń](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). W tym celu klienci mogą napotkać limit liczby publicznych adresów IP dozwolonych na subskrypcję, która powoduje niepowodzenie wdrożenia usługi Azure bastionu.

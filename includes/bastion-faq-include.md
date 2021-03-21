---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ba0e7173c41e26a698596fa18bf1fc1453f3fb3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628926"
---
### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Czy do nawiązania połączenia za pośrednictwem usługi Azure bastionu potrzebny jest publiczny adres IP na mojej maszynie wirtualnej?

Nie. Po nawiązaniu połączenia z maszyną wirtualną za pomocą usługi Azure bastionu nie potrzebujesz publicznego adresu IP na maszynie wirtualnej platformy Azure, z którą nawiązujesz połączenie. Usługa bastionu otworzy sesję/połączenie RDP/SSH z maszyną wirtualną za pośrednictwem prywatnego adresu IP maszyny wirtualnej w sieci wirtualnej.

### <a name="is-ipv6-supported"></a>Czy jest obsługiwany protokół IPv6?

W tej chwili protokół IPv6 nie jest obsługiwany. Usługa Azure bastionu obsługuje tylko protokół IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Czy potrzebuję klienta RDP lub SSH?

Nie. Klient protokołu RDP lub SSH nie jest potrzebny do uzyskania dostępu do maszyny wirtualnej platformy Azure w Azure Portal. Użyj [Azure Portal](https://portal.azure.com) , aby zezwolić na dostęp do maszyny wirtualnej za pośrednictwem protokołu RDP/SSH bezpośrednio w przeglądarce.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Czy potrzebuję agenta uruchomionego na maszynie wirtualnej platformy Azure?

Nie. Nie musisz instalować agenta ani żadnego oprogramowania w przeglądarce ani na maszynie wirtualnej platformy Azure. Usługa bastionu jest bez agentów i nie wymaga dodatkowego oprogramowania dla protokołu RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Ile współbieżnych sesji protokołu RDP i SSH wykonuje każda pomoc techniczna platformy Azure bastionu?

Protokoły RDP i SSH są protokołem opartym na użyciu. Wysokie użycie sesji spowoduje, że host bastionu będzie obsługiwał niższą łączną liczbę sesji. Poniższe wartości liczbowe zakładają normalne dzienne przepływy pracy.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Jakie funkcje są obsługiwane w sesji protokołu RDP?

Obecnie obsługiwane jest tylko kopiowanie/wklejanie tekstu. Funkcje, takie jak kopiowanie plików, nie są obsługiwane. Możesz bezpłatnie podzielić się swoją opinią na temat nowych funkcji na [stronie opinii o usłudze Azure bastionu](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Czy bastionu zapewnianie zaostrzonych zadań z maszynami wirtualnymi AADJ dołączonymi do rozszerzenia?

Ta funkcja nie działa z komputerami przyłączonymi do rozszerzenia maszyny wirtualnej AADJ przy użyciu użytkowników usługi Azure AD. Aby uzyskać więcej informacji, zobacz [maszyny wirtualne platformy Microsoft Azure i usługi Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Które przeglądarki są obsługiwane?

Użyj przeglądarki Microsoft Edge lub Google Chrome w systemie Windows. W przypadku komputerów Mac firmy Apple należy użyć przeglądarki Google Chrome. Program Microsoft Edge chrom jest również obsługiwany odpowiednio w systemach Windows i Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Gdzie usługa Azure bastionu przechowuje dane klienta?

Usługa Azure bastionu nie przenosi ani nie zapisuje danych klienta z regionu, w którym jest wdrożony.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Czy wszystkie role są wymagane do uzyskania dostępu do maszyny wirtualnej?

Aby można było nawiązać połączenie, wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika na zasobie usługi Azure Bastion

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Jakie są ceny?

Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Czy usługa Azure bastionu wymaga RDS CAL do celów administracyjnych na maszynach wirtualnych hostowanych na platformie Azure?

Nie, dostęp do maszyn wirtualnych z systemem Windows Server za pomocą usługi Azure bastionu nie wymaga [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) , gdy jest używany wyłącznie do celów administracyjnych.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Które układy klawiatury są obsługiwane podczas sesji zdalnej bastionu?

Usługa Azure bastionu obecnie obsługuje układ klawiatury en-us-QWERTY wewnątrz maszyny wirtualnej.  Obsługa innych ustawień regionalnych układu klawiatury jest w toku.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Czy zdefiniowane przez użytkownika Routing (UDR) jest obsługiwany w podsieci usługi Azure bastionu?

Nie. UDR nie jest obsługiwana w podsieci bastionu platformy Azure.

W przypadku scenariuszy obejmujących zarówno usługę Azure bastionu, jak i zaporę sieci wirtualnej (urządzenie WUS) w tej samej sieci wirtualnej, nie trzeba wymuszać ruchu z podsieci bastionu platformy Azure do zapory platformy Azure, ponieważ komunikacja między usługą Azure bastionu i maszynami wirtualnymi jest prywatna. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do maszyn wirtualnych za zaporą platformy Azure za pomocą bastionu](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Dlaczego otrzymuję komunikat o błędzie "sesja wygasła" przed rozpoczęciem sesji bastionu?

Sesja powinna zostać zainicjowana tylko z Azure Portal. Zaloguj się do Azure Portal i ponownie Rozpocznij sesję. Jeśli przejdziesz do adresu URL bezpośrednio z innej sesji lub karty przeglądarki, ten błąd jest oczekiwany. Gwarantuje to, że sesja jest bezpieczniejsza i dostęp do sesji można uzyskać tylko za pomocą Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Jak mogę obsługiwać błędy wdrażania?

Przejrzyj wszystkie komunikaty o błędach i [Zgłoś żądanie pomocy technicznej w Azure Portal zgodnie z](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) wymaganiami. Awarie wdrożenia mogą wynikać z [ograniczeń, przydziałów i ograniczeń subskrypcji platformy Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md). W tym celu klienci mogą napotkać limit liczby publicznych adresów IP dozwolonych na subskrypcję, która powoduje niepowodzenie wdrożenia usługi Azure bastionu.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Jak mogę uwzględnić platformę Azure bastionu w planie odzyskiwania po awarii?

Usługa Azure bastionu jest wdrażana w ramach usługi sieci wirtualnych lub komunikacji równorzędnej sieci wirtualnych i jest skojarzona z regionem platformy Azure. Użytkownik jest odpowiedzialny za wdrażanie usługi Azure bastionu w sieci wirtualnej lokacji odzyskiwania po awarii (DR). W przypadku awarii regionu platformy Azure wykonaj operację przełączenia w tryb failover dla maszyn wirtualnych do regionu DR. Następnie użyj hosta usługi Azure bastionu wdrożonego w regionie DR, aby nawiązać połączenie z maszynami wirtualnymi, które zostały teraz wdrożone w tym miejscu.

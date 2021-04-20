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
ms.openlocfilehash: c0ca74b6cf63e3c81d830eca76eea6815548faa1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732506"
---
### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Czy potrzebuję publicznego adresu IP na maszynie wirtualnej, aby nawiązać połączenie za pośrednictwem Azure Bastion?

Nie. W przypadku nawiązywania połączenia z maszyną wirtualną przy użyciu Azure Bastion nie potrzebujesz publicznego adresu IP na maszynie wirtualnej platformy Azure, z którym jest nawiązywane połączenie. Usługa Bastion otworzy sesję/połączenie RDP/SSH z maszyną wirtualną za pośrednictwem prywatnego adresu IP maszyny wirtualnej w sieci wirtualnej.

### <a name="is-ipv6-supported"></a>Czy protokół IPv6 jest obsługiwany?

Obecnie protokół IPv6 nie jest obsługiwany. Azure Bastion obsługuje tylko protokół IPv4.

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Czy można używać Azure Bastion strefami Prywatna strefa DNS Azure?

Korzystanie z Azure Bastion strefami Prywatna strefa DNS Azure nie jest obecnie obsługiwane. Przed wdrożeniem zasobu Azure Bastion upewnij się, że sieć wirtualna hosta nie jest połączona z prywatną strefą DNS.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Czy potrzebuję klienta RDP lub SSH?

Nie. Nie potrzebujesz klienta RDP lub SSH, aby uzyskać dostęp do protokołu RDP/SSH na maszynie wirtualnej platformy Azure w Azure Portal. Użyj [Azure Portal,](https://portal.azure.com) aby uzyskać dostęp RDP/SSH do maszyny wirtualnej bezpośrednio w przeglądarce.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Czy potrzebuję agenta uruchomionego na maszynie wirtualnej platformy Azure?

Nie. Nie musisz instalować agenta ani żadnego oprogramowania w przeglądarce ani na maszynie wirtualnej platformy Azure. Usługa Bastion jest bez agenta i nie wymaga dodatkowego oprogramowania dla protokołu RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Ile współbieżnych sesji protokołu RDP i SSH obsługuje Azure Bastion protokołu SSH?

Protokoły RDP i SSH są oparte na użyciu. Wysokie użycie sesji spowoduje, że host bastionu będzie obsługiwać niższą całkowitą liczbę sesji. Poniższe liczby zakładają normalne dzienne przepływy pracy.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Jakie funkcje są obsługiwane w sesji protokołu RDP?

Obecnie obsługiwane jest tylko kopiowanie/wklejanie tekstu. Funkcje, takie jak kopiowanie plików, nie są obsługiwane. Możesz podzielić się swoją opinią na temat nowych funkcji na [Azure Bastion opinii.](https://feedback.azure.com/forums/217313-networking?category_id=367303)

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Czy wzmacnianie zabezpieczeń usługi Bastion działa z maszynami wirtualnmi przyłączoną do rozszerzenia usługi AADJ?

Ta funkcja nie działa z maszynami przyłączonymi do maszyny wirtualnej usługi AADJ przy użyciu użytkowników usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Maszyny wirtualne platformy Azure z systemem Windows i usługa Azure AD.](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Które przeglądarki są obsługiwane?

Przeglądarka musi obsługiwać kod HTML 5. Użyj przeglądarki Microsoft Edge Lub Google Chrome w systemie Windows. W przypadku komputerów Mac firmy Apple użyj przeglądarki Google Chrome. Microsoft Edge Chromium jest również obsługiwany odpowiednio w systemach Windows i Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Gdzie Azure Bastion dane klientów?

Azure Bastion nie przenosi ani nie przechowuje danych klientów poza regionem, w których zostały wdrożone.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Czy do uzyskania dostępu do maszyny wirtualnej są wymagane jakiekolwiek role?

W celu nawiązaniu połączenia wymagane są następujące role:

* Rola czytelnika na maszynie wirtualnej
* Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej
* Rola czytelnika na zasobie usługi Azure Bastion

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Jaki jest cennik?

Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Czy Azure Bastion wymagać RDS CAL do celów administracyjnych na hostowanych na platformie Azure maszyn wirtualnych?

Nie, dostęp do maszyn wirtualnych z systemem Windows Server przez Azure Bastion nie wymaga RDS CAL [tylko](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) do celów administracyjnych.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Które układy klawiatury są obsługiwane podczas sesji zdalnej usługi Bastion?

Azure Bastion obecnie obsługuje układ klawiatury en-us-qwerty wewnątrz maszyny wirtualnej.  Obsługa innych lokalizacji regionalnych układu klawiatury jest w toku.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Czy routing zdefiniowany przez użytkownika (UDR) jest obsługiwany Azure Bastion podsieci?

Nie. UDR nie jest obsługiwany w Azure Bastion podsieci.

W przypadku scenariuszy, które obejmują zarówno sieć wirtualną Azure Bastion, jak i Azure Firewall/wirtualne urządzenie sieciowe (WUS) w tej samej sieci wirtualnej, nie trzeba wymuszać ruchu z podsieci Azure Bastion do sieci Azure Firewall, ponieważ komunikacja między siecią Azure Bastion a maszynami wirtualnymi jest prywatna. Aby uzyskać więcej informacji, zobacz [Accessing VMs behind Azure Firewall with Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)(Uzyskiwanie dostępu do maszyn wirtualnych za Azure Firewall bastionem).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Dlaczego otrzymuję komunikat o błędzie "Sesja wygasła" przed rozpoczęciem sesji usługi Bastion?

Sesja powinna być inicjowana tylko z Azure Portal. Zaloguj się do Azure Portal i ponownie rozpocznij sesję. Jeśli do adresu URL zostanie przejść bezpośrednio z innej sesji lub karty przeglądarki, ten błąd będzie oczekiwany. Pomaga to zapewnić, że sesja jest bezpieczniejsza i że dostęp do niej można uzyskać tylko za pośrednictwem Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Jak mogę obsługiwać błędy wdrażania?

Przejrzyj wszystkie komunikaty o błędach [i w razie](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) potrzeby wyślij żądanie Azure Portal pomocy technicznej. Niepowodzenia wdrażania mogą wynikać z [limitów subskrypcji, przydziałów i ograniczeń platformy Azure.](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) W szczególności klienci mogą napotkać limit liczby dozwolonych publicznych adresów IP na subskrypcję, co spowoduje niepowodzenie Azure Bastion wdrożenia.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Jak mogę uwzględnić Azure Bastion w planie odzyskiwania po awarii?

Azure Bastion jest wdrażana w ramach sieci wirtualnych lub równorzędnych sieci wirtualnych i jest skojarzona z regionem świadczenia usługi Azure. Użytkownik jest odpowiedzialny za wdrażanie Azure Bastion sieci wirtualnej lokacji odzyskiwania po awarii. W przypadku awarii regionu świadczenia usługi Azure wykonaj operację trybu failover dla maszyn wirtualnych w regionie awarii. Następnie użyj hosta Azure Bastion wdrożonego w regionie dr yg. W tym celu połącz się z maszynami wirtualnych, które zostały w tym miejscu wdrożone.

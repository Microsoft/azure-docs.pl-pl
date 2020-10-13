---
title: Rozwiązywanie problemów z dostępem aplikacji maszyny wirtualnej na platformie Azure | Microsoft Docs
description: Te szczegółowe kroki rozwiązywania problemów umożliwiają odizolowanie problemów z łączeniem się z aplikacjami uruchomionymi na maszynach wirtualnych na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nie można uruchomić aplikacji, program nie zostanie otwarty, zablokowano port nasłuchu, nie można uruchomić programu, port nasłuchiwania jest zablokowany
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: dec5aeaac6f39a106899094e674864d3bd10dc02
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966342"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Rozwiązywanie problemów z łącznością aplikacji na maszynach wirtualnych platformy Azure

Istnieją różne przyczyny, w których nie można uruchomić aplikacji uruchomionej na maszynie wirtualnej platformy Azure (VM) lub połączyć się z nią. Przyczyną może być to, że aplikacja nie działa lub nie nasłuchuje na oczekiwanych portach, zablokowany port nasłuchiwania lub reguły sieciowe nie przechodzą prawidłowo ruchu do aplikacji. W tym artykule opisano podejście metodyczne w celu znalezienia i rozwiązania problemu.

Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną przy użyciu protokołu RDP lub SSH, najpierw zapoznaj się z jednym z następujących artykułów:

* [Rozwiązywanie problemów z połączeniami Pulpit zdalny z maszyną wirtualną platformy Azure z systemem Windows](troubleshoot-rdp-connection.md)
* [Rozwiązywanie problemów z połączeniami Secure Shell (SSH) z maszyną wirtualną platformy Azure opartą](troubleshoot-ssh-connection.md)na systemie Linux.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure w [witrynie MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie możesz także zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

## <a name="quick-start-troubleshooting-steps"></a>Kroki szybkiego uruchamiania
Jeśli masz problemy z połączeniem z aplikacją, spróbuj wykonać następujące czynności ogólne. Po każdym kroku spróbuj ponownie nawiązać połączenie z aplikacją:

* Ponowne uruchomienie maszyny wirtualnej
* Utwórz ponownie reguły punktu końcowego/reguły zapory/sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)
  * [Model Menedżer zasobów — zarządzanie grupami zabezpieczeń sieci](../../virtual-network/manage-network-security-group.md)
  * [Model klasyczny — zarządzanie Cloud Services punktami końcowymi](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Łączenie z innej lokalizacji, na przykład innej sieci wirtualnej platformy Azure
* Ponowne wdrożenie maszyny wirtualnej
  * [Ponowne wdrażanie maszyny wirtualnej z systemem Windows](redeploy-to-new-node-windows.md)
  * [Ponowne wdrażanie maszyny wirtualnej z systemem Linux](redeploy-to-new-node-linux.md)
* Utwórz ponownie maszynę wirtualną

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością punktu końcowego (RDP/SSH/http itp.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Szczegółowy przegląd rozwiązywania problemów
Istnieją cztery główne obszary do rozwiązywania problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure.

![Rozwiązywanie problemów z nie można uruchomić aplikacji](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikacja działająca na maszynie wirtualnej platformy Azure.
   * Czy sama aplikacja działa prawidłowo?
2. Maszyna wirtualna platformy Azure.
   * Czy maszyna wirtualna działa poprawnie i odpowiada na żądania?
3. Punkty końcowe sieci platformy Azure.
   * Punkty końcowe usługi w chmurze dla maszyn wirtualnych w klasycznym modelu wdrażania.
   * Sieciowe grupy zabezpieczeń i przychodzące reguły NAT dla maszyn wirtualnych w Menedżer zasobów model wdrażania.
   * Czy ruch od użytkowników do maszyny wirtualnej/aplikacji ma być przepływany na oczekiwanych portach?
4. Urządzenie sieci Internet Edge.
   * Czy reguły zapory w miejscu uniemożliwiają poprawne przepływowanie ruchu?

W przypadku komputerów klienckich, które uzyskują dostęp do aplikacji za pośrednictwem sieci VPN typu lokacja-lokacja lub połączenia ExpressRoute, główne obszary, które mogą spowodować problemy, to aplikacja i maszyna wirtualna platformy Azure.

Aby określić źródło problemu i jego korektę, wykonaj następujące kroki.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1. Uzyskiwanie dostępu do aplikacji z docelowej maszyny wirtualnej
Spróbuj uzyskać dostęp do aplikacji za pomocą odpowiedniego programu klienckiego z maszyny wirtualnej, na której jest uruchomiona. Użyj nazwy hosta lokalnego, lokalnego adresu IP lub adresu sprzężenia zwrotnego (127.0.0.1).

![Uruchom aplikację bezpośrednio z maszyny wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Na przykład jeśli aplikacja jest serwerem sieci Web, Otwórz przeglądarkę na maszynie wirtualnej i spróbuj uzyskać dostęp do strony sieci Web hostowanej na maszynie wirtualnej.

Jeśli masz dostęp do aplikacji, przejdź do [kroku 2](#step2).

Jeśli nie możesz uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* Aplikacja jest uruchomiona na docelowej maszynie wirtualnej.
* Aplikacja nasłuchuje na oczekiwanych portach TCP i UDP.

Na maszynach wirtualnych z systemem Windows i Linux Użyj polecenia **netstat-a** , aby wyświetlić aktywne porty nasłuchiwania. Sprawdź dane wyjściowe dla oczekiwanych portów, na których aplikacja powinna nasłuchiwać. Uruchom ponownie aplikację lub skonfiguruj ją tak, aby korzystała z oczekiwanych portów w razie potrzeby, a następnie spróbuj ponownie uzyskać dostęp do aplikacji.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Krok 2. Uzyskiwanie dostępu do aplikacji z innej maszyny wirtualnej w tej samej sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji z innej maszyny wirtualnej, ale w tej samej sieci wirtualnej, przy użyciu nazwy hosta maszyny wirtualnej lub adresu IP publicznego, prywatnego lub dostawcy przypisanego do platformy Azure. W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania nie należy używać publicznego adresu IP usługi w chmurze.

![Uruchom aplikację z innej maszyny wirtualnej](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Na przykład jeśli aplikacja jest serwerem sieci Web, spróbuj uzyskać dostęp do strony sieci Web z przeglądarki na innej maszynie wirtualnej w tej samej sieci wirtualnych.

Jeśli masz dostęp do aplikacji, przejdź do [kroku 3](#step3).

Jeśli nie możesz uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* Zapora hosta na docelowej maszynie wirtualnej zezwala na ruch przychodzący przychodzących żądań i odpowiedzi.
* Wykrywanie intruza lub oprogramowanie do monitorowania sieci uruchomione na docelowej maszynie wirtualnej zezwala na ruch.
* Cloud Services punkty końcowe lub sieciowe grupy zabezpieczeń zezwalają na ruch:
  * [Model klasyczny — zarządzanie Cloud Services punktami końcowymi](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model Menedżer zasobów — zarządzanie grupami zabezpieczeń sieci](../../virtual-network/manage-network-security-group.md)
* Oddzielny składnik działający na maszynie wirtualnej w ścieżce między testową maszyną wirtualną a maszyną wirtualną, taką jak moduł równoważenia obciążenia lub zapora, zezwala na ruch.

Na maszynie wirtualnej z systemem Windows należy użyć zapory systemu Windows z zabezpieczeniami zaawansowanymi, aby określić, czy reguły zapory wykluczają ruch przychodzący i wychodzący aplikacji.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Krok 3. dostęp do aplikacji spoza sieci wirtualnej
Spróbuj uzyskać dostęp do aplikacji z komputera spoza sieci wirtualnej jako maszyny wirtualnej, na której działa aplikacja. Użyj innej sieci jako oryginalnego komputera klienckiego.

![Uruchom aplikację z komputera poza siecią wirtualną](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Na przykład jeśli aplikacja jest serwerem sieci Web, spróbuj uzyskać dostęp do strony sieci Web z przeglądarki działającej na komputerze, który nie znajduje się w sieci wirtualnej.

Jeśli nie możesz uzyskać dostępu do aplikacji, sprawdź następujące ustawienia:

* W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania:
  
  * Sprawdź, czy konfiguracja punktu końcowego maszyny wirtualnej zezwala na ruch przychodzący, w szczególności protokół (TCP lub UDP) i numery portów Public i Private.
  * Sprawdź, czy listy kontroli dostępu (ACL) w punkcie końcowym nie uniemożliwiają ruchu przychodzącego z Internetu.
  * Aby uzyskać więcej informacji, zobacz [jak skonfigurować punkty końcowe na maszynie wirtualnej](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints).
* W przypadku maszyn wirtualnych utworzonych przy użyciu Menedżer zasobów modelu wdrażania:
  
  * Sprawdź, czy konfiguracja reguły NAT dla ruchu przychodzącego dla maszyny wirtualnej zezwala na ruch przychodzący, w szczególności protokół (TCP lub UDP) i numery portów Public i Private.
  * Sprawdź, czy sieciowe grupy zabezpieczeń zezwalają na ruch przychodzący i przychodzącej odpowiedzi.
  * Aby uzyskać więcej informacji, zobacz [co to jest sieciowa Grupa zabezpieczeń?](../../virtual-network/network-security-groups-overview.md)

Jeśli maszyna wirtualna lub punkt końcowy jest członkiem zestawu o zrównoważonym obciążeniu:

* Sprawdź, czy protokół sondy (TCP lub UDP) i numer portu są poprawne.
* Jeśli protokół sondy i port różnią się od protokołu i portu o zrównoważonym obciążeniu:
  * Sprawdź, czy aplikacja nasłuchuje na protokole sondowania (TCP lub UDP) i numer portu (Użyj polecenia **netstat – a** na docelowej maszynie wirtualnej).
  * Sprawdź, czy Zapora hosta na docelowej maszynie wirtualnej zezwala na przychodzące żądania sondowania i ruch odpowiedzi sondy wychodzącej.

Jeśli masz dostęp do aplikacji, upewnij się, że urządzenie z internetową granicą zezwala na:

* Aplikacja wychodząca żąda ruchu z komputera klienckiego do maszyny wirtualnej platformy Azure.
* Ruch przychodzący odpowiedzi aplikacji z maszyny wirtualnej platformy Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4. Jeśli nie możesz uzyskać dostępu do aplikacji, użyj opcji Sprawdź poprawność adresu IP, aby sprawdzić ustawienia. 

Aby uzyskać więcej informacji, zobacz [Omówienie monitorowania sieci platformy Azure](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby
[Rozwiązywanie problemów z połączeniami Pulpit zdalny z maszyną wirtualną platformy Azure z systemem Windows](troubleshoot-rdp-connection.md)

[Rozwiązywanie problemów z połączeniami Secure Shell (SSH) z maszyną wirtualną platformy Azure opartą na systemie Linux](troubleshoot-ssh-connection.md)
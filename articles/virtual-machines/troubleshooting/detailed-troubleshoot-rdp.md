---
title: Szczegółowe Rozwiązywanie problemów z pulpitem zdalnym na platformie Azure | Microsoft Docs
description: Przejrzyj szczegółowe kroki rozwiązywania problemów z pulpitem zdalnym, w których nie można maszyn wirtualnych z systemem Windows na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nie można nawiązać połączenia z pulpitem zdalnym, rozwiązywać problemów z pulpitem zdalnym, pulpit zdalny nie może nawiązać połączenia, błędy pulpitu zdalnego, rozwiązywanie problemów z pulpitem zdalnym
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 5782765504eb1e0cb57558d3d4772d08de6b4d25
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913111"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Szczegółowe procedury rozwiązywania problemów z połączeniem pulpitu zdalnego z maszynami wirtualnymi systemu Windows na platformie Azure
Ten artykuł zawiera szczegółowe kroki rozwiązywania problemów w celu zdiagnozowania i rozwiązania złożonych błędów Pulpit zdalny dla maszyn wirtualnych platformy Azure opartych na systemie Windows.

> [!IMPORTANT]
> Aby wyeliminować typowe błędy Pulpit zdalny, przed kontynuowaniem należy zapoznać się [z artykułem podstawowe informacje pulpit zdalny o rozwiązywaniu problemów](troubleshoot-rdp-connection.md) .

Może pojawić się komunikat o błędzie Pulpit zdalny, który nie przypomina żadnych określonych komunikatów o błędach przedstawionych w [przewodniku rozwiązywania problemów w podstawowym pulpit zdalny](troubleshoot-rdp-connection.md). Wykonaj następujące kroki, aby określić, dlaczego klient Pulpit zdalny (RDP) nie może nawiązać połączenia z usługą RDP na maszynie wirtualnej platformy Azure.


Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure w [witrynie MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie możesz także zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję **Uzyskaj pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Składniki połączenia Pulpit zdalny
Następujące składniki są objęte połączeniem RDP:

![Diagram przedstawiający składniki objęte połączeniem Pulpit zdalny (RDP).](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Przed kontynuowaniem może być pomocne sprawdzenie, co zmieniło się od czasu ostatniego pomyślnego nawiązania połączenia z maszyną wirtualną Pulpit zdalny. Na przykład:

* Publiczny adres IP maszyny wirtualnej lub usługi w chmurze zawierającej MASZYNę wirtualną (nazywany również wirtualnym adresem IP adresu [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) został zmieniony. Awaria protokołu RDP może być spowodowana tym, że w pamięci podręcznej klienta DNS nadal jest zarejestrowany *stary adres IP* dla nazwy DNS. Opróżnij pamięć podręczną klienta DNS i spróbuj ponownie nawiązać połączenie z maszyną wirtualną. Lub spróbuj połączyć się bezpośrednio z nowym adresem VIP.
* Używasz aplikacji innych firm do zarządzania połączeniami Pulpit zdalny, zamiast korzystać z połączenia wygenerowanego przez Azure Portal. Sprawdź, czy konfiguracja aplikacji zawiera poprawny port TCP dla ruchu Pulpit zdalny. Możesz sprawdzić ten port dla klasycznej maszyny wirtualnej w [Azure Portal](https://portal.azure.com), klikając ustawienia maszyny wirtualnej > punkty końcowe.

## <a name="preliminary-steps"></a>Kroki wstępne
Przed przejściem do szczegółowego rozwiązania problemu

* Sprawdź stan maszyny wirtualnej w Azure Portal w poszukiwaniu oczywistych problemów.
* Postępuj zgodnie z [krokami szybkie rozwiązywanie typowych błędów protokołu RDP w przewodniku dotyczącym podstawowych problemów](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* W przypadku obrazów niestandardowych upewnij się, że wirtualny dysk twardy jest prawidłowo przygotowany przed przekazaniem go. Aby uzyskać więcej informacji, zobacz [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](../windows/prepare-for-upload-vhd-image.md).


Spróbuj ponownie nawiązać połączenie z maszyną wirtualną za pośrednictwem Pulpit zdalny po wykonaniu tych kroków.

## <a name="detailed-troubleshooting-steps"></a>Szczegółowe kroki rozwiązywania problemów
Klient Pulpit zdalny może nie być w stanie uzyskać dostępu do usługi Pulpit zdalny na maszynie wirtualnej platformy Azure ze względu na problemy w następujących źródłach:

* [Pulpit zdalny komputera klienckiego](#source-1-remote-desktop-client-computer)
* [Intranetowe Urządzenie brzegowe organizacji](#source-2-organization-intranet-edge-device)
* [Punkt końcowy usługi w chmurze i lista kontroli dostępu (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Sieciowe grupy zabezpieczeń](#source-4-network-security-groups)
* [Maszyna wirtualna platformy Azure z systemem Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Źródło 1: Pulpit zdalny komputera klienckiego
Sprawdź, czy komputer może nawiązać Pulpit zdalny połączenia z innym lokalnym komputerem z systemem Windows.

![Diagram składników w ramach połączenia Pulpit zdalny (RDP) z wyróżnionym klientem RDP i Strzałka wskazująca na inny komputer lokalny wskazujący połączenie.](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Jeśli nie możesz, sprawdź, czy na komputerze nie ma następujących ustawień:

* Ustawienie zapory lokalnej, która blokuje ruch Pulpit zdalny.
* Zainstalowane lokalnie oprogramowanie proxy klienta, które uniemożliwia połączenia Pulpit zdalny.
* Zainstalowane lokalnie oprogramowanie do monitorowania sieci, które uniemożliwia połączenia Pulpit zdalny.
* Inne typy oprogramowania zabezpieczającego, które monitorują ruch lub zezwalają/nie zezwalają na określone typy ruchu, który uniemożliwia połączenia Pulpit zdalny.

W każdym z tych przypadków tymczasowo wyłącz oprogramowanie i spróbuj połączyć się z komputerem lokalnym za pośrednictwem Pulpit zdalny. Jeśli możesz ustalić faktyczną przyczynę tego problemu, skontaktuj się z administratorem sieci, aby skorygować ustawienia oprogramowania w celu zezwalania na połączenia Pulpit zdalny.

## <a name="source-2-organization-intranet-edge-device"></a>Źródło 2: Urządzenie brzegowe organizacji intranetowej
Sprawdź, czy komputer połączony bezpośrednio z Internetem może nawiązać Pulpit zdalny połączenia z maszyną wirtualną platformy Azure.

![Diagram składników w ramach połączenia Pulpit zdalny (RDP) z klientem RDP połączonym z Internetem i strzałką wskazującą maszynę wirtualną platformy Azure wskazującej połączenie.](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Jeśli nie masz komputera połączonego bezpośrednio z Internetem, Utwórz i przetestuj nową maszynę wirtualną platformy Azure w grupie zasobów lub usłudze w chmurze. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Windows na platformie Azure](../windows/quick-create-portal.md). Po zakończeniu testu można usunąć maszynę wirtualną i grupę zasobów lub usługę w chmurze.

Jeśli można utworzyć połączenie Pulpit zdalny z komputerem połączonym bezpośrednio z Internetem, sprawdź, czy w organizacji intranetowego urządzenia brzegowego:

* Wewnętrzna zapora blokująca połączenia HTTPS z Internetem.
* Serwer proxy uniemożliwiający nawiązywanie połączeń Pulpit zdalny.
* Wykrywanie intruza lub oprogramowanie do monitorowania sieci działające na urządzeniach w sieci brzegowej, które uniemożliwiają Pulpit zdalny połączeń.

Skontaktuj się z administratorem sieci, aby skorygować ustawienia intranetowej urządzenia brzegowego, aby umożliwić nawiązywanie połączeń Pulpit zdalny z Internetem za pośrednictwem protokołu HTTPS.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Źródło 3: punkt końcowy usługi w chmurze i lista ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania Sprawdź, czy inna maszyna wirtualna platformy Azure, która znajduje się w tej samej usłudze w chmurze lub sieci wirtualnej, może nawiązać Pulpit zdalny połączenia z MASZYNą wirtualną platformy Azure.

![Diagram składników Pulpit zdalny (RDP) z wyróżnioną jedną maszyną wirtualną platformy Azure i strzałką wskazującą inną maszynę wirtualną platformy Azure w ramach tej samej usługi w chmurze wskazującej połączenie.](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> W przypadku maszyn wirtualnych utworzonych w Menedżer zasobów przejdź do [źródła 4: sieciowe grupy zabezpieczeń](#source-4-network-security-groups).

Jeśli nie masz innej maszyny wirtualnej w tej samej usłudze w chmurze lub sieci wirtualnej, utwórz ją. Wykonaj kroki opisane w sekcji [Tworzenie maszyny wirtualnej z systemem Windows na platformie Azure](../windows/quick-create-portal.md). Po zakończeniu testu Usuń testową maszynę wirtualną.

Jeśli można nawiązać połączenie za pośrednictwem Pulpit zdalny z maszyną wirtualną w tej samej usłudze w chmurze lub sieci wirtualnej, sprawdź następujące ustawienia:

* Konfiguracja punktu końcowego dla Pulpit zdalny ruchu na docelowej maszynie wirtualnej: prywatny port TCP punktu końcowego musi być zgodny z portem TCP, na którym nasłuchuje usługa Pulpit zdalny maszyny wirtualnej (wartość domyślna to 3389).
* Lista ACL dla punktu końcowego ruchu Pulpit zdalny na docelowej maszynie wirtualnej: listy ACL umożliwiają określenie dozwolonych lub zabronionych ruchu przychodzącego z Internetu na podstawie jego źródłowego adresu IP. Nieprawidłowo skonfigurowane listy ACL mogą uniemożliwiać ruch przychodzący Pulpit zdalny do punktu końcowego. Sprawdź listy kontroli dostępu, aby upewnić się, że ruch przychodzący z publicznych adresów IP serwera proxy lub innego serwera granicznego jest dozwolony. Aby uzyskać więcej informacji, zobacz [co to jest lista Access Control sieciowych?](/previous-versions/azure/virtual-network/virtual-networks-acl)

Aby sprawdzić, czy punkt końcowy jest źródłem problemu, Usuń bieżący punkt końcowy i Utwórz nowy, wybierając port losowy w zakresie 49152 – 65535 dla numeru portu zewnętrznego. Aby uzyskać więcej informacji, zobacz [jak skonfigurować punkty końcowe na maszynie wirtualnej](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

## <a name="source-4-network-security-groups"></a>Źródło 4: sieciowe grupy zabezpieczeń
Sieciowe grupy zabezpieczeń umożliwiają bardziej szczegółową kontrolę nad dozwolonym ruchem przychodzącym i wychodzącym. Można tworzyć reguły obejmujące podsieci i usługi w chmurze w sieci wirtualnej platformy Azure.

Użyj [weryfikacji przepływu IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) , aby potwierdzić, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Możesz również przejrzeć obowiązujące zasady grupy zabezpieczeń, aby upewnić się, że przychodząca reguła sieciowej grupy zabezpieczeń "Zezwalaj" istnieje i ma priorytet dla portu RDP (domyślnie 3389). Aby uzyskać więcej informacji, zobacz Używanie obowiązujących [reguł zabezpieczeń w celu rozwiązywania problemów z przepływem ruchu maszyn wirtualnych](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Źródło 5: maszyna wirtualna platformy Azure oparta na systemie Windows
![Diagram składników w ramach połączenia Pulpit zdalny (RDP) z maszyną wirtualną platformy Azure wyróżnioną w ramach usługi w chmurze i komunikatem, że może to być "możliwe Źródło problemów". Niebieska linia wskazuje, że reguły grupy zabezpieczeń sieci mogą blokować ruch do lub z maszyny wirtualnej platformy Azure.](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Postępuj zgodnie z instrukcjami w [tym artykule](./reset-rdp.md). Ten artykuł resetuje usługę Pulpit zdalny na maszynie wirtualnej:

* Włącz regułę domyślną zapory systemu Windows "Pulpit zdalny" (port TCP 3389).
* Włącz połączenia Pulpit zdalny, ustawiając wartość rejestru HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections na 0.

Spróbuj ponownie nawiązać połączenie z komputera. Jeśli nadal nie można nawiązać połączenia za pośrednictwem Pulpit zdalny, sprawdź, czy są dostępne następujące problemy:

* Usługa Pulpit zdalny nie jest uruchomiona na docelowej maszynie wirtualnej.
* Usługa Pulpit zdalny nie nasłuchuje na porcie TCP 3389.
* Zapora systemu Windows lub inna zapora lokalna ma regułę ruchu wychodzącego uniemożliwiającą Pulpit zdalny ruchem.
* Wykrywanie intruza lub oprogramowanie do monitorowania sieci działające na maszynie wirtualnej platformy Azure uniemożliwia połączenia Pulpit zdalny.

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania można użyć zdalnej sesji Azure PowerShell do maszyny wirtualnej platformy Azure. Najpierw należy zainstalować certyfikat dla usługi hostingu w chmurze maszyny wirtualnej. Przejdź do pozycji [Skonfiguruj Bezpieczny zdalny dostęp do usługi Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) i Pobierz plik skryptu **InstallWinRMCertAzureVM.ps1** na komputer lokalny.

Następnie Zainstaluj Azure PowerShell, jeśli jeszcze tego nie zrobiono. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

Następnie otwórz wiersz polecenia Azure PowerShell i Zmień bieżący folder na lokalizację pliku skryptu **InstallWinRMCertAzureVM.ps1** . Aby uruchomić skrypt Azure PowerShell, należy ustawić odpowiednie zasady wykonywania. Uruchom polecenie **Get-ExecutionPolicy** , aby określić bieżący poziom zasad. Aby uzyskać informacje na temat ustawiania odpowiedniego poziomu, zobacz [Set-executionpolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-5.1&preserve-view=true).

Następnie wprowadź nazwę subskrypcji platformy Azure, nazwę usługi w chmurze i nazwę maszyny wirtualnej (usuwając < i > znaki), a następnie Uruchom te polecenia.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Można uzyskać poprawną nazwę subskrypcji z właściwości *subscriptionname* wyświetlania polecenia **Get-AzureSubscription** . Nazwę usługi w chmurze dla maszyny wirtualnej można uzyskać z kolumny *ServiceName* na ekranie polecenia **Get-AzureVM** .

Sprawdź, czy masz nowy certyfikat. Otwórz przystawkę Certyfikaty dla bieżącego użytkownika i Wyszukaj w folderze **Zaufane główne certyfikaty certyfikacji\Certyfikaty** . Powinien zostać wyświetlony certyfikat z nazwą DNS usługi w chmurze w kolumnie wystawiony dla (przykład: cloudservice4testing.cloudapp.net).

Następnie zainicjuj zdalną sesję Azure PowerShell przy użyciu tych poleceń.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Po wprowadzeniu prawidłowych poświadczeń administratora powinien zostać wyświetlony komunikat podobny do następującego Azure PowerShell:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Pierwsza część tego monitu to nazwa usługi w chmurze, która zawiera docelową maszynę wirtualną, która może się różnić od "cloudservice4testing.cloudapp.net". Teraz można wydać Azure PowerShell polecenia dla tej usługi w chmurze, aby zbadać wymienione problemy i poprawić konfigurację.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Aby ręcznie poprawić Usługi pulpitu zdalnego nasłuchiwanie portu TCP
W wierszu polecenia zdalna sesja Azure PowerShell uruchom to polecenie.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Właściwość numer_portu pokazuje bieżący numer portu. W razie potrzeby zmień numer portu Pulpit zdalny z powrotem na wartość domyślną (3389) za pomocą tego polecenia.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Sprawdź, czy port został zmieniony na 3389 przy użyciu tego polecenia.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Zakończ sesję zdalnego Azure PowerShell za pomocą tego polecenia.

```powershell
Exit-PSSession
```

Sprawdź, czy punkt końcowy Pulpit zdalny dla maszyny wirtualnej platformy Azure używa również portu TCP 3398 jako portu wewnętrznego. Uruchom ponownie maszynę wirtualną platformy Azure i spróbuj ponownie nawiązać połączenie Pulpit zdalny.

## <a name="additional-resources"></a>Dodatkowe zasoby
[Jak zresetować hasło lub usługę Pulpit zdalny dla maszyn wirtualnych z systemem Windows](./reset-rdp.md)

[How to install and configure Azure PowerShell](/powershell/azure/)

[Rozwiązywanie problemów z połączeniami Secure Shell (SSH) z maszyną wirtualną platformy Azure opartą na systemie Linux](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

[Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie wirtualnej platformy Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)

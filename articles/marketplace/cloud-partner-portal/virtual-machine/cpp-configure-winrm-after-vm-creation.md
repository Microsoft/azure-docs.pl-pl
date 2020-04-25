---
title: Konfigurowanie usługi WinRM po utworzeniu maszyny wirtualnej platformy Azure | Portal Azure Marketplace
description: Wyjaśnia, jak skonfigurować Windows Remote Management (WinRM) po utworzeniu maszyny wirtualnej hostowanej na platformie Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: 3a67371ce6f951a9e446ab639ea5b59248b79565
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144139"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurowanie usługi WinRM po utworzeniu maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

W tym artykule opisano sposób konfigurowania istniejącej maszyny wirtualnej hostowanej na platformie Azure w celu włączenia usługi WinRM za pośrednictwem protokołu HTTPS.  Ta konfiguracja dotyczy tylko maszyn wirtualnych z systemem Windows i wymaga następującego dwuetapowego procesu:

1. Włącz ruch portów dla protokołu WinRM przez HTTPS.  To ustawienie należy skonfigurować dla maszyny wirtualnej w Azure Portal.
2. Skonfiguruj maszynę wirtualną, aby włączyć usługę WinRM, uruchamiając dostarczone skrypty programu PowerShell.


## <a name="enabling-port-traffic"></a>Włączanie ruchu portu

Protokół WinRM przez HTTPS używa portu 5986, który nie jest domyślnie włączony w przypadku wstępnie skonfigurowanych maszyn wirtualnych z systemem Windows oferowanych w witrynie Azure Marketplace. Aby włączyć ten protokół, wykonaj następujące kroki, aby dodać nową regułę do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) z [Azure Portal](https://portal.azure.com).  Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).

1. Przejdź do kart **Virtual Machines, >**   < *VM-Name* >   **> Settings/Networking**.
2. Kliknij nazwę sieciowej grupy zabezpieczeń (w tym przykładzie **testvm11002**), aby wyświetlić jej właściwości:

    ![Właściwości sieciowej grupy zabezpieczeń](./media/nsg-properties.png)
 
3. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń ruchu przychodzącego** , aby wyświetlić ten blok.
4. Kliknij pozycję **+ Dodaj** , aby utworzyć nową regułę `WinRM_HTTPS` o nazwie dla portu TCP 5986.

    ![Dodaj regułę zabezpieczeń sieci dla ruchu przychodzącego](./media/nsg-new-rule.png)

5. Po zakończeniu podawania wartości kliknij przycisk **OK** .  Lista reguł zabezpieczeń dla ruchu przychodzącego powinna zawierać następujące nowe wpisy.

    ![Lista reguł zabezpieczeń sieci dla ruchu przychodzącego](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurowanie maszyny wirtualnej w celu włączenia usługi WinRM 

Wykonaj następujące kroki, aby włączyć i skonfigurować funkcję Windows Remote Management na maszynie wirtualnej z systemem Windows.   

1. Ustanów Pulpit zdalny połączenie z maszyną wirtualną hostowaną na platformie Azure.  Aby uzyskać więcej informacji, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Pozostałe kroki zostaną uruchomione na maszynie wirtualnej.
2. Pobierz następujące pliki i Zapisz je w folderze na maszynie wirtualnej:
    - [ConfigureWinRM. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [MakeCert. exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf. cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Otwórz **konsolę programu PowerShell** z podniesionymi uprawnieniami (**Uruchom jako administrator**). 
4. Uruchom następujące polecenie, podając wymagany parametr: w pełni kwalifikowana nazwa domeny (FQDN) dla maszyny wirtualnej: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Skrypt konfiguracji programu PowerShell 1](./media/powershell-file1.png)

    Ten skrypt zależy od innych dwóch plików znajdujących się w tym samym folderze.


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi WinRM możesz przystąpić do [wdrożenia maszyny wirtualnej ze swoich wirtualnych dysków twardych](./cpp-deploy-vm-vhd.md).

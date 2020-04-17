---
title: Łączenie się z maszyną wirtualną systemu Windows Server
description: Dowiedz się, jak połączyć się z maszyną wirtualną systemu Windows i zalogować się do niej przy użyciu portalu Azure i modelu wdrażania Usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: c4f5df4a76792c64ab92bbe8d6e4b84080fbd8fe
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459189"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Jak połączyć się i zalogować się na maszynie wirtualnej platformy Azure z systemem Windows
Korzystając z przycisku **Połącz** w witrynie Azure Portal, uruchomisz sesję pulpitu zdalnego z poziomu pulpitu systemu Windows. Najpierw łączysz się z maszyną wirtualną, a następnie logujesz się.

Aby połączyć się z maszyną wirtualną systemu Windows z komputera Mac, należy zainstalować klienta RDP dla komputerów Mac, takiego jak [Pulpit zdalny firmy Microsoft](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
1. Przejdź do [witryny Azure portal,](https://portal.azure.com/) aby połączyć się z maszyną wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.
2. Wybierz maszynę wirtualną z listy.
3. Na początku strony maszyny wirtualnej wybierz pozycję **Połącz**.
4. Na stronie **Połącz z maszyną wirtualną** wybierz pozycję **RDP**, a następnie wybierz odpowiedni **adres IP** i **numer portu**. W większości przypadków należy użyć domyślnego adresu IP i portu. Wybierz opcję **Pobierz plik RDP**. Jeśli maszyna wirtualna ma zestaw zasad just-in-time, należy najpierw wybrać przycisk **Poproś o dostęp,** aby zażądać dostępu, zanim będzie można pobrać plik RDP. Aby uzyskać więcej informacji na temat zasad just-in-time, zobacz [Zarządzanie dostępem do maszyny wirtualnej przy użyciu zasad just in time](../../security-center/security-center-just-in-time.md).
5. Otwórz pobrany plik RDP i wybierz polecenie **Połącz**, gdy wyświetli się odpowiedni monit. Otrzymasz ostrzeżenie, że `.rdp` plik pochodzi od nieznanego wydawcy. Jest to oczekiwane. W oknie **Podłączanie pulpitu zdalnego** wybierz pozycję **Połącz,** aby kontynuować.
   
    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/connect-logon/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wprowadź poświadczenia dla konta na maszynie wirtualnej, a następnie wybierz **przycisk OK**.
   
     **Konto lokalne:** Zazwyczaj jest to nazwa użytkownika konta lokalnego i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  
   
    **Maszyna wirtualna przyłączona do domeny:** Jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *Domena*&#92;*Nazwa użytkownika*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.
   
    **Kontroler domeny:** Jeśli maszyna wirtualna jest kontrolerem domeny, wprowadź nazwę użytkownika i hasło konta administratora domeny dla tej domeny.
4. Wybierz **opcję Tak,** aby zweryfikować tożsamość maszyny wirtualnej i zakończyć rejestrowanie.
   
   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Jeśli przycisk **Połącz** w portalu jest wyszarzony i nie masz połączenia z platformą Azure za pośrednictwem [połączenia protokã³i](../../expressroute/expressroute-introduction.md) lub [sieci VPN między lokacjami,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) przed użyciem protokołu RDP należy utworzyć i przypisać maszynę wirtualną publiczny adres IP. Aby uzyskać więcej informacji, zobacz [Publiczne adresy IP na platformie Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Łączenie się z maszyną wirtualną za pomocą programu PowerShell

 

Jeśli używasz programu PowerShell i masz zainstalowany moduł programu Azure `Get-AzRemoteDesktopFile` PowerShell można również połączyć przy użyciu polecenia cmdlet, jak pokazano poniżej.

W tym przykładzie natychmiast uruchomi się połączenie RDP, co spowoduje wyświetlenie podobnych monitów, jak powyżej.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Można również zapisać plik RDP do wykorzystania w przyszłości.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązawaniem połączenia, zobacz [Rozwiązywanie problemów z połączeniami pulpitu zdalnego](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


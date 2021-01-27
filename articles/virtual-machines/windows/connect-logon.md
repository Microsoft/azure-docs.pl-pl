---
title: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows Server
description: Dowiedz się, jak nawiązać połączenie i zalogować się do maszyny wirtualnej z systemem Windows przy użyciu Azure Portal i Menedżer zasobów model wdrażania.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dacf34d7098472e98c7f68f7f60fa9bac1a4e5ec
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875775"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows
Korzystając z przycisku **Połącz** w witrynie Azure Portal, uruchomisz sesję pulpitu zdalnego z poziomu pulpitu systemu Windows. Najpierw Nawiąż połączenie z maszyną wirtualną, a następnie zaloguj się.

Aby nawiązać połączenie z maszyną wirtualną z systemem Windows z komputera Mac, należy zainstalować klienta RDP dla komputerów Mac, na przykład [pulpit zdalny Microsoft](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną
1. Przejdź do [Azure Portal](https://portal.azure.com/) , aby nawiązać połączenie z maszyną wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.
2. Wybierz maszynę wirtualną z listy.
3. Na początku strony maszyny wirtualnej wybierz pozycję **Połącz**.
4. Na stronie **Połącz z maszyną wirtualną** wybierz pozycję **RDP**, a następnie wybierz odpowiedni **adres IP** i **numer portu**. W większości przypadków należy użyć domyślnego adresu IP i portu. Wybierz pozycję **Pobierz plik RDP**. Jeśli maszyna wirtualna ma zestaw zasad just-in-Time, najpierw musisz wybrać przycisk **Żądaj dostępu** , aby zażądać dostępu, zanim będzie można pobrać plik RDP. Aby uzyskać więcej informacji na temat zasad just in Time, zobacz [Zarządzanie dostępem do maszyn wirtualnych przy użyciu zasad just in Time](../../security-center/security-center-just-in-time.md).
5. Otwórz pobrany plik RDP i wybierz polecenie **Połącz**, gdy wyświetli się odpowiedni monit. Zostanie wyświetlone ostrzeżenie, że `.rdp` plik pochodzi od nieznanego wydawcy. Jest to oczekiwane zachowanie. W oknie **Podłączanie pulpitu zdalnego** wybierz pozycję **Połącz** , aby kontynuować.
   
    ![Zrzut ekranu przedstawiający ostrzeżenie o nieznanym wydawcy.](./media/connect-logon/rdp-warn.png)
3. W oknie **Zabezpieczenia systemu Windows** wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. Wprowadź poświadczenia dla konta na maszynie wirtualnej, a następnie wybierz przycisk **OK**.
   
     **Konto lokalne**: zazwyczaj jest to nazwa użytkownika konta lokalnego i hasło określone podczas tworzenia maszyny wirtualnej. W tym przypadku domeną jest nazwa maszyny wirtualnej wprowadzana jako *nazwa_maszyny_wirtualnej*&#92;*nazwa_użytkownika*.  
   
    **Maszyna wirtualna przyłączona do domeny**: Jeśli maszyna wirtualna należy do domeny, wprowadź nazwę użytkownika w formacie *domena*&#92;*nazwa_użytkownika*. Konto musi również należeć do grupy Administratorzy albo mieć przydzielone uprawnienia dostępu zdalnego do maszyny wirtualnej.
   
    **Kontroler domeny**: Jeśli maszyna wirtualna jest kontrolerem domeny, wprowadź nazwę użytkownika i hasło konta administratora domeny dla tej domeny.
4. Wybierz pozycję **tak** , aby zweryfikować tożsamość maszyny wirtualnej i zakończyć logowanie.
   
   ![Zrzut ekranu przedstawiający komunikat dotyczący potwierdzenia tożsamości maszyny wirtualnej.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Jeśli przycisk **Połącz** w portalu jest wyszarzony i nie nawiązano połączenia z platformą Azure za pośrednictwem usługi [Express Route](../../expressroute/expressroute-introduction.md) lub [sieci VPN typu lokacja-lokacja](../../vpn-gateway/tutorial-site-to-site-portal.md) , przed użyciem protokołu RDP należy utworzyć i przypisać maszynę wirtualną jako publiczny adres IP. Aby uzyskać więcej informacji, zobacz [publiczne adresy IP na platformie Azure](../../virtual-network/public-ip-addresses.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu programu PowerShell

 

Jeśli używasz programu PowerShell i masz zainstalowany moduł Azure PowerShell, możesz również nawiązać połączenie za pomocą `Get-AzRemoteDesktopFile` polecenia cmdlet, jak pokazano poniżej.

W tym przykładzie natychmiast zostanie uruchomione połączenie RDP, przechodząc przez podobne polecenia zgodnie z powyższymi instrukcjami.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Plik RDP można także zapisać do użytku w przyszłości.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z połączeniem, zobacz [Rozwiązywanie problemów z połączeniami pulpit zdalny](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json).
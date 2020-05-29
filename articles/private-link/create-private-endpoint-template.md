---
title: Szablon usługi Azure Private Endpoint ARM
description: Dowiedz się więcej o usłudze Azure Private link
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 97b38d9db53db5c53090df54b283e6e2c85f3e88
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84172340"
---
# <a name="create-a-private-endpoint---resource-manager-template"></a>Tworzenie prywatnego szablonu punktu końcowego Menedżer zasobów

W tym przewodniku szybki start użyjesz szablonu Menedżer zasobów, aby utworzyć prywatny punkt końcowy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki Start można także wykonać przy użyciu [Azure Portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](create-private-endpoint-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Ten szablon służy do tworzenia prywatnego punktu końcowego dla serwera SQL Azure.

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-private-endpoint-sql/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

W szablonie zdefiniowano wiele zasobów platformy Azure:


- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : prywatny punkt końcowy do uzyskiwania dostępu do prywatnego serwera SQL Azure
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : używany do rozpoznawania prywatnego adresu IP punktu końcowego 
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : Aby skojarzyć prywatny punkt końcowy z prywatną strefą DNS
- [**Microsoft. SQL/serwery**](/azure/templates/microsoft.sql/servers) : Azure SQL Server z przykładową bazą danych
- [**Microsoft. SQL/serwery/bazy danych**](/azure/templates/microsoft.sql/servers/databases) : przykładowa baza danych
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Virtual Network miejsce wdrożenia prywatnego punktu końcowego 
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicIpAddresses) : publiczny adres IP w celu uzyskania dostępu do maszyny wirtualnej
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : maszyna wirtualna do testowania połączenia prywatnego z prywatnym punktem końcowym do programu Azure SQL Server
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : interfejs sieciowy dla maszyny wirtualnej 

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon Menedżer zasobów na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon umożliwia utworzenie prywatnego punktu końcowego, serwera SQL Azure, infrastruktury sieciowej oraz maszyn wirtualnych do zweryfikowania.

   [![Wdróż na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów.
3. Wpisz identyfikator logowania i hasło administratora SQL.
3. Wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
3. Wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup**. Wdrożenie może potrwać 20 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia
> [!NOTE]
> szablon ARM generuje unikatową nazwę zasobu maszyny wirtualnej myVm<b>{unikatowy}</b> i dla zasobu usługi Azure SQL Server SqlServer<b>{unikatowy</b> identyfikator} Zastąp wartość <b>{Unique}</b> wartością wygenerowaną.

### <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm {unikatowym}* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm {unikatowy}*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

### <a name="access-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera SQL Database prywatnie z poziomu maszyny wirtualnej

W tej sekcji nawiążesz połączenie z serwerem SQL Database z maszyny wirtualnej przy użyciu prywatnego punktu końcowego.

 1. W Pulpit zdalny *myVM {unikatowy}* Otwórz program PowerShell.
 2. Wprowadź polecenie nslookup SqlServer {unikatowy identyfikator}. Database. Windows. NET   zostanie wyświetlony komunikat podobny do tego: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    sqlserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  sqlserver.database.windows.net 
```
 3. Zainstaluj SQL Server Management Studio 
 4. W obszarze Połącz z serwerem wprowadź lub wybierz następujące informacje: typ serwera: wybierz pozycję aparat bazy danych.
 Nazwa serwera: wybierz wartość SqlServer {unikatowy}. Database. Windows. NET username: Wprowadź nazwę użytkownika podaną podczas tworzenia.
 Hasło: wprowadź hasło podane podczas tworzenia.
 Zapamiętaj hasło: wybierz pozycję tak.
 
 5. Wybierz pozycję **Połącz**.
 6. Przeglądaj **bazy danych** z menu po lewej stronie.
 7. Zdefiniować Tworzenie lub zapytanie o informacje z *przykładowej bazy danych*
 8. Zamknij połączenie pulpitu zdalnego z *myVm {unikatowy}*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu prywatnego punktu końcowego, Usuń grupę zasobów. Spowoduje to usunięcie prywatnego punktu końcowego i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym łączu platformy Azure](private-link-overview.md)

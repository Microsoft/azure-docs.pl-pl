---
title: Szablon usługi Azure Private Endpoint ARM
description: Dowiedz się więcej o usłudze Azure Private link
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: af00119f1da3368b8592e020eee1ebb2a39a8501
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669957"
---
# <a name="quickstart-create-a-private-endpoint---resource-manager-template"></a>Szybki Start: Tworzenie prywatnego szablonu punktu końcowego Menedżer zasobów

W tym przewodniku szybki start użyjesz szablonu Menedżer zasobów, aby utworzyć prywatny punkt końcowy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki Start można także wykonać przy użyciu [Azure Portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](create-private-endpoint-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Ten szablon służy do tworzenia prywatnego punktu końcowego dla serwera SQL Azure.

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. SQL/serwery**](/azure/templates/microsoft.sql/servers) : Azure SQL Server z przykładową bazą danych
- [**Microsoft. SQL/serwery/bazy danych**](/azure/templates/microsoft.sql/servers/databases) : przykładowa baza danych
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Virtual Network miejsce wdrożenia prywatnego punktu końcowego
- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : prywatny punkt końcowy do uzyskiwania dostępu do prywatnego serwera SQL Azure
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : używany do rozpoznawania prywatnego adresu IP punktu końcowego
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : Aby skojarzyć prywatny punkt końcowy z prywatną strefą DNS
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicIpAddresses) : publiczny adres IP w celu uzyskania dostępu do maszyny wirtualnej
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : interfejs sieciowy dla maszyny wirtualnej
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : maszyna wirtualna do testowania połączenia prywatnego z prywatnym punktem końcowym do programu Azure SQL Server

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon Menedżer zasobów na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon umożliwia utworzenie prywatnego punktu końcowego, serwera SQL Azure, infrastruktury sieciowej oraz maszyn wirtualnych do zweryfikowania.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów,
3. Wpisz nazwę logowania i hasło administratora SQL
4. Wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
5. Wybierz pozycję **Zgadzam się na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup**. Wdrożenie może potrwać 20 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

> [!NOTE]
> Szablon ARM generuje unikatową nazwę zasobu maszyny wirtualnej myVm<b>{unikatowy}</b> i dla zasobu usługi Azure SQL Server SqlServer<b>{unikatowy</b> identyfikator} Zastąp wartość <b>{Unique}</b> wartością wygenerowaną.

### <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną _myVm {unikatowym}_ z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź _myVm {unikatowy}_.

2. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

3. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (_rdp_), który zostanie pobrany na komputer.

4. Otwórz pobrany \* plik RDP.

   a. Po wyświetleniu monitu wybierz pozycję **Połącz**.

   b. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

      > [!NOTE]
      > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

5. Wybierz przycisk **OK**.

6. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

7. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

### <a name="access-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera SQL Database prywatnie z poziomu maszyny wirtualnej

W tej sekcji nawiążesz połączenie z serwerem SQL Database z maszyny wirtualnej przy użyciu prywatnego punktu końcowego.

1.  W Pulpit zdalny _myVM {unikatowy}_ Otwórz program PowerShell.
2.  Wprowadź polecenie nslookup SqlServer {unikatowy identyfikator}. Database. Windows. NET   zostanie wyświetlony komunikat podobny do tego:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Zainstaluj SQL Server Management Studio
4.  W obszarze Połącz z serwerem wprowadź lub wybierz następujące informacje: typ serwera: wybierz pozycję aparat bazy danych.
    Nazwa serwera: wybierz wartość SqlServer {unikatowy}. Database. Windows. NET username: Wprowadź nazwę użytkownika podaną podczas tworzenia.
    Hasło: wprowadź hasło podane podczas tworzenia.
    Zapamiętaj hasło: wybierz pozycję tak.

5.  Wybierz pozycję **Połącz**.
6.  Przeglądaj **bazy danych** z menu po lewej stronie.
7.  Zdefiniować Tworzenie lub zapytanie o informacje z _przykładowej bazy danych_
8.  Zamknij połączenie pulpitu zdalnego z _myVm {unikatowy}_.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu prywatnego punktu końcowego, Usuń grupę zasobów. Spowoduje to usunięcie prywatnego punktu końcowego i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [prywatnym łączu platformy Azure](private-link-overview.md)

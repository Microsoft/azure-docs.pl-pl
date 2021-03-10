---
title: Szybki Start — tworzenie prywatnego punktu końcowego przy użyciu szablonu ARM
description: W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM), aby utworzyć prywatny punkt końcowy.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: e80adc16e08e676ac2daabec01a11c10d537c547
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562949"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu szablonu ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM), aby utworzyć prywatny punkt końcowy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Możesz również ukończyć ten przewodnik Szybki Start przy użyciu [Azure Portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](create-private-endpoint-cli.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz konta platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten szablon służy do tworzenia prywatnego punktu końcowego dla wystąpienia Azure SQL Database.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. SQL/serwery**](/azure/templates/microsoft.sql/servers): wystąpienie SQL Database z przykładową bazą danych.
- [**Microsoft. SQL/serwery/bazy danych**](/azure/templates/microsoft.sql/servers/databases): przykładowa baza danych.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): Sieć wirtualna, w której wdrożono prywatny punkt końcowy.
- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): prywatny punkt końcowy do uzyskiwania dostępu do wystąpienia SQL Database.
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): Strefa użyta do rozpoznania prywatnego adresu IP punktu końcowego.
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): Grupa stref używana do kojarzenia prywatnego punktu końcowego z prywatną strefą DNS.
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicIpAddresses): publiczny adres IP używany do uzyskiwania dostępu do maszyny wirtualnej.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): interfejs sieciowy dla maszyny wirtualnej.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): maszyna wirtualna używana do testowania połączenia prywatnego z prywatnym punktem końcowym do wystąpienia elementu SQL Database.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Poniżej przedstawiono sposób wdrażania szablonu ARM na platformie Azure:

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz pozycję **Wdróż na platformie Azure**. Szablon umożliwia utworzenie prywatnego punktu końcowego, wystąpienia SQL Database, infrastruktury sieciowej i maszyny wirtualnej do zweryfikowania.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów.
3. Wpisz hasło administratora SQL i zaloguj się.
4. Wpisz nazwę użytkownika i hasło administratora maszyny wirtualnej.
5. Zapoznaj się z instrukcjami dotyczącymi warunków i postanowień. Jeśli zgadzasz się, wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**  >  . Wdrożenie może potrwać 20 minut lub dłużej.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

> [!NOTE]
> Szablon ARM generuje unikatową nazwę zasobu maszyny wirtualnej myVm<b>{unikatowy}</b> i dla zasobu SQL Database SqlServer<b>{unikatowy identyfikator}</b> . Zastąp wygenerowaną wartość **{unikatowy}**.

### <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną _myVm {unikatowym}_ z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź _myVm {unikatowy}_.

2. Wybierz pozycję **Połącz**. Zostanie otwarte okno **łączenie z maszyną wirtualną** .

3. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol (_RDP_) i pobiera go na komputer.

4. Otwórz pobrany plik RDP.

   a. Po wyświetleniu monitu wybierz pozycję **Połącz**.

   b. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

      > [!NOTE]
      > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

5. Wybierz przycisk **OK**.

6. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

7. Po wyświetleniu pulpitu maszyny wirtualnej Zminimalizuj go, aby wrócić do pulpitu lokalnego.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>Uzyskaj dostęp do serwera SQL Database prywatnie z maszyny wirtualnej

Poniżej przedstawiono sposób nawiązywania połączenia z serwerem SQL Database z maszyny wirtualnej przy użyciu prywatnego punktu końcowego.

1.  W Pulpit zdalny _myVM {unikatowy}_ Otwórz program PowerShell.
2.  Wprowadź następujące polecenie: nslookup SqlServer {unikatowy}. Database. Windows. NET. 
    Zostanie wyświetlony komunikat podobny do tego:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Zainstaluj SQL Server Management Studio.
4.  W obszarze **Połącz z serwerem** wprowadź lub wybierz następujące informacje:
    - **Typ serwera**: wybierz pozycję **aparat bazy danych**.
    - **Nazwa serwera**: wybierz pozycję **SqlServer {unikatowy}. Database. Windows. NET**.
    - **Nazwa użytkownika**: Wprowadź nazwę użytkownika podaną podczas tworzenia.
    - **Hasło**: wprowadź hasło podane podczas tworzenia.
    - **Zapamiętaj hasło**: wybierz pozycję  **tak**.

5.  Wybierz pozycję **Połącz**.
6.  Z menu po lewej stronie wybierz pozycję **bazy danych**.
7.  Opcjonalnie można utworzyć lub zbadać informacje z _przykładowej bazy danych_.
8.  Zamknij Pulpit zdalny połączenie, aby _myVm {unikatowy}_.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu prywatnego punktu końcowego, Usuń grupę zasobów. Spowoduje to usunięcie prywatnego punktu końcowego i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usług, które obsługują prywatny punkt końcowy, zobacz:
> [!div class="nextstepaction"]
> [Dostępność linku prywatnego](private-link-overview.md#availability)

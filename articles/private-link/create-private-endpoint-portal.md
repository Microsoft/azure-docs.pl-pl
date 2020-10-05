---
title: Szybki Start — zarządzanie prywatnymi punktami końcowymi na platformie Azure
description: Dowiedz się, jak utworzyć prywatny punkt końcowy przy użyciu Azure Portal w tym przewodniku Szybki Start
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: ef6d49c9046ba04bbac40ec9bf555e12d2faa8f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84021708"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal

Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych. W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną na platformie Azure Virtual Network, logicznym serwerze SQL z prywatnym punktem końcowym platformy Azure przy użyciu Azure Portal. Następnie możesz bezpiecznie uzyskać dostęp do SQL Database z maszyny wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku (w tym przykładzie programu SQL Server na platformie Azure).

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz Virtual Network i podsieć, która będzie hostować maszynę wirtualną, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku.

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | Zachodnio-środkowe stany USA    |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Utwórz maszynę wirtualną

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region | Wybierz pozycję **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter**. |
    | Rozmiar | Pozostaw domyślną wartość **Standard DS1 v2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **nie**. |
    |||

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Tworzenie maszyny wirtualnej — dyski** pozostaw wartości domyślne i wybierz przycisk **Dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(New) myVm-IP**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    |||


1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

1. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

## <a name="create-a-logical-sql-server"></a>Tworzenie logicznego serwera SQL

W tej sekcji utworzysz logiczny serwer SQL Server na platformie Azure. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **Databases**  >  **bazy danych SQL Database**.

1. W obszarze **Tworzenie bazy danych SQL — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły bazy danych** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa bazy danych  | Wprowadź *bazę danych*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    |||
5. W obszarze **serwer**wybierz pozycję **Utwórz nowy**. 
6. W obszarze **nowy serwer**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Nazwa serwera  | Wprowadź *tekst*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
    | Identyfikator logowania administratora serwera| Wprowadź wybraną nazwę administratora. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 8 znaków i spełniać zdefiniowane wymagania. |
    | Lokalizacja | Wybierz region platformy Azure, w którym chcesz mieć SQL Server. |
    
7. Wybierz przycisk **OK**. 
8. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację. 
9. Po wyświetleniu komunikatu Sprawdzanie poprawności zakończone powodzeniem kliknij przycisk **Utwórz**. 
10. Po wyświetleniu komunikatu Sprawdzanie poprawności zakończone powodzeniem kliknij przycisk Utwórz. 

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz program SQL Server i dodasz do niego prywatny punkt końcowy. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **prywatne centrum linków (wersja zapoznawcza)**.
2. W **centrum linków prywatnych — Omówienie**opcji **tworzenia połączenia prywatnego z usługą**wybierz pozycję **Rozpocznij**.
1. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa | Wprowadź *myPrivateEndpoint*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    |Region|Wybierz pozycję **WestCentralUS**.|
    |||
5. Wybierz pozycję **Dalej: zasób**.
6. W obszarze **Utwórz prywatny punkt końcowy zasobu**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. SQL/Server**. |
    | Zasób |Wybierz *pozycję* Wyznacz|
    |Docelowy zasób podrzędny |Wybierz *sqlServer*|
    |||
7. Wybierz pozycję **Dalej: Konfiguracja**.
8. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — Konfiguracja**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**SIEĆ**| |
    | Sieć wirtualna| Wybierz pozycję *MyVirtualNetwork*. |
    | Podsieć | Wybierz pozycję Moja *podsieć*. |
    |**INTEGRACJA PRYWATNEJ USŁUGI DNS**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |SELECT *(New) privatelink. Database. Windows. NET* |
    |||

1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację. 
2. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu Pulpitu zdalnego (RDP)


Po utworzeniu **myVm**Połącz się z nim za pośrednictwem Internetu w następujący sposób: 

1. Na pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol (*RDP*) i pobiera go na komputer.

1. Otwórz *pobrany plik RDP* .

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz pozycję **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-sql-database-privately-from-the-vm"></a>Dostęp SQL Database prywatnie z maszyny wirtualnej

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.

2. Wprowadź `nslookup myserver.database.windows.net`. 

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Zainstaluj [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. W obszarze **Połącz z serwerem**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera| Wybierz pozycję **Aparat bazy danych**.|
    | Nazwa serwera| Wybierz *MyServer.Database.Windows.NET* |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika, username@servername która jest dostępna podczas tworzenia programu SQL Server. |
    |Hasło |Wprowadź hasło podane podczas tworzenia programu SQL Server. |
    |Remember password (Zapamiętaj hasło)|Wybierz pozycję **Tak**.|
    |||
1. Wybierz pozycję **Połącz**.
2. Przeglądaj bazy danych z menu po lewej stronie.
3. Zdefiniować Utwórz lub zapytaj informacje z bazy danych.
4. Zamknij połączenie pulpitu zdalnego z *myVm*. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego, programu SQL Server i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby: 
1. Wprowadź w polu **wyszukiwania** w górnej części portalu *i wybierz pozycję* *moja zasobów z* wyników wyszukiwania. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę myResourceGroup, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono MASZYNę wirtualną w sieci wirtualnej, logicznym serwerze SQL i prywatnym punkcie końcowym dostępu prywatnego. Połączenie z jedną maszyną wirtualną z Internetu i bezpieczne komunikowanie się SQL Database przy użyciu prywatnego linku. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz [co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md).

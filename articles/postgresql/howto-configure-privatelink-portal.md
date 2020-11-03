---
title: Prywatny link-Azure Portal-Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować link prywatny dla Azure Database for PostgreSQL-pojedynczego serwera z Azure Portal
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: 98d3beef72d314f93f6a2bc580b1dd5de5735f23
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242470"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-portal"></a>Tworzenie prywatnego linku do Azure Database for PostgreSQL-pojedynczego serwera przy użyciu portalu i zarządzanie nim

Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych.  W tym artykule dowiesz się, jak za pomocą Azure Portal utworzyć maszynę wirtualną w usłudze Azure Virtual Network i Azure Database for PostgreSQL pojedynczym serwerze przy użyciu prywatnego punktu końcowego platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Funkcja Link prywatny jest dostępna tylko dla serwerów Azure Database for PostgreSQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer bazy danych znajduje się w jednej z tych warstw cenowych.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Tworzenie maszyny wirtualnej platformy Azure

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku (serwer PostgreSQL na platformie Azure).

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej
W tej sekcji utworzysz Virtual Network i podsieć, która będzie hostować maszynę wirtualną, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **sieci wirtualnej**.
2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową** , wprowadź nazwę *myResourceGroup* , a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Wybierz pozycję **Europa Zachodnia**.|
    | Podsieć — nazwa | Wprowadź nazwę moja *podsieć*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
    |||
3. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.

### <a name="create-virtual-machine"></a>Utwórz maszynę wirtualną

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**.

2. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region | Wybierz pozycję **Europa Zachodnia**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter**. |
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


1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie** , na której platforma Azure zweryfikuje konfigurację.

1. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

> [!NOTE]
> W niektórych przypadkach Azure Database for PostgreSQL i podsieć wirtualna znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
> - Upewnij się, że w subskrypcji jest zarejestrowany dostawca zasobów **Microsoft. DBforPostgreSQL** . Aby uzyskać więcej informacji, zobacz temat [Resource-Manager-Registration][resource-manager-portal]

## <a name="create-an-azure-database-for-postgresql-single-server"></a>Tworzenie Azure Database for PostgreSQL pojedynczego serwera

W tej sekcji utworzysz serwer Azure Database for PostgreSQL na platformie Azure. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz**  >  **bazę danych** zasobów  >  **Azure Database for PostgreSQL**.

1. W obszarze **Opcja wdrażania Azure Database for PostgreSQL** wybierz pozycję **pojedynczy serwer** i podaj następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **Szczegóły serwera** |  |
    |Nazwa serwera  | Wprowadź *tekst*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
    | Nazwa użytkownika administratora| Wprowadź wybraną nazwę administratora. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 8 znaków i spełniać zdefiniowane wymagania. |
    | Lokalizacja | Wybierz region platformy Azure, w którym ma być przechowywany serwer PostgreSQL. |
    |Wersja  | Wybierz wymaganą wersję bazy danych serwera PostgreSQL.|
    | Obliczenia i magazyn| Wybierz warstwę cenową, która jest wymagana dla serwera na podstawie obciążenia. |
    |||
 
7. Wybierz przycisk **OK**. 
8. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie** , na której platforma Azure zweryfikuje konfigurację. 
9. Gdy zobaczysz komunikat o przekazaniu walidacji, wybierz pozycję **Utwórz**. 
10. Gdy zobaczysz komunikat o przekazaniu walidacji, wybierz pozycję Utwórz. 

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz serwer PostgreSQL i dodasz do niego prywatny punkt końcowy. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **prywatny link**.
2. W **centrum linków prywatnych — Omówienie** opcji **tworzenia połączenia prywatnego z usługą** wybierz pozycję **Rozpocznij**.

    :::image type="content" source="media/concepts-data-access-and-security-private-link/privatelink-overview.png" alt-text="Omówienie linku prywatnego":::

1. W obszarze **Tworzenie prywatnego punktu końcowego — podstawy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź *myPrivateEndpoint*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    |Region|Wybierz pozycję **Europa Zachodnia**.|
    |||
5. Wybierz pozycję **Dalej: zasób**.
6. W obszarze **Utwórz prywatny punkt końcowy zasobu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. DBforPostgreSQL/serwery**. |
    | Zasób |Wybierz *pozycję* Wyznacz|
    |Docelowy zasób podrzędny |Wybierz *postgresqlServer*|
    |||
7. Wybierz pozycję **Dalej: Konfiguracja**.
8. W obszarze **Tworzenie prywatnego punktu końcowego — konfiguracja** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**SIEĆ**| |
    | Sieć wirtualna| Wybierz pozycję *MyVirtualNetwork*. |
    | Podsieć | Wybierz pozycję Moja *podsieć*. |
    |**INTEGRACJA PRYWATNEJ USŁUGI DNS**||
    |Integruj z prywatną strefą DNS |Wybierz pozycję **Tak**. |
    |Prywatna strefa DNS |SELECT *(New) privatelink. Postgres. Database. Azure. com* |
    |||

    > [!Note] 
    > Użyj wstępnie zdefiniowanej prywatnej strefy DNS dla usługi lub podaj nazwę preferowanego strefy DNS. Aby uzyskać szczegółowe informacje, zapoznaj się z [konfiguracją strefy DNS usług platformy Azure](../private-link/private-endpoint-dns.md) .

1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie** , na której platforma Azure zweryfikuje konfigurację. 
2. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**. 

    :::image type="content" source="media/concepts-data-access-and-security-private-link/show-postgres-private-link.png" alt-text="Utworzono link prywatny":::

    > [!NOTE] 
    > Nazwa FQDN w ustawieniu DNS klienta nie jest rozpoznawana jako prywatny adres IP skonfigurowany. Konieczne będzie skonfigurowanie strefy DNS dla skonfigurowanej nazwy FQDN, jak pokazano [poniżej](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu Pulpitu zdalnego (RDP)


Po utworzeniu **myVm** Połącz się z nim za pośrednictwem Internetu w następujący sposób: 

1. Na pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol ( *RDP* ) i pobiera go na komputer.

1. Otwórz *pobrany plik RDP* .

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta** , aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz pozycję **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Dostęp do serwera PostgreSQL z maszyny wirtualnej do prywatnego

1. W Pulpit zdalny *myVM* Otwórz program PowerShell.

2. Wprowadź  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com` . 

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Przetestuj połączenie prywatne linku dla serwera PostgreSQL przy użyciu dowolnego dostępnego klienta. W poniższym przykładzie użyto [usługi Azure Data Studio](/sql/azure-data-studio/download?view=sql-server-ver15) do wykonania tej operacji.

4. W obszarze **nowe połączenie** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera| Wybierz pozycję **PostgreSQL**.|
    | Nazwa serwera| Wybierz *mydemopostgresserver.privatelink.Postgres.Database.Azure.com* |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika, username@servername która jest dostępna podczas tworzenia serwera PostgreSQL. |
    |Hasło |Wprowadź hasło podane podczas tworzenia serwera PostgreSQL. |
    |Protokół SSL|Wybierz pozycję **wymagane**.|
    ||

5. Wybierz pozycję Połącz.

6. Przeglądaj bazy danych z menu po lewej stronie.

7. Zdefiniować Utwórz lub Zbadaj informacje z serwera postgreSQL.

8. Zamknij połączenie pulpitu zdalnego z myVm.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy skończysz korzystać z prywatnego punktu końcowego, serwera PostgreSQL i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wprowadź *w* polu **wyszukiwania** w górnej części portalu  *i wybierz pozycję* moja zasobów   z wyników wyszukiwania.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź wartość webresourcename **, aby wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym instruktażu utworzono MASZYNę wirtualną w sieci wirtualnej, na serwerze Azure Database for PostgreSQL-pojedynczym i prywatnym punkcie końcowym dostępu prywatnego. Nawiązano połączenie z jedną maszyną wirtualną z Internetu i bezpiecznie komunikuje się z serwerem PostgreSQL za pomocą linku prywatnego. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz [co to jest prywatny punkt końcowy platformy Azure](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
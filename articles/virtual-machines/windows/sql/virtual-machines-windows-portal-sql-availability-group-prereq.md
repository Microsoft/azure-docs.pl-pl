---
title: 'Samouczek: wymagania wstępne dotyczące grupy dostępności'
description: W tym samouczku pokazano, jak skonfigurować wymagania wstępne dotyczące tworzenia grupy dostępności programu SQL Server zawsze włączone na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: c80a4c07e0649612b4c024cac79833c4b730f55e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060168"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Wymagania wstępne dotyczące tworzenia zawsze dostępnych grup dostępności na SQL Server na maszynach wirtualnych platformy Azure

W tym samouczku pokazano, jak wykonać wymagania wstępne związane z tworzeniem [SQL Server zawsze włączone grupy dostępności na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Po zakończeniu wymagań wstępnych masz kontroler domeny, dwie SQL Server maszyny wirtualne i serwer monitora w jednej grupie zasobów.

**Szacowany czas**: ukończenie wymagań wstępnych może potrwać kilka godzin. Wiele z nich poświęca więcej czasu na tworzenie maszyn wirtualnych.

Na poniższym diagramie przedstawiono sposób kompilowania w samouczku.

![Grupa dostępności](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Przejrzyj dokumentację grupy dostępności

W tym samouczku przyjęto założenie, że masz podstawową wiedzę na temat SQL Server zawsze włączonymi grupami dostępności. Jeśli nie znasz tej technologii, zobacz temat [Omówienie zawsze włączonych grup dostępności (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Tworzenie konta platformy Azure
Musisz mieć konto platformy Azure. Możesz [otworzyć bezpłatne konto platformy Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) lub [aktywować korzyści dla subskrybentów programu Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij **+** , aby utworzyć nowy obiekt w portalu.

   ![Nowy obiekt](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Wpisz **grupę zasobów** w oknie wyszukiwania w **portalu Marketplace** .

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Kliknij pozycję **Grupa zasobów**.
5. Kliknij przycisk **Utwórz**.
6. W polu **Nazwa grupy zasobów**wpisz nazwę grupy zasobów. Na przykład wpisz **SQL-ha-RG**.
7. Jeśli masz wiele subskrypcji platformy Azure, sprawdź, czy subskrypcja jest subskrypcją platformy Azure, w której chcesz utworzyć grupę dostępności.
8. Wybierz lokalizację. Lokalizacja jest regionem świadczenia usługi Azure, w którym chcesz utworzyć grupę dostępności. Ten artykuł kompiluje wszystkie zasoby w jednej lokalizacji platformy Azure.
9. Sprawdź, czy jest zaznaczone pole wyboru **Przypnij do pulpitu nawigacyjnego** . To ustawienie opcjonalne umieszcza skrót dla grupy zasobów na pulpicie nawigacyjnym Azure Portal.

   ![Grupa zasobów](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Kliknij pozycję **Utwórz**, aby utworzyć grupę zasobów.

Platforma Azure tworzy grupę zasobów i przypina skrót do grupy zasobów w portalu.

## <a name="create-the-network-and-subnets"></a>Tworzenie sieci i podsieci
Następnym krokiem jest utworzenie sieci i podsieci w grupie zasobów platformy Azure.

Rozwiązanie używa jednej sieci wirtualnej z dwiema podsieciami. [Omówienie sieci wirtualnej](../../../virtual-network/virtual-networks-overview.md) zawiera więcej informacji na temat sieci na platformie Azure.

Aby utworzyć sieć wirtualną:

1. W Azure Portal w grupie zasobów kliknij pozycję **+ Dodaj**. 

   ![Nowy element](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Wyszukaj w **sieci wirtualnej**.

     ![Wyszukaj sieć wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Kliknij pozycję **Sieć wirtualna**.
4. W **sieci wirtualnej**kliknij **Menedżer zasobów** model wdrażania, a następnie kliknij przycisk **Utwórz**.

    W poniższej tabeli przedstawiono ustawienia sieci wirtualnej:

   | **Pole** | Wartość |
   | --- | --- |
   | **Nazwa** |autoHAVNET |
   | **Przestrzeń adresowa** |10.33.0.0/24 |
   | **Nazwa podsieci** |Administrator |
   | **Zakres adresów podsieci** |10.33.0.0/29 |
   | **Subskrypcja** |Określ subskrypcję, która ma zostać użyta. **Subskrypcja** jest pusta, jeśli masz tylko jedną subskrypcję. |
   | **Grupa zasobów** |Wybierz pozycję **Użyj istniejącej** i wybierz nazwę grupy zasobów. |
   | **Lokalizacja** |Określ lokalizację platformy Azure. |

   Przestrzeń adresowa i zakres adresów podsieci mogą różnić się od tabeli. W zależności od subskrypcji Portal sugeruje dostępną przestrzeń adresową i odpowiedni zakres adresów podsieci. Jeśli nie jest dostępna wystarczająca przestrzeń adresowa, użyj innej subskrypcji.

   W przykładzie jest użyta nazwa podsieci **administrator**. Ta podsieć jest dla kontrolerów domeny.

5. Kliknij przycisk **Utwórz**.

   ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

System Azure powraca do pulpitu nawigacyjnego portalu i powiadamia o utworzeniu nowej sieci.

### <a name="create-a-second-subnet"></a>Utwórz drugą podsieć
Nowa sieć wirtualna ma jedną podsieć o nazwie **admin**. Kontrolery domeny używają tej podsieci. Maszyny wirtualne SQL Server używają drugiej podsieci o nazwie **SQL**. Aby skonfigurować tę podsieć:

1. Na pulpicie nawigacyjnym kliknij utworzoną grupę zasobów — **SQL-ha-RG**. Znajdź sieć w grupie zasobów w obszarze **zasoby**.

    Jeśli polecenie **SQL-ha-RG** nie jest widoczne, Znajdź je przez kliknięcie pozycji **grupy zasobów** i filtrowanie według nazwy grupy zasobów.
2. Kliknij pozycję **autoHAVNET** na liście zasobów. 
3. W sieci wirtualnej **autoHAVNET** w obszarze **Ustawienia** wybierz pozycję **podsieci**.

    Zanotuj już utworzoną podsieć.

   ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Utwórz drugą podsieć. Kliknij pozycję **+ podsieć**.
6. W obszarze **Dodaj podsieć**Skonfiguruj podsieć, wpisując w polu **Nazwa**wartość **sqlsubnet** . Platforma Azure automatycznie określa prawidłowy **zakres adresów**. Sprawdź, czy ten zakres adresów zawiera co najmniej 10 adresów. W środowisku produkcyjnym może być wymagane więcej adresów.
7. Kliknij przycisk **OK**.

    ![Konfigurowanie sieci wirtualnej](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

Poniższa tabela zawiera podsumowanie ustawień konfiguracji sieci:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |**autoHAVNET** |
| **Przestrzeń adresowa** |Ta wartość zależy od dostępnych przestrzeni adresowych w ramach subskrypcji. Typową wartością jest 10.0.0.0/16. |
| **Nazwa podsieci** |**administracja** |
| **Zakres adresów podsieci** |Ta wartość jest zależna od dostępnych zakresów adresów w Twojej subskrypcji. Typową wartością jest 10.0.0.0/24. |
| **Nazwa podsieci** |**sqlsubnet** |
| **Zakres adresów podsieci** |Ta wartość jest zależna od dostępnych zakresów adresów w Twojej subskrypcji. Typową wartością jest 10.0.1.0/24. |
| **Subskrypcja** |Określ subskrypcję, która ma zostać użyta. |
| **Grupa zasobów** |**SQL-HA — RG** |
| **Lokalizacja** |Określ tę samą lokalizację, którą wybrano dla grupy zasobów. |

## <a name="create-availability-sets"></a>Tworzenie zestawów dostępności

Przed utworzeniem maszyn wirtualnych należy utworzyć zestawy dostępności. Zestawy dostępności zmniejszają czas przestoju planowanych lub nieplanowanych zdarzeń konserwacji. Zestaw dostępności platformy Azure to logiczna Grupa zasobów, którą usługa Azure umieszcza w fizycznych domenach błędów i domenach aktualizacji. Domena błędów zapewnia, że członkowie zestawu dostępności mają osobne zasoby dotyczące mocy i sieci. Domena aktualizacji zapewnia, że członkowie zestawu dostępności nie są w tym samym czasie przełączane do trybu konserwacji. Aby uzyskać więcej informacji, zobacz [Manage the availability of virtual machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Zarządzanie dostępnością maszyn wirtualnych).

Potrzebne są dwa zestawy dostępności. Jeden z nich jest przeznaczony dla kontrolerów domeny. Sekunda jest dla SQL Server maszyn wirtualnych.

Aby utworzyć zestaw dostępności, przejdź do grupy zasobów, a następnie kliknij przycisk **Dodaj**. Filtruj wyniki, wpisując **zestaw dostępności**. W wynikach kliknij pozycję **zestaw dostępności** , a następnie kliknij pozycję **Utwórz**.

Skonfiguruj dwa zestawy dostępności zgodnie z parametrami w poniższej tabeli:

| **Pole** | Zestaw dostępności kontrolera domeny | SQL Server zestaw dostępności |
| --- | --- | --- |
| **Nazwa** |adavailabilityset |sqlavailabilityset |
| **Grupa zasobów** |SQL-HA — RG |SQL-HA — RG |
| **Domeny błędów** |3 |3 |
| **Domeny aktualizacji** |5 |3 |

Po utworzeniu zestawów dostępności Wróć do grupy zasobów w Azure Portal.

## <a name="create-domain-controllers"></a>Tworzenie kontrolerów domeny
Po utworzeniu sieci, podsieci i zestawów dostępności można przystąpić do tworzenia maszyn wirtualnych dla kontrolerów domeny.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Tworzenie maszyn wirtualnych dla kontrolerów domeny
Aby utworzyć i skonfigurować kontrolery domeny, Wróć do grupy zasobów **SQL-ha-RG** .

1. Kliknij pozycję **Dodaj**. 
2. Wpisz **Windows Server 2016 Datacenter**.
3. Kliknij pozycję **Windows Server 2016 Datacenter**. W **systemie Windows Server 2016 Datacenter**Sprawdź, czy model wdrażania jest **Menedżer zasobów**, a następnie kliknij przycisk **Utwórz**. 

Powtórz powyższe kroki, aby utworzyć dwie maszyny wirtualne. Nadaj nazwę obu maszynom wirtualnym:

* AD-Primary-DC
* AD-pomocniczy — DC

  > [!NOTE]
  > Maszyna wirtualna **AD-pomocnicza kontrolera domeny** jest opcjonalna, aby zapewnić wysoką dostępność Active Directory Domain Services.
  >
  >

W poniższej tabeli przedstawiono ustawienia tych dwóch maszyn:

| **Pole** | Wartość |
| --- | --- |
| **Nazwa** |Pierwszy kontroler domeny: *AD-Primary-DC*.</br>Drugi kontroler domeny *AD-pomocniczy — DC*. |
| **Typ dysku maszyny wirtualnej** |SSD |
| **Nazwa użytkownika** |Administrator domeny |
| **Hasło** |Contoso! 0000 |
| **Subskrypcja** |*Twoja subskrypcja* |
| **Grupa zasobów** |SQL-HA — RG |
| **Lokalizacja** |*Twoja lokalizacja* |
| **Rozmiar** |DS1_V2 |
| **Storage** | **Użyj dysków** - zarządzanych**tak** |
| **Sieć wirtualna** |autoHAVNET |
| **Podsieci** |administrator |
| **Publiczny adres IP** |*Taka sama nazwa jak maszyna wirtualna* |
| **Sieciowa Grupa zabezpieczeń** |*Taka sama nazwa jak maszyna wirtualna* |
| **Zestaw dostępności** |adavailabilityset </br>**Domeny błędów**: 2 </br>**Domeny aktualizacji**: 2|
| **Diagnostyka** |Enabled (Włączony) |
| **Konto magazynu diagnostyki** |*Utworzone automatycznie* |

   >[!IMPORTANT]
   >Maszynę wirtualną można umieścić w zestawie dostępności tylko podczas jej tworzenia. Nie można zmienić zestawu dostępności po utworzeniu maszyny wirtualnej. Zobacz [Zarządzanie dostępnością maszyn wirtualnych](../manage-availability.md).

Platforma Azure tworzy maszyny wirtualne.

Po utworzeniu maszyn wirtualnych Skonfiguruj kontroler domeny.

### <a name="configure-the-domain-controller"></a>Konfiguruj kontroler domeny
W poniższych krokach skonfiguruj komputer **AD-Primary-DC** jako kontroler domeny dla Corp.contoso.com.

1. W portalu Otwórz grupę zasobów **SQL-ha-RG** i wybierz maszynę **AD-podstawowa-DC** . Na serwerze **AD-Primary-DC**kliknij pozycję **Połącz** , aby otworzyć plik RDP na potrzeby dostępu do pulpitu zdalnego.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Zaloguj się przy użyciu skonfigurowanego konta administratora (**\DomainAdmin**) i hasła (**contoso! 0000**).
3. Domyślnie powinien zostać wyświetlony pulpit nawigacyjny **Menedżer serwera** .
4. Kliknij link **Dodaj role i funkcje** na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Wybierz pozycję **dalej** , dopóki nie uzyskasz do sekcji **role serwera** .
6. Wybierz role serwera **Active Directory Domain Services** i **DNS** . Po wyświetleniu monitu Dodaj wszelkie dodatkowe funkcje, które są wymagane przez te role.

   > [!NOTE]
   > System Windows wyświetli ostrzeżenie, że nie ma statycznego adresu IP. Jeśli testujesz konfigurację, kliknij przycisk **Kontynuuj**. W przypadku scenariuszy produkcyjnych Ustaw adres IP na statyczny w Azure Portal lub [Użyj programu PowerShell, aby ustawić statyczny adres IP komputera kontrolera domeny](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Okno dialogowe Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Klikaj przycisk **dalej** , aż przejdziesz do sekcji **potwierdzenie** . Zaznacz pole wyboru **Automatycznie uruchom ponownie serwer docelowy, jeśli** jest to wymagane.
8. Kliknij przycisk **Zainstaluj**.
9. Po zakończeniu instalacji funkcji Wróć do pulpitu nawigacyjnego **Menedżer serwera** .
10. Wybierz opcję Nowy **AD DS** w okienku po lewej stronie.
11. Kliknij link **więcej** na żółtym pasku ostrzegawczym.

    ![Okno dialogowe AD DS na maszynie wirtualnej serwera DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. W kolumnie **Akcja** okna dialogowego **wszystkie szczegóły zadania serwera** kliknij pozycję **Podnieś poziom tego serwera do poziomu kontrolera domeny**.
13. W **Kreatorze konfiguracji Active Directory Domain Services**Użyj następujących wartości:

    | **Stronic** | Ustawienie |
    | --- | --- |
    | **Konfiguracja wdrażania** |**Dodawanie nowego lasu**<br/> **Nazwa domeny głównej** = Corp.contoso.com |
    | **Opcje kontrolera domeny** |**Hasło DSRM** = contoso! 0000<br/>**Potwierdź hasło** = contoso! 0000 |
14. Kliknij przycisk **dalej** , aby przejść przez pozostałe strony kreatora. Na stronie **Sprawdzanie wymagań wstępnych** Sprawdź, czy jest wyświetlany następujący komunikat: **wszystkie testy wymagań wstępnych zostały pomyślnie zakończone**powodzeniem. Możesz przejrzeć wszystkie odpowiednie komunikaty ostrzegawcze, ale można kontynuować instalację.
15. Kliknij przycisk **Zainstaluj**. Maszyna wirtualna **AD-podstawowa-DC** automatycznie wykonuje ponowny rozruch.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Zanotuj adres IP podstawowego kontrolera domeny

Użyj podstawowego kontrolera domeny dla systemu DNS. Zanotuj adres IP podstawowego kontrolera domeny.

Jednym ze sposobów uzyskania adresu IP podstawowego kontrolera domeny jest użycie Azure Portal.

1. Na Azure Portal Otwórz grupę zasobów.

2. Kliknij podstawowy kontroler domeny.

3. Na podstawowym kontrolerze domeny kliknij pozycję **interfejsy sieciowe**.

![Interfejsy sieciowe](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Zanotuj prywatny adres IP dla tego serwera.

### <a name="configure-the-virtual-network-dns"></a>Konfigurowanie usługi DNS w sieci wirtualnej
Po utworzeniu pierwszego kontrolera domeny i włączeniu usługi DNS na pierwszym serwerze skonfiguruj sieć wirtualną tak, aby korzystała z tego serwera dla systemu DNS.

1. W Azure Portal kliknij sieć wirtualną.

2. W obszarze **Ustawienia**kliknij pozycję **serwer DNS**.

3. Kliknij pozycję **niestandardowe**i wpisz prywatny adres IP podstawowego kontrolera domeny.

4. Kliknij przycisk **Zapisz**.

### <a name="configure-the-second-domain-controller"></a>Skonfiguruj drugi kontroler domeny
Po ponownym uruchomieniu podstawowego kontrolera domeny można skonfigurować drugi kontroler domeny. Ten opcjonalny krok zapewnia wysoką dostępność. Wykonaj następujące kroki, aby skonfigurować drugi kontroler domeny:

1. W portalu Otwórz grupę zasobów **SQL-ha-RG** i wybierz pozycję **AD----------kontroler-DC** . W obszarze **AD-pomocniczy kontroler domeny**kliknij pozycję **Połącz** , aby otworzyć plik RDP na potrzeby dostępu do pulpitu zdalnego.
2. Zaloguj się do maszyny wirtualnej przy użyciu skonfigurowanego konta administratora (**BUILTIN\DomainAdmin**) i hasła (**contoso! 0000**).
3. Zmień preferowany adres serwera DNS na adres kontrolera domeny.
4. W **Centrum sieci i udostępniania**kliknij interfejs sieciowy.
   ![Interfejs sieciowy](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Kliknij pozycję **Właściwości**.
6. Wybierz pozycję **Protokół internetowy w wersji 4 (TCP/IPv4)** , a następnie kliknij pozycję **Właściwości**.
7. Wybierz opcję **Użyj następujących adresów serwerów DNS** i określ adres podstawowego kontrolera domeny w **preferowanym serwerze DNS**.
8. Kliknij przycisk **OK**, a następnie **Zamknij** , aby zatwierdzić zmiany. Teraz można przyłączyć maszynę wirtualną do **Corp.contoso.com**.

   >[!IMPORTANT]
   >Jeśli nastąpi utrata połączenia z pulpitem zdalnym po zmianie ustawienia DNS, przejdź do Azure Portal i ponownie uruchom maszynę wirtualną.

9. Z poziomu pulpitu zdalnego na pomocniczy kontroler domeny Otwórz **Menedżer serwera pulpit nawigacyjny**.
10. Kliknij link **Dodaj role i funkcje** na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Wybierz pozycję **dalej** , dopóki nie uzyskasz do sekcji **role serwera** .
12. Wybierz role serwera **Active Directory Domain Services** i **DNS** . Po wyświetleniu monitu Dodaj wszelkie dodatkowe funkcje, które są wymagane przez te role.
13. Po zakończeniu instalacji funkcji Wróć do pulpitu nawigacyjnego **Menedżer serwera** .
14. Wybierz opcję Nowy **AD DS** w okienku po lewej stronie.
15. Kliknij link **więcej** na żółtym pasku ostrzegawczym.
16. W kolumnie **Akcja** okna dialogowego **wszystkie szczegóły zadania serwera** kliknij pozycję **Podnieś poziom tego serwera do poziomu kontrolera domeny**.
17. W obszarze **Konfiguracja wdrożenia**wybierz pozycję **Dodaj kontroler domeny do istniejącej domeny**.
    ![Konfiguracja wdrożenia](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Kliknij pozycję **Wybierz**.
19. Połącz się za pomocą konta administratora (**Corp. CONTOSO. COM\domainadmin**) i hasło (**contoso! 0000**).
20. W **Wybierz domenę z lasu**, kliknij domenę, a następnie kliknij przycisk **OK**.
21. W obszarze **Opcje kontrolera domeny**Użyj wartości domyślnych i ustaw hasło trybu DSRM.

    >[!NOTE]
    >Na stronie **Opcje DNS** może zostać wyświetlone ostrzeżenie informujące o tym, że nie można utworzyć delegowania dla tego serwera DNS. To ostrzeżenie można zignorować w środowiskach nieprodukcyjnych.
22. Klikaj przycisk **dalej** , aż okno dialogowe osiągnie sprawdzanie **wymagań wstępnych** . Następnie kliknij pozycję **Zainstaluj**.

Gdy serwer zakończy zmiany konfiguracji, należy ponownie uruchomić serwer.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Dodaj prywatny adres IP do drugiego kontrolera domeny do serwera DNS sieci VPN

W Azure Portal w obszarze Sieć wirtualna Zmień serwer DNS w taki sposób, aby obejmował adres IP pomocniczego kontrolera domeny. To ustawienie umożliwia nadmiarowość usługi DNS.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a>Konfigurowanie kont domeny

W następnych krokach skonfigurujesz konta Active Directory. W poniższej tabeli przedstawiono konta:

| |Konto instalacji<br/> |SqlServer-0 <br/>Konto usługi SQL Server i programu SQL Agent |SqlServer-1<br/>Konto usługi SQL Server i programu SQL Agent
| --- | --- | --- | ---
|**Imię** |Instalowanie |SQLSvc1 | SQLSvc2
|**Nazwa SamAccountName użytkownika** |Instalowanie |SQLSvc1 | SQLSvc2

Aby utworzyć każde konto, wykonaj następujące czynności.

1. Zaloguj się na komputerze z **usługą AD-podstawowa-DC** .
2. W **Menedżer serwera**wybierz pozycję **Narzędzia**, a następnie kliknij pozycję **Centrum administracyjne usługi Active Directory**.   
3. Wybierz pozycję **Corp (local)** w okienku po lewej stronie.
4. W okienku odpowiednie **zadania** wybierz pozycję **Nowy**, a następnie kliknij pozycję **użytkownik**.
   ![Centrum administracyjne usługi Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Ustaw hasło złożone dla każdego konta.<br/> W przypadku środowisk nieprodukcyjnych należy ustawić konto użytkownika tak, aby nigdy nie wygasło.

5. Kliknij przycisk **OK** , aby utworzyć użytkownika.
6. Powtórz powyższe kroki dla każdego z trzech kont.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Przyznawanie wymaganych uprawnień do konta instalacji
1. W **Centrum administracyjne usługi Active Directory**wybierz pozycję **Corp (local)** w okienku po lewej stronie. Następnie w okienku **zadania** po prawej stronie kliknij pozycję **Właściwości**.

    ![Właściwości użytkownika CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Wybierz pozycję **rozszerzenia**, a następnie kliknij przycisk **Zaawansowane** na karcie **zabezpieczenia** .
3. W oknie dialogowym **Zaawansowane ustawienia zabezpieczeń dla firm** kliknij przycisk **Dodaj**.
4. Kliknij pozycję **Wybierz podmiot zabezpieczeń**, Wyszukaj pozycję **CORP\Install**, a następnie kliknij przycisk **OK**.
5. Zaznacz pole wyboru **Czytaj wszystkie właściwości** .

6. Zaznacz pole wyboru **Utwórz obiekty komputerów** .

     ![Uprawnienia użytkownika Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Kliknij przycisk **OK**, a następnie kliknij ponownie przycisk **OK**. Zamknij okno właściwości **firmowe** .

Po zakończeniu konfigurowania Active Directory i obiektów użytkownika Utwórz dwie SQL Server maszyny wirtualne oraz maszynę wirtualną serwera monitora. Następnie Dołącz wszystkie trzy do domeny.

## <a name="create-sql-server-vms"></a>Tworzenie maszyn wirtualnych SQL Server

Utwórz trzy dodatkowe maszyny wirtualne. Rozwiązanie wymaga dwóch maszyn wirtualnych z wystąpieniami SQL Server. Trzecią maszynę wirtualną będzie pełnić rolę monitora. System Windows Server 2016 może korzystać z [monitora w chmurze](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), jednak w celu zapewnienia spójności z poprzednimi systemami operacyjnymi ten dokument używa maszyny wirtualnej dla monitora.  

Przed rozpoczęciem należy wziąć pod uwagę następujące decyzje dotyczące projektu.

* **Magazyn — Managed Disks platformy Azure**

   W przypadku magazynu maszyn wirtualnych Użyj usługi Azure Managed Disks. Firma Microsoft zaleca Managed Disks dla SQL Server maszyn wirtualnych. Funkcja Dyski zarządzane obsługuje magazyn w tle. Ponadto jeśli maszyny wirtualne z funkcją Dyski zarządzane znajdują się w tym samym zestawie dostępności, platforma Azure dystrybuuje zasoby magazynu w celu zapewnienia odpowiedniej nadmiarowości. Aby uzyskać więcej informacji, zobacz [Omówienie funkcji Dyski zarządzane platformy Azure](../managed-disks-overview.md). Aby uzyskać szczegółowe informacje o dyskach zarządzanych w zestawie dostępności, zobacz [używanie Managed disks w przypadku maszyn wirtualnych w zestawie dostępności](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Prywatne adresy IP sieci w środowisku produkcyjnym**

   W przypadku maszyn wirtualnych w tym samouczku są stosowane publiczne adresy IP. Publiczny adres IP umożliwia zdalne nawiązywanie połączenia bezpośrednio z maszyną wirtualną za pośrednictwem Internetu, co ułatwia wykonywanie czynności konfiguracyjnych. W środowiskach produkcyjnych firma Microsoft zaleca tylko prywatne adresy IP, aby zmniejszyć liczbę luk w zabezpieczeniach SQL Server wystąpienia zasobu maszyny wirtualnej.

### <a name="create-and-configure-the-sql-server-vms"></a>Tworzenie i Konfigurowanie maszyn wirtualnych SQL Server
Następnie utwórz trzy maszyny wirtualne — dwie SQL Server maszyny wirtualne oraz maszynę wirtualną dla dodatkowego węzła klastra. Aby utworzyć wszystkie maszyny wirtualne, Wróć do grupy zasobów **SQL-ha-RG** , kliknij przycisk **Dodaj**, wyszukaj odpowiedni element galerii, kliknij pozycję **maszyna wirtualna**, a następnie kliknij pozycję **z galerii**. Informacje w poniższej tabeli ułatwiają tworzenie maszyn wirtualnych:


| Strona | Maszyna wirtualna 1 | Maszyna wirtualna 2 | VM3 |
| --- | --- | --- | --- |
| Wybierz odpowiedni element galerii |**Windows Server 2016 Datacenter** |**SQL Server 2016 z dodatkiem SP1 Enterprise w systemie Windows Server 2016** |**SQL Server 2016 z dodatkiem SP1 Enterprise w systemie Windows Server 2016** |
| **Podstawowe informacje** o konfiguracji maszyny wirtualnej |**Nazwa** = Cluster-FSW<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = contoso! 0000<br/>**Subskrypcja** = subskrypcja<br/>**Grupa zasobów** = SQL-ha-RG<br/>**Lokalizacja** = Twoja lokalizacja na platformie Azure |**Nazwa** = SqlServer-0<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = contoso! 0000<br/>**Subskrypcja** = subskrypcja<br/>**Grupa zasobów** = SQL-ha-RG<br/>**Lokalizacja** = Twoja lokalizacja na platformie Azure |**Nazwa** = SqlServer-1<br/>**Nazwa użytkownika** = administrator domeny<br/>**Hasło** = contoso! 0000<br/>**Subskrypcja** = subskrypcja<br/>**Grupa zasobów** = SQL-ha-RG<br/>**Lokalizacja** = Twoja lokalizacja na platformie Azure |
| **Rozmiar** konfiguracji maszyny wirtualnej |**Size** = DS1\_v2 (1 vCPU, 3,5 GB) |**Size** = DS2\_v2 (2 procesorów wirtualnych vCPU, 7 GB)</br>Rozmiar musi obsługiwać magazyn SSD (Obsługa dysków w warstwie Premium). )) |**Size** = DS2\_v2 (2 procesorów wirtualnych vCPU, 7 GB) |
| **Ustawienia** konfiguracji maszyny wirtualnej |**Magazyn**: Użyj dysków zarządzanych.<br/>**Virtual Network** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>**Publiczny adres IP** generowany automatycznie.<br/>**Sieciowa Grupa zabezpieczeń** = brak<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj automatycznie wygenerowanego konta magazynu<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |**Magazyn**: Użyj dysków zarządzanych.<br/>**Virtual Network** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>**Publiczny adres IP** generowany automatycznie.<br/>**Sieciowa Grupa zabezpieczeń** = brak<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj automatycznie wygenerowanego konta magazynu<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |**Magazyn**: Użyj dysków zarządzanych.<br/>**Virtual Network** = autoHAVNET<br/>**Subnet** = sqlsubnet (10.1.1.0/24)<br/>**Publiczny adres IP** generowany automatycznie.<br/>**Sieciowa Grupa zabezpieczeń** = brak<br/>**Monitorowanie diagnostyki** = włączone<br/>**Konto magazynu diagnostyki** = Użyj automatycznie wygenerowanego konta magazynu<br/>**Zestaw dostępności** = sqlAvailabilitySet<br/> |
| **Ustawienia SQL Server** konfiguracji maszyny wirtualnej |Nie dotyczy |**SQL Connectivity** = prywatny (w Virtual Network)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = wyłączone<br/>**Konfiguracja magazynu** = ogólne<br/>**Automatyczne stosowanie poprawek** = niedziela o 2:00<br/>**Automatyczne kopie zapasowe** = wyłączone</br>**Integracja Azure Key Vault** = wyłączona |**SQL Connectivity** = prywatny (w Virtual Network)<br/>**Port** = 1433<br/>**Uwierzytelnianie SQL** = wyłączone<br/>**Konfiguracja magazynu** = ogólne<br/>**Automatyczne stosowanie poprawek** = niedziela o 2:00<br/>**Automatyczne kopie zapasowe** = wyłączone</br>**Integracja Azure Key Vault** = wyłączona |

<br/>

> [!NOTE]
> Proponowane tutaj rozmiary maszyn są przeznaczone do testowania grup dostępności na maszynach wirtualnych platformy Azure. Aby uzyskać najlepszą wydajność w obciążeniu produkcyjnym, zapoznaj się z zaleceniami dotyczącymi rozmiarów SQL Server maszyn i konfiguracji w obszarze [najlepsze rozwiązania w zakresie wydajności dla SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Po całkowitym zainicjowaniu obsługi administracyjnej trzech maszyn wirtualnych należy przyłączyć je do domeny **Corp.contoso.com** i przyznać prawa administracyjne CORP\Install do maszyn.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Przyłącz serwery do domeny

Teraz można przyłączyć maszyny wirtualne do **Corp.contoso.com**. Wykonaj następujące czynności dla maszyn wirtualnych SQL Server i serwera monitora udziału plików:

1. Połącz się zdalnie z maszyną wirtualną za pomocą **BUILTIN\DomainAdmin**.
2. W **Menedżer serwera**kliknij pozycję **serwer lokalny**.
3. Kliknij link **grupy roboczej** .
4. W sekcji **Nazwa komputera** kliknij przycisk **Zmień**.
5. Zaznacz pole wyboru **domena** i wpisz **Corp.contoso.com** w polu tekstowym. Kliknij przycisk **OK**.
6. W podręcznym oknie dialogowym **zabezpieczenia systemu Windows** określ poświadczenia dla domyślnego konta administratora domeny (**CORP\DomainAdmin**) i hasło (**contoso! 0000**).
7. Gdy zostanie wyświetlony komunikat "Witamy w domenie corp.contoso.com", kliknij przycisk **OK**.
8. Kliknij przycisk **Zamknij**, a następnie w oknie dialogowym podręcznym kliknij pozycję **Uruchom ponownie teraz** .

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Dodaj użytkownika Corp\Install jako administratora na każdej maszynie wirtualnej klastra

Po ponownym uruchomieniu każdej maszyny wirtualnej jako członka domeny Dodaj **CORP\Install** jako członka lokalnej grupy administratorów.

1. Poczekaj na ponowne uruchomienie maszyny wirtualnej, a następnie ponownie uruchom plik RDP z podstawowego kontrolera domeny, aby zalogować się do programu **SqlServer-0** przy użyciu konta **CORP\DomainAdmin** .
   >[!TIP]
   >Upewnij się, że logujesz się przy użyciu konta administratora domeny. W poprzednich krokach użyto WBUDOWANEgo konta administratora. Teraz, gdy serwer znajduje się w domenie, użyj konta domeny. W sesji RDP Określ*nazwę użytkownika* *domeny*\\.

2. W **Menedżer serwera**wybierz pozycję **Narzędzia**, a następnie kliknij pozycję **Zarządzanie komputerem**.
3. W oknie **Zarządzanie komputerem** rozwiń węzeł **Użytkownicy i grupy lokalne**, a następnie wybierz pozycję **grupy**.
4. Kliknij dwukrotnie grupę **administratorzy** .
5. W oknie dialogowym **Właściwości administratorów** kliknij przycisk **Dodaj** .
6. Wprowadź **CORP\Install**użytkownika, a następnie kliknij przycisk **OK**.
7. Kliknij przycisk **OK** , aby zamknąć okno dialogowe **Właściwości administratora** .
8. Powtórz poprzednie kroki w programie **SqlServer-1** i **cluster-FSW**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Ustawianie kont usługi SQL Server

Na każdej maszynie SQL Server należy ustawić konto usługi SQL Server. Użyj kont utworzonych podczas konfigurowania kont domeny.

1. Otwórz **Menedżera konfiguracji programu SQL Server**.
2. Kliknij prawym przyciskiem myszy usługę SQL Server, a następnie kliknij pozycję **Właściwości**.
3. Ustawianie konta i hasła.
4. Powtórz te kroki na drugiej maszynie wirtualnej SQL Server.  

W przypadku grup dostępności SQL Server każda SQL Server maszyna wirtualna musi być uruchomiona jako konto domeny.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Utwórz logowanie na każdej maszynie wirtualnej SQL Server dla konta instalacji

Aby skonfigurować grupę dostępności, użyj konta instalacji (CORP\install). To konto musi być członkiem stałej roli serwera **sysadmin** na każdej maszynie SQL Server VM. Poniższe kroki tworzą logowanie do konta instalacji:

1. Połącz się z serwerem za pośrednictwem Remote Desktop Protocol (RDP) przy użyciu * \<konta\>MachineName \DomainAdmin* .

1. Otwórz SQL Server Management Studio i Połącz się z lokalnym wystąpieniem SQL Server.

1. W **Eksplorator obiektów**kliknij pozycję **zabezpieczenia**.

1. Kliknij prawym przyciskiem myszy pozycję **nazwy logowania**. Kliknij pozycję **Nowa nazwa logowania**.

1. W obszarze **Logowanie — nowy**kliknij przycisk **Wyszukaj**.

1. Kliknij pozycję **lokalizacje**.

1. Wprowadź poświadczenia sieciowe administratora domeny.

1. Użyj konta instalacji.

1. Ustaw Logowanie jako należące do stałej roli serwera **sysadmin** .

1. Kliknij przycisk **OK**.

Powtórz powyższe kroki na drugiej maszynie SQL Server VM.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Dodawanie funkcji klastra trybu failover do obu maszyn wirtualnych SQL Server

Aby dodać funkcje klastra trybu failover, wykonaj następujące czynności na obu maszynach wirtualnych SQL Server:

1. Połącz się z SQL Server maszyną wirtualną za pośrednictwem Remote Desktop Protocol (RDP) przy użyciu konta *CORP\install* . Otwórz **pulpit nawigacyjny Menedżer serwera**.
2. Kliknij link **Dodaj role i funkcje** na pulpicie nawigacyjnym.

    ![Menedżer serwera — Dodawanie ról](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Wybierz pozycję **dalej** , dopóki nie uzyskasz do sekcji **funkcje serwera** .
4. W obszarze **funkcje**wybierz pozycję **klaster trybu failover**.
5. Dodaj wszelkie dodatkowe wymagane funkcje.
6. Kliknij przycisk **Instaluj** , aby dodać funkcje.

Powtórz kroki na drugiej maszynie wirtualnej SQL Server.

  >[!NOTE]
  > Ten krok wraz z przyłączaniem maszyn wirtualnych SQL Server do klastra trybu failover może być teraz zautomatyzowany przy użyciu [interfejsu wiersza polecenia maszyny wirtualnej Azure SQL](virtual-machines-windows-sql-availability-group-cli.md) i [szablonów szybkiego startu platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall">Skonfiguruj zaporę na każdej maszynie wirtualnej SQL Server

Rozwiązanie wymaga otwarcia następujących portów TCP w zaporze:

- **SQL Server maszynę wirtualną**:<br/>
   Port 1433 dla domyślnego wystąpienia SQL Server.
- **Sonda modułu równoważenia obciążenia platformy Azure:**<br/>
   Dowolny dostępny port. Przykłady często używają 59999.
- **Punkt końcowy dublowania bazy danych:** <br/>
   Dowolny dostępny port. Przykłady często używają 5022.

Porty zapory muszą być otwarte na obu maszynach wirtualnych SQL Server.

Metoda otwierania portów zależy od używanego rozwiązania zapory. W następnej sekcji wyjaśniono, jak otworzyć porty w zaporze systemu Windows. Otwórz wymagane porty na każdej z maszyn wirtualnych SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Otwieranie portu TCP w zaporze

1. Na pierwszym ekranie **startowym** SQL Server uruchom **zaporę systemu Windows z zabezpieczeniami zaawansowanymi**.
2. W okienku po lewej stronie wybierz pozycję **reguły ruchu przychodzącego**. W prawym okienku kliknij pozycję **Nowa reguła**.
3. W obszarze **Typ reguły**wybierz pozycję **port**.
4. Dla portu Określ wartość **TCP** i wpisz odpowiednie numery portów. Zobacz poniższy przykład:

   ![Zapora SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Kliknij przycisk **Dalej**.
6. Na stronie **Akcja** pozostaw wybrane **połączenie** , a następnie kliknij przycisk **dalej**.
7. Na stronie **profil** zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **dalej**.
8. Na stronie **Nazwa** Określ nazwę reguły (na przykład **sondy Azure lb**) w polu tekstowym **Nazwa** , a następnie kliknij przycisk **Zakończ**.

Powtórz te kroki na drugiej maszynie wirtualnej SQL Server.

## <a name="configure-system-account-permissions"></a>Konfigurowanie uprawnień konta systemowego

Aby utworzyć konto dla konta System i udzielić odpowiednich uprawnień, wykonaj następujące czynności na każdym wystąpieniu SQL Server:

1. Utwórz konto dla `[NT AUTHORITY\SYSTEM]` każdego wystąpienia SQL Server. Następujący skrypt tworzy to konto:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. `[NT AUTHORITY\SYSTEM]` W każdym wystąpieniu SQL Server Udziel następujących uprawnień:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Następujący skrypt przyznaje te uprawnienia:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Następne kroki

* [SQL Server Utwórz zawsze dostępną grupę dostępności na maszynach wirtualnych platformy Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

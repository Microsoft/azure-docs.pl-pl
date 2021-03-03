---
title: Replikowanie danych przez ExpressRoute z migracją serwera Azure Migrate
description: Jak używać usługi Azure ExpressRoute do replikacji z migracją Azure Migrate Server
author: ms-deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: fe44ff423240cbe24d91cef0c0f4bb803ad8df98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746550"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Replikowanie danych przez ExpressRoute z Azure Migrate: Migracja serwera

W tym artykule dowiesz się, jak skonfigurować [Azure Migrate: Migracja serwera](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) w celu replikowania danych za pośrednictwem obwodu usługi ExpressRoute podczas migrowania serwerów na platformę Azure.

## <a name="understand-azure-expressroute-circuits"></a>Omówienie obwodów usługi Azure ExpressRoute
Obwód usługi ExpressRoute (ER) łączy infrastrukturę lokalną z firmą Microsoft za pośrednictwem dostawcy łączności. Obwody usługi ExpressRoute można skonfigurować tak, aby korzystały z prywatnej komunikacji równorzędnej, komunikacji równorzędnej firmy Microsoft lub obu tych elementów. Zapoznaj się z artykułem dotyczącym [obwodów usługi ExpressRoute i komunikacji równorzędnej](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) , aby dowiedzieć się więcej o różnych opcjach komunikacji równorzędnej dostępnych z ExpressRoute.

Narzędzie migracji serwera Azure Migrate ułatwia Migrowanie serwerów lokalnych i serwerów z innych chmur do usługi Azure Virtual Machines. To narzędzie działa przez skonfigurowanie trwającego strumienia replikacji w celu replikowania danych z serwerów, które mają zostać zmigrowane do dysków zarządzanych w ramach subskrypcji platformy Azure. Gdy wszystko będzie gotowe do migracji serwerów, zreplikowane dane na platformie Azure są używane do migrowania serwerów.

Dane zreplikowane z serwerów lokalnych można skonfigurować do wysyłania do subskrypcji platformy Azure za pośrednictwem Internetu (przy użyciu bezpiecznego połączenia szyfrowanego) lub za pośrednictwem połączenia ExpressRoute. W przypadku dużej liczby serwerów do migracji przy użyciu usługi ExpressRoute na potrzeby replikacji może pomóc w wydajniejszym migrowaniu serwerów przy użyciu przepustowości udostępnionej w obwodzie usługi ExpressRoute.

W tym artykule omówiono
> [!div class="checklist"]
>
> * Jak replikować dane przy użyciu obwodu usługi ExpressRoute z prywatną komunikację równorzędną.
> * Jak replikować dane za pomocą obwodu usługi ExpressRoute przy użyciu komunikacji równorzędnej firmy Microsoft.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Replikowanie danych przy użyciu obwodu usługi ExpressRoute z prywatną komunikację równorzędną

> [!NOTE]
> Replikowanie za pośrednictwem prywatnego obwodu komunikacji równorzędnej jest obecnie obsługiwane tylko w przypadku [migracji bez agentów maszyn wirtualnych VMware na platformę Azure](./tutorial-migrate-vmware.md). Obsługa prywatnych punktów końcowych dla innych [metod replikacji](./migrate-services-overview.md#azure-migrate-server-migration-tool) będzie dostępna wkrótce.

W metodzie migrowania maszyn wirtualnych VMware na platformę Azure bez wykorzystania agentów urządzenie Azure Migrate najpierw przekazuje dane replikacji do konta magazynu (konto magazynu w pamięci podręcznej) w ramach subskrypcji. Zreplikowane dane z konta magazynu pamięci podręcznej są następnie przenoszone do dysków zarządzanych przez repliki w ramach subskrypcji przez usługę Azure Migrate. Aby użyć prywatnego obwodu komunikacji równorzędnej na potrzeby replikacji, należy utworzyć i dołączyć prywatny punkt końcowy do użycia konta magazynu pamięci podręcznej. Prywatne punkty końcowe używają co najmniej jednego prywatnego adresu IP z sieci wirtualnej (VNet), efektywnie przełączając konto magazynu do sieci wirtualnej platformy Azure. Prywatny punkt końcowy umożliwia urządzeniu Azure Migrate łączenie się z kontem magazynu pamięci podręcznej przy użyciu prywatnej komunikacji równorzędnej ExpressRoute i transfer danych bezpośrednio z prywatnego adresu IP. <br/>  

![Proces replikacji](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Oprócz danych replikacji urządzenie Azure Migrate komunikuje się z usługą Azure Migrate na potrzeby działań płaszczyzny kontroli, takich jak organizowanie replikacji. Komunikacja między urządzeniem Azure Migrate a usługą Azure Migrate nadal odbywa się za pośrednictwem Internetu w publicznym punkcie końcowym usługi Azure Migrate.
> - Prywatny punkt końcowy konta magazynu powinien być dostępny z sieci, w której jest wdrożone urządzenie Azure Migrate.
> - Serwer DNS musi być skonfigurowany do rozpoznawania zapytań DNS przez urządzenie Azure Migrate dla punktu końcowego usługi blob magazynu pamięci podręcznej do prywatnego adresu IP prywatnego punktu końcowego dołączonego do konta magazynu pamięci podręcznej.
> - Konto magazynu pamięci podręcznej musi być dostępne w jego publicznym punkcie końcowym. (Usługa Azure Migrate używa publicznego punktu końcowego konta magazynu pamięci podręcznej, aby przenieść dane z konta magazynu do dysków zarządzanych repliki). 


### <a name="1-pre-requisites"></a>1. wymagania wstępne

Użytkownik platformy Azure tworzący prywatny punkt końcowy powinien mieć następujące uprawnienia do grupy zasobów i sieci wirtualnej, w której zostanie utworzony prywatny punkt końcowy.

**Przypadek użycia** | **Uprawnienia** 
--- | --- 
 Utwórz prywatne punkty końcowe i zarządzaj nimi. | Microsoft. Network/privateEndpoint/zapis/akcja<br/>Microsoft. Network/privateEndpoint/odczyt/akcja  
|Dołącz prywatny punkt końcowy do sieci wirtualnej/podsieci.<br/>Jest to wymagane w sieci wirtualnej, w której zostanie utworzony prywatny punkt końcowy.| Microsoft. Network/virtualNetworks/Subnet/Join/Action Microsoft. Network/virtualNetworks/Join/Action
|Połącz prywatny punkt końcowy z kontem magazynu. <br/>| Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnectionApproval/Action <br/> Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnections/Read
|Utwórz interfejs sieciowy i dołącz go do sieciowej grupy zabezpieczeń. | Microsoft. Network/networkInterfaces/odczyt <br/> Microsoft. Network/networkInterfaces/podsieci/zapis <br/> Microsoft. Network/networkInterfaces/podsieci/odczyt<br/> Microsoft. Network/networkSecurityGroups/Join/Action (opcjonalnie)
Tworzenie prywatnych stref DNS i zarządzanie nimi.| Rola współautora strefy Prywatna strefa DNS <br/> _Oraz_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/Write Microsoft. Network/privateDnsZones/Read <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/odczyt <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/odczyt <br/> Microsoft. Network/virtualNetworks/Join/Action 

### <a name="2-identify-the-cache-storage-account"></a>2. Zidentyfikuj konto magazynu pamięci podręcznej 
 
Azure Migrate automatycznie tworzy konto magazynu pamięci podręcznej podczas konfigurowania replikacji (przy użyciu środowiska Azure Portal) dla maszyny wirtualnej po raz pierwszy w projekcie Azure Migrate. Konto magazynu jest tworzone w tej samej subskrypcji i grupie zasobów, w której utworzono projekt Azure Migrate w programie.

Aby utworzyć i zlokalizować konto magazynu:

1. Użyj Azure Portal środowiska Azure Migrate, aby replikować co najmniej jedną maszynę wirtualną w projekcie.
2. Przejdź do grupy zasobów projektu Azure Migrate.
3. Znajdź konto magazynu pamięci podręcznej, identyfikując prefiks **"LSA"** w nazwie konta magazynu.

![Widok grupy zasobów](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Jeśli masz więcej niż jedno konto magazynu z prefiksem **"LSA"** w grupie zasobów, możesz sprawdzić konto magazynu, przechodząc do menu Ustawienia replikacji i konfiguracja docelowa dla dowolnej z replikowanych maszyn wirtualnych w projekcie. <br/> 
> ![Przegląd ustawień replikacji](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. Uaktualnij konto magazynu pamięci podręcznej do Ogólnego przeznaczenia v2 

Prywatne punkty końcowe można tworzyć tylko na koncie magazynu Ogólnego przeznaczenia v2 (GPv2). Jeśli konto magazynu pamięci podręcznej nie jest kontem magazynu GPv2, należy je uaktualnić do GPv2, wykonując następujące czynności:

1. Przejdź do konta magazynu.
2. Wybierz pozycję **Konfiguracja**.
3. W obszarze **rodzaj konta** wybierz pozycję **Uaktualnij**.
4. W obszarze **Potwierdź uaktualnianie** wpisz nazwę konta.
5. Wybierz pozycję **Uaktualnij** w dolnej części strony.

![Uaktualnij konto magazynu](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. Utwórz prywatny punkt końcowy dla konta magazynu

1. Przejdź do konta magazynu, wybierz pozycję **Sieć** z menu po lewej stronie, a następnie wybierz kartę **połączenia prywatnego punktu końcowego** .  
2. Wybierz pozycję **+ prywatny punkt końcowy**.

    a. W oknie **Tworzenie prywatnego punktu końcowego** — wybierz **subskrypcję** i **grupę zasobów**. Podaj nazwę prywatnego punktu końcowego i wybierz region konta magazynu.  
    ![Okno konfiguracji środowiska PE](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. Na karcie **zasób** Podaj **nazwę subskrypcji** , w której znajduje się konto magazynu. W polu **Typ zasobu** wybierz pozycję **Microsoft. Storage/storageAccounts** . W polu **zasób** Podaj nazwę konta magazynu replikacji typu GPv2. Wybierz **obiekt BLOB** jako **docelowy zasób podrzędny**.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. Na karcie **Konfiguracja** wybierz **sieć wirtualną** i **podsieć** dla prywatnego punktu końcowego konta magazynu.  

    > [!Note]
    > Sieć wirtualna musi zawierać punkt końcowy bramy ExpressRoute lub musi być połączona z siecią wirtualną za pomocą bramy ExpressRoute. 

    W sekcji **integracja prywatna strefa DNS** wybierz pozycję **tak** i Zintegruj z prywatną strefą DNS. Wybranie opcji **tak** powoduje automatyczne połączenie strefy DNS z wybraną siecią wirtualną i dodanie rekordów DNS, które są wymagane do rozpoznawania nazw DNS nowych adresów IP i w pełni kwalifikowanych nazwy domen utworzonych dla prywatnego punktu końcowego. Dowiedz się więcej o [prywatnych strefach DNS.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Możesz również dodać **Tagi** dla prywatnego punktu końcowego.  

    e. Kontynuuj **przeglądanie i tworzenie** po zakończeniu wprowadzania szczegółów. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć prywatny punkt końcowy.

    > [!Note]
    > Jeśli użytkownik tworzący prywatny punkt końcowy jest również właścicielem konta magazynu, prywatny punkt końcowy zostanie zaakceptowany. W przeciwnym razie właściciel musi zatwierdzić prywatny punkt końcowy do użycia. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Utwórz prywatne strefy DNS i ręcznie Dodaj rekordy DNS (opcjonalnie)

Jeśli nie wybrano opcji integracji z prywatną strefą DNS w momencie tworzenia prywatnego punktu końcowego, wykonaj kroki opisane w tej sekcji, aby ręcznie utworzyć prywatną strefę DNS. 

> [!Note]
> Jeśli wybrano opcję **tak** , aby przeprowadzić integrację z prywatną strefą DNS, można pominąć tę sekcję. 

1. Utwórz prywatną strefę DNS. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  Na stronie **strefy prywatna strefa DNS** wybierz przycisk **+ Dodaj** , aby rozpocząć tworzenie nowej strefy.  
    b.  Na stronie **Tworzenie prywatnego strefy DNS** wprowadź wymagane szczegóły. Wprowadź nazwę prywatnej strefy DNS jako _privatelink_. blob.Core.Windows.NET. c. Przejdź do karty **Recenzja + tworzenie** , aby przejrzeć i utworzyć strefę DNS.

2. Połącz prywatną strefę DNS z siecią wirtualną.  

    Prywatna strefa DNS utworzona powyżej musi być połączona z siecią wirtualną, do której jest dołączony prywatny punkt końcowy.

    a. Przejdź do prywatnej strefy DNS utworzonej w poprzednim kroku i przejdź do pozycji linki sieci wirtualnej w lewej części strony. Wybierz przycisk **+ Dodaj** .   
    b. Wprowadź wymagane szczegóły. Pola **subskrypcji** i **sieci wirtualnej** muszą być wypełnione odpowiednimi szczegółami sieci wirtualnej, w której jest podłączony prywatny punkt końcowy. Pozostałe pola mogą pozostać w stanie innym niż.

3. Następnym krokiem jest dodanie rekordów DNS do strefy DNS. Dodaj wpis dla w pełni kwalifikowanej nazwy domeny konta magazynu do prywatnej strefy DNS.

    a. Przejdź do prywatnej strefy DNS i przejdź do sekcji **Przegląd** w lewej części strony. Wybierz pozycję **+ zestaw rekordów** , aby rozpocząć Dodawanie rekordów.  

    b. Na stronie **Dodawanie zestawu rekordów** Dodaj wpis dla w pełni kwalifikowanej nazwy domeny i prywatnego adresu IP jako rekord typu.

> [!Important]
> Możesz wymagać dodatkowych ustawień DNS, aby rozwiązać prywatny adres IP prywatnego punktu końcowego konta magazynu ze środowiska źródłowego. Zapoznaj się z [tym artykułem](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) , aby poznać wymaganą konfigurację systemu DNS.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Replikowanie danych przy użyciu obwodu usługi ExpressRoute przy użyciu komunikacji równorzędnej firmy Microsoft

Możesz użyć komunikacji równorzędnej firmy Microsoft lub istniejącej publicznej domeny komunikacji równorzędnej (przestarzałe dla nowych połączeń ExpressRoute), aby skierować ruch związany z replikacją za pomocą obwodu usługi ExpressRoute, jak pokazano na poniższym diagramie.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Nawet w przypadku danych replikacji przechodzących przez obwód komunikacji równorzędnej firmy Microsoft nadal będziesz potrzebować połączenia z Internetem z lokacji lokalnej w celu przeprowadzenia innej komunikacji (płaszczyzny kontroli) z usługą Azure Migrate. Istnieją dodatkowe adresy URL, które nie są dostępne za pośrednictwem ExpressRoute, że urządzenie replikacji/host funkcji Hyper-V musi mieć dostęp do organizowania procesu replikacji. Wymagania dotyczące adresu URL można sprawdzić w zależności od scenariusza migracji, [migracji programu VMware bez agenta](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) lub [migracji opartych na agentach](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance).  

Jeśli używasz serwera proxy w lokacji lokalnej i chcesz użyć usługi ExpressRoute do obsługi ruchu związanego z replikacją, musisz skonfigurować obejście serwera proxy dla odpowiednich adresów URL na urządzeniu lokalnym. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Konfigurowanie reguł obejścia serwera proxy na urządzeniu Azure Migrate (dla migracji bez agentów VMware)

1. Zaloguj się (pulpit zdalny) do urządzenia Azure Migrateowego.   
2. Otwórz plik C:/ProgramData/MicrosoftAzure/config/appliance.jsprzy użyciu programu Notatnik.
3. W pliku Zmień wiersz "EnableProxyBypassList": "false" na "EnableProxyBypassList": "true". Zapisz zmiany i ponownie uruchom urządzenie.
4. Po ponownym uruchomieniu programu po otwarciu Menedżera konfiguracji urządzeń będzie można zobaczyć opcję obejścia serwera proxy w interfejsie użytkownika aplikacji sieci Web. Dodaj poniższe adresy URL do listy obejścia serwera proxy.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Konfigurowanie reguł obejścia serwera proxy na urządzeniu replikacji (dla migracji opartych na agentach)

Wykonaj poniższe kroki, aby skonfigurować listę obejścia serwera proxy na serwerze konfiguracji i serwerach przetwarzania:

1. [Pobierz narzędzie PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) , aby uzyskać dostęp do kontekstu użytkownika systemu.
2. Otwórz program Internet Explorer w kontekście użytkownika systemu, uruchamiając następujący wiersz polecenia PsExec-s-i "użycie ścieżki%ProgramFiles%\Internet Explorer\iexplore.exe"
3. Dodaj ustawienia serwera proxy w programie IE.
4. Na liście pomijania Dodaj adres URL usługi Azure Storage. *. blob. Core. Windows. NET.  

Powyższe reguły obejścia zapewniają, że ruch związany z replikacją będzie przepływał przez ExpressRoute, podczas gdy komunikacja zarządzania może przekroczyć serwer proxy dla Internetu.  

Ponadto należy zaanonsować trasy w filtrze tras dla następujących Wspólnot protokołu BGP, aby ruch związany z replikacją Azure Migrate przechodzą przez obwód usługi ExpressRoute zamiast Internetu.  

- Regionalna społeczność protokołu BGP dla regionu źródłowego platformy Azure (Azure Migrate regionu projektu)
- Regionalna społeczność protokołu BGP dla docelowego regionu platformy Azure (region migracji)
- Społeczność BGP dla Azure Active Directory (12076:5060)

Dowiedz się więcej o [filtrach tras](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) i liście [społeczności protokołu BGP dla ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp). 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [obwodów usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- Dowiedz się więcej o [domenach routingu ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- Dowiedz się więcej o [prywatnych punktach końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
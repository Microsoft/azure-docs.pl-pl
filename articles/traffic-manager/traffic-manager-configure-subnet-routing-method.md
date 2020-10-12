---
title: Konfigurowanie routingu ruchu podsieci — Traffic Manager platformy Azure
description: W tym artykule opisano sposób konfigurowania Traffic Manager w celu kierowania ruchu z określonych podsieci.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: b1901ddce2eb9c8ff5ec9ac90a56379e74c11aa6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401371"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Bezpośrednie kierowanie ruchu do określonych punktów końcowych na podstawie podsieci użytkownika przy użyciu usługi Traffic Manager

W tym artykule opisano, jak skonfigurować metodę routingu ruchu dla podsieci. Metoda routingu ruchu **Podsieć** pozwala na mapowanie zestawu zakresów adresów IP do określonych punktów końcowych. Po odebraniu żądania usługa Traffic Manager sprawdza źródłowy adres IP żądania i zwraca skojarzony z nim punkt końcowy.

W scenariuszu opisanym w tym artykule przy użyciu routingu podsieci, w zależności od adresu IP zapytania użytkownika, ruch jest kierowany do wewnętrznej witryny sieci Web lub produkcyjnej witryny sieci Web.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby w tym samouczku zobaczyć usługi Traffic Manager w działaniu, trzeba wdrożyć:
- dwie podstawowe witryny internetowe działające w różnych regionach platformy Azure — **Wschodnie stany USA** (służącą jako wewnętrzna witryna internetowa) i **Europa Zachodnia** (służącą jako witryna internetowa w środowisku produkcyjnym).
- dwa testowe maszyny wirtualne do testowania usługi Traffic Manager — jedną maszynę wirtualną w regionie **Wschodnie stany USA** i drugą maszynę wirtualna w regionie **Europa Zachodnia**.

Testowe maszyny wirtualne służą do zilustrowania sposobu, w jaki usługa Traffic Manager kieruje ruch użytkowników do wewnętrznej witryny internetowej lub produkcyjnej witryny internetowej na podstawie podsieci, z której pochodzi zapytanie użytkownika.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

### <a name="create-websites"></a>Tworzenie witryn internetowych

W tej sekcji opisano tworzenie dwóch wystąpień witryny internetowej, które zapewniają dwa punkty końcowe usługi dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Tworzenie dwóch witryn internetowej obejmuje następujące kroki:
1. Utwórz dwie maszyny wirtualne w celu uruchomienia podstawowej witryny internetowej — jedną w regionie **Wschodnie stany USA**, a drugą w regionie **Europa Zachodnia**.
2. Zainstaluj serwer IIS na każdej maszynie wirtualnej i zaktualizuj domyślną stronę witryny internetowej, aby informowała o nazwie maszyny wirtualnej, z którą łączy się użytkownik podczas odwiedzania witryny internetowej.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn internetowych
W tej sekcji utworzysz dwie maszyny wirtualne *myEndpointVMEastUS* i *MyEndpointVMWEurope* w regionach **Wschodnie stany USA** i **Europa Zachodnia** .

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszynę wirtualną systemu Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myIISVMEastUS|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Nowy** , a następnie wpisz *myResourceGroupTM1*.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:
    
    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet1*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |Sieciowa grupa zabezpieczeń|Wybierz opcję **Podstawowa**, a następnie z listy rozwijanej **Dodaj publiczne porty wejściowe** wybierz opcje **HTTP** i **RDP** |
    |Diagnostyka rozruchu|Wybierz opcję **Wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki 1–6 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Grupa zasobów | Wybierz pozycję **Nowa**, a następnie wpisz *myResourceGroupTM2*|
    |Lokalizacja|West Europe|
    |Nazwa maszyny wirtualnej | myIISVMWEurope|
    |Sieć wirtualna | Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **nazwa** wprowadź *myVNet2*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |||

8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

   ![Tworzenie maszyny wirtualnej](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji należy zainstalować serwer IIS na dwóch maszynach wirtualnych — *myIISVMEastUS*   &  *myIISVMWEurope*, a następnie zaktualizować domyślną stronę witryny sieci Web. Niestandardowa strona witryny internetowej przedstawia nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do **narzędzi administracyjnych systemu Windows** > **Menedżer serwera**.
7. Uruchom program Windows PowerShell w systemie *myIISVMEastUS* i użyj następujących poleceń, aby zainstalować serwer IIS i zaktualizować domyślny plik htm.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Zamknij połączenie protokołu RDP z maszyną wirtualną *myIISVMEastUS*.
9. Powtórz kroki od 1-6 do, tworząc połączenie RDP z *myIISVMWEurope* maszyny wirtualnej w grupie zasobów *myResourceGroupTM2* , aby zainstalować usługi IIS i dostosować jej domyślną stronę sieci Web.
10. Uruchom program Windows PowerShell w systemie *myIISVMWEurope* i użyj następujących poleceń, aby zainstalować serwer IIS i zaktualizować domyślny plik htm.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS serwerów usług IIS — *myIISVMEastUS* i *myIISVMWEurope*.

1. Kliknij opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1–3 dla maszyny wirtualnej o nazwie *myIISVMWEurope* znajdującej się w grupie zasobów *myResourceGroupTM1*.

### <a name="create-test-vms"></a>Tworzenie testowych maszyn wirtualnych

W tej sekcji utworzysz maszynę wirtualną (*mVMEastUS* i *myVMWestEurope*) w każdym regionie świadczenia usługi Azure (**Wschodnie stany USA** i **Europa Zachodnia**. Te maszyny wirtualne posłużą do testowania, jak usługa Traffic Manager kieruje ruch do najbliższego serwera usług IIS, kiedy użytkownik nawiguje do witryny internetowej.

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszynę wirtualną systemu Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myVMEastUS|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Istniejąca** i wybierz grupę *myResourceGroupTM1*.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet3*, a jako nazwę podsieci wprowadź *mySubnet3*.|
    |Sieciowa grupa zabezpieczeń|Wybierz opcję **Podstawowa**, a następnie z listy rozwijanej **Dodaj publiczne porty wejściowe** wybierz opcje **HTTP** i **RDP** |
    |Diagnostyka rozruchu|Wybierz opcję **Wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki 1–5 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa maszyny wirtualnej | *myVMWEurope*|
    |Grupa zasobów | Wybierz pozycję **Istniejąca**, a następnie wpisz *myResourceGroupTM2*|
    |Sieć wirtualna | Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet4*, a jako nazwę podsieci wprowadź *mySubnet4*.|
    |||

8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
Utwórz profil usługi Traffic Manager, który umożliwia zwracanie określonych punktów końcowych w oparciu o źródłowy adres IP żądania.

1. W lewej górnej części ekranu wybierz kolejno pozycje **Utwórz zasób**  >  **Sieć**  >  **Traffic Manager**  >  **Utwórz**profil.
2. W obszarze **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz metodę routingu**Podsieć**.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję **Istniejąca** i wprowadź *myResourceGroupTM1*. |
    | |                              |
    |

    ![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne, na których działają serwery IIS — *myIISVMEastUS*  &  *myIISVMWEurope* , aby kierować ruchem użytkowników na podstawie podsieci zapytania użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Punkt końcowy platformy Azure                                   |
    | Nazwa           | myTestWebSiteEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *myIISVMEastUS-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |  Ustawienia routingu dla podsieci    |   Dodaj adres IP testowej maszyny wirtualnej *myVMEastUS* . Wszystkie zapytania użytkownika pochodzące z tej maszyny wirtualnej będą kierowane do *myTestWebSiteEndpoint*.    |

4. Powtórz kroki 2 i 3, aby dodać kolejny punkt końcowy o nazwie *myProductionEndpoint* dla publicznego adresu IP *myIISVMWEurope-IP* skojarzony z maszyną wirtualną serwera IIS o nazwie *myIISVMWEurope*. Aby uzyskać **ustawienia routingu podsieci**, Dodaj adres IP testowej maszyny wirtualnej — *myVMWestEurope*. Wszystkie zapytania użytkownika z tej testowej maszyny wirtualnej będą kierowane do punktu końcowego *myProductionWebsiteEndpoint*.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

    ![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager
W tej sekcji przetestujesz kierowanie ruchu użytkowników z danej podsieci do określonego punktu końcowego przez usługę Traffic Manager. Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:
1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl informacje o działaniu usługi Traffic Manager w następujący sposób:
    - Z poziomu testowej maszyny wirtualnej (*myVMEastUS*), która znajduje się w regionie **Wschodnie stany USA** , w przeglądarce internetowej przejdź do nazwy DNS profilu Traffic Manager.
    - Z testową maszyną wirtualną (*myVMEastUS*), która znajduje się w regionie **Europa Zachodnia** , w przeglądarce internetowej przejdź do nazwy DNS profilu Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager
Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
1. Kliknij pozycję **Przegląd**.
2. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

   ![Nazwa DNS usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
1. W przeglądarce internetowej na maszynie wirtualnej *myVMEastUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Ponieważ adres IP *myVMEastUS* maszyny wirtualnej jest skojarzony z punktem końcowym *myIISVMEastUS*, przeglądarka sieci Web uruchamia testową serwer witryny sieci Web — *myIISVMEastUS*.

   ![Testowanie profilu usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Następnie połącz się z maszyną wirtualną *myVMWestEurope* znajdującą się w **Europie zachodniej** , korzystając z kroków 1-5 i przejdź do nazwy domeny profilu Traffic Manager z tej maszyny wirtualnej. Ponieważ adres IP *myVMWestEurope* maszyny wirtualnej jest skojarzony z punktem końcowym *myIISVMEastUS*, przeglądarka sieci Web uruchamia testową serwer witryny sieci Web — *myIISVMWEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager
Kiedy grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**) przestaną być potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).

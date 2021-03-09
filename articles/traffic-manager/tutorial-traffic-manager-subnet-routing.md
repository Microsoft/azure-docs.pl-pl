---
title: Samouczek — Konfigurowanie routingu ruchu podsieci za pomocą usługi Azure Traffic Manager
description: W tym samouczku wyjaśniono, jak skonfigurować Traffic Manager do kierowania ruchu z podsieci użytkowników do określonych punktów końcowych.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 9b916f9942b0459b41d98b952fad072ae48318b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505441"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Samouczek: kierowanie ruchu do określonych punktów końcowych na podstawie podsieci użytkownika przy użyciu Traffic Manager

W tym artykule opisano, jak skonfigurować metodę routingu ruchu dla podsieci. Metoda routingu ruchu **podsieci** umożliwia mapowanie zestawu zakresów adresów IP na określone punkty końcowe. Gdy żądanie zostanie odebrane przez Traffic Manager, sprawdza źródłowy adres IP żądania i zwraca skojarzony z nim punkt końcowy.

W tym samouczku za pomocą routingu podsieci, w zależności od adresu IP zapytania użytkownika, ruch jest kierowany do wewnętrznej witryny sieci Web lub produkcyjnej witryny sieci Web.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych z podstawową witryną internetową w usługach IIS
> * Tworzenie dwóch testowych maszyn wirtualnych, aby zobaczyć usługę Traffic Manager w działaniu
> * Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS
> * Tworzenie profilu usługi Traffic Manager w celu kierowania ruchu na podstawie podsieci użytkownika
> * Dodawanie punktów końcowych maszyny wirtualnej do profilu usługi Traffic Manager
> * Wyświetlanie informacji o działaniu usługi Traffic Manager

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wyświetlić Traffic Manager w działaniu, ten samouczek wymaga wdrożenia następujących czynności:

- dwie podstawowe witryny internetowe działające w różnych regionach platformy Azure — **Wschodnie stany USA** (służącą jako wewnętrzna witryna internetowa) i **Europa Zachodnia** (służącą jako witryna internetowa w środowisku produkcyjnym).
- dwa testowe maszyny wirtualne do testowania usługi Traffic Manager — jedną maszynę wirtualną w regionie **Wschodnie stany USA** i drugą maszynę wirtualna w regionie **Europa Zachodnia**.

Testowe maszyny wirtualne służą do zilustrowania sposobu, w jaki usługa Traffic Manager kieruje ruch użytkowników do wewnętrznej witryny internetowej lub produkcyjnej witryny internetowej na podstawie podsieci, z której pochodzi zapytanie użytkownika.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Tworzenie witryn internetowych

W tej sekcji opisano tworzenie dwóch wystąpień witryny internetowej, które zapewniają dwa punkty końcowe usługi dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Tworzenie dwóch witryn internetowej obejmuje następujące kroki:

1. Utwórz dwie maszyny wirtualne w celu uruchomienia podstawowej witryny internetowej — jedną w regionie **Wschodnie stany USA**, a drugą w regionie **Europa Zachodnia**.
2. Zainstaluj serwer IIS na każdej maszynie wirtualnej i zaktualizuj domyślną stronę witryny internetowej, aby informowała o nazwie maszyny wirtualnej, z którą łączy się użytkownik podczas odwiedzania witryny internetowej.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn internetowych

W tej sekcji utworzysz dwie maszyny wirtualne *myIISVMEastUS* i *MyIISVMWestEurope* w regionach **Wschodnie stany USA** i **Europa Zachodnia** .

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2019 Datacenter**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **Utwórz nową** , a następnie wpisz **myResourceGroupTM1**.
   - **Szczegóły wystąpienia**  >  **Nazwa maszyny wirtualnej**: wpisz *myIISVMEastUS*.
   - **Szczegóły wystąpienia**  >  **Region**: wybierz pozycję **Wschodnie stany USA**.
   - **Konto administratora**  >  **Nazwa** użytkownika: Wprowadź wybraną nazwę użytkownika.
   - **Konto administratora**  >  **Hasło**: Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Reguły portów ruchu **przychodzącego**  >  **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - Reguły portów ruchu **przychodzącego**  >  **Wybierz pozycję porty wejściowe**: wybierz pozycję **RDP** i **protokół http** w polu ściągania.

3. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej: dyski**, a następnie kliknij kolejno pozycje **Sieć** i **Dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przejrzyj i utwórz**.
5. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.  
6. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę wirtualną o nazwie *myIISVMWestEurope*, z nazwą **grupy zasobów** *MyResourceGroupTM2*, **lokalizacją** *Europa Zachodnia* i wszystkimi innymi ustawieniami takimi jak *myIISVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie Kontynuuj pozostałymi krokami do momentu utworzenia obu maszyn wirtualnych.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji należy zainstalować serwer IIS na dwóch maszynach wirtualnych — *myIISVMEastUS*  &  *myIISVMWestEurope*, a następnie zaktualizować domyślną stronę witryny sieci Web. Dostosowana Strona witryny sieci Web zawiera nazwę maszyny wirtualnej, z którą nawiązujesz połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz pozycję *myIISVMEastUS* , która znajduje się w grupie zasobów *myResourceGroupTM1* .
2. Na stronie **Przegląd** wybierz pozycję **Połącz**, a następnie w obszarze **Połącz z maszyną wirtualną** wybierz pozycję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz opcję **tak** lub **Kontynuuj**, aby kontynuować połączenie.
6. Na pulpicie serwera przejdź do **narzędzi administracyjnych systemu Windows** > **Menedżer serwera**.
7. Uruchom program Windows PowerShell na maszynie wirtualnej *myIISVMEastUS* i użyj następujących poleceń, aby zainstalować serwer IIS i zaktualizować domyślny plik htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Zamknij połączenie RDP z maszyną wirtualną *myIISVMEastUS* .
9. Powtórz kroki od 1-6 do, tworząc połączenie RDP z *myIISVMWestEurope* maszyny wirtualnej w grupie zasobów *myResourceGroupTM2* , aby zainstalować usługi IIS i dostosować jej domyślną stronę sieci Web.
10. Uruchom program Windows PowerShell na maszynie wirtualnej *myIISVMWestEurope* i użyj następujących poleceń, aby zainstalować serwer IIS i zaktualizować domyślny plik htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS serwerów usług IIS — *myIISVMEastUS* i *myIISVMWestEurope*.

1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz pozycję *myIISVMEastUS* , która znajduje się w grupie zasobów *myResourceGroupTM1* .
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1-3 dla maszyny wirtualnej o nazwie *myIISVMWestEurope* , która znajduje się w grupie zasobów *myResourceGroupTM2* .

### <a name="create-test-vms"></a>Tworzenie testowych maszyn wirtualnych

W tej sekcji utworzysz maszynę wirtualną (*myVMEastUS* i *myVMWestEurope*) w każdym regionie świadczenia usługi Azure (**Wschodnie stany USA** i **Europa Zachodnia**). Te maszyny wirtualne będą używane do testowania, w jaki sposób Traffic Manager kieruje ruchem użytkowników na podstawie podsieci zapytania użytkownika.

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2019 Datacenter**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **myResourceGroupTM1**.
   - **Szczegóły wystąpienia**  >  **Nazwa maszyny wirtualnej**: wpisz *myVMEastUS*.
   - **Szczegóły wystąpienia**  >  **Region**: wybierz pozycję **Wschodnie stany USA**.
   - **Konto administratora**  >  **Nazwa** użytkownika: Wprowadź wybraną nazwę użytkownika.
   - **Konto administratora**  >  **Hasło**: Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Reguły portów ruchu **przychodzącego**  >  **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - Reguły portów ruchu **przychodzącego**  >  **Wybierz pozycję porty wejściowe**: wybierz pozycję **RDP** w polu ściągania.

3. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej: dyski**, a następnie kliknij kolejno pozycje **Sieć** i **Dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przejrzyj i utwórz**.
5. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.  
6. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę wirtualną o nazwie *myVMWestEurope*, z nazwą **grupy zasobów** *MyResourceGroupTM2*, **lokalizacją** *Europa Zachodnia* i wszystkimi innymi ustawieniami takimi jak *myVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager, który umożliwia zwracanie określonych punktów końcowych w oparciu o źródłowy adres IP żądania.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**. Wyszukaj *profil Traffic Manager* i wybierz pozycję **Utwórz**.
2. W **profilu tworzenia Traffic Manager** wprowadź lub wybierz poniższe informacje. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**.

    ![Tworzenie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz metodę routingu **Podsieć**.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję **Istniejąca** i wprowadź *myResourceGroupTM1*. |

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne, na których działają serwery IIS — *myIISVMEastUS*  &  *myIISVMWestEurope* , aby kierować ruchem użytkowników na podstawie podsieci zapytania użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W bloku **Profil usługi Traffic Manager** w sekcji **Ustawienia** wybierz pozycję **Punkty końcowe**, a następnie wybierz pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Punkt końcowy platformy Azure                                   |
    | Nazwa           | myInternalWebSiteEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *myIISVMEastUS-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |  Ustawienia routingu dla podsieci    |   Dodaj adres IP testowej maszyny wirtualnej *myVMEastUS* . Wszystkie zapytania użytkownika pochodzące z tej maszyny wirtualnej będą kierowane do *myInternalWebSiteEndpoint*.    |

4. Powtórz kroki 2 i 3, aby dodać kolejny punkt końcowy o nazwie *myProdWebsiteEndpoint* dla publicznego adresu IP *myIISVMWestEurope-IP* skojarzony z maszyną wirtualną serwera IIS o nazwie *myIISVMWestEurope*. Aby uzyskać **ustawienia routingu podsieci**, Dodaj adres IP testowej maszyny wirtualnej — *myVMWestEurope*. Każde zapytanie użytkownika z tej testowej maszyny wirtualnej będzie kierowane do punktu końcowego *myProdWebsiteEndpoint*.
5. Po zakończeniu dodawania obu punktów końcowych są one wyświetlane w **profilu Traffic Manager** wraz z ich stanem monitorowania w **trybie online**.

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

W tej sekcji przetestujesz kierowanie ruchu użytkowników z danej podsieci do określonego punktu końcowego przez usługę Traffic Manager. Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:

1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl informacje o działaniu usługi Traffic Manager w następujący sposób:
    - Z poziomu testowej maszyny wirtualnej (*myVMEastUS*), która znajduje się w regionie **Wschodnie stany USA** , w przeglądarce internetowej przejdź do nazwy DNS profilu Traffic Manager.
    - Z testową maszyną wirtualną (*myVMWestEurope*), która znajduje się w regionie **Europa Zachodnia** , w przeglądarce internetowej przejdź do nazwy DNS profilu Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager

Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach wybierz profil Traffic Manager.
2. Wybierz pozycję **Omówienie**.
3. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager

W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz pozycję *myVMEastUS* , która znajduje się w grupie zasobów *myResourceGroupTM1* .
2. Na stronie **Przegląd** wybierz pozycję **Połącz**, a następnie w obszarze **Połącz z maszyną wirtualną** wybierz pozycję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz opcję **tak** lub **Kontynuuj**, aby kontynuować połączenie.
6. W przeglądarce internetowej na maszynie wirtualnej *myVMEastUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Ponieważ adres IP *myVMEastUS* maszyny wirtualnej jest skojarzony z punktem końcowym *myInternalWebsiteEndpoint*, przeglądarka sieci Web uruchamia testową serwer witryny sieci Web — *myIISVMEastUS*.

7. Następnie połącz się z maszyną wirtualną *myVMWestEurope* znajdującą się w **Europie zachodniej** , korzystając z kroków 1-5 i przejdź do nazwy domeny profilu Traffic Manager z tej maszyny wirtualnej. Ponieważ adres IP *myVMWestEurope* maszyny wirtualnej jest skojarzony z punktem końcowym *myProductionWebsiteEndpoint*, przeglądarka sieci Web uruchamia testową serwer witryny sieci Web — *myIISVMWestEurope*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Kiedy grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**) przestaną być potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodzie routingu podsieci, zobacz:

> [!div class="nextstepaction"]
> [Metoda routingu ruchu podsieci](traffic-manager-routing-methods.md#subnet)

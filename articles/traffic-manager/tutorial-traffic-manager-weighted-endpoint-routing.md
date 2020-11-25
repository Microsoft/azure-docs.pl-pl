---
title: 'Samouczek: kierowanie ruchu do ważonych punktów końcowych — Traffic Manager platformy Azure'
description: W tym artykule zawierającym samouczek opisano sposób kierowania ruchu do punktów końcowych z wagami za pomocą usługi Traffic Manager.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: 55c316a370b9e44e906e48b4716201384567c9c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003790"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Samouczek: sterowanie routingiem ruchu za pomocą punktów końcowych z wagami przy użyciu usługi Traffic Manager

W tym samouczku opisano, jak sterować routingiem ruchu użytkowników między punktami końcowymi metodą routingu ważonego za pomocą usługi Azure Traffic Manager. W przypadku tej metody routingu należy przypisać wagi do każdego punktu końcowego w konfiguracji profilu usługi Traffic Manager. Ruch użytkowników jest kierowany zgodnie z wagami przypisanymi do poszczególnych punktów końcowych. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Im większa jest wartość wagi przypisana do punktu końcowego, tym wyższy jest priorytet.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych z podstawową witryną internetową w usługach IIS
> * Tworzenie dwóch testowych maszyn wirtualnych, aby zobaczyć usługę Traffic Manager w działaniu
> * Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS
> * Tworzenie profilu usługi Traffic Manager.
> * Dodawanie punktów końcowych maszyny wirtualnej do profilu usługi Traffic Manager
> * Sprawdź działanie usługi Traffic Manager.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby zobaczyć usługę Traffic Manager w działaniu, na potrzeby tego samouczka trzeba wdrożyć:

- dwa wystąpienia podstawowych witryn internetowych działających w różnych regionach świadczenia usługi Azure — Wschodnie stany USA i Europa Zachodnia,
- dwie testowe maszyny wirtualne do testowania usługi Traffic Manager — jedną maszynę wirtualną w regionie Wschodnie stany USA i drugą w regionie Europa Zachodnia. Testowe maszyny wirtualne są używane w celu zilustrowania sposobu kierowania ruchu użytkowników przez usługę Traffic Manager do witryny internetowej, która ma wyższą wagę przypisaną do punktu końcowego.

### <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

### <a name="create-websites"></a>Tworzenie witryn internetowych

W tej sekcji opisano tworzenie dwóch wystąpień witryny internetowej, które zapewniają dwa punkty końcowe usługi dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Aby utworzyć te dwie witryny internetowe, wykonaj następujące czynności:

1. Utwórz dwie maszyny wirtualne w celu uruchomienia podstawowej witryny internetowej — jedną w regionie Wschodnie stany USA, a drugą w regionie Europa Zachodnia.
2. Zainstaluj serwer usług IIS na każdej maszynie wirtualnej. Zaktualizuj domyślną stronę internetową, aby informowała o nazwie maszyny wirtualnej, z którą łączy się użytkownik podczas odwiedzania witryny internetowej.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn internetowych

W tej sekcji utworzysz dwie maszyny wirtualne (*myIISVMEastUS* i *myIISVMWestEurope*) w regionach Wschodnie stany USA i Europa Zachodnia.

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2019 Datacenter**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **Utwórz nową** , a następnie wpisz **myResourceGroupTM1**.
   - **Szczegóły wystąpienia**  >  **Nazwa maszyny wirtualnej**: wpisz *myIISVMEastUS*.
   - **Szczegóły wystąpienia**  >  **Region**: wybierz pozycję **Wschodnie stany USA**.
   - **Konto administratora**  >  **Nazwa** użytkownika: Wprowadź wybraną nazwę użytkownika.
   - **Konto administratora**  >  **Hasło**: Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Reguły portów ruchu **przychodzącego**  >  **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - Reguły portów ruchu **przychodzącego**  >  **Wybierz pozycję porty wejściowe**: wybierz pozycję **RDP** i **protokół http** w polu ściąganie.

3. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej: dyski**, a następnie kliknij kolejno pozycje **Sieć** i **Dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przeglądanie + tworzenie**.
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.  
6. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę wirtualną o nazwie *myIISVMWestEurope*, z nazwą **grupy zasobów** *MyResourceGroupTM2*, **lokalizacją** *Europa Zachodnia* i wszystkimi innymi ustawieniami takimi jak *myIISVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

![Tworzenie maszyny wirtualnej](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej części należy zainstalować serwer IIS na dwóch maszynach wirtualnych myIISVMEastUS i myIISVMWestEurope, a następnie zaktualizować domyślną stronę sieci Web. Dostosowana strona internetowa wyświetla nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki internetowej.

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Z listy zasobów wybierz pozycję **myIISVMEastUS** w grupie zasobów **myResourceGroupTM1**.
2. Na stronie **Przegląd** wybierz pozycję **Połącz**. W obszarze **Połącz z maszyną wirtualną** wybierz pozycję **Pobierz plik RDP**.
3. Otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło, które zostały określone podczas tworzenia tej maszyny wirtualnej. Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **tak** lub **Kontynuuj** , aby kontynuować połączenie.
6. Na pulpicie serwera przejdź do **narzędzi administracyjnych systemu Windows**  >  **Menedżer serwera**.
7. Otwórz program Windows PowerShell na maszynie wirtualnej VM1. Użyj poniższych poleceń, aby zainstalować serwer usług IIS i zaktualizować domyślny plik HTM.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Instalowanie usług IIS i dostosowywanie strony internetowej](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Zamknij połączenie protokołu RDP z maszyną wirtualną **myIISVMEastUS**.
9. Powtórz kroki 1–8. Utwórz połączenie RDP z **myIISVMWestEurope** maszyny wirtualnej w grupie zasobów **myResourceGroupTM2** , aby zainstalować usługi IIS i dostosować jej domyślną stronę sieci Web.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS serwerów usług IIS myIISVMEastUS i myIISVMWestEurope.

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Z listy zasobów wybierz pozycję **myIISVMEastUS** w grupie zasobów **myResourceGroupTM1**.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę. Następnie wybierz pozycję **Zapisz**.
4. Powtórz kroki 1-3 dla maszyny wirtualnej o nazwie **myIISVMWestEurope** w grupie zasobów **myResourceGroupTM2** .

### <a name="create-a-test-vm"></a>Tworzenie testowej maszyny wirtualnej

W tej sekcji utworzysz maszynę wirtualną (*myVMEastUS* i *myVMWestEurope*) w każdym regionie świadczenia usługi Azure (**Wschodnie stany USA** i **Europa Zachodnia**). Te maszyny wirtualne będą używane do testowania, w jaki sposób Traffic Manager kieruje ruch do punktu końcowego witryny sieci Web, który ma wyższą wartość wagi.

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2019 Datacenter**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **myResourceGroupTM1**.
   - **Szczegóły wystąpienia**  >  **Nazwa maszyny wirtualnej**: wpisz *myVMEastUS*.
   - **Szczegóły wystąpienia**  >  **Region**: wybierz pozycję **Wschodnie stany USA**.
   - **Konto administratora**  >  **Nazwa** użytkownika: Wprowadź wybraną nazwę użytkownika.
   - **Konto administratora**  >  **Hasło**: Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Reguły portów ruchu **przychodzącego**  >  **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - Reguły portów ruchu **przychodzącego**  >  **Wybierz pozycję porty wejściowe**: wybierz pozycję **RDP** w polu ściąganie.

3. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej: dyski**, a następnie kliknij kolejno pozycje **Sieć** i **Dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przeglądanie + tworzenie**.
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.  
6. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę wirtualną o nazwie *myVMWestEurope*, z nazwą **grupy zasobów** *MyResourceGroupTM2*, **lokalizacją** *Europa Zachodnia* i wszystkimi innymi ustawieniami takimi jak *myVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager, bazując na metodzie routingu **ważonego**.

1. W lewym górnym rogu ekranu wybierz kolejno pozycje **Utwórz zasób**  >  **Sieć**  >  **Traffic Manager**  >  **Utwórz** profil.
2. W bloku **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**.

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                   | Wprowadź unikatową nazwę w obrębie strefy trafficmanager.net. Wynikiem jest nazwa DNS trafficmanager.net, która jest używana do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz **ważoną** metodę routingu.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję **Użyj istniejącej**, a następnie wybierz grupę **myResourceGroupTM1**. |
    |        |   |

    ![Tworzenie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne, na których działają serwery IIS myIISVMEastUS i myIISVMWestEurope, aby skierować do nich ruch użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji. Wybierz profil w wyświetlanych wynikach.
2. W bloku **Profil usługi Traffic Manager** w sekcji **Ustawienia** wybierz pozycję **Punkty końcowe** > **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **OK**.

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Wprowadź punkt końcowy platformy Azure.                                   |
    | Nazwa           | Wprowadź wartość **myEastUSEndpoint**.                                        |
    | Typ zasobu docelowego           | Wybierz pozycję **Publiczny adres IP**.                          |
    | Zasób docelowy          | Wybierz publiczny adres IP, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie **myIISVMEastUS-ip**. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |  Waga      | Wprowadź wartość **100**.        |
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać kolejny punkt końcowy o nazwie **myWestEuropeEndpoint** dla publicznego adresu IP **myIISVMWestEurope-IP**. Ten adres jest skojarzony z maszyną wirtualną serwera IIS o nazwie myIISVMWestEurope. W ustawieniu **Waga** wprowadź **25**.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w profilu usługi Traffic Manager, ze stanem monitorowania **Online**.

## <a name="test-the-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

Aby zobaczyć usługę Traffic Manager w działaniu, wykonaj następujące czynności:

1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Sprawdź działanie usługi Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager

Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach wybierz profil usługi Traffic Manager.
2. Wybierz pozycję **Omówienie**.
3. Profil usługi Traffic Manager wyświetli swoją nazwę DNS. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

   ![Nazwa DNS usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager

W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Z listy zasobów wybierz pozycję **myVMEastUS** w grupie zasobów **myResourceGroupTM1**.
2. Na stronie **Przegląd** wybierz pozycję **Połącz**. W obszarze **Połącz z maszyną wirtualną** wybierz pozycję **Pobierz plik RDP**.
3. Otwórz pobrany plik RDP. Jeśli zostanie wyświetlony monit, wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **tak** lub **Kontynuuj** , aby kontynuować połączenie.
6. W przeglądarce internetowej na maszynie wirtualnej myVMEastUS wprowadź nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Nastąpi przekierowanie do witryny internetowej hostowanej na serwerze usług IIS myIISVMEastUS, ponieważ ma on przypisaną wyższą wartość wagi (**100**). MyIISVMWestEurope serwera IIS ma przypisaną dolną wartość wagi punktu końcowego wynoszącą **25**.

   ![Testowanie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Powtórz kroki 1-6 myVMWestEurope na maszynie wirtualnej, aby zobaczyć ważoną odpowiedź witryny sieci Web.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeżeli nie potrzebujesz już grup zasobów utworzonych w ramach tego samouczka, możesz je usunąć. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodach routingu, zobacz:

> [!div class="nextstepaction"]
> [Metoda routingu ruchu](traffic-manager-routing-methods.md)

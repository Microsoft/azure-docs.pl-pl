---
title: Samouczek — poprawianie odpowiedzi witryny internetowej za pomocą usługi Azure Traffic Manager
description: W tym samouczku opisano sposób tworzenia profilu Traffic Manager w celu utworzenia witryny sieci Web o wysokim stopniu odpowiedzi.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: d8262a80fac42f103d571523c75c5064d5d43949
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003824"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Samouczek: ulepszanie odpowiedzi witryny sieci Web przy użyciu Traffic Manager

W tym samouczku opisano, jak za pomocą programu Traffic Manager utworzyć witrynę sieci Web o wysokiej dostępności przez kierowanie ruchu użytkowników do witryny sieci Web przy najniższym opóźnieniu. Zwykle centrum danych o najniższym opóźnieniu jest tym, co jest najbliższe odległości geograficznej.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych z podstawową witryną internetową w usługach IIS
> * Tworzenie dwóch testowych maszyn wirtualnych, aby zobaczyć usługę Traffic Manager w działaniu
> * Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS
> * Utwórz profil Traffic Manager na potrzeby ulepszonej wydajności witryny sieci Web
> * Dodawanie punktów końcowych maszyny wirtualnej do profilu usługi Traffic Manager
> * Wyświetlanie informacji o działaniu usługi Traffic Manager

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby w tym samouczku zobaczyć usługi Traffic Manager w działaniu, trzeba wdrożyć:

- Dwa wystąpienia podstawowych witryn sieci Web działających w różnych regionach platformy Azure — **Wschodnie stany USA** i **Europa Zachodnia**.
- Dwie testowe maszyny wirtualne do testowania Traffic Manager-jednej maszyny wirtualnej w **regionie Wschodnie stany USA** oraz druga maszyna wirtualna w **Europie zachodniej**. Testowe maszyny wirtualne służą do zilustrowania sposobu, w jaki Traffic Manager kieruje ruchem użytkownika do witryny sieci Web działającej w tym samym regionie, co zapewnia najniższy czas oczekiwania.

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
   - Reguły portów ruchu **przychodzącego**  >  **Wybierz pozycję porty wejściowe**: wybierz pozycję **RDP** i **protokół http** w polu ściąganie.

3. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej: dyski**, a następnie kliknij kolejno pozycje **Sieć** i **Dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przeglądanie + tworzenie**.
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.  
6. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę wirtualną o nazwie *myIISVMWestEurope*, z nazwą **grupy zasobów** *MyResourceGroupTM2*, **lokalizacją** *Europa Zachodnia* i wszystkimi innymi ustawieniami takimi jak *myIISVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

   ![Tworzenie maszyny wirtualnej](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji należy zainstalować serwer IIS na dwóch maszynach wirtualnych *myIISVMEastUS* i *myIISVMWestEurope*, a następnie zaktualizować domyślną stronę witryny sieci Web. Niestandardowa strona witryny internetowej przedstawia nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do **narzędzi administracyjnych systemu Windows** > **Menedżer serwera**.
7. Uruchom program Windows PowerShell na maszynie wirtualnej VM1 i użyj poniższych poleceń, aby zainstalować serwer usług IIS i zaktualizować domyślny plik HTM.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalowanie usług IIS i dostosowywanie strony internetowej](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Zamknij połączenie protokołu RDP z maszyną wirtualną *myIISVMEastUS*.
9. Powtórz kroki od 1-8 do, tworząc połączenie RDP z *myIISVMWestEurope* maszyny wirtualnej w grupie zasobów *myResourceGroupTM2* , aby zainstalować usługi IIS i dostosować jej domyślną stronę sieci Web.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS serwerów usług IIS — *myIISVMEastUS* i *myIISVMWestEurope*.

1. Kliknij opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1-3 dla maszyny wirtualnej o nazwie *myIISVMWestEurope* , która znajduje się w grupie zasobów *myResourceGroupTM2* .

### <a name="create-test-vms"></a>Tworzenie testowych maszyn wirtualnych

W tej sekcji utworzysz maszynę wirtualną (*myVMEastUS* i *myVMWestEurope*) w każdym regionie świadczenia usługi Azure (**Wschodnie stany USA** i **Europa Zachodnia**). Te maszyny wirtualne posłużą do testowania, jak usługa Traffic Manager kieruje ruch do najbliższego serwera usług IIS, kiedy użytkownik nawiguje do witryny internetowej.

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

Utwórz profil Traffic Manager, który kieruje ruchem użytkownika przez wysłanie ich do punktu końcowego z najniższym opóźnieniem.

1. W lewej górnej części ekranu wybierz kolejno pozycje **Utwórz zasób**  >  **Sieć**  >  **Traffic Manager**  >  **Utwórz** profil.
2. W obszarze **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz metodę routingu **wydajności** .                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz grupę zasobów *myResourceGroupTM1*. |
    | Lokalizacja                | Wybierz pozycję **Wschodnie stany USA**. To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.                              |
    |

    ![Tworzenie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne, na których działają serwery IIS — *myIISVMEastUS*  &  *myIISVMWestEurope* , aby skierować ruch użytkowników do najbliższego punktu końcowego do użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Punkt końcowy platformy Azure                                   |
    | Nazwa           | myEastUSEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *myIISVMEastUS-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać kolejny punkt końcowy o nazwie *myWestEuropeEndpoint* dla publicznego adresu IP *myIISVMWestEurope-IP* skojarzony z maszyną wirtualną serwera IIS o nazwie *myIISVMWestEurope*.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

    ![Dodawanie punktu końcowego usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

W tej sekcji przetestujesz sposób, w jaki Traffic Manager kieruje ruchem użytkowników do najbliższych maszyn wirtualnych z uruchomioną witryną sieci Web w celu zapewnienia minimalnego opóźnienia. Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:

1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl informacje o działaniu usługi Traffic Manager w następujący sposób:
    - Z poziomu testowej maszyny wirtualnej (*myVMEastUS*), która znajduje się w regionie **Wschodnie stany USA** , w przeglądarce internetowej przejdź do nazwy DNS profilu Traffic Manager.
    - Z testową maszyną wirtualną (*myVMWestEurope*), która znajduje się w regionie **Europa Zachodnia** , w przeglądarce internetowej przejdź do nazwy DNS profilu Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager

Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
2. Kliknij pozycję **Przegląd**.
3. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

   ![Nazwa DNS usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager

W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
1. W przeglądarce internetowej na maszynie wirtualnej *myVMEastUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Ponieważ maszyna wirtualna znajduje się w **regionie Wschodnie stany USA**, nastąpi przekierowanie do najbliższej witryny sieci Web hostowanej na najbliższym serwerze IIS *myIISVMEastUS* , który znajduje się w **regionie Wschodnie stany USA**.

   ![Zrzut ekranu pokazujący profil "Traffic Manager" w przeglądarce sieci Web.](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Następnie połącz się z maszyną wirtualną *myVMWestEurope* znajdującą się w **Europie zachodniej** , korzystając z kroków 1-5 i przejdź do nazwy domeny profilu Traffic Manager z tej maszyny wirtualnej. Ponieważ maszyna wirtualna znajduje się w **zachodniej Europie**, jesteś teraz kierowany do witryny sieci Web hostowanej na najbliższym serwerze IIS *myIISVMWestEurope* , który znajduje się w **Europie zachodniej**.

   ![Testowanie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Kiedy grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**) przestaną być potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dystrybuowanie ruchu do zestawu punktów końcowych](traffic-manager-configure-weighted-routing-method.md)

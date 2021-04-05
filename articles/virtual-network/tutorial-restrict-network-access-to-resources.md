---
title: Ograniczanie dostępu do zasobów PaaS — samouczek Azure Portal
description: W tym samouczku dowiesz się, jak ograniczyć i zablokować dostęp sieciowy do zasobów platformy Azure, takich jak usługi Azure Storage i Azure SQL Database, za pomocą punktów końcowych usługi dla sieci wirtualnej z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368297"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Samouczek: ograniczanie dostępu sieciowego do zasobów PaaS za pomocą punktów końcowych usługi dla sieci wirtualnej z użyciem witryny Azure Portal

Punkty końcowe usługi dla sieci wirtualnej umożliwiają ograniczenie dostępu sieciowego do niektórych zasobów usługi platformy Azure do podsieci sieci wirtualnej. Możesz również uniemożliwić dostęp internetowy do zasobów. Punkty końcowe usługi zapewniają bezpośrednie połączenie z sieci wirtualnej z obsługiwanymi usługami platformy Azure, umożliwiając korzystanie z prywatnej przestrzeni adresowej sieci wirtualnej w celu uzyskiwania dostępu do usług platformy Azure. Ruch kierowany do zasobów platformy Azure za pośrednictwem punktów końcowych usługi zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej z jedną podsiecią
> * Dodawanie podsieci i włączanie punktu końcowego usługi
> * Tworzenie zasobów platformy Azure i zezwalanie na dostęp sieciowy do nich tylko z podsieci
> * Wdrażanie maszyny wirtualnej w każdej podsieci
> * Potwierdzanie dostępu do zasobu z podsieci
> * Potwierdzanie zablokowania dostępu do zasobu z podsieci i z Internetu

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-restrict-network-access-to-resources-cli.md) lub [programu Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **sieci wirtualne**.
3. Kliknij pozycję **+ Dodaj** i wprowadź następujące informacje: 

   |Ustawienie|Wartość|
   |----|----|
   |Subskrypcja| Wybierz swoją subskrypcję|
   |Grupa zasobów | Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *myResourceGroup*.|
   |Nazwa| Wprowadź *myVirtualNetwork* |
   |Region (Region)| Wybierz **(US) Wschodnie stany USA** |

   ![Wprowadzanie podstawowych informacji o sieci wirtualnej](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Kliknij przycisk **Dalej: adresy IP >**
   
   |Ustawienie|Wartość|
   |----|----|
   |IPv4Address| Pozostaw jako domyślne |
   |Nazwa podsieci| Kliknij przycisk **domyślne** i Zmień nazwę z "domyślne" na "publiczny"|
   |Zakres adresów podsieci| Pozostaw jako domyślne|

5. Kliknij przycisk **Dalej: zabezpieczenia >** 
   
   |Ustawienie|Wartość|
   |----|----|   
   |BastionHost| Wyłącz|
   |Ochrona przed atakami DDOS| Wyłącz|
   |Firewall| Wyłącz|

6. Po zakończeniu kliknij przycisk **Przeglądaj i Utwórz**.
7. Jeśli sprawdzanie poprawności zostanie zakończone pomyślnie, kliknij przycisk **Utwórz**.
8. Poczekaj na zakończenie wdrożenia, a następnie kliknij pozycję **Przejdź do zasobu** lub przechodź do następnej sekcji. 

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi

Punkty końcowe usługi są włączane dla poszczególnych usług i podsieci. Aby utworzyć podsieć i włączyć punkt końcowy usługi dla podsieci:

1. Jeśli nie jesteś jeszcze na stronie zasobów sieci wirtualnej, możesz wyszukać nowo utworzoną sieć w polu **Wyszukaj zasoby, usługi i dokumenty** w górnej części portalu, wprowadzić *myVirtualNetwork* i wybrać ją z listy.
2. W menu **Ustawienia** (po lewej) wybierz pozycję **podsieci**, a następnie wybierz pozycję **+ podsieć**, jak pokazano poniżej:

    ![Dodawanie podsieci](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. W obszarze **Dodaj podsieć** wybierz lub wprowadź następujące informacje, a następnie wybierz **OK**:

    |Ustawienie|Wartość|
    |----|----|
    |Nazwa| Prywatny |
    |Zakres adresów| Pozostaw jako domyślne|
    |Punkty końcowe usługi| Wybierz pozycję **Microsoft. Storage**|
    |Zasady punktów końcowych usługi | Pozostaw wartość domyślną 0 |

> [!CAUTION]
> Przed włączeniem punktu końcowego usługi dla istniejącej podsieci zawierającej zasoby zapoznaj się z sekcją [Zmiana ustawień podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

4. Kliknij przycisk **Zapisz**, a następnie zamknij okno podsieci po prawej stronie. Nowo utworzona podsieć powinna zostać wyświetlona na liście.

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczanie dostępu sieciowego dla podsieci

Domyślnie wszystkie wystąpienia maszyn wirtualnych w podsieci mogą komunikować się ze wszystkimi zasobami. Możesz ograniczyć komunikację do i ze wszystkich zasobów w podsieci, tworząc sieciową grupę zabezpieczeń i kojarząc ją z podsiecią:

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.
2. Wybierz pozycję **Sieć**, a następnie wybierz (lub Wyszukaj) **sieciowe grupy zabezpieczeń**.
3. Na stronie **grupy zabezpieczeń sieci** kliknij pozycję **+ Dodaj**.
4. Wprowadź następujące informacje 

    |Ustawienie|Wartość|
    |----|----|
    |Subskrypcja| Wybierz swoją subskrypcję|
    |Grupa zasobów | Z listy wybierz pozycję Moja *zasobów*|
    |Nazwa| Wprowadź **myNsgPrivate** |
    |Lokalizacja| Wybierz **Wschodnie stany USA** |

5. Kliknij przycisk **Przegląd + Utwórz** i po przekazaniu sprawdzania poprawności kliknij przycisk **Utwórz**.
6. Po utworzeniu sieciowej grupy zabezpieczeń kliknij pozycję **Przejdź do zasobu** lub Wyszukaj *myNsgPrivate*.
7. W obszarze **Ustawienia** po lewej stronie wybierz pozycję **reguły zabezpieczeń dla ruchu wychodzącego**.
8. Wybierz pozycję **+ Dodaj**.
9. Utwórz regułę, która umożliwia komunikację wychodzącą do usługi Azure Storage. Wprowadź lub wybierz następujące informacje, a następnie wybierz polecenie **Dodaj**:

    |Ustawienie|Wartość|
    |----|----|
    |Źródło| Wybierz pozycję **VirtualNetwork** |
    |Zakresy portów źródłowych| * |
    |Miejsce docelowe | Wybierz **tag usługi**|
    |Docelowy tag usługi | Wybierz pozycję **Magazyn**|
    |Zakresy portów docelowych| Pozostaw wartość domyślną *8080* |
    |Protokół|Dowolny|
    |Akcja|Zezwalaj|
    |Priorytet|100|
    |Nazwa|Zmień nazwę na **Zezwalaj-Storage-All**|

10. Utwórz kolejną regułę zabezpieczeń dla ruchu wychodzącego, która nie zezwala na komunikację z Internetem. Ta reguła zastępuje regułę domyślną we wszystkich grupach zabezpieczeń sieci, umożliwiającą wychodzącą komunikacja internetową. Wykonaj kroki 6-9 od powyższych, używając następujących wartości:

    |Ustawienie|Wartość|
    |----|----|
    |Źródło| Wybierz pozycję **VirtualNetwork** |
    |Zakresy portów źródłowych| * |
    |Miejsce docelowe | Wybierz **tag usługi**|
    |Docelowy tag usługi| Wybierz pozycję **Internet**|
    |Zakresy portów docelowych| * |
    |Protokół|Dowolny|
    |Akcja|**Zmień wartość domyślną na *Odmów*** |
    |Priorytet|110|
    |Nazwa|Zmień na *Odmów — Internet — wszystko*|

11. Utwórz *regułę zabezpieczeń dla ruchu przychodzącego* zezwalającą na ruch Remote Desktop Protocol (RDP) do podsieci z dowolnego miejsca. Reguła zastępuje domyślną regułę zabezpieczeń, która zakazuje całego ruchu przychodzącego z Internetu. Połączenia pulpitu zdalnego są dozwolone do podsieci, aby później można było przetestować łączność. 
12. W obszarze **Ustawienia** wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego**.
13. Wybierz pozycję **+ Dodaj** i użyj następujących wartości:

    |Ustawienie|Wartość|
    |----|----|
    |Źródło| Dowolne |
    |Zakresy portów źródłowych| * |
    |Miejsce docelowe | Wybierz pozycję **VirtualNetwork**|
    |Zakresy portów docelowych| Zmień na *3389* |
    |Protokół|Dowolny|
    |Akcja|Zezwalaj|
    |Priorytet|120|
    |Nazwa|Zmień na *Zezwalaj-RDP-All*|

   >[!WARNING] 
   > Port RDP 3389 jest uwidoczniony w Internecie. Jest to zalecane tylko na potrzeby testowania. W przypadku *środowisk produkcyjnych* zalecamy użycie połączenia sieci VPN lub prywatnego.

1.  W obszarze **Ustawienia** wybierz pozycję **Podsieci**.
2.  Kliknij pozycję **+ Skojarz**.
3.  W obszarze **Sieć wirtualna** wybierz pozycję **myVirtualNetwork**.
4.  W obszarze **podsieć** wybierz pozycję **prywatny**, a następnie wybierz przycisk **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Ograniczanie dostępu sieciowego do zasobu

Kroki wymagane do ograniczenia dostępu sieciowego do zasobów utworzonych za pośrednictwem usług platformy Azure, które są włączone dla punktów końcowych usługi, różnią się w zależności od usług. Zobacz dokumentację poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. W pozostałej części tego samouczka znajdują się, jako przykład, kroki ograniczające dostęp do konta usługi Azure Storage.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wprowadź ciąg "konto magazynu" na pasku wyszukiwania, a następnie wybierz go z menu rozwijanego.
3. Kliknij pozycję **+ Dodaj**.
4. Wprowadź następujące informacje:

    |Ustawienie|Wartość|
    |----|----|
    |Subskrypcja| Wybierz swoją subskrypcję|
    |Grupa zasobów| Wybierz pozycję Moja *resourceName*|
    |Nazwa konta magazynu| Wprowadź nazwę, która jest unikatowa dla wszystkich lokalizacji platformy Azure, ma długość od 3 do 24 znaków oraz zawiera tylko cyfry i małe litery.|
    |Lokalizacja| Wybierz **(US) Wschodnie stany USA** |
    |Wydajność|Standardowa|
    |Rodzaj konta| StorageV2 (ogólnego przeznaczenia wersja 2)|
    |Replikacja| Magazyn lokalnie nadmiarowy (LRS)|

5. Wybierz pozycję **Utwórz + Przejrzyj** i po przekazaniu weryfikacji walidacji kliknij pozycję **Utwórz**. 

>[!NOTE] 
> Wdrożenie może potrwać kilka minut.

6. Po utworzeniu konta magazynu kliknij pozycję **Przejdź do zasobu**

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

1. Przejdź do strony Przegląd konta magazynu.
2. Wybierz ikonę aplikacji **udziały plików** , a następnie kliknij pozycję **+ udział plików**.

    |Ustawienie|Wartość|
    |----|----|
    |Nazwa| mój plik — udostępnianie|
    |limit przydziału| "Ustaw na wartość maksymalną" |

   ![Konto magazynu](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Kliknij pozycję **Utwórz**.
4. Udział plików powinien być wyświetlany w oknie platformy Azure, jeśli nie jest kliknięty przycisk **Odśwież**

### <a name="restrict-network-access-to-a-subnet"></a>Ograniczanie dostępu sieciowego do podsieci

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów znajdujących się w dowolnej sieci, w tym w Internecie. Można ograniczyć dostęp do sieci z Internetu i wszystkie pozostałe podsieci w ramach wszystkich sieci wirtualnych (poza podsiecią *prywatną* w sieci wirtualnej *myVirtualNetwork* ). Aby ograniczyć dostęp sieciowy do podsieci:

1. W obszarze **Ustawienia** konta magazynu (z unikatowymi nazwami) wybierz pozycję **Sieć**.
2. Wybierz pozycję **Wybrane sieci**.
3. Wybierz pozycję **+ Dodaj istniejącą sieć wirtualną**.
4. W obszarze **Dodaj sieci** wybierz następujące wartości, a następnie wybierz pozycję **Dodaj**:

    |Ustawienie|Wartość|
    |----|----|
    |Subskrypcja| Wybierz swoją subskrypcję|
    |Sieci wirtualne| **myVirtualNetwork**|
    |Podsieci| **Prywatne**|

    ![Zrzut ekranu przedstawia okienko Dodawanie sieci, w którym można wprowadzić określone wartości.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Kliknij przycisk **Dodaj** , a następnie natychmiast kliknij ikonę **Zapisz** , aby zapisać zmiany.
6. W obszarze **Ustawienia** dla konta magazynu wybierz pozycję **klucze dostępu**, jak pokazano na poniższej ilustracji:

      ![Zrzut ekranu przedstawia klucze dostępu wybrane z ustawień, w których można uzyskać klucz.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Kliknij przycisk **Pokaż klucze** i zanotuj wartości **klucza** , ponieważ trzeba będzie ręcznie wprowadzić Klucz1 w późniejszym kroku podczas mapowania udziału plików na literę dysku na maszynie wirtualnej.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Aby przetestować dostęp sieciowy do konta magazynu, należy wdrożyć maszynę wirtualną w każdej podsieci.

### <a name="create-the-first-virtual-machine"></a>Tworzenie pierwszej maszyny wirtualnej

1. W obszarze "Wyszukaj zasoby. . ." na pasku Znajdź pozycję **maszyny wirtualne**.
2. Wybierz pozycję **+ dodaj > maszynę wirtualną**. 
3. Wprowadź następujące informacje:

   |Ustawienie|Wartość|
   |----|----|
   |Subskrypcja| Wybierz swoją subskrypcję|
   |Grupa zasobów| Wybierz pozycję * * dla zasobu, który został utworzony wcześniej.|
   |Nazwa maszyny wirtualnej| Wprowadź *myVmPublic*|
   |Region (Region) | (USA) Wschodnie stany USA
   |Opcje dostępności| Strefa dostępności|
   |Strefa dostępności | 1 |
   |Obraz | Windows Server 2019 Datacenter — Gen1 |
   |Rozmiar | Wybierz rozmiar wystąpienia maszyny wirtualnej, którego chcesz użyć |
   |Nazwa użytkownika|Wprowadź wybraną nazwę użytkownika.|
   |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Publiczne porty wejściowe | Zezwalaj na wybrane porty |
   |Wybierz porty wejściowe | Pozostaw domyślnie ustawiony *protokół RDP (3389)* |

   ![Wybieranie sieci wirtualnej](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Wybierz kartę **Sieć** , a następnie wybierz pozycję **myVirtualNetwork**. 
5. Wybierz podsieć *publiczną* .
6. W obszarze **Sieć sieciowa Grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. Portal automatycznie utworzy sieciową grupę zabezpieczeń, która zezwala na ruch przez port 3389, który musi być otwarty w celu nawiązania połączenia z maszyną wirtualną w kolejnym kroku. 

   ![Wprowadzanie podstawowych informacji o maszynie wirtualnej](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Wybierz pozycję **Przejrzyj i Utwórz**, a następnie **Utwórz** i poczekaj na zakończenie wdrożenia.
8. Kliknij pozycję **Przejdź do zasobu** lub Otwórz stronę **Narzędzia główne > maszyny wirtualne** , a następnie wybierz utworzoną maszynę wirtualną *myVmPublic*, która powinna zostać uruchomiona.

### <a name="create-the-second-virtual-machine"></a>Tworzenie drugiej maszyny wirtualnej

1. Ponownie wykonaj kroki 1-8, ale w kroku 3 nadaj maszynie wirtualnej nazwę *myVmPrivate* i ustaw **Publiczny port wejściowy** na wartość "none" (brak). 
2. W kroku 4-5 wybierz podsieć **prywatną** .

>[!NOTE]
> Ustawienia **grupy zabezpieczeń sieci karty** sieciowej i **publicznych portów przychodzących** powinny stanowić odbicie obrazu widocznego poniżej, w tym niebieskie okno potwierdzenia z informacją o tym, że cały publiczny ruch internetowy zostanie zablokowany domyślnie.

   ![Utwórz prywatną maszynę wirtualną](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Wybierz pozycję **Przejrzyj i Utwórz**, a następnie **Utwórz** i poczekaj na zakończenie wdrożenia. 

>[!WARNING]
> Nie należy przechodzić do następnego kroku, dopóki wdrożenie nie zostanie ukończone.

4. Zaczekaj na pokazane poniżej okno potwierdzenia i kliknij pozycję **Przejdź do zasobu**.

   ![Utwórz okno potwierdzenia prywatnej maszyny wirtualnej](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

1. Po utworzeniu maszyny wirtualnej *myVmPrivate* kliknij pozycję **Przejdź do zasobu**. 
2. Połącz się z maszyną wirtualną, wybierając pozycję **połącz > RDP**.
3. Po wybraniu przycisku **Połącz** zostanie utworzony plik Remote Desktop Protocol (RDP). Kliknij pozycję **Pobierz plik RDP** , aby pobrać go do komputera.  
4. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może być konieczne wybranie **pozycji więcej opcji**, a następnie **użycie innego konta** w celu określenia poświadczeń wprowadzonych podczas tworzenia maszyny wirtualnej. W polu adres e-mail wprowadź określone wcześniej poświadczenia "konto administratora: nazwa_użytkownika". 
5. Wybierz pozycję **OK**.
6. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie. Powinna zostać wyświetlona wartość startowa maszyny wirtualnej, jak pokazano:

   ![Pokaż prywatną maszynę wirtualną z systemem](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. W oknie maszyna wirtualna Otwórz wystąpienie interfejsu wiersza polecenia programu PowerShell.
8. Za pomocą poniższego skryptu zmapuj udział plików platformy Azure na dysk Z przy użyciu programu PowerShell. Przed uruchomieniem poleceń, które obserwują, Zastąp `<storage-account-key>` i oba `<storage-account-name>` pola wartościami, które zostały podane, i "d wcześniej w obszarze [Tworzenie konta magazynu](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   Program PowerShell zwraca dane wyjściowe podobne do następujących przykładowych danych wyjściowych:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Udział plików platformy Azure został pomyślnie mapowany na dysk Z.

9.   Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

1. Wprowadź ciąg *myVmPublic* w polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu.
2. Gdy pozycja **myVmPublic** pojawi się w wynikach wyszukiwania, wybierz ją.
3. Wykonaj kroki 1-8 powyżej w obszarze [Potwierdź dostęp do konta magazynu](#confirm-access-to-storage-account) dla maszyny wirtualnej *myVmPublic* .

   Po chwili pojawi się komunikat o błędzie: `New-PSDrive : Access is denied`. Odmowa dostępu nastąpi, ponieważ maszyna wirtualna *myVmPublic* jest wdrożona w podsieci *Public*. W podsieci *Public* nie ma punktu końcowego usługi włączonego dla usługi Azure Storage. Konto magazynu zezwala jedynie na dostęp do sieci z podsieci *Private*, ale nie z podsieci *Public*.

4. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*.
5. W Azure Portal przejdź do konta magazynu z unikatowymi nazwami, które zostało utworzone wcześniej.
6. W obszarze usługa plików wybierz pozycję **udziały plików**, *udział my-File* i utworzony wcześniej.
7. Powinien zostać wyświetlony następujący komunikat o błędzie:

   ![Błąd odmowy dostępu](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> Odmowa dostępu następuje, ponieważ komputer nie znajduje się w podsieci *Private* sieci wirtualnej *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją wraz ze wszystkimi zasobami, które zawiera:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku został włączony punkt końcowy usługi dla podsieci sieci wirtualnej. Wiesz teraz, że punkty końcowe usługi można włączyć dla zasobów wdrożonych w wielu usługach platformy Azure. Zostało utworzone konto usługi Azure Storage i dostęp sieciowy do konta magazynu został ograniczony tylko do zasobów w podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Service endpoints overview (Omówienie punktów końcowych usługi)](virtual-network-service-endpoints-overview.md) i [Manage subnets (Zarządzanie podsieciami)](virtual-network-manage-subnet.md).

Jeśli masz wiele sieci wirtualnych w ramach Twojego konta, możesz chcieć połączyć ze sobą dwie sieci wirtualne, aby zasoby w każdej sieci wirtualnej mogły komunikować się ze sobą. Aby dowiedzieć się, jak połączyć sieci wirtualne, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Łączenie sieci wirtualnych](./tutorial-connect-virtual-networks-portal.md)

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
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: e6716d2ad4252169cfbbf611b0dadc5b077cd362
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042570"
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

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób** .
2. Wybierz pozycję **Sieć** , a następnie wybierz pozycję **Sieć wirtualna** .
3. Wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Utwórz** :

   |Ustawienie|Wartość|
   |----|----|
   |Nazwa| myVirtualNetwork |
   |Przestrzeń adresowa| 10.0.0.0/16|
   |Subskrypcja| Wybierz swoją subskrypcję|
   |Grupa zasobów | Wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę *myResourceGroup* .|
   |Lokalizacja| Wybierz **Wschodnie stany USA** |
   |Nazwa podsieci| Publiczne|
   |Zakres adresów podsieci| 10.0.0.0/24|
   |Ochrona przed atakami DDOS| Podstawowa|
   |Punkty końcowe usługi| Disabled|
   |Zapora| Disabled|

   ![Wprowadzanie podstawowych informacji o sieci wirtualnej](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi

Punkty końcowe usługi są włączane dla poszczególnych usług i podsieci. Utwórz podsieć i włącz punkt końcowy usługi dla podsieci.

1. W polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu wprowadź ciąg *myVirtualNetwork* . Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Dodaj podsieć do sieci wirtualnej. W obszarze **Ustawienia** wybierz pozycję **podsieci** , a następnie wybierz pozycję **+ podsieć** , jak pokazano na poniższej ilustracji:

    ![Dodawanie podsieci](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. W obszarze **Dodaj podsieć** wybierz lub wprowadź następujące informacje, a następnie wybierz **OK** :

    |Ustawienie|Wartość|
    |----|----|
    |Nazwa| Prywatny |
    |Zakres adresów| 10.0.1.0/24|
    |Punkty końcowe usługi| Wybierz pozycję **Microsoft.Storage** w obszarze **Usługi**|

> [!CAUTION]
> Przed włączeniem punktu końcowego usługi dla istniejącej podsieci zawierającej zasoby zapoznaj się z sekcją [Zmiana ustawień podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczanie dostępu sieciowego dla podsieci

Domyślnie wszystkie maszyny wirtualne w podsieci mogą komunikować się ze wszystkimi zasobami. Można ograniczyć komunikację z zasobami w podsieci, tworząc grupę zabezpieczeń sieci i kojarząc ją z podsiecią.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób** .
2. Wybierz pozycję **Sieć** , a następnie wybierz pozycję **Sieciowa grupa zabezpieczeń** .
3. W obszarze **Tworzenie sieciowej grupy zabezpieczeń** wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Utwórz** :

    |Ustawienie|Wartość|
    |----|----|
    |Nazwa| myNsgPrivate |
    |Subskrypcja| Wybierz swoją subskrypcję|
    |Grupa zasobów | Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroup* .|
    |Lokalizacja| Wybierz **Wschodnie stany USA** |

4. Po utworzeniu sieciowej grupy zabezpieczeń wprowadź ciąg *myNsgPrivate* w polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu. Gdy pozycja **myNsgPrivate** pojawi się w wynikach wyszukiwania, wybierz ją.
5. W obszarze **USTAWIENIA** wybierz pozycję **Reguły zabezpieczeń dla ruchu wychodzącego** .
6. Wybierz pozycję **+ Dodaj** .
7. Utwórz regułę, która umożliwia komunikację wychodzącą do usługi Azure Storage. Wprowadź lub wybierz następujące informacje, a następnie wybierz polecenie **Dodaj** :

    |Ustawienie|Wartość|
    |----|----|
    |Źródło| Wybierz pozycję **VirtualNetwork** |
    |Zakresy portów źródłowych| * |
    |Miejsce docelowe | Wybierz **tag usługi**|
    |Docelowy tag usługi | Wybierz pozycję **Magazyn**|
    |Zakresy portów docelowych| * |
    |Protokół|Dowolny|
    |Akcja|Zezwalaj|
    |Priorytet|100|
    |Nazwa|Allow-Storage-All|

8. Utwórz kolejną regułę zabezpieczeń dla ruchu wychodzącego, która nie zezwala na komunikację z Internetem. Ta reguła zastępuje regułę domyślną we wszystkich grupach zabezpieczeń sieci, umożliwiającą wychodzącą komunikacja internetową. Ponownie wykonaj kroki 5–7, używając następujących wartości:

    |Ustawienie|Wartość|
    |----|----|
    |Źródło| Wybierz pozycję **VirtualNetwork** |
    |Zakresy portów źródłowych| * |
    |Miejsce docelowe | Wybierz **tag usługi**|
    |Docelowy tag usługi| Wybierz pozycję **Internet**|
    |Zakresy portów docelowych| * |
    |Protokół|Dowolny|
    |Akcja|Zablokuj|
    |Priorytet|110|
    |Nazwa|Deny-Internet-All|

9. W obszarze **USTAWIENIA** , wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego** .
10. Wybierz pozycję **+ Dodaj** .
11. Utwórz regułę zabezpieczeń dla ruchu przychodzącego zezwalającą na ruch protokołu RDP (Remote Desktop) do podsieci z dowolnego miejsca. Reguła zastępuje domyślną regułę zabezpieczeń, która zakazuje całego ruchu przychodzącego z Internetu. Połączenia pulpitu zdalnego są dozwolone do podsieci, aby później można było przetestować łączność. W obszarze **USTAWIENIA** wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego** , a następnie wybierz przycisk **+ Dodaj** , wprowadź poniższe wartości i wybierz polecenie **Dodaj** :

    |Ustawienie|Wartość|
    |----|----|
    |Źródło| Dowolne |
    |Zakresy portów źródłowych| * |
    |Miejsce docelowe | Wybierz pozycję **VirtualNetwork**|
    |Zakresy portów docelowych| 3389 |
    |Protokół|Dowolny|
    |Akcja|Zezwalaj|
    |Priorytet|120|
    |Nazwa|Allow-RDP-All|

12. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci** .
13. Wybierz pozycję **+ Skojarz**
14. W obszarze **Skojarz podsieć** wybierz pozycję **Sieć wirtualna** , a następnie wybierz pozycję **myVirtualNetwork** w obszarze **Wybierz sieć wirtualną** .
15. W obszarze **Wybierz podsieć** wybierz pozycję **Private** , a następnie wybierz przycisk **OK** .

## <a name="restrict-network-access-to-a-resource"></a>Ograniczanie dostępu sieciowego do zasobu

Kroki niezbędne do ograniczenia dostępu sieciowego do zasobów utworzonych za pomocą usług platformy Azure obsługujących punkty końcowe usługi różnią się w zależności od usługi. Zobacz dokumentację poszczególnych usług, aby poznać konkretne kroki dla każdej usługi. W pozostałej części tego samouczka znajdują się, jako przykład, kroki ograniczające dostęp do konta usługi Azure Storage.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób** .
2. Wybierz pozycję **Magazyn** , a następnie wybierz pozycję **Konto magazynu — obiekt blob, plik, tabela, kolejka** .
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Utwórz** :

    |Ustawienie|Wartość|
    |----|----|
    |Nazwa| Wprowadź nazwę, która jest unikatowa dla wszystkich lokalizacji platformy Azure, ma długość od 3 do 24 znaków oraz zawiera tylko cyfry i małe litery.|
    |Rodzaj konta|StorageV2 (ogólnego przeznaczenia wersja 2)|
    |Lokalizacja| Wybierz **Wschodnie stany USA** |
    |Replikacja| Magazyn lokalnie nadmiarowy (LRS)|
    |Subskrypcja| Wybierz swoją subskrypcję|
    |Grupa zasobów | Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroup* .|

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

1. Po utworzeniu konta magazynu wprowadź nazwę konta magazynu w polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu. Gdy nazwa Twojego konta magazynu zostanie wyświetlona w wynikach wyszukiwania, wybierz je.
2. Wybierz pozycję **Pliki** , jak pokazano na poniższym obrazie:

   ![Konto magazynu](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Wybierz pozycję **+ udział plików** .
4. Wprowadź wartość *my-file-share* w obszarze **Nazwa** , a następnie wybierz przycisk **OK** .
5. Zamknij pole **Usługa pliku** .

### <a name="restrict-network-access-to-a-subnet"></a>Ograniczanie dostępu sieciowego do podsieci

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów znajdujących się w dowolnej sieci, w tym w Internecie. Zabroń dostępu z Internetu i wszystkich innych podsieci we wszystkich sieciach wirtualnych z wyjątkiem podsieci *Private* sieci wirtualnej *myVirtualNetwork* .

1. W obszarze **USTAWIENIA** dla konta magazynu wybierz pozycję **Zapory i sieci wirtualne** .
2. Wybierz pozycję **Wybrane sieci** .
3. Wybierz pozycję **+ Dodaj istniejącą sieć wirtualną** .
4. W obszarze **Dodaj sieci** wybierz następujące wartości, a następnie wybierz pozycję **Dodaj** :

    |Ustawienie|Wartość|
    |----|----|
    |Subskrypcja| Wybierz subskrypcję.|
    |Sieci wirtualne|Wybierz pozycję **myVirtualNetwork** w obszarze **Sieci wirtualne**|
    |Podsieci| Wybierz pozycję **Private** w obszarze **Podsieci**|

    ![Zrzut ekranu przedstawia okienko Dodawanie sieci, w którym można wprowadzić określone wartości.](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. Wybierz pozycję **Zapisz** .
6. Zamknij pole **Zapory i sieci wirtualne** .
7. W obszarze **USTAWIENIA** dla konta magazynu wybierz pozycję **Klucze dostępu** , jak pokazano na poniższym obrazie:

      ![Zrzut ekranu przedstawia klucze dostępu wybrane z ustawień, w których można uzyskać klucz.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Zanotuj wartość **Klucz** , ponieważ trzeba będzie ją ręcznie wprowadzić w dalszym kroku podczas mapowania udziału plików na literę dysku na maszynie wirtualnej.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Aby przetestować dostęp sieciowy do konta magazynu, należy wdrożyć maszynę wirtualną w każdej podsieci.

### <a name="create-the-first-virtual-machine"></a>Tworzenie pierwszej maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób** .
2. Wybierz pozycję **Wystąpienia obliczeniowe** , a następnie wybierz pozycję **Windows Server 2016 Datacenter** .
3. Wprowadź lub wybierz następujące informacje, a następnie wybierz przycisk **OK** :

   |Ustawienie|Wartość|
   |----|----|
   |Nazwa| myVmPublic|
   |Nazwa użytkownika|Wprowadź wybraną nazwę użytkownika.|
   |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Subskrypcja| Wybierz subskrypcję.|
   |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup** .|
   |Lokalizacja| Wybierz pozycję **Wschodnie stany USA** .|

   ![Wprowadzanie podstawowych informacji o maszynie wirtualnej](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz** .
5. W obszarze **Ustawienia** wybierz pozycję **Sieć** , a następnie wybierz pozycję **myVirtualNetwork** . Następnie wybierz pozycję **Podsieć** i wybierz pozycję **Public** , jak pokazano na poniższym obrazie:

   ![Wybieranie sieci wirtualnej](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. W obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane** . Portal automatycznie utworzy sieciową grupę zabezpieczeń, która zezwala na ruch przez port 3389, który musi być otwarty w celu nawiązania połączenia z maszyną wirtualną w kolejnym kroku. Wybierz przycisk **OK** na stronie **Ustawienia** .
7. Na stronie **Podsumowanie** wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie maszyny wirtualnej. Wdrożenie maszyny wirtualnej zajmuje kilka minut, ale możesz przejść do następnego kroku podczas tworzenia maszyny wirtualnej.

### <a name="create-the-second-virtual-machine"></a>Tworzenie drugiej maszyny wirtualnej

Ponownie wykonaj kroki 1–7, ale w kroku 3 nadaj maszynie wirtualnej nazwę *myVmPrivate* , a w kroku 5 wybierz podsieć **Private** .

Wdrożenie maszyny wirtualnej potrwa kilka minut. Nie należy przechodzić do następnego kroku, dopóki nie zostanie zakończone jej tworzenie i w portalu nie zostaną otwarte jej ustawienia.

## <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

1. Po zakończeniu tworzenia maszyny wirtualnej *myVmPrivate* platforma Azure otworzy jej ustawienia. Połącz się z maszyną wirtualną, wybierając przycisk **Połącz** , jak pokazano na poniższym obrazie:

   ![Nawiązywanie połączenia z maszyną wirtualną](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Po wybraniu przycisku **Połącz** zostanie utworzony i pobrany na komputer plik Remote Desktop Protocol (rdp).  
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz** . Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji** , a następnie pozycji **Użyj innego konta** , aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
4. Wybierz pozycję **OK** .
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj** , aby nawiązać połączenie.
6. Na maszynie wirtualnej *myVmPrivate* mapuj udział plików platformy Azure na dysk Z przy użyciu programu PowerShell. Przed uruchomieniem poniższych poleceń zastąp zmienne `<storage-account-key>` i `<storage-account-name>` wartościami podanymi i pobranymi w sekcji [Tworzenie konta magazynu](#create-a-storage-account).

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

7. Z poziomu wiersza polecenia upewnij się, że maszyna wirtualna nie ma łączności wychodzącej do Internetu:

   ```
   ping bing.com
   ```

   Nie otrzymasz żadnych odpowiedzi, ponieważ sieciowa grupa zabezpieczeń skojarzona z podsiecią *Private* nie zezwala na dostęp ruchu wychodzącego do Internetu.

8. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate* .

## <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

1. Wprowadź ciąg *myVmPublic* w polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu.
2. Gdy pozycja **myVmPublic** pojawi się w wynikach wyszukiwania, wybierz ją.
3. Wykonaj kroki 1–6 w sekcji [Potwierdzanie dostępu do konta magazynu](#confirm-access-to-storage-account) dla maszyny wirtualnej *myVmPublic* .

   Po chwili pojawi się komunikat o błędzie: `New-PSDrive : Access is denied`. Odmowa dostępu nastąpi, ponieważ maszyna wirtualna *myVmPublic* jest wdrożona w podsieci *Public* . W podsieci *Public* nie ma punktu końcowego usługi włączonego dla usługi Azure Storage. Konto magazynu zezwala jedynie na dostęp do sieci z podsieci *Private* , ale nie z podsieci *Public* .

4. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic* .

5. Na swoim komputerze przejdź do witryny [Azure Portal](https://portal.azure.com).
6. Wprowadź nazwę utworzonego konta magazynu w polu **Szukaj zasobów, usług i dokumentów** . Gdy nazwa Twojego konta magazynu zostanie wyświetlona w wynikach wyszukiwania, wybierz je.
7. Wybierz pozycję **pliki** .
8. Zostanie wyświetlony błąd pokazany na poniższym obrazie:

   ![Błąd odmowy dostępu](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   Odmowa dostępu następuje, ponieważ komputer nie znajduje się w podsieci *Private* sieci wirtualnej *MyVirtualNetwork* .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją wraz ze wszystkimi zasobami, które zawiera:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów** .
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku został włączony punkt końcowy usługi dla podsieci sieci wirtualnej. Wiesz teraz, że punkty końcowe usługi można włączyć dla zasobów wdrożonych w wielu usługach platformy Azure. Zostało utworzone konto usługi Azure Storage i dostęp sieciowy do konta magazynu został ograniczony tylko do zasobów w podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Service endpoints overview (Omówienie punktów końcowych usługi)](virtual-network-service-endpoints-overview.md) i [Manage subnets (Zarządzanie podsieciami)](virtual-network-manage-subnet.md).

Jeśli masz wiele sieci wirtualnych w ramach Twojego konta, możesz chcieć połączyć ze sobą dwie sieci wirtualne, aby zasoby w każdej sieci wirtualnej mogły komunikować się ze sobą. Aby dowiedzieć się, jak połączyć sieci wirtualne, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Łączenie sieci wirtualnych](./tutorial-connect-virtual-networks-portal.md)

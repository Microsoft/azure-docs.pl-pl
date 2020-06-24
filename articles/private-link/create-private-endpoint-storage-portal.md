---
title: Nawiązywanie prywatnego połączenia z kontem magazynu za pomocą prywatnego punktu końcowego platformy Azure
description: Dowiedz się, jak połączyć się prywatnie z kontem magazynu na platformie Azure przy użyciu prywatnego punktu końcowego.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1ccbb685ceb406fd7a52edf793b53d9e1c32630b
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737329"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Nawiązywanie prywatnego połączenia z kontem magazynu za pomocą prywatnego punktu końcowego platformy Azure
Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się prywatnie z zasobami łączy prywatnych.

W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej platformy Azure, konto magazynu z prywatnym punktem końcowym przy użyciu Azure Portal. Następnie możesz bezpiecznie uzyskać dostęp do konta magazynu z maszyny wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku (konta magazynu w tym przykładzie).

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku.

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVirtualNetwork          |
| **\<region-name>**          | Zachodnio-środkowe stany USA      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

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
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Utwórz maszynę wirtualną**, pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

## <a name="create-your-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
W tej sekcji utworzysz prywatne konto magazynu przy użyciu prywatnego punktu końcowego. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **Magazyn**  >  **konto magazynu**.

1. W obszarze **Tworzenie konta magazynu — podstawowe**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa konta magazynu  | Wprowadź *mojekontomagazynu*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    | Region | Wybierz pozycję **WestCentralUS**. |
    | Wydajność| Pozostaw wartość domyślną **Standardowa**. |
    | Rodzaj konta | Pozostaw domyślny **Magazyn (ogólnego przeznaczenia w wersji 2)**. |
    | Replikacja | Wybierz **Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)**. |
    |||
  
3. Wybierz pozycję **Dalej: sieć**.
4. W obszarze **Tworzenie konta magazynu — sieć**, metoda połączenia wybierz pozycję **prywatny punkt końcowy**.
5. W obszarze **Tworzenie konta magazynu — sieć**wybierz pozycję **Dodaj prywatny punkt końcowy**. 
6. W obszarze **Utwórz prywatny punkt końcowy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    |Lokalizacja|Wybierz pozycję **WestCentralUS**.|
    |Nazwa|Wprowadź *myPrivateEndpoint*.  |
    |Zasób podrzędny magazynu|Pozostaw domyślny **obiekt BLOB**. |
    | **SIECI** |  |
    | Sieć wirtualna  | Wybierz pozycję *MyVirtualNetwork* *z grupy zasobów*. |
    | Podsieć | Wybierz pozycję Moja *podsieć*. |
    | **PRYWATNA INTEGRACJA Z USŁUGĄ DNS**|  |
    | Integracja z prywatną strefą DNS  | Pozostaw wartość domyślną **tak**. |
    | Prywatna strefa DNS  | Pozostaw wartość domyślną **(New) privatelink.blob.Core.Windows.NET**. |
    |||
7. Wybierz przycisk **OK**. 
8. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację. 
9. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**. 
10. Przejdź do zasobu konta magazynu, który właśnie został utworzony.
11. Z menu po lewej stronie wybierz pozycję **klucze dostępu** .
12. Wybierz pozycję **Kopiuj** w parametrach połączenia dla Klucz1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Połącz się z maszyną wirtualną *myVm* z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz *pobrany plik RDP* .

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-storage-account-privately-from-the-vm"></a>Dostęp do konta magazynu prywatnego z maszyny wirtualnej

W tej sekcji nastąpi połączenie prywatne z kontem magazynu przy użyciu prywatnego punktu końcowego.

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.
2. Wprowadź `nslookup mystorageaccount.blob.core.windows.net` komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Zainstaluj [Eksplorator usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Wybierz pozycję **konta magazynu** po kliknięciu prawym przyciskiem myszy.
5. Wybierz pozycję **Połącz z usługą Azure Storage**.
6. Wybierz pozycję **Użyj parametrów połączenia**.
7. Wybierz przycisk **Dalej**.
8. Wprowadź parametry połączenia, wklejając wcześniej skopiowane informacje.
9. Wybierz przycisk **Dalej**.
10. Wybierz pozycję **Połącz**.
11. Przeglądanie kontenerów obiektów blob z mojekontomagazynu 
12. Zdefiniować Utwórz foldery i/lub Przekaż pliki do *mojekontomagazynu*. 
13. Zamknij połączenie pulpitu zdalnego z *myVM*. 

Dodatkowe opcje uzyskiwania dostępu do konta magazynu:
- Eksplorator usługi Microsoft Azure Storage to autonomiczna bezpłatna aplikacja oferowana przez firmę Microsoft, która umożliwia wizualne korzystanie z danych usługi Azure Storage w systemach Windows, macOS i Linux. Możesz zainstalować aplikację, aby przeglądać prywatnie zawartość konta magazynu. 
 
- Narzędzie AzCopy jest kolejną opcją do przesyłania danych z obsługą skryptów o wysokiej wydajności dla usługi Azure Storage. Narzędzie AzCopy służy do transferowania danych do i z usług Blob, File i Table Storage. 


## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego, konta magazynu i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby: 
1. Wprowadź w polu **wyszukiwania** w górnej części portalu *i wybierz pozycję* *moja zasobów z* wyników wyszukiwania. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start utworzono MASZYNę wirtualną w sieci wirtualnej i na koncie magazynu oraz prywatnym punkcie końcowym. Nawiązano połączenie z jedną maszyną wirtualną z Internetu i bezpieczny dostęp do konta magazynu za pomocą linku prywatnego. Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md).

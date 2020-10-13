---
title: 'Samouczek: łączenie się z kontem magazynu przy użyciu prywatnego punktu końcowego platformy Azure'
titleSuffix: Azure Private Link
description: Zacznij korzystać z prywatnego punktu końcowego platformy Azure, aby połączyć się z kontem magazynu prywatnie.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 69bee753c2134b6eebe9c5df0a554c965208ad7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91366227"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Samouczek: łączenie się z kontem magazynu przy użyciu prywatnego punktu końcowego platformy Azure

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się z prywatnymi prywatnymi zasobami.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną i hosta bastionu.
> * Tworzy maszynę wirtualną.
> * Utwórz konto magazynu za pomocą prywatnego punktu końcowego.
> * Testuj połączenie z prywatnym punktem końcowym konta magazynu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu zostanie użyty do nawiązania bezpiecznego połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region           | Wybierz **Wschodnie stany USA** |

3. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

4. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź **10.1.0.0/16** |

5. W obszarze **Nazwa podsieci**wybierz pozycję **domyślny**wyraz.

6. W obszarze **Edytuj podsieć**wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź **maskę** |
    | Zakres adresów podsieci | Wprowadź **10.1.0.0/24** |

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **zabezpieczenia** .

9. W obszarze **BastionHost**wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. </br> W obszarze **Nazwa**wprowadź **myBastionIP**. </br> Kliknij przycisk **OK**. |


8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

W tej sekcji utworzysz maszynę wirtualną, która będzie używana do testowania prywatnego punktu końcowego.


1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej** lub Wyszukaj **maszynę wirtualną** w polu wyszukiwania.
   
2. W obszarze **Utwórz maszynę wirtualną**wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM** |
    | Region | Wybierz **Wschodnie stany USA** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter-Gen1** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | **myVNet** |
    | Podsieć | **mySubnet** |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Grupa zabezpieczeń sieci karty sieciowej | **Podstawowe**|
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
   
5. Wybierz pozycję **Przeglądanie + tworzenie**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="create-storage-account-with-a-private-endpoint"></a>Tworzenie konta magazynu za pomocą prywatnego punktu końcowego

W tej sekcji utworzysz konto magazynu i skonfigurujesz prywatny punkt końcowy.

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób magazyn zasobów**  >  **Storage**  >  **Storage account**lub Wyszukaj **konto magazynu** w polu wyszukiwania.

2. Na karcie **podstawowe** w obszarze **Tworzenie konta magazynu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa konta magazynu | Wprowadź **mojekontomagazynu**. Jeśli nazwa jest niedostępna, wprowadź unikatową nazwę. |
    | Lokalizacja | Wybierz **Wschodnie stany USA** |
    | Wydajność | Pozostaw wartość domyślną **Standard** |
    | Rodzaj konta | Pozostaw domyślny **Magazyn (ogólnego przeznaczenia w wersji 2)** |
    | Replikacja| Pozostaw domyślny **Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)** |
   
3. Wybierz kartę **Sieć** lub wybierz przycisk **Dalej: sieć** .

4. Na karcie **Sieć** w obszarze **Metoda łączności** wybierz pozycję **prywatny punkt końcowy**.

5. W obszarze **prywatny punkt końcowy**wybierz pozycję **+ Dodaj**.

6. W obszarze **Utwórz prywatny punkt końcowy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | Lokalizacja | Wybierz **Wschodnie stany USA** |
    | Nazwa | Wprowadź **myPrivateEndpoint** |
    | Podzasób magazynu | Pozostaw domyślny **obiekt BLOB** |
    | **Sieć** |  |
    | Sieć wirtualna | Wybierz **myVNet** |
    | Podsieć | Wybierz pozycję **mySubnet** |
    | **Integracja Prywatna strefa DNS** |
    | Integruj z prywatną strefą DNS | Pozostaw wartość domyślną **tak** |
    | Prywatna strefa DNS | Pozostaw domyślne (nowe) privatelink.blob.core.windows.net |

7. Kliknij przycisk **OK**.

8. Wybierz pozycję **Przeglądanie + tworzenie**.

9. Wybierz pozycję **Utwórz**.

10. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

11. Wybierz pozycję **myResourceGroup**.

12. Wybierz konto magazynu utworzone w poprzednich krokach.

13. W sekcji **Ustawienia** konta magazynu wybierz pozycję **klucze dostępu**.

14. Wybierz pozycję Kopiuj w **parametrach połączenia** dla **Klucz1**.

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z kontem magazynu w prywatnym punkcie końcowym.

1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

2. Wybierz pozycję **myResourceGroup**.

3. Wybierz pozycję **myVM**.

4. Na stronie Przegląd dla **myVM**wybierz pozycję **Połącz** , a następnie **bastionu**.

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

8. Wprowadź `nslookup <storage-account-name>.blob.core.windows.net`. Zamień **\<storage-account-name>** na nazwę konta magazynu utworzonego w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    Prywatny adres IP **10.1.0.5** jest zwracany dla nazwy konta magazynu.  Ten adres znajduje się w podsieci sieci wirtualnej, która została wcześniej utworzona.

9. Zainstaluj [Eksplorator usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows) na maszynie wirtualnej.

10. Po zainstalowaniu **Eksplorator usługi Microsoft Azure Storage** wybierz pozycję **Zakończ** .  Pozostaw zaznaczone pole, aby otworzyć aplikację.

11. Na ekranie **łączenie z usługą Azure Storage** wybierz pozycję **Użyj parametrów połączenia**.

12. Wybierz opcję **Dalej**.

13. Wprowadź nazwę konta magazynu z poprzednich kroków w polu **Nazwa wyświetlana**.

14. W polu **Parametry połączenia**wklej parametry połączenia z konta magazynu skopiowanego w poprzednich krokach.

15. Wybierz opcję **Dalej**.

16. Sprawdź, czy ustawienia są poprawne w **podsumowaniu połączenia**.  

17. Wybierz pozycję **Połącz**.

18. Zamknij połączenie z usługą **myVM**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i konto magazynu, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

2. Wybierz pozycję **myResourceGroup**.

3. Wybierz pozycję **Usuń grupę zasobów**.

4. W polu **wpisz nazwę grupy zasobów**wprowadź **adres** .

5. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć usługę linku prywatnego:
> [!div class="nextstepaction"]
> [Tworzenie usługi łącza prywatnego](create-private-link-service-portal.md)

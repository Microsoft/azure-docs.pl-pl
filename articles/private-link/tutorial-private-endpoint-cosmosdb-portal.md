---
title: 'Samouczek: Nawiązywanie połączenia z kontem usługi Azure Cosmos przy użyciu prywatnego punktu końcowego platformy Azure'
titleSuffix: Azure Private Link
description: Rozpocznij pracę z tym samouczkiem przy użyciu prywatnego punktu końcowego platformy Azure, aby połączyć się z kontem usługi Azure Cosmos prywatnie.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 3a7e75641f6bb84b490231fcd06e04c3cbad06d3
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063470"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Samouczek: Nawiązywanie połączenia z kontem usługi Azure Cosmos przy użyciu prywatnego punktu końcowego platformy Azure

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się z prywatnymi prywatnymi zasobami.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną i hosta bastionu.
> * Tworzy maszynę wirtualną.
> * Utwórz konto Cosmos DB przy użyciu prywatnego punktu końcowego.
> * Testuj połączenie z prywatnym punktem końcowym konta Cosmos DB.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu zostanie użyty do nawiązania bezpiecznego połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region (Region)           | Wybierz **Wschodnie stany USA** |

3. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

4. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź **10.1.0.0/16** |

5. W obszarze **Nazwa podsieci** wybierz pozycję **domyślny** wyraz.

6. W obszarze **Edytuj podsieć** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź **maskę** |
    | Zakres adresów podsieci | Wprowadź **10.1.0.0/24** |

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **zabezpieczenia** .

9. W obszarze **BastionHost** wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |


8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

W tej sekcji utworzysz maszynę wirtualną, która będzie używana do testowania prywatnego punktu końcowego.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej** lub Wyszukaj **maszynę wirtualną** w polu wyszukiwania.
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM** |
    | Region (Region) | Wybierz **Wschodnie stany USA** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter-Gen1** |
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
    | Grupa zabezpieczeń sieci karty sieciowej | **Podstawowa**|
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
   
5. Wybierz pozycję **Przejrzyj i utwórz**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Tworzenie konta Cosmos DB przy użyciu prywatnego punktu końcowego

W tej sekcji utworzysz konto Cosmos DB i skonfigurujesz prywatny punkt końcowy.

1. W menu po lewej stronie wybierz pozycję **Utwórz**  >  **bazę danych** zasobów  >  **Cosmos DB konto** lub Wyszukaj **konto Cosmos DB** w polu wyszukiwania.

2. Na karcie **podstawy** **Utwórz konto Cosmos DB** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz swoją subskrypcję platformy Azure. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **Szczegóły wystąpienia** |  |
    | Nazwa konta | Wprowadź **mycosmosdb**. Jeśli nazwa jest niedostępna, wprowadź unikatową nazwę. |
    | Interfejs API | Wybierz pozycję **Core (SQL)**. |
    | Lokalizacja | Wybierz pozycję **Wschodnie stany USA**. |
    | Tryb wydajności | Pozostaw domyślną **tymczasową przepływność**. |
    | Zastosuj rabat na podstawie warstwy Bezpłatna | Pozostaw wartość domyślną **nie stosuj**. |
    | Nadmiarowość geograficzna | Pozostaw domyślne ustawienie **Wyłącz**. |
    | Moduły zapisujące obsługujące wiele regionów | Pozostaw domyślne ustawienie **Wyłącz**. |
   
3. Wybierz kartę **Sieć** lub wybierz przycisk **Dalej: sieć** .

4. Na karcie **Sieć** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Łączność sieciowa** | |
    | Metoda łączności | Wybierz pozycję **prywatny punkt końcowy**. |
    | **Konfigurowanie zapory** | |
    | Zezwalaj na dostęp z Azure Portal | Pozostaw domyślne ustawienie **Zezwalaj**. |
    | Zezwalaj na dostęp z mojego adresu IP | Pozostaw domyślne **Odmów**. |

5. W obszarze **prywatny punkt końcowy** wybierz pozycję **+ Dodaj**.

6. W obszarze **Utwórz prywatny punkt końcowy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | Lokalizacja | Wybierz **Wschodnie stany USA** |
    | Nazwa | Wprowadź **myPrivateEndpoint** |
    | Podzasób docelowy | Pozostaw wartość domyślną **(SQL)** |
    | **Sieć** |  |
    | Sieć wirtualna | Wybierz **myVNet** |
    | Podsieć | Wybierz pozycję **mySubnet** |
    | **Integracja Prywatna strefa DNS** |
    | Integruj z prywatną strefą DNS | Pozostaw wartość domyślną **tak** |
    | Prywatna strefa DNS | Pozostaw domyślne (nowe) privatelink.documents.azure.com |

7. Wybierz przycisk **OK**.

8. Wybierz pozycję **Przejrzyj i utwórz**.

9. Wybierz pozycję **Utwórz**.

### <a name="add-a-database-and-a-container"></a>Dodawanie bazy danych i kontenera

1. Wybierz pozycję odbiór **do zasobu** lub w menu po lewej stronie Azure Portal wybierz pozycję **wszystkie zasoby**  >  **mycosmosdb**.

2. W menu po lewej stronie wybierz pozycję **Eksplorator danych**.

3. W oknie **Eksplorator danych** wybierz pozycję **nowy kontener**.

4. W obszarze **Dodaj kontener** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Identyfikator bazy danych | Pozostaw wartość domyślną **Utwórz nowy**. </br> Wprowadź **mydatabaseid** w polu tekstowym. |
    | Przepływność (400 – 100 000 RU/s) | Pozostaw domyślną wartość **ręcznie**. </br> W polu tekstowym wprowadź **400** . |
    | Identyfikator kontenera | Wprowadź **mycontainerid** |
    | Klucz partycji | Wprowadź **/MyKey** |

5. Wybierz przycisk **OK**.

6. W sekcji **Ustawienia** konta CosmosDB wybierz pozycję **klucze**.

7. Wybierz opcję Kopiuj dla **podstawowych parametrów połączenia**.

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z kontem Cosmos DB w prywatnym punkcie końcowym.

1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz pozycję **myResourceGroup**.

1. Wybierz pozycję **myVM**.

1. Na stronie Przegląd dla **myVM** wybierz pozycję **Połącz** , a następnie **bastionu**.

1. Wybierz przycisk **bastionu Użyj** niebieska.

1. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

1. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

1. Wprowadź `nslookup <cosmosdb-account-name>.documents.azure.com` i sprawdź poprawność rozpoznawania nazw. Zamień **\<cosmosdb-account-name>** na nazwę konta Cosmos DB utworzonego w poprzednich krokach. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Prywatny adres IP **10.1.0.5** jest zwracany dla nazwy konta Cosmos DB.  Ten adres znajduje się w podsieci sieci wirtualnej, która została wcześniej utworzona.
    
1. Pobierz Azure Cosmos DB podstawowe parametry połączenia z portalu. Prawidłowe parametry połączenia są w formacie:
   
   Konta interfejsu API SQL: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` dla Azure Cosmos DB API dla MongoDB: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. Zainstaluj [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) na maszynie wirtualnej.

1. Po zainstalowaniu **Eksplorator usługi Microsoft Azure Storage** wybierz pozycję **Zakończ** .  Pozostaw zaznaczone pole, aby otworzyć aplikację.

1. Na ekranie **łączenie z usługą Azure Storage** wybierz pozycję **Anuluj**.

1. W Eksplorator usługi Storage wybierz prawy przycisk myszy na **kontach Cosmos DB** i wybierz pozycję **Połącz z Cosmos DB**.

1. Pozostaw wartość domyślną **SQL** w obszarze **Wybierz interfejs API**.

1. W polu **Parametry połączenia** wklej parametry połączenia z konta Cosmos DB skopiowane w poprzednich krokach.

1. Wybierz opcję **Dalej**.

1. Sprawdź, czy ustawienia są poprawne w **podsumowaniu połączenia**.  

1. Wybierz pozycję **Połącz**.

1. Zamknij połączenie z usługą **myVM**.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i konto Cosmos DB, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

2. Wybierz pozycję **myResourceGroup**.

3. Wybierz pozycję **Usuń grupę zasobów**.

4. W polu **wpisz nazwę grupy zasobów** wprowadź **adres** .

5. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono:

* Sieć wirtualna i Host bastionu.
* Maszyna wirtualna.
* Konto Cosmos DB.

Dowiedz się, jak utworzyć usługę linku prywatnego:
> [!div class="nextstepaction"]
> [Tworzenie usługi łącza prywatnego](create-private-link-service-portal.md)

---
title: Samouczek — nawiązywanie połączenia z serwerem Azure SQL przy użyciu prywatnego punktu końcowego platformy Azure
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak utworzyć serwer Azure SQL z prywatnym punktem końcowym przy użyciu Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: dfbd1f4077caa2fd80a00e3cf950735363a0a971
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099900"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Samouczek — nawiązywanie połączenia z serwerem Azure SQL przy użyciu prywatnego punktu końcowego platformy Azure — Azure Portal

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się z prywatnymi prywatnymi zasobami.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną i hosta bastionu.
> * Tworzy maszynę wirtualną.
> * Utwórz serwer Azure SQL i prywatny punkt końcowy.
> * Przetestuj łączność z prywatnym punktem końcowym programu SQL Server.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.


## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu zostanie użyty do nawiązania bezpiecznego połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **CreateSQLEndpointTutorial — RG** |
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
    | Grupa zasobów | Wybierz **CreateSQLEndpointTutorial** |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Tworzenie serwera Azure SQL i prywatnego punktu końcowego

W tej sekcji utworzysz program SQL Server na platformie Azure. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >    >  **bazy danych SQL Database**.

1. Na karcie **podstawy** **tworzenia bazy danych SQL** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **CreateSQLEndpointTutorial**. Ta grupa zasobów została utworzona w poprzedniej sekcji.|
    | **Szczegóły bazy danych** |  |
    | Nazwa bazy danych  | Wprowadź **mysqldatabase**. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    | Serwer | Wybierz pozycję **Utwórz nowy**. |

6. W obszarze **nowy serwer** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa serwera  | Wprowadź nazwę **mysqlserver**. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
    | Identyfikator logowania administratora serwera | Wprowadź wybraną nazwę administratora. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 8 znaków i spełniać zdefiniowane wymagania. |
    | Lokalizacja | Wybierz **Wschodnie stany USA** |
    
7. Wybierz przycisk **OK**.

8. Wybierz kartę **Sieć** lub wybierz przycisk **Dalej: sieć** .

9. Na karcie **Sieć** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Łączność sieciowa** | |
    | Metoda łączności | Wybierz pozycję **prywatny punkt końcowy**. |
   
10. Wybierz pozycję **+ Dodaj prywatny punkt końcowy** w **prywatnych punktach końcowych**.

11. W obszarze **Utwórz prywatny punkt końcowy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **CreateSQLEndpointTutorial**. |
    | Lokalizacja | Wybierz pozycję **Wschodnie stany USA**. |
    | Nazwa | Wprowadź **myPrivateSQLendpoint**. |
    | Docelowy zasób podrzędny | Wybierz pozycję **SqlServer**. |
    | **Sieć** |  |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Podsieć | Wybierz pozycję Moja **podsieć**. |
    | **Integracja Prywatna strefa DNS** | |
    | Integruj z prywatną strefą DNS | Pozostaw wartość domyślną **tak**. |
    | Prywatna strefa DNS | Pozostaw wartość domyślną **(New) privatelink.Database.Windows.NET**. |

12. Wybierz przycisk **OK**. 

13. Wybierz pozycję **Przejrzyj i utwórz**.

14. Wybierz pozycję **Utwórz**.

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z programem SQL Server w ramach prywatnego punktu końcowego.

1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

2. Wybierz pozycję **CreateSQLEndpointTutorial**.

3. Wybierz pozycję **myVM**.

4. Na stronie Przegląd dla **myVM** wybierz pozycję **Połącz** , a następnie **bastionu**.

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

8. Wprowadź `nslookup <sqlserver-name>.database.windows.net`. Zamień **\<sqlserver-name>** na nazwę serwera SQL, który został utworzony w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Prywatny adres IP **10.1.0.5** jest zwracany dla nazwy programu SQL Server.  Ten adres znajduje się w podsieci sieci wirtualnej, która została wcześniej utworzona.


9. Zainstaluj [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) w **myVM**.

10. Otwórz **SQL Server Management Studio**.

4. W obszarze **Połącz z serwerem** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera | Wybierz pozycję **Aparat bazy danych**.|
    | Nazwa serwera | Wprowadź **\<sqlserver-name> . Database.Windows.NET** |
    | Uwierzytelnianie | Wybierz pozycję **Uwierzytelnianie SQL Server**. |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika wprowadzoną podczas tworzenia serwera |
    | Hasło | Wprowadź hasło wprowadzone podczas tworzenia serwera |
    | Remember password (Zapamiętaj hasło) | Wybierz pozycję **Tak**. |

1. Wybierz pozycję **Połącz**.
2. Przeglądaj bazy danych z menu po lewej stronie.
3. Zdefiniować Utwórz lub Zbadaj informacje z **mysqldatabase**.
4. Zamknij połączenie pulpitu zdalnego z **myVM**. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego, programu SQL Server i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby: 
1. Wprowadź **CreateSQLEndpointTutorial** w polu **wyszukiwania** w górnej części portalu i wybierz pozycję **CreateSQLEndpointTutorial** z wyników wyszukiwania. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. Wprowadź CreateSQLEndpointTutorial w **polu wpisz nazwę grupy zasobów** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono:

* Sieć wirtualna i Host bastionu.
* Maszyna wirtualna.
* Serwer SQL Azure z prywatnym punktem końcowym.

Maszyna wirtualna została użyta do bezpiecznego testowania łączności z programem SQL Server przez prywatny punkt końcowy.

W następnym kroku warto również zainteresować [aplikację internetową z prywatnym połączeniem z architekturą usługi Azure SQL Database](https://docs.microsoft.com/azure/architecture/example-scenario/private-web-app/private-web-app) , która łączy aplikację sieci Web spoza sieci wirtualnej z prywatnym punktem końcowym bazy danych.

Dowiedz się, jak utworzyć usługę linku prywatnego:
> [!div class="nextstepaction"]
> [Tworzenie usługi łącza prywatnego](create-private-link-service-portal.md)
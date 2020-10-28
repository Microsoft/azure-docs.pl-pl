---
title: 'Samouczek: łączenie się z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure'
titleSuffix: Azure Private Link
description: Rozpocznij pracę z tym samouczkiem przy użyciu prywatnego punktu końcowego platformy Azure, aby połączyć się z usługą webapp prywatnie.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896977"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Samouczek: łączenie się z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą komunikować się z prywatnymi prywatnymi zasobami.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną i hosta bastionu.
> * Tworzy maszynę wirtualną.
> * Utwórz element WEBAPP.
> * Utwórz prywatny punkt końcowy.
> * Testuj połączenie z prywatnym punktem końcowym aplikacji sieci Web.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!Note]
> Prywatny punkt końcowy jest dostępny w regionach publicznych dla PremiumV2 warstwy PremiumV3 aplikacji sieci Web systemu Windows, aplikacji sieci Web w systemie Linux oraz planu Azure Functions Premium (czasami określanego jako elastyczny plan Premium). 

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
    | Region           | Wybierz **Europa Zachodnia** |

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

7. Wybierz pozycję **Zapisz** .

8. Wybierz kartę **zabezpieczenia** .

9. W obszarze **BastionHost** wybierz pozycję **enable (Włącz** ). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy** . </br> W obszarze **Nazwa** wprowadź **myBastionIP** . </br> Wybierz przycisk **OK** . |


8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz pozycję **Utwórz** .

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
    | Region | Wybierz **Europa Zachodnia** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter-Gen1** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski** , a następnie pozycję **Dalej: Sieć** .
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | **myVNet** |
    | Podsieć | **mySubnet** |
    | Publiczny adres IP | Wybierz pozycję **Brak** . |
    | Grupa zabezpieczeń sieci karty sieciowej | **Podstawowe**|
    | Publiczne porty wejściowe | Wybierz pozycję **Brak** . |
   
5. Wybierz pozycję **Przeglądanie + tworzenie** . 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** .

## <a name="create-web-app"></a>Tworzenie aplikacji internetowej

W tej sekcji utworzysz aplikację sieci Web.

1. W menu po lewej stronie wybierz pozycję **Utwórz zasób**  >  **Storage**  >  **Web App** Storage lub Wyszukaj **aplikację sieci Web** w polu wyszukiwania.

2. Na karcie **podstawowe** w temacie **Tworzenie aplikacji sieci Web** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź **mywebapp** . Jeśli nazwa jest niedostępna, wprowadź unikatową nazwę. |
    | Publikowanie | Wybierz pozycję **Kod** . |
    | Stos środowiska uruchomieniowego | Wybierz pozycję **.NET Core 3,1 (LTS)** . |
    | System operacyjny | Wybierz pozycję **Windows** . |
    | Region | Wybierz **Europa Zachodnia** |
    | **Plan usługi App Service** |  |
    | Plan Windows (Europa Zachodnia) | Wybierz pozycję **Utwórz nowy** . </br> Wprowadź **myServicePlan** w polu **Nazwa** . |
    | Jednostka SKU i rozmiar | Wybierz pozycję **Zmień rozmiar** . </br> Wybierz pozycję **P2V2** na ekranie **selektora specyfikacji** . </br> Wybierz przycisk **Zastosuj** . |
   
3. Wybierz pozycję **Przeglądanie + tworzenie** .

4. Wybierz pozycję **Utwórz** .

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="Karta podstawy tworzenia aplikacji sieci Web w Azure Portal." border="true":::

## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

1. W menu po lewej stronie wybierz pozycję **wszystkie zasoby**  >  **mywebapp** lub nazwę wybraną podczas tworzenia.

2. W przeglądzie aplikacji sieci Web wybierz pozycję **Ustawienia**  >  **Sieć** .

3. W obszarze **Sieć** wybierz pozycję **Konfiguruj połączenia prywatnego punktu końcowego** .

4. Wybierz pozycję **+ Dodaj** na ekranie **połączenia prywatnego punktu końcowego** .

5. Wprowadź lub wybierz następujące informacje na ekranie **Dodawanie prywatnego punktu końcowego** :

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **mywebappendpoint** . |
    | Subskrypcja | Wybierz subskrypcję. |
    | Sieć wirtualna | Wybierz pozycję **myVNet** . |
    | Podsieć | Wybierz pozycję Moja **podsieć** . |
    | Integruj z prywatną strefą DNS | Wybierz pozycję **Tak** . |

6. Wybierz przycisk **OK** .
    

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z aplikacją sieci Web w prywatnym punkcie końcowym.

1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

2. Wybierz pozycję **myResourceGroup** .

3. Wybierz pozycję **myVM** .

4. Na stronie Przegląd dla **myVM** wybierz pozycję **Połącz** , a następnie **bastionu** .

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

8. Wprowadź `nslookup <webapp-name>.azurewebsites.net`. Zamień **\<webapp-name>** na nazwę aplikacji sieci Web utworzonej w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Prywatny adres IP **10.1.0.5** jest zwracany dla nazwy aplikacji sieci Web.  Ten adres znajduje się w podsieci sieci wirtualnej, która została wcześniej utworzona.

9. Otwórz przeglądarkę sieci Web na komputerze lokalnym, a następnie wprowadź zewnętrzny adres URL aplikacji sieci Web, **https:// \<webapp-name> . azurewebsites.NET** .

10. Sprawdź, czy otrzymujesz stronę **403** . Ta strona wskazuje, że aplikacja sieci Web nie jest dostępna zewnętrznie.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="Karta podstawy tworzenia aplikacji sieci Web w Azure Portal." border="true":::

11. W bastionu połączenie z **myVM** Otwórz program Internet Explorer.

12. Wprowadź adres URL aplikacji sieci Web, **https:// \<webapp-name> . azurewebsites.NET** .

13. Sprawdź, czy zostanie wyświetlona strona domyślna aplikacja sieci Web.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Karta podstawy tworzenia aplikacji sieci Web w Azure Portal." border="true":::

18. Zamknij połączenie z usługą **myVM** .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i aplikację sieci Web, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów** .

2. Wybierz pozycję **myResourceGroup** .

3. Wybierz pozycję **Usuń grupę zasobów** .

4. W polu **wpisz nazwę grupy zasobów** wprowadź **adres** .

5. Wybierz pozycję **Usuń** .

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć usługę linku prywatnego:
> [!div class="nextstepaction"]
> [Tworzenie usługi łącza prywatnego](create-private-link-service-portal.md)

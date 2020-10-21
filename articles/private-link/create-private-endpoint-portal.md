---
title: Szybki Start — tworzenie prywatnego punktu końcowego przy użyciu Azure Portal
description: Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak utworzyć prywatny punkt końcowy przy użyciu Azure Portal.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 3deeca4635f33b63a6e0bcecc0c829d3df88e352
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327513"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal

Zacznij korzystać z prywatnego linku platformy Azure przy użyciu prywatnego punktu końcowego, aby bezpiecznie połączyć się z aplikacją internetową platformy Azure.

W tym przewodniku szybki start utworzysz prywatny punkt końcowy dla aplikacji internetowej platformy Azure i wdrożono maszynę wirtualną w celu przetestowania połączenia prywatnego.  

Prywatne punkty końcowe można utworzyć dla różnych rodzajów usług platformy Azure, takich jak Azure SQL i Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Aplikacja internetowa platformy Azure z planem usługi App Service **PremiumV2** lub nowszym wdrożonym w ramach subskrypcji platformy Azure.  
    * Aby uzyskać więcej informacji i zapoznać się z przykładem, zobacz [Szybki Start: Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../app-service/quickstart-dotnetcore.md). 
    * Aby uzyskać szczegółowy samouczek dotyczący tworzenia aplikacji sieci Web i punktu końcowego, zobacz [Samouczek: łączenie się z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure](tutorial-private-endpoint-webapp-portal.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu zostanie użyty do nawiązania bezpiecznego połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **CreatePrivateEndpointQS — RG** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region           | Wybierz pozycję **\<your-web-app-region>**. </br> Wybierz region, w którym wdrożono aplikację sieci Web.|

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
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. </br> W obszarze **Nazwa**wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |


8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz pozycję **Utwórz**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

W tej sekcji utworzysz maszynę wirtualną, która będzie używana do testowania prywatnego punktu końcowego.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej** lub Wyszukaj **maszynę wirtualną** w polu wyszukiwania.
   
2. W obszarze **Utwórz maszynę wirtualną**wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję **CreatePrivateEndpointQS — RG** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM** |
    | Region | Wybierz pozycję **\<your-web-app-region>**. </br> Wybierz region, w którym wdrożono aplikację sieci Web. |
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
    | Grupa zabezpieczeń sieci karty sieciowej | **Podstawowe**|
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
   
5. Wybierz pozycję **Przejrzyj i utwórz**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz prywatny punkt końcowy dla aplikacji sieci Web utworzonej w sekcji wymagania wstępne.

1. W lewym górnym rogu ekranu w portalu wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **prywatny link**lub w polu wyszukiwania wprowadź **łącze prywatne**.

2. Wybierz pozycję **Utwórz**.

3. W **prywatnym centrum połączenia**wybierz pozycję **prywatne punkty końcowe** w menu po lewej stronie.

4. W obszarze **prywatne punkty końcowe**wybierz pozycję **+ Dodaj**.

5. Na karcie **podstawy** **Utwórz prywatny punkt końcowy**, wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **CreatePrivateEndpointQS-RG**. Ta grupa zasobów została utworzona w poprzedniej sekcji.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa  | Wprowadź **myPrivateEndpoint**. |
    | Region | Wybierz pozycję **\<your-web-app-region>**. </br> Wybierz region, w którym wdrożono aplikację sieci Web. |

6. Wybierz kartę **zasób** lub przycisk **Dalej: zasób** w dolnej części strony.
    
7. W obszarze **zasób**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Metoda połączenia | Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Web/Sites**. |
    | Zasób | Wybierz pozycję **\<your-web-app-name>**. </br> Wybierz nazwę aplikacji sieci Web utworzonej w sekcji wymagania wstępne. |
    | Docelowy zasób podrzędny | Wybierz pozycję **Lokacje**. |

8. Wybierz kartę **Konfiguracja** lub przycisk **Dalej: Konfiguracja** w dolnej części ekranu.

9. W obszarze **Konfiguracja**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Sieć** |  |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Podsieć | Wybierz pozycję Moja **podsieć**. |
    | **Integracja Prywatna strefa DNS** |  |
    | Integruj z prywatną strefą DNS | Pozostaw wartość domyślną **tak**. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Prywatne strefy DNS | Pozostaw wartość domyślną **(New) privatelink.azurewebsites.NET**.
    

13. Wybierz pozycję **Przejrzyj i utwórz**.

14. Wybierz pozycję **Utwórz**.

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z aplikacją sieci Web w prywatnym punkcie końcowym.

1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

2. Wybierz pozycję **CreatePrivateEndpointQS-RG**.

3. Wybierz pozycję **myVM**.

4. Na stronie Przegląd dla **myVM**wybierz pozycję **Połącz** , a następnie **bastionu**.

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

8. Wprowadź `nslookup <your-webapp-name>.azurewebsites.net`. Zamień **\<your-webapp-name>** na nazwę aplikacji sieci Web utworzonej w poprzednich krokach.  Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Prywatny adres IP **10.1.0.5** jest zwracany dla nazwy aplikacji sieci Web.  Ten adres znajduje się w podsieci sieci wirtualnej, która została wcześniej utworzona.

11. W bastionu połączenie z **myVM**Otwórz program Internet Explorer.

12. Wprowadź adres URL aplikacji sieci Web, **https:// \<your-webapp-name> . azurewebsites.NET**.

13. Jeśli aplikacja nie została wdrożona, zostanie wyświetlona domyślna strona aplikacji sieci Web:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Domyślna strona aplikacji sieci Web." border="true":::

18. Zamknij połączenie z usługą **myVM**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i aplikację sieci Web, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

2. Wybierz pozycję **CreatePrivateEndpointQS-RG**.

3. Wybierz pozycję **Usuń grupę zasobów**.

4. Wprowadź **CreatePrivateEndpointQS-RG** w polu **wpisz nazwę grupy zasobów**.

5. Wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:

* Sieć wirtualna i Host bastionu.
* Maszyna wirtualna.
* Prywatny punkt końcowy dla aplikacji internetowej platformy Azure.

Maszyna wirtualna została użyta do bezpiecznego testowania łączności z aplikacją sieci Web w prywatnym punkcie końcowym.



Aby uzyskać więcej informacji na temat usług, które obsługują prywatny punkt końcowy, zobacz:
> [!div class="nextstepaction"]
> [Dostępność linku prywatnego](private-link-overview.md#availability)

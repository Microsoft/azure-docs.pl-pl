---
title: 'Samouczek: Tworzenie bramy translatora adresów sieciowych — Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Wprowadzenie do tworzenia bramy NAT przy użyciu Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 27b76f27b4e680c7e21f497f15b0fbf05fa40064
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108801"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Samouczek: Tworzenie bramy NAT przy użyciu Azure Portal

W tym samouczku przedstawiono sposób korzystania z usługi Azure Virtual Network translatora adresów sieciowych. Utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz sieć wirtualną.
> * Tworzy maszynę wirtualną.
> * Utwórz bramę NAT i skojarz ją z siecią wirtualną.
> * Połącz się z maszyną wirtualną i Sprawdź adres IP translatora adresów sieciowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Sieć wirtualna

Przed wdrożeniem maszyny wirtualnej i użyciem bramy NAT należy utworzyć grupę zasobów i sieć wirtualną.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

3. Wybierz przycisk **Utwórz**.

4. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **Utwórz nowy**. </br> Wprowadź **myResourceGroupNAT**. </br> Wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region (Region)           | SELECT **(Europa) Europa Zachodnia** |

5. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

6. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź **10.1.0.0/16** |

7. Wybierz pozycję **+ Dodaj podsieć**. 

8. W obszarze **Edytuj podsieć** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź **maskę** |
    | Zakres adresów podsieci | Wprowadź **10.1.0.0/24** |

9. Wybierz pozycję **Dodaj**.

10. Wybierz kartę **zabezpieczenia** .

11. W obszarze **BastionHost** wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |

12. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

13. Wybierz przycisk **Utwórz**.

## <a name="nat-gateway"></a>Brama translatora adresów sieciowych

Można użyć co najmniej jednego publicznego zasobu adresów IP, publicznych prefiksów IP lub obu tych zasobów. Dodamy zasób publicznego adresu IP i zasób bramy NAT.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieci > Brama translatora adresów** sieciowych lub Wyszukaj **bramę translatora adresów sieciowych** w polu wyszukiwania.

2. Wybierz przycisk **Utwórz**. 

3. W obszarze **Utwórz bramę translacji adresów sieciowych (NAT)** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz swoją subskrypcję platformy Azure.                                  |
    | Grupa zasobów   | Wybierz pozycję **myResourceGroupNAT**. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myNATgateway**                                    |
    | Region (Region)           | SELECT **(Europa) Europa Zachodnia**  |
    | Strefa dostępności | Wybierz pozycję **Brak**. |
    | Limit czasu bezczynności (minuty) | Wprowadź **10**. |

4. Wybierz kartę **wychodzący adres IP** lub wybierz przycisk **Dalej: wychodzący adres IP** w dolnej części strony.

5. Na karcie **wychodzący adres IP** wprowadź lub wybierz następujące informacje:

    | **Ustawienie** | **Wartość** |
    | ----------- | --------- |
    | Publiczne adresy IP | Wybierz pozycję **Utwórz nowy publiczny adres IP**. </br> W polu **Nazwa** wprowadź **myPublicIP**. </br> Wybierz przycisk **OK**. |

6. Wybierz kartę **podsieć** lub wybierz przycisk **Dalej: podsieć** w dolnej części strony.

7. Na karcie **podsieć** wybierz pozycję **myVNet** w polu ściąganie **sieci wirtualnej** .

8. Zaznacz pole wyboru obok pozycji moja **podsieć**.

9. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

10. Wybierz przycisk **Utwórz**.
    
## <a name="virtual-machine"></a>Maszyna wirtualna

W tej sekcji utworzysz maszynę wirtualną służącą do testowania bramy translatora adresów sieciowych i weryfikacji publicznego adresu IP połączenia wychodzącego.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 

2. Na stronie **Utwórz maszynę wirtualną** na karcie **podstawowe** wprowadź lub wybierz następujące informacje:

    | **Ustawienie** | **Wartość** |
    | ----------- | --------- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroupNAT**. |
    | **Szczegóły wystąpienia** |   |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM**. |
    | Region (Region) | Wybierz **(Europa) Europa Zachodnia**. |
    | Opcje dostępności | Pozostaw wartość domyślną nie wymaga nadmiarowości. |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter-Gen1**. |
    | Rozmiar | Wybierz **Standard_DS1_v2**. |
    | **Konto administratora** |   |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika dla maszyny wirtualnej. |
    | Hasło | Wprowadź hasło. |
    | Potwierdź hasło | Potwierdź hasło. |
    | **Reguły portów ruchu przychodzącego** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |

3. Wybierz kartę **dyski** lub kliknij przycisk **Dalej: dyski** w dolnej części strony.

4. Pozostaw wartość domyślną na karcie **dyski** .

5. Wybierz kartę **Sieć** lub kliknij przycisk **Dalej: sieć** w dolnej części strony.

6. Na karcie **Sieć** wprowadź lub wybierz następujące informacje:

    | **Ustawienie** | **Wartość** |
    | ----------- | --------- |
    | **Interfejs sieciowy** |   |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Podsieć | Wybierz pozycję Moja **podsieć**. |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **Podstawowa**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |

7. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

8. Wybierz przycisk **Utwórz**.

## <a name="test-nat-gateway"></a>Testowanie bramy translatora adresów sieciowych

W tej sekcji przetestujemy bramę translatora adresów sieciowych. Najpierw odnajdziemy publiczny adres IP bramy translatora adresów sieciowych. Następnie nastąpi połączenie z testową maszyną wirtualną i zweryfikowanie połączenia wychodzącego za pomocą bramy translatora adresów sieciowych.
    
1. Znajdź publiczny adres IP dla bramy translatora adresów sieciowych na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP**.

2. Zanotuj publiczny adres IP:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Odnajdź publiczny adres IP bramy translatora adresów sieciowych" border="true":::

3. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM** , która znajduje się w grupie zasobów **myResourceGroupNAT** .

4. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myTestVM**.

8. Wprowadź **https://whatsmyip.com** na pasku adresu.

9. Sprawdź, czy adres IP wyświetlany jest zgodny z adresem bramy NAT zanotowanym w poprzednim kroku:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer pokazujący zewnętrzny adres IP dla ruchu wychodzącego" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i bramę translatora adresów sieciowych, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

2. Wybierz grupę zasobów **myResourceGroupNAT** .

3. Wybierz pozycję **Usuń grupę zasobów**.

4. Wprowadź **myResourceGroupNAT** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Virtual Network translator adresów sieciowych, zobacz:
> [!div class="nextstepaction"]
> [Omówienie Virtual Network NAT](nat-overview.md)

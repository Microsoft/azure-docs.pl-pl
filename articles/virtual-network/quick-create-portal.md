---
title: 'Szybki Start: tworzenie sieci wirtualnej — Azure Portal'
titleSuffix: Azure Virtual Network
description: W tym przewodniku szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu Azure Portal.
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606072"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu witryny Azure Portal

W tym przewodniku szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu Azure Portal. Wdrażane są dwie maszyny wirtualne. Następnie można bezpiecznie komunikować się między maszynami wirtualnymi i łączyć się z maszynami wirtualnymi z Internetu. Sieć wirtualna jest podstawowym blokiem konstrukcyjnym sieci prywatnej na platformie Azure. Pozwala ona na bezpieczne komunikowanie się ze sobą i z Internetem za pomocą zasobów platformy Azure, takich jak maszyny wirtualne.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu.

2. W polu wyszukiwania wprowadź **Virtual Network**. Wybierz **Virtual Network** w wynikach wyszukiwania.

3. Na stronie **Virtual Network** wybierz pozycję **Utwórz**.

4. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**.  </br> Wprowadź nazwę **zasobu**. </br> Wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** |   |
    | Nazwa | Wprowadź nazwę **myVNet**. |
    | Region (Region) | Wybierz pozycję **(US) Wschodnie stany USA**. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Utwórz Azure Portal sieci wirtualnej" border="true":::

5. Wybierz kartę **adresy IP** lub kliknij przycisk **Dalej: adresy IP** w dolnej części strony.

6. W **obszarze przestrzeń adresowa IPv4** wybierz istniejącą przestrzeń adresową i zmień ją na **10.1.0.0/16**.

7. Wybierz pozycję **+ Dodaj** **podsieć, a następnie wprowadź dla** **nazwy podsieci** i **10.1.0.0/24** **zakres adresów podsieci**.

8. Wybierz pozycję **Dodaj**.

9. Wybierz kartę **zabezpieczenia** lub wybierz przycisk **Dalej: zabezpieczenia** w dolnej części strony.

10. W obszarze **BastionHost** wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |

11. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

12. Wybierz przycisk **Utwórz**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM1** |
    | Region (Region) | Wybierz **(US) Wschodnie stany USA** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów ruchu przychodzącego** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Podsieć | Wybierz pozycję **mySubnet** |
    | Publiczny adres IP | Nie zaznaczaj **niczego** |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **podstawowa**|
    | Sieć publicznych portów przychodzących | Wybierz pozycję **Brak**. |
   
5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM2** |
    | Region (Region) | Wybierz **(US) Wschodnie stany USA** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów ruchu przychodzącego** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Podsieć | Wybierz pozycję **mySubnet** |
    | Publiczny adres IP | Nie zaznaczaj **niczego** |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **podstawowa**|
    | Sieć publicznych portów przychodzących | Wybierz pozycję **Brak**. |
   
5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="connect-to-myvm1"></a>Nawiązywanie połączenia z usługą myVM1

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać PRYWATNą maszyną wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Wybierz nazwę prywatnej maszyny wirtualnej **myVM1**.

3. Na pasku menu maszyny wirtualnej wybierz pozycję **Połącz**, a następnie wybierz pozycję **bastionu**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Łączenie się z usługą myVM1 za pomocą usługi Azure bastionu" border="true":::

4. Na stronie **Połącz** wybierz przycisk **bastionu Użyj** niebieska.

5. Na stronie **bastionu** wprowadź wcześniej nazwę użytkownika i hasło, które zostały utworzone dla maszyny wirtualnej.

6. Wybierz pozycję **Połącz**.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. W bastionu połączenie **myVM1** Otwórz program PowerShell.

2. Wprowadź `ping myvm2`.

    Zostanie wyświetlony komunikat podobny do tego wyjściowego:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Zamknij połączenie bastionu z **myVM1**.

4. Wykonaj kroki opisane w temacie [Connect to myVM1](#connect-to-myvm1), a następnie połącz się z **myVM2**.

5. Otwórz program PowerShell w systemie **myVM2**, wpisz polecenie `ping myvm1` .

    Zostanie wyświetlony komunikat podobny do tego:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Zamknij połączenie bastionu z **myVM2**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym przewodniku Szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. 

Nawiązano połączenie z jedną maszyną wirtualną z Internetu i bezpiecznie komunikuje się między tymi dwiema maszynami wirtualnymi.

Gdy skończysz korzystać z sieci wirtualnej i maszyn wirtualnych, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wyszukaj i wybierz pozycję Moja **resourceName**.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę **myResourceGroup**, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat ustawień sieci wirtualnej, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](manage-virtual-network.md).

Aby dowiedzieć się więcej o typach komunikacji sieciowej maszyny wirtualnej, zobacz [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md).

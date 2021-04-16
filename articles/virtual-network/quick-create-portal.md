---
title: 'Szybki start: tworzenie sieci wirtualnej — Azure Portal'
titleSuffix: Azure Virtual Network
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu Azure Portal.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531174"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu witryny Azure Portal

Z tego przewodnika Szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu Azure Portal. Wdrażasz dwie maszyny wirtualne. Następnie bezpiecznie komunikuje się między maszynami wirtualnych i łączy się z maszynami wirtualnych z Internetu. Sieć wirtualna to podstawowy blok blokowy sieci prywatnej na platformie Azure. Dzięki temu zasoby platformy Azure, takie jak maszyny wirtualne, mogą bezpiecznie komunikować się ze sobą i z Internetem.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz **pozycję Utwórz zasób** w lewym górnym rogu portalu.

2. W polu wyszukiwania wprowadź **Virtual Network**. Wybierz **Virtual Network** w wynikach wyszukiwania.

3. Na stronie **Virtual Network** wybierz pozycję **Utwórz.**

4. W **obszarze Tworzenie sieci wirtualnej** wprowadź lub wybierz te informacje na **karcie** Podstawy:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**.  </br> Wprowadź **myResourceGroup**. </br> Wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** |   |
    | Nazwa | Wprowadź nazwę **myVNet**. |
    | Region (Region) | Wybierz **pozycję (USA) Wschodnie usa.** |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Tworzenie sieci wirtualnej Azure Portal" border="true":::

5. Wybierz **kartę Adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

6. W **przestrzeni adresowej IPv4** wybierz istniejącą przestrzeń adresową i zmień ją na **10.1.0.0/16.**

7. Wybierz **pozycję + Dodaj podsieć,** a następnie wprowadź wartość **MySubnet** w polach Nazwa podsieci i **10.1.0.0/24** w polach Zakres **adresów podsieci.** 

8. Wybierz pozycję **Dodaj**.

9. Wybierz **kartę** Zabezpieczenia lub wybierz przycisk **Dalej:** Zabezpieczenia w dolnej części strony.

10. W **obszarze BastionHost** wybierz pozycję **Włącz.** Wprowadź te informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa podsieci AzureBastionSubnet | Wprowadź **wartość 10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W **nazwa**, wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |

11. Wybierz **kartę Przeglądanie + tworzenie** lub wybierz przycisk **Przejrzyj i utwórz.**

12. Wybierz przycisk **Utwórz**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W lewym górnym rogu portalu wybierz pozycję Utwórz **zasób** Obliczeniowa  >    >  **maszyna wirtualna.** 
   
2. W **obszarze Tworzenie maszyny wirtualnej** wpisz lub wybierz wartości na karcie **Podstawy:**

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz **grupę myResourceGroup** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM1** |
    | Region (Region) | Wybierz **pozycję (USA) Wschodnie usa** |
    | Opcje dostępności | Wybierz pozycję **Nadmiarowość infrastruktury nie jest wymagana** |
    | Obraz | Wybierz **pozycję Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz **pozycję Nie** |
    | Rozmiar | Wybieranie rozmiaru maszyny wirtualnej lub ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów przychodzących** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz **pozycję myVNet.** |
    | Podsieć | Wybierz pozycję **mySubnet** |
    | Publiczny adres IP | Wybierz pozycję **Brak** |
    | Sieciowa grupa zabezpieczeń kart sieciowych | Wybierz pozycję **Podstawowa**|
    | Sieć publicznych portów przychodzących | Wybierz pozycję **Brak**. |
   
5. Wybierz **kartę Przeglądanie + tworzenie** lub niebieski przycisk **Przejrzyj i** utwórz w dolnej części strony.
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

1. W lewym górnym rogu portalu wybierz pozycję Utwórz **zasób** Obliczeniowa  >    >  **maszyna wirtualna.** 
   
2. W **obszarze Tworzenie maszyny wirtualnej** wpisz lub wybierz wartości na karcie **Podstawy:**

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz **grupę myResourceGroup** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM2** |
    | Region (Region) | Wybierz **pozycję (USA) Wschodnie usa** |
    | Opcje dostępności | Wybierz pozycję **Nadmiarowość infrastruktury nie jest wymagana** |
    | Obraz | Wybierz **pozycję Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz **pozycję Nie** |
    | Rozmiar | Wybieranie rozmiaru maszyny wirtualnej lub ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów przychodzących** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz **pozycję myVNet.** |
    | Podsieć | Wybierz pozycję **mySubnet** |
    | Publiczny adres IP | Wybierz pozycję **Brak** |
    | Sieciowa grupa zabezpieczeń kart sieciowych | Wybierz pozycję **Podstawowa**|
    | Sieć publicznych portów przychodzących | Wybierz pozycję **Brak**. |
   
5. Wybierz **kartę Przeglądanie + tworzenie** lub niebieski przycisk **Przejrzyj i** utwórz w dolnej części strony.
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>Nawiązywanie połączenia z urządzeniem myVM1

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby zarządzać prywatną maszyną wirtualną. Wyszukaj i wybierz **pozycję Maszyny wirtualne.**

2. Wybierz nazwę swojej prywatnej maszyny wirtualnej **myVM1.**

3. Na pasku menu maszyny wirtualnej wybierz pozycję **Połącz,** a następnie wybierz **pozycję Bastion**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Nawiązywanie połączenia z urządzeniem myVM1 przy użyciu Azure Bastion" border="true":::

4. Na stronie **Połącz** wybierz niebieski przycisk **Użyj bastionu.**

5. Na stronie **Bastion** wprowadź nazwę użytkownika i hasło utworzone wcześniej dla maszyny wirtualnej.

6. Wybierz pozycję **Połącz**.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. W połączeniu bastionu **myVM1** otwórz program PowerShell.

2. Wprowadź `ping myvm2`.

    Zostanie wyświetlony komunikat podobny do tych danych wyjściowych:

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

3. Zamknij połączenie bastionu z **myVM1.**

4. Wykonaj kroki opisane w te tematu Connect to myVM1 (Nawiązywanie połączenia z [urządzeniem myVM1),](#connect-to-myvm1)ale nawiąz połączenie z **myVM2**.

5. Otwórz program PowerShell na **myVM2** i wprowadź `ping myvm1` .

    Otrzymasz komunikat podobny do tego:

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

7. Zamknij połączenie bastionu z **myVM2.**

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym przewodniku Szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. 

Nałączono połączenie z jedną maszyną wirtualną z Internetu i bezpiecznego komunikowania się między dwiema maszynami wirtualnmi.

Po użyciu sieci wirtualnej i maszyn wirtualnych usuń grupę zasobów i wszystkie zasoby, które zawiera:

1. Wyszukaj i wybierz **grupę myResourceGroup.**

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę **myResourceGroup**, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat ustawień sieci wirtualnej, [zobacz Create, change, or delete a virtual network (Tworzenie, zmienianie lub usuwanie sieci wirtualnej).](manage-virtual-network.md)

Aby dowiedzieć się więcej o typach komunikacji sieciowej maszyny wirtualnej, zobacz [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md).

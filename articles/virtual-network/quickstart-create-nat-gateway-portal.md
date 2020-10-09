---
title: 'Samouczek: Tworzenie bramy translatora adresów sieciowych — Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: W tym przewodniku szybki start pokazano, jak utworzyć bramę NAT przy użyciu Azure Portal
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 5010dc08b695a0376ace5dde935c63caf0c39633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711412"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Samouczek: Tworzenie bramy NAT przy użyciu Azure Portal

W tym samouczku przedstawiono sposób korzystania z usługi Azure Virtual Network translatora adresów sieciowych. Utworzysz bramę translatora adresów sieciowych, aby zapewnić łączność wychodzącą dla maszyny wirtualnej na platformie Azure. 

Jeśli wolisz, możesz wykonać te kroki przy użyciu [interfejsu wiersza polecenia platformy Azure](quickstart-create-nat-gateway-cli.md), [Azure PowerShell](quickstart-create-nat-gateway-powershell.md)lub wdrożyć [szablon ARM](quickstart-create-nat-gateway-powershell.md) zamiast portalu.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

Przed wdrożeniem maszyny wirtualnej i użyciem bramy NAT należy utworzyć grupę zasobów i sieć wirtualną.

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Wschodnie stany USA 2      |
| **\<IPv4-address-space>**   | 192.168.0.0 \ 16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Tworzenie maszyny wirtualnej do korzystania z bramy translatora adresów sieciowych

Teraz utworzymy maszynę wirtualną do korzystania z usługi translatora adresów sieciowych. Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, który umożliwia dostęp do maszyny wirtualnej. Usługa translatora adresów sieciowych obsługuje kierunek przepływu i zastępuje domyślne miejsce docelowe w podsieci. Publiczny adres IP maszyny wirtualnej nie będzie używany dla połączeń wychodzących.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **Ubuntu Server 18,04 LTS**lub Wyszukaj **Ubuntu Server 18,04 LTS** w obszarze wyszukiwania w portalu Marketplace.

2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **myResourceGroupNAT**.
   - **Szczegóły wystąpienia**  >  **Nazwa maszyny wirtualnej**: wpisz **myVM**.
   - **Szczegóły wystąpienia**  >  **Region** > wybierz pozycję **Wschodnie stany USA 2**.
   - **Konto administratora**  >  **Typ uwierzytelniania**: wybierz opcję **hasło**.
   - **Konto administratora** > wprowadź **nazwę użytkownika**, **hasło**i potwierdź informacje o **haśle** .
   - Reguły portów ruchu **przychodzącego**  >  **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - Reguły portów ruchu **przychodzącego**  >  **Wybierz porty wejściowe**: wybierz pozycję **SSH (22)**
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.

3. Na karcie **Sieć** upewnij się, że wybrano następujące elementy:
   - **Sieć wirtualna**: **myVnet**
   - **Subnet**: Moja **podsieć**
   - > **publicznego adresu IP** wybierz pozycję **Utwórz nowy**.  W oknie **Tworzenie publicznego adresu IP** wpisz **MyPublicIPVM** w polu **Nazwa** , a następnie wybierz pozycję **standardowa** dla **jednostki SKU**.  Kliknij przycisk **OK**.
   - **Grupa zabezpieczeń sieci karty sieciowej**: wybierz pozycję **podstawowa**.
   - **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty**.
   - **Wybierz porty wejściowe**: Upewnij się, że wybrano protokół **SSH** .

4. Na karcie **Zarządzanie** w obszarze **monitorowanie**ustaw opcję **Diagnostyka rozruchu** na **wyłączona**.

5. Wybierz pozycję **Przeglądanie + tworzenie**. 

6. Przejrzyj ustawienia i kliknij przycisk **Utwórz**.

## <a name="create-the-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

Można użyć co najmniej jednego publicznego zasobu adresów IP, publicznych prefiksów IP lub obu tych zasobów. Dodamy zasób publicznego adresu IP, prefiks publicznego adresu IP i zasób bramy NAT.

W tej sekcji szczegółowo opisano, jak utworzyć i skonfigurować następujące składniki usługi NAT przy użyciu zasobu bramy translatora adresów sieciowych:
  - Publiczna Pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy translatora adresów sieciowych.
  - Zmień limit czasu bezczynności z wartości domyślnej wynoszącej 4 minuty na 10 minut.

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **publiczny adres IP**lub Wyszukaj **publiczny adres IP** w obszarze wyszukiwania w portalu Marketplace.

2. W obszarze **Utwórz publiczny adres IP**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Wersja protokołu IP | Wybierz pozycję **IPv4**.
    | SKU | Wybierz pozycję **Standardowy**.
    | Nazwa | Wprowadź **myPublicIP**. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **myResourceGroupNAT**. |
    | Lokalizacja | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).|

3. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **publiczny prefiks adresu IP**lub Wyszukaj pozycję **publiczny adres IP** w obszarze wyszukiwania w portalu Marketplace. 

2. W obszarze **Utwórz publiczny prefiks adresu IP**wpisz lub wybierz następujące wartości z karty **podstawowe** :
   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **myResourceGroupNAT**>
   - **Szczegóły wystąpienia**  >  **Nazwa**: wpisz **myPublicIPprefix**.
   - **Szczegóły wystąpienia**  >  **Region**: wybierz pozycję **Wschodnie stany USA 2**.
   - **Szczegóły wystąpienia**  >  **Rozmiar prefiksu**: Wybierz **/31 (2 adresy)**

3. Pozostaw ustawienia domyślne i wybierz pozycję **Przegląd + Utwórz**.

4. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
   

### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy NAT

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **brama translatora adresów sieciowych**lub Wyszukaj **bramę translatora adresów sieciowych** w obszarze wyszukiwania w portalu Marketplace.

2. W obszarze **Utwórz bramę translacji adresów sieciowych (NAT)** wpisz lub wybierz następujące wartości z karty **podstawowe** :
   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **myResourceGroupNAT**.
   - **Szczegóły wystąpienia**  >  **Nazwa bramy translatora adresów sieciowych**: wpisz **myNATgateway**.
   - **Szczegóły wystąpienia**  >  **Region**: wybierz pozycję **Wschodnie stany USA 2**.
   - **Szczegóły wystąpienia**  >  **Limit czasu bezczynności (w minutach)**: typ **10**.
   - Wybierz kartę **publiczny adres IP** lub wybierz pozycję **Dalej: publiczny adres IP**.

3. Na karcie **publiczny adres IP** wpisz lub wybierz następujące wartości:
   - **Publiczne adresy IP**: wybierz pozycję **myPublicIP**.
   - **Prefiksy publicznych adresów IP**: wybierz pozycję **myPublicIPprefix**.
   - Wybierz kartę **podsieć** lub wybierz pozycję **Dalej: podsieć**.

4. Na karcie **podsieć** wpisz lub wybierz następujące wartości:
   - **Virtual Network**: wybierz pozycję **myResourceGroupNAT**  >  **myVnet**.
   - **Nazwa podsieci**: zaznacz pole wyboru obok pozycji moja **podsieć**.

5. Wybierz pozycję **Przeglądanie + tworzenie**.

6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="discover-the-ip-address-of-the-vm"></a>Odnajdywanie adresu IP maszyny wirtualnej

1. Po lewej stronie portalu wybierz pozycję **grupy zasobów**.
2. Wybierz pozycję **myResourceGroupNAT**.
3. Wybierz pozycję **myVM**.
4. W obszarze **Przegląd**skopiuj wartość **publiczny adres IP** i wklej ją do programu Notepad, aby można było korzystać z niej w celu uzyskania dostępu do maszyny wirtualnej.

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do Notatnika, aby można było używać go do uzyskiwania dostępu do maszyny wirtualnej.

## <a name="sign-in-to-vm"></a>Logowanie do maszyny wirtualnej

Otwórz [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do połączenia SSH z maszyną wirtualną.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Teraz można przystąpić do korzystania z usługi translatora adresów sieciowych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, Brama translatora adresów sieciowych i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupNAT** , która zawiera bramę translatora adresów sieciowych, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono bramę translatora adresów sieciowych oraz maszynę wirtualną w celu jej użycia. 

Przejrzyj metryki w Azure Monitor, aby zobaczyć, jak działa usługa translatora adresów sieciowych. Diagnozuj problemy, takie jak wyczerpanie zasobów dostępnych portów.  Wyczerpanie zasobów portów źródeł adresów sieciowych polega na dodaniu dodatkowych zasobów publicznego adresu IP lub publicznych zasobów prefiksu adresów IP.


- Dowiedz się więcej o [usłudze Azure Virtual Network translator adresów sieciowych](./nat-overview.md)
- Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Przewodnik Szybki Start dotyczący wdrażania [zasobu bramy NAT przy użyciu Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]


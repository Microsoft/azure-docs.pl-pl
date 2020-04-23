---
title: 'Samouczek: Tworzenie i testowanie bramy NAT — witryna Azure portal'
titlesuffix: Azure Virtual Network NAT
description: W tym samouczku pokazano, jak utworzyć bramę NAT przy użyciu portalu Azure i przetestować usługę NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: ceadbb4297ad0c5ce28470dd75b3f3496c9c5152
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084747"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Samouczek: Tworzenie bramy NAT przy użyciu portalu Azure i testowanie usługi NAT

W tym samouczku utworzysz bramę NAT, aby zapewnić łączność wychodzącą dla maszyn wirtualnych na platformie Azure. Aby przetestować bramę NAT, należy wdrożyć maszynę wirtualną źródłową i docelową. Przetestujesz bramę NAT, nawiązując połączenia wychodzące z publicznym adresem IP ze źródła do docelowej maszyny wirtualnej.  Ten samouczek wdraża źródło i miejsce docelowe w dwóch różnych sieciach wirtualnych w tej samej grupie zasobów tylko dla uproszczenia.

Jeśli wolisz, możesz wykonać te kroki przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-create-validate-nat-gateway-cli.md) lub [programu Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md), zamiast korzystać z portalu.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Przygotowanie źródła dla ruchu wychodzącego

Poprowadzimy Cię przez konfigurację pełnego środowiska testowego i wykonanie samych testów w następnych krokach. Zaczniemy od źródła, które będzie używać zasobu bramy NAT, który tworzymy w późniejszych krokach.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

Przed wdrożeniem maszyny Wirtualnej i można użyć bramy NAT, musimy utworzyć grupę zasobów i sieci wirtualnej.

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroupNAT |
| **\<>nazwa sieci wirtualnej** | myVNetsource (źródło myVNetsource)          |
| **\<nazwa regionu>**          | Wschodnie stany USA 2      |
| **\<>przestrzeni adresowej IPv4**   | 192.168.0.0/16          |
| **\<>nazwa podsieci**          | mySubnetsource        |
| **\<>zakresu adresu podsieci** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Tworzenie źródłowa maszyna wirtualna

Teraz utworzymy maszynę wirtualną do korzystania z usługi NAT. Ta maszyna wirtualna ma publiczny adres IP do użycia jako publiczny adres IP na poziomie wystąpienia, aby umożliwić dostęp do maszyny Wirtualnej. Usługa NAT jest świadoma kierunku przepływu i zastąpi domyślne miejsce docelowe Internetu w podsieci. Publiczny adres IP maszyny Wirtualnej nie będzie używany dla połączeń wychodzących.

Aby przetestować bramę NAT, przypiszemy publiczny zasób adresu IP jako publiczny adres IP na poziomie wystąpienia, aby uzyskać dostęp do tej maszyny wirtualnej z zewnątrz. Ten adres jest używany tylko do uzyskania do niego dostęp do testu.  Zademonstrujemy, jak usługa NAT ma pierwszeństwo przed innymi opcjami wychodzącymi.

Można również utworzyć tę maszynę wirtualną bez publicznego adresu IP i utworzyć inną maszynę wirtualną do użycia jako jumpbox bez publicznego adresu IP jako ćwiczenia.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **Compute** > **Ubuntu Server 18.04 LTS**lub wyszukaj w wyszukiwarce Marketplace serwer **Ubuntu Server 18.04 LTS.**

2. W **obszarze Tworzenie maszyny wirtualnej**wprowadź lub wybierz następujące wartości na karcie **Podstawy:**
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupNAT**.
   - **Szczegóły** > wystąpienia**Nazwa maszyny wirtualnej:** wprowadź **myVMsource**.
   - **Region szczegółów** > **Region** wystąpienia > wybierz **pozycję Wschodnia us 2**.
   - **Dane** > **uwierzytelniające**konta administratora wprowadź : Wybierz **hasło**.
   - **Konto administratora** > Wprowadź **nazwę użytkownika,** **hasło**i pozycję Potwierdź informacje o **haśle.**
   - **Reguły portów** > **przychodzących Publiczne porty przychodzące**: Wybierz **opcję Zezwalaj na wybrane porty**.
   - **Reguły portów** > **przychodzących Wybierz porty przychodzące:** Wybierz **SSH (22)**
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.

3. Na karcie **Sieć** upewnij się, że zaznaczono następujące elementy:
   - **Sieć wirtualna**: **myVnetsource**
   - **Podsieć**: **mySubnetsource**
   - **Publiczne** > IP Wybierz pozycję **Utwórz nowy**.  W oknie **Tworzenie publicznego adresu IP** wprowadź **myPublicIPsourceVM** w polu **Nazwa.** Wybierz **standard** dla **jednostki SKU**. Pozostaw resztę na wartości domyślnej i kliknij **przycisk OK**.
   - **Grupa zabezpieczeń sieciowej karty sieciowej**: Wybierz pozycję **Podstawowa**.
   - **Publiczne porty przychodzące**: Wybierz **opcję Zezwalaj na wybrane porty**.
   - **Wybierz porty przychodzące**: Upewnij się, że wybrano **SSH.**

4. Na karcie **Zarządzanie** w obszarze **Monitorowanie**ustaw **diagnostykę rozruchu** na **Wyłącz**.

5. Wybierz pozycję **Przegląd + utwórz**.

6. Przejrzyj ustawienia i kliknij przycisk **Utwórz**.

## <a name="create-the-nat-gateway"></a>Tworzenie bramy NAT

Można użyć co najmniej jednego publicznego adresu IP, publicznych prefiksów IP lub obu z bramą NAT. Dodamy publiczny zasób IP, publiczny prefiks IP i zasób bramy NAT.

W tej sekcji opisano, jak można tworzyć i konfigurować następujące składniki usługi NAT przy użyciu zasobu bramy NAT:
  - Publiczna pula adresów IP i publiczny prefiks IP do użycia dla przepływów wychodzących przetłumaczonych przez zasób bramy NAT.
  - Zmień limit czasu bezczynności z domyślnego 4 minut na 10 minut.

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz** > **publiczny adres IP****sieciowy** > lub wyszukaj **publiczny adres IP** w wyszukiwarce Marketplace. 

2. W **obszarze Tworzenie publicznego adresu IP**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Wersja IP | Wybierz pozycję **IPv4**.
    | SKU | Wybierz **opcję Standardowy**.
    | Nazwa | Wprowadź **myPublicIPsource**. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz **myResourceGroupNAT**. |
    | Lokalizacja | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).|

3. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-public-ip-prefix"></a>Tworzenie publicznego prefiksu IP

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz** > **prefiks publiczny adres IP****sieciowej** > lub wyszukaj **prefiks publiczny adres IP** w wyszukiwarce Marketplace.

2. W **obszarze Tworzenie publicznego prefiksu IP**wprowadź lub wybierz następujące wartości na karcie **Podstawy:**
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupNAT**>
   - **Szczegóły** > wystąpienia**Nazwa**: enter **myPublicIPprefixsource**.
   - **Szczegóły** > wystąpienia**Region:** Wybierz **wschodnie stany USA 2**.
   - **Szczegóły wystąpienia** > **Rozmiar prefiksu**: Wybierz **/31 (2 adresy)**

3. Pozostaw resztę wartości domyślnych i wybierz **pozycję Przejrzyj + utwórz**.

4. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.


### <a name="create-a-nat-gateway-resource"></a>Tworzenie zasobu bramy TRANSLATORA

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz bramę** > **nat****sieciową** > lub wyszukaj **bramę NAT** w wyszukiwarce Marketplace.

2. W **obszarze Tworzenie bramy translacji adresów sieciowych (NAT)** wprowadź lub wybierz następujące wartości na karcie **Podstawy:**
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupNAT**.
   - **Szczegóły** > wystąpienia**NAT nazwa bramy:** wprowadź **myNATgateway**.
   - **Szczegóły** > wystąpienia**Region:** Wybierz **wschodnie stany USA 2**.
   - **Szczegóły wystąpienia****Limit czasu bezczynności (minuty)**: wprowadź **10**. > 
   - Wybierz kartę **Publiczny adres IP** lub wybierz pozycję **Dalej: Publiczny adres IP**.

3. Na karcie **Publiczny adres IP** wprowadź lub wybierz następujące wartości:
   - **Publiczne adresy IP:** Wybierz **myPublicIPsource**.
   - **Publiczne prefiksy IP:** Wybierz **myPublicIPprefixsource**.
   - Wybierz kartę **Podsieć** lub wybierz pozycję **Dalej: Podsieć**.

4. Na karcie **Podsieć** wprowadź lub wybierz następujące wartości:
   - **Sieć wirtualna**: Wybierz **myResourceGroupNAT** > **myVnetsource**.
   - **Nazwa podsieci**: Zaznacz pole obok **mySubnetsource**.

5. Wybierz pozycję **Przegląd + utwórz**.

6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

Cały ruch wychodzący do miejsc docelowych w Internecie jest teraz korzystający z usługi NAT.  Nie jest konieczne skonfigurowanie UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Przygotowanie miejsca docelowego dla ruchu wychodzącego

Teraz utworzymy miejsce docelowe dla ruchu wychodzącego przetłumaczonego przez usługę NAT, aby umożliwić jego przetestowanie.


## <a name="virtual-network-and-parameters-for-destination"></a>Sieć wirtualna i parametry miejsca docelowego

Przed wdrożeniem maszyny Wirtualnej dla miejsca docelowego, musimy utworzyć sieć wirtualną, w której może przebywać docelowa maszyna wirtualna. Poniżej przedstawiono te same kroki, jak w przypadku źródłowej maszyny Wirtualnej z kilkoma małymi zmianami, aby udostępnić docelowy punkt końcowy.

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroupNAT |
| **\<>nazwa sieci wirtualnej** | myVNetdestination          |
| **\<nazwa regionu>**          | Wschodnie stany USA 2      |
| **\<>przestrzeni adresowej IPv4**   | 192.168.0.0/16          |
| **\<>nazwa podsieci**          | mySubnetdestination        |
| **\<>zakresu adresu podsieci** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Tworzenie docelowej maszyny wirtualnej

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **Compute** > **Ubuntu Server 18.04 LTS**lub wyszukaj w wyszukiwarce Marketplace serwer **Ubuntu Server 18.04 LTS.**

2. W **obszarze Tworzenie maszyny wirtualnej**wprowadź lub wybierz następujące wartości na karcie **Podstawy:**
   - **Grupa** > **zasobów**subskrypcji: Wybierz **myResourceGroupNAT**.
   - **Szczegóły** > wystąpienia**Nazwa maszyny wirtualnej:** wprowadź **myVMdestination**.
   - **Region szczegółów** > **Region** wystąpienia > wybierz **pozycję Wschodnia us 2**.
   - **Dane** > **uwierzytelniające**konta administratora wprowadź : Wybierz **hasło**.
   - **Konto administratora** > Wprowadź **nazwę użytkownika,** **hasło**i pozycję Potwierdź informacje o **haśle.**
   - **Reguły portów** > **przychodzących Publiczne porty przychodzące**: Wybierz **opcję Zezwalaj na wybrane porty**.
   - **Reguły portów** > **przychodzących Wybierz porty przychodzące:** Wybierz **SSH (22)** i **HTTP (80)**.
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.

3. Na karcie **Sieć** upewnij się, że zaznaczono następujące elementy:
   - **Sieć wirtualna**: **myVnetdestination**
   - **Podsieć**: **mySubnetdestination**
   - **Publiczne** > IP Wybierz pozycję **Utwórz nowy**.  W oknie **Tworzenie publicznego adresu IP** wprowadź program **myPublicIPdestinationVM** w polu **Nazwa.** Wybierz **standard** dla **jednostki SKU**. Pozostaw resztę na wartości domyślnej i kliknij **przycisk OK**.
   - **Grupa zabezpieczeń sieciowej karty sieciowej**: Wybierz pozycję **Podstawowa**.
   - **Publiczne porty przychodzące**: Wybierz **opcję Zezwalaj na wybrane porty**.
   - **Wybierz porty przychodzące:** Wybrano opcję **Potwierdź,** że są zaznaczone protokoły SSH i **HTTP.**

4. Na karcie **Zarządzanie** w obszarze **Monitorowanie**ustaw **diagnostykę rozruchu** na **Wyłącz**.

5. Wybierz pozycję **Przegląd + utwórz**.

6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Przygotowanie serwera sieci web i przetestowanie ładunku na docelowej maszynie wirtualnej

Najpierw musimy odnajdować adres IP docelowej maszyny Wirtualnej. 

1. Po lewej stronie portalu wybierz pozycję **Grupy zasobów**.
2. Wybierz **myResourceGroupNAT**.
3. Wybierz **myVMdestination**.
4. W **przeglądzie**skopiuj wartość **publicznego adresu IP** i wklej do notatnika, aby można było jej użyć do uzyskania dostępu do maszyny Wirtualnej.

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć w kolejnych krokach. Wskazać, że jest to docelowa maszyna wirtualna.

### <a name="sign-in-to-destination-vm"></a>Logowanie się do docelowej maszyny Wirtualnej

Otwórz usługę [Azure Cloud Shell](https://shell.azure.com) w przeglądarce. Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Po zalogowaniu się skopiuj i wklej następujące polecenia.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Te polecenia zaktualizują maszynę wirtualną, zainstalują nginx i utworzą plik o rozmiarze 100 KB. Ten plik zostanie pobrany ze źródłowej maszyny Wirtualnej przy użyciu usługi NAT.

Zamknij sesję SSH z docelową maszyną wirtualną.

## <a name="prepare-test-on-source-vm"></a>Przygotowanie testu na źródłowej maszynie wirtualnej

Najpierw musimy odnajdować adres IP źródłowej maszyny Wirtualnej.

1. Po lewej stronie portalu wybierz pozycję **Grupy zasobów**.
2. Wybierz **myResourceGroupNAT**.
3. Wybierz **myVMsource**.
4. W **przeglądzie**skopiuj wartość **publicznego adresu IP** i wklej do notatnika, aby można było jej użyć do uzyskania dostępu do maszyny Wirtualnej.

>[!IMPORTANT]
>Skopiuj publiczny adres IP, a następnie wklej go do notatnika, aby można było go użyć w kolejnych krokach. Wskazać, że jest to źródło maszyny wirtualnej.

### <a name="log-into-source-vm"></a>Zaloguj się do źródłowego vm

Otwórz nową kartę usługi [Azure Cloud Shell](https://shell.azure.com) w przeglądarce.  Użyj adresu IP pobranego w poprzednim kroku do protokołu SSH na maszynie wirtualnej. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Skopiuj i wklej następujące polecenia, aby przygotować się do testowania usługi NAT.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

To polecenie zaktualizuje maszynę wirtualną, zainstaluje go, [zainstaluje hej](https://github.com/rakyll/hey) z GitHub i zaktualizuje środowisko powłoki.

Teraz możesz przystąpić do testowania usługi NAT.

## <a name="validate-nat-service"></a>Sprawdzanie poprawności usługi NAT

Po zalogowaniu się do źródłowej maszyny Wirtualnej można użyć **curl** i hej do **generowania** żądań do docelowego adresu IP.

Użyj curl, aby pobrać plik 100-KB.  Zastąp ** \<>docelowy adresu IP** w poniższym przykładzie docelowym adresem IP skopiowanym wcześniej.  **Parametr --output** wskazuje, że pobrany plik zostanie odrzucony.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Można również wygenerować serię żądań przy użyciu **hej**. Ponownie zastąp ** \<>docelowy adresu IP** na wcześniej skopiowany adres IP.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

To polecenie wygeneruje 100 żądań, 10 jednocześnie, z limitem czasu 30 sekund i bez ponownego użycia połączenia TCP.  Każde żądanie zostanie pobrane 100 Kbyt.  Pod koniec biegu **hej** zgłosi niektóre statystyki dotyczące tego, jak dobrze zrobiła to usługa NAT.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebna, usuń grupę zasobów, bramę NAT i wszystkie powiązane zasoby. Wybierz grupę zasobów **myResourceGroupNAT** zawierającą bramę NAT, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono bramę NAT, utworzono maszynę wirtualną źródłową i docelową, a następnie przetestowano bramę NAT.

Przejrzyj metryki w usłudze Azure Monitor, aby wyświetlić działanie usługi NAT. Diagnozowanie problemów, takich jak wyczerpanie zasobów dostępnych portów SNAT.  Wyczerpanie zasobów portów SNAT można łatwo rozwiązać, dodając dodatkowe zasoby publicznego adresu IP lub publiczne zasoby prefiksów IP lub oba te zasoby.

- Dowiedz się więcej o [nat sieci wirtualnej](./nat-overview.md)
- Dowiedz się więcej o [zasobie bramy NAT](./nat-gateway-resource.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu programu Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Szybki start do wdrażania [zasobu bramy NAT przy użyciu portalu Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]


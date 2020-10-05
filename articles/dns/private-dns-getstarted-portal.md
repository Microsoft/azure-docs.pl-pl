---
title: Szybki Start — tworzenie prywatnej strefy DNS platformy Azure przy użyciu Azure Portal
description: W tym przewodniku szybki start utworzysz i testujesz prywatną strefę DNS i rekord w Azure DNS. Jest to przewodnik krok po kroku dotyczący tworzenia pierwszej prywatnej strefy i rekordu DNS przy użyciu Azure Portal i zarządzania nią.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: rohink
ms.openlocfilehash: 52bf9e061eb57c7ce6ea698b7468b5ba5e11b4e8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78244976"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Szybki Start: Tworzenie prywatnej strefy DNS platformy Azure przy użyciu Azure Portal

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki tworzenia pierwszej prywatnej strefy i rekordu DNS przy użyciu Azure Portal.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, należy określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są one nazywane *połączonymi* sieciami wirtualnymi. Gdy Autorejestracja jest włączona, Azure DNS aktualizuje także rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia jej adres IP lub został usunięty.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie prywatnej strefy DNS
> * Tworzenie sieci wirtualnej
> * Łączenie sieci wirtualnej
> * Tworzenie testowych maszyn wirtualnych
> * Tworzenie dodatkowego rekordu DNS
> * Testowanie strefy prywatnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Jeśli wolisz, możesz ukończyć ten przewodnik Szybki Start przy użyciu [Azure PowerShell](private-dns-getstarted-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Tworzenie prywatnej strefy DNS

Poniższy przykład tworzy strefę DNS o nazwie **Private.contoso.com** w grupie zasobów o nazwie **MyAzureResourceGroup**.

Strefa DNS zawiera wpisy DNS dla domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, należy utworzyć strefę DNS dla tej nazwy domeny.

![Wyszukiwanie stref Prywatna strefa DNS](media/private-dns-portal/search-private-dns.png)

1. Na pasku wyszukiwania portalu wpisz **prywatne strefy DNS** w polu tekstowym Wyszukaj, a następnie naciśnij klawisz **Enter**.
1. Wybierz pozycję **prywatna strefa DNS strefa**.
2. Wybierz pozycję **Utwórz prywatną strefę DNS**.

1. Na stronie **Tworzenie strefy prywatna strefa DNS** wpisz lub wybierz następujące wartości:

   - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wpisz *MyAzureResourceGroup*, a następnie wybierz **przycisk OK**. Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure.
   -  **Name**: wpisz *Private.contoso.com* w tym przykładzie.
1. W obszarze **Lokalizacja grupy zasobów**wybierz pozycję **zachodnio-środkowe stany USA**.

1. Wybierz pozycję **Recenzja + Utwórz**.

1. Wybierz przycisk **Utwórz**.

Tworzenie strefy może potrwać kilka minut.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (wybierz istniejącą grupę zasobów) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | Zachodnio-środkowe stany USA      |
| **\<IPv4-address-space>**   | 10.2.0.0 \ 16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0 \ 24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Łączenie sieci wirtualnej

Aby połączyć prywatną strefę DNS z siecią wirtualną, należy utworzyć link sieci wirtualnej.

![Dodaj łącze sieci wirtualnej](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otwórz grupę zasobów **MyAzureResourceGroup** i wybierz strefę prywatną **Private.contoso.com** .
2. W okienku po lewej stronie wybierz pozycję **linki sieci wirtualnej**.
3. Wybierz pozycję **Dodaj**.
4. Wpisz **link** do **nazwy linku**.
5. W obszarze **Sieć wirtualna**wybierz pozycję **myAzureVNet**.
6. Zaznacz pole wyboru **Włącz rejestrację autorejestrowania** .
7. Wybierz przycisk **OK**.

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

1. Na stronie portalu w lewym górnym rogu wybierz pozycję **Utwórz zasób**, a następnie wybierz pozycję **Windows Server 2016 centrum**danych.
1. Wybierz pozycję **MyAzureResourceGroup** dla grupy zasobów.
1. Wpisz **myVM01** — jako nazwę maszyny wirtualnej.
1. Wybierz pozycję **zachodnie stany USA** dla **regionu**.
1. Wprowadź nazwę dla nazwy użytkownika administratora.
2. Wprowadź hasło i Potwierdź hasło.
5. W przypadku **publicznych portów ruchu przychodzącego**wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz pozycję **RDP (3389)** w obszarze **Wybieranie portów przychodzących**.
10. Zaakceptuj inne wartości domyślne dla strony, a następnie kliknij przycisk **Dalej: dyski >**.
11. Zaakceptuj wartości domyślne na stronie **dyski** , a następnie kliknij przycisk **dalej: sieć >**.
1. Upewnij się, że wybrano **myAzureVNet** dla sieci wirtualnej.
1. Zaakceptuj inne wartości domyślne dla strony, a następnie kliknij przycisk **Dalej: zarządzanie >**.
2. W obszarze **Diagnostyka rozruchu**wybierz pozycję **wyłączone**, zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przegląd + Utwórz**.
1. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.

Powtórz te kroki i Utwórz inną maszynę wirtualną o nazwie **myVM02**.

Ukończenie obu maszyn wirtualnych może potrwać kilka minut.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

 Poniższy przykład tworzy rekord z względną nazwą **bazy danych** w strefie DNS **Private.contoso.com**w grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów to **DB.private.contoso.com**. Typ rekordu to "A" z adresem IP **myVM01**.

1. Otwórz grupę zasobów **MyAzureResourceGroup** i wybierz strefę prywatną **Private.contoso.com** .
2. Wybierz pozycję **+ Zestaw rekordów**.
3. W obszarze **Nazwa**wpisz **DB**.
4. W polu **adres IP**wpisz adres IP, który będzie widoczny dla **myVM01**. Ta wartość powinna być rejestrowana po uruchomieniu maszyny wirtualnej.
5. Wybierz przycisk **OK**.

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz można testować rozpoznawanie nazw dla strefy prywatnej **Private.contoso.com** .

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurowanie maszyn wirtualnych w celu zezwolenia na ruch przychodzący protokołu ICMP

Do przetestowania rozpoznawania nazw możesz użyć polecenia ping. W tym celu skonfiguruj zaporę na obydwu maszynach wirtualnych, aby zezwolić na przychodzące pakiety protokołu ICMP.

1. Połącz się z maszyną wirtualną myVM01 i otwórz okno programu Windows PowerShell, używając uprawnień administratora.
2. Uruchom następujące polecenie:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Powtórz dla maszyny wirtualnej myVM02.

### <a name="ping-the-vms-by-name"></a>Wysyłanie polecenia ping do maszyn wirtualnych według nazwy

1. W wierszu polecenia programu Windows PowerShell maszyny wirtualnej myVM02 wyślij polecenie ping do maszyny wirtualnej myVM01 przy użyciu automatycznie zarejestrowanej nazwy hosta:
   ```
   ping myVM01.private.contoso.com
   ```
   Wyświetlone dane wyjściowe powinny wyglądać mniej więcej tak:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Teraz wyślij polecenie ping do utworzonej wcześniej nazwy **db**:
   ```
   ping db.private.contoso.com
   ```
   Wyświetlone dane wyjściowe powinny wyglądać mniej więcej tak:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Usuwanie wszystkich zasobów

Gdy nie jest już potrzebne, Usuń grupę zasobów **MyAzureResourceGroup** , aby usunąć zasoby utworzone w tym przewodniku Szybki Start.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Scenariusze Azure DNS Private Zones](private-dns-scenarios.md)


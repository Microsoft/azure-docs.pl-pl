---
title: Szybki start — tworzenie prywatnej strefy DNS platformy Azure przy użyciu Azure Portal
description: W tym przewodniku Szybki start utworzysz i przetestujemy prywatną strefę i rekord DNS w Azure DNS. Ten przewodnik krok po kroku zawiera instrukcje tworzenia pierwszej prywatnej strefy i rekordu DNS oraz zarządzania nimi przy użyciu Azure Portal.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: 2115062db8615ca782cb987903e5ebfc83400cd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535442"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Szybki start: tworzenie prywatnej strefy DNS platformy Azure przy użyciu Azure Portal

W tym przewodniku Szybki start otworzymy pierwszą prywatną strefę i rekord DNS przy użyciu Azure Portal.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, musisz utworzyć strefę DNS dla tej nazwy domeny. Każdy rekord DNS domeny zostanie utworzony w tej strefie DNS. Aby opublikować prywatną strefę DNS w sieci wirtualnej, należy określić listę sieci wirtualnych, które mogą rozpoznawać rekordy w strefie.  Są one nazywane *połączonymi sieciami* wirtualnymi. Po włączeniu autorejestracji program Azure DNS także aktualizuje rekordy strefy za każdym razem, gdy maszyna wirtualna jest tworzona, zmienia jej adres IP lub jest usuwana.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.

Jeśli wolisz, możesz ukończyć ten przewodnik Szybki start przy [użyciu](private-dns-getstarted-powershell.md) Azure PowerShell wiersza polecenia [platformy Azure.](private-dns-getstarted-cli.md)

## <a name="create-a-private-dns-zone"></a>Tworzenie prywatnej strefy DNS

Poniższy przykład tworzy strefę DNS o **nazwie private.contoso.com** w grupie zasobów o nazwie **MyAzureResourceGroup.**

Strefa DNS zawiera wpisy DNS dla domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, należy utworzyć strefę DNS dla tej nazwy domeny.

![Prywatna strefa DNS wyszukiwania stref](media/private-dns-portal/search-private-dns.png)

1. Na pasku wyszukiwania portalu wpisz **prywatne strefy DNS** w polu tekstowym wyszukiwania i naciśnij klawisz **Enter**.
1. Wybierz **Prywatna strefa DNS strefę .**
2. Wybierz **pozycję Utwórz prywatną strefę DNS.**

1. Na **stronie Prywatna strefa DNS strefy czasowej** wpisz lub wybierz następujące wartości:

   - **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź *nazwę MyAzureResourceGroup* i wybierz przycisk **OK.** Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure.
   -  **Nazwa:** *wpisz private.contoso.com* w tym przykładzie.
1. W **pozycji Lokalizacja grupy zasobów** wybierz pozycję **Zachodnio-środkowe stany USA.**

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. Wybierz przycisk **Utwórz**.

Tworzenie strefy może potrwać kilka minut.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (Wybierz istniejącą grupę zasobów) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | Zachodnio-środkowe stany USA      |
| **\<IPv4-address-space>**   | 10.2.0.0\16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Łączenie sieci wirtualnej

Aby połączyć prywatną strefę DNS z siecią wirtualną, należy utworzyć link sieci wirtualnej.

![Dodawanie linku sieci wirtualnej](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Otwórz **grupę zasobów MyAzureResourceGroup** i wybierz **private.contoso.com** strefę prywatną.
2. W okienku po lewej stronie wybierz pozycję **Linki sieci wirtualnej.**
3. Wybierz pozycję **Dodaj**.
4. Wpisz **myLink** w nazwie **linku**.
5. W **przypadku opcji Sieć wirtualna** wybierz pozycję **myAzureVNet.**
6. Zaznacz pole **wyboru Włącz automatyczną** rejestrację.
7. Wybierz przycisk **OK**.

## <a name="create-the-test-virtual-machines"></a>Tworzenie testowych maszyn wirtualnych

Teraz utworzysz dwie maszyny wirtualne, aby umożliwić przetestowanie strefy prywatnej DNS:

1. Na stronie portalu w lewym górnym rogu wybierz pozycję **Utwórz zasób, a** następnie wybierz pozycję **Windows Server 2016 Datacenter.**
1. Wybierz **grupę zasobów MyAzureResourceGroup.**
1. Wpisz **myVM01** — jako nazwę maszyny wirtualnej.
1. Wybierz **region Zachodnio-środkowe stany USA.** 
1. Wprowadź nazwę użytkownika administratora.
2. Wprowadź hasło i potwierdź je.
5. W **przypadku opcji Publiczne porty przychodzące** wybierz pozycję Zezwalaj **na** wybrane porty, a następnie wybierz pozycję **RDP (3389)** w opcji **Wybierz porty przychodzące.**
10. Zaakceptuj inne wartości domyślne dla strony, a następnie kliknij przycisk **Dalej: Dyski >**.
11. Zaakceptuj wartości domyślne na stronie **Dyski,** a następnie kliknij przycisk **Dalej: Sieć >**.
1. Upewnij się, że dla sieci wirtualnej wybrano sieć **myAzureVNet.**
1. Zaakceptuj inne wartości domyślne dla strony, a następnie kliknij przycisk **Dalej: Zarządzanie >**.
2. W **przypadku diagnostyki** rozruchu wybierz **pozycję Wył.,** zaakceptuj inne wartości domyślne, a następnie wybierz pozycję Przejrzyj **i utwórz.**
1. Przejrzyj ustawienia, a następnie kliknij pozycję **Utwórz.**

Powtórz te kroki i utwórz inną maszynę wirtualną **o nazwie myVM02.**

Ukończenie obu maszyn wirtualnych potrwa kilka minut.

## <a name="create-an-additional-dns-record"></a>Tworzenie dodatkowego rekordu DNS

 Poniższy przykład tworzy rekord o nazwie względnej **db** w strefie DNS private.contoso.com **w** grupie zasobów **MyAzureResourceGroup**. W pełni kwalifikowana nazwa zestawu rekordów to **db.private.contoso.com**. Typ rekordu to "A" z adresem IP **myVM01.**

1. Otwórz **grupę zasobów MyAzureResourceGroup** i wybierz **private.contoso.com** strefę prywatną.
2. Wybierz pozycję **+ Zestaw rekordów**.
3. W **nazwa**, typ **db**.
4. W **przypadku adresu IP** wpisz wyświetlony adres IP dla **myVM01.** Powinno to zostać automatycznie zarejestrowane podczas pracy maszyny wirtualnej.
5. Wybierz przycisk **OK**.

## <a name="test-the-private-zone"></a>Testowanie strefy prywatnej

Teraz możesz przetestować rozdzielczość nazw dla swojej **private.contoso.com** prywatnej.

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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów **MyAzureResourceGroup** nie będzie już potrzebna, usuń ją, aby usunąć zasoby utworzone w tym przewodniku Szybki start.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Scenariusze usługi Azure DNS Private Zones](private-dns-scenarios.md)

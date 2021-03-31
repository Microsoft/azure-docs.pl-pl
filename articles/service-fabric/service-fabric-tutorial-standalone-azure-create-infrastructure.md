---
title: Tworzenie infrastruktury dla klastra na maszynach wirtualnych platformy Azure
description: W tym samouczku dowiesz się, jak skonfigurować infrastrukturę maszyny wirtualnej platformy Azure do uruchamiania klastra Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: e9f62f944fff331bcf2dad1b380161e563614219
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90561844"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Samouczek: tworzenie infrastruktury maszyny wirtualnej platformy Azure na potrzeby hostowania klastra Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz klaster autonomiczny hostowany na maszynach wirtualnych platformy Azure i zainstaluje na nim aplikację.

Niniejszy samouczek jest pierwszą częścią serii. W tym artykule opisano Generowanie zasobów maszyn wirtualnych platformy Azure wymaganych do hostowania autonomicznego klastra Service Fabric. W następnych artykułach opisano sposób instalowania autonomicznego zestawu usługi Service Fabric, instalowania w klastrze przykładowej aplikacji i na koniec czyszczenia klastra.

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu wystąpień AzureVM
> * Modyfikowanie grupy zabezpieczeń
> * Logowanie się do jednego z wystąpień
> * Przygotowywanie wystąpienia na potrzeby usługi Service Fabric

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure.  Jeśli nie masz jeszcze konta, przejdź do [Azure Portal](https://portal.azure.com) , aby go utworzyć.

## <a name="create-azure-virtual-machine-instances"></a>Tworzenie wystąpień maszyn wirtualnych platformy Azure

1. Zaloguj się do Azure Portal i wybierz pozycję **maszyny wirtualne** (nie Virtual Machines (klasyczne)).

   ![Azure Portal maszynę wirtualną][az-console]

2. Wybierz przycisk **Dodaj** , co spowoduje otwarcie formularza **Utwórz maszynę wirtualną** .

3. Na karcie **podstawy** Pamiętaj, aby wybrać żądaną subskrypcję i grupę zasobów (zaleca się użycie nowej grupy zasobów).

4. Zmień typ **obrazu** na **Windows Server 2016 Datacenter**. 
 
5. Zmień **rozmiar** wystąpienia na **Standardowy DS2 v2**. Ustaw **nazwę użytkownika** i **hasło** administratora, zwracając uwagę na to, co się stało.

6. Pozostaw teraz zablokowane **reguły portów dla ruchu przychodzącego** . Skonfigurujemy te zasady w następnej sekcji.

7. Na karcie **Sieć** utwórz nową **Virtual Network** i zanotuj jej nazwę.

8. Następnie ustaw wartość Advanced **Network Security Group** (karta sieciowa). Utwórz nową grupę zabezpieczeń, zwracając nazwę i Utwórz następujące reguły, aby zezwolić na ruch TCP z dowolnego źródła:

   ![Zrzut ekranu przedstawia tworzenie reguł zezwalających na ruch przychodzący TCP.][sf-inbound]

   * Port `3389` , protokół RDP i ICMP (łączność podstawowa).
   * Porty `19000-19003` dla Service Fabric.
   * Porty `19080-19081` dla Service Fabric.
   * Port `8080` dla żądań przeglądarki sieci Web.

   > [!TIP]
   > Aby połączyć maszyny wirtualne z usługą Service Fabric, maszyny wirtualne, które hostują infrastrukturę, muszą mieć takie same poświadczenia.  Istnieją dwa podstawowe sposoby na uzyskanie spójnych poświadczeń: dołączenie wszystkich hostów do tej samej domeny lub ustawienie takiego samego hasła administratora na każdej maszynie wirtualnej. Na szczęście platforma Azure umożliwia łatwe łączenie się z wszystkimi maszynami wirtualnymi w tej samej **sieci wirtualnej** , dlatego będziemy mieć wszystkie nasze wystąpienia w tej samej sieci.

9. Dodaj kolejną regułę. Ustaw tag źródło jako **Usługa** i ustaw tag usługi źródłowej na **VirtualNetwork**. Service Fabric wymaga otwarcia następujących portów do komunikacji w klastrze: 135137-139, 445, 20001-20031, 20606-20861.

   ![Zrzut ekranu przedstawia tworzenie reguł zezwalających na ruch TCP dla klastra.][vnet-inbound]

10. Pozostałe opcje są akceptowane w stanie domyślnym. Przejrzyj je, jeśli chcesz, a następnie uruchom maszynę wirtualną.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Tworzenie większej liczby wystąpień dla klastra Service Fabric

Uruchom jeszcze dwie **Virtual Machines**, pamiętaj, aby zachować te same ustawienia, które opisano w poprzedniej sekcji. W szczególności należy zachować tę samą nazwę użytkownika i hasło administratora. Nie należy ponownie tworzyć **sieciowej grupy zabezpieczeń** **Virtual Network** i karty sieciowej. Wybierz utworzone wcześniej z menu rozwijanego. Wdrożenie poszczególnych wystąpień może potrwać kilka minut.

## <a name="connect-to-your-instances"></a>Nawiązywanie połączenia z wystąpieniami

1. Wybierz jedno z wystąpień z sekcji **maszyna wirtualna** .

2. Na karcie **Omówienie** Zanotuj *prywatny* adres IP. Następnie kliknij przycisk **Połącz**.

3. Na karcie **RDP** Zwróć uwagę na to, że korzystamy z publicznego adresu IP i portu 3389, który został wcześniej otwarty. Pobierz plik RDP.
 
4. Otwórz plik RDP i po wyświetleniu monitu wprowadź nazwę użytkownika i hasło podane w konfiguracji maszyny wirtualnej.

5. Po nawiązaniu połączenia z wystąpieniem należy sprawdzić, czy Rejestr zdalny był uruchomiony, włączyć protokół SMB i otworzyć wymagane porty dla protokołu SMB i rejestru zdalnego.

   Aby włączyć protokół SMB, jest to polecenie programu PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. W celu otwarcia portów w zaporze użyto tego polecenia programu PowerShell:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Powtórz ten proces dla innych wystąpień i ponownie Zanotuj prywatne adresy IP.

## <a name="verify-your-settings"></a>Weryfikowanie ustawień

1. Aby sprawdzić poprawność łączności podstawowej, nawiąż połączenie z jedną z maszyn wirtualnych przy użyciu protokołu RDP.

2. Otwórz **wiersz polecenia** z tej maszyny wirtualnej, a następnie użyj polecenia ping, aby nawiązać połączenie z jednej maszyny wirtualnej do innej, zastępując poniższy adres IP jednym z ponotowanych wcześniej prywatnych adresów IP (nie jest to adres IP maszyny wirtualnej, z którą nawiązano połączenie).

   ```
   ping 172.31.20.163
   ```

   Jeśli w Twoich danych wyjściowych cztery razy pojawia się komunikat podobny do tego: `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, oznacza to, że połączenie między wystąpieniami działa.

3. Teraz użyj następującego polecenia, aby zweryfikować, że udostępnianie za pomocą protokołu SMB działa:

   ```
   net use * \\172.31.20.163\c$
   ```

   Powinien zostać wyświetlony komunikat podobny do następującego: `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Teraz Twoje wystąpienia są prawidłowo przygotowane do Service Fabric.

## <a name="next-steps"></a>Następne kroki

W pierwszej części serii pokazano, jak uruchomić trzy wystąpienia maszyn wirtualnych platformy Azure i skonfigurować je na potrzeby instalacji Service Fabric:

> [!div class="checklist"]
> * Tworzenie zestawu wystąpień maszyn wirtualnych platformy Azure
> * Modyfikowanie grupy zabezpieczeń
> * Logowanie się do jednego z wystąpień
> * Przygotowywanie wystąpienia na potrzeby usługi Service Fabric

Przejdź do części drugiej tej serii, aby skonfigurować usługę Service Fabric w klastrze.

> [!div class="nextstepaction"]
> [Zainstaluj usługę Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png

---
title: 'Łączenie klasycznych sieci wirtualnych do Azure Resource Manager sieci wirtualnych: Portal | Microsoft Docs'
description: Procedura łączenia klasycznej sieci wirtualnych w celu Menedżer zasobów sieci wirtualnych przy użyciu VPN Gateway i portalu
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 9d31bcaad01b9b762e57bd619d45c1f53ffb201e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376806"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Łączenie sieci wirtualnych z różnych modeli wdrażania przy użyciu portalu

W tym artykule pokazano, jak połączyć klasyczne sieci wirtualnych z programem Menedżer zasobów sieci wirtualnych, aby umożliwić zasobom znajdującym się w osobnych modelach wdrażania komunikację ze sobą. Kroki opisane w tym artykule wykorzystują głównie Azure Portal, ale można również utworzyć tę konfigurację przy użyciu programu PowerShell, wybierając artykuł z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Połączenie klasycznej sieci wirtualnej z siecią wirtualną Menedżer zasobów jest podobne do łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenie między sieci wirtualnychami, które znajdują się w różnych subskrypcjach i w różnych regionach. Można także połączyć sieci wirtualnych, które mają już połączenia z sieciami lokalnymi, tak długo, jak Brama, z której zostały skonfigurowane, jest dynamiczna lub oparta na trasach. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz jeszcze bramy sieci wirtualnej i nie chcesz jej utworzyć, możesz zamiast tego rozważyć łączenie się z usługą sieci wirtualnych przy użyciu wirtualnych sieci równorzędnych. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem



* W tych krokach przyjęto założenie, że oba sieci wirtualnych zostały już utworzone. Jeśli używasz tego artykułu jako ćwiczenia i nie masz usługi sieci wirtualnych, w tych krokach znajdują się linki ułatwiające ich tworzenie.
* Sprawdź, czy zakresy adresów dla sieci wirtualnych nie nakładają się na siebie ani nie nakładają się na żadne z zakresów dla innych połączeń, z którymi bramy mogą być połączone.
* Zainstaluj najnowsze polecenia cmdlet programu PowerShell dla programu Menedżer zasobów i zarządzania usługami (klasyczne). W tym artykule używamy obu Azure Portal i programu PowerShell. Program PowerShell jest wymagany do utworzenia połączenia z klasycznej sieci wirtualnej do Menedżer zasobów sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/). 

### <a name="example-settings"></a><a name="values"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Klasyczna Sieć wirtualna**

Nazwa sieci wirtualnej = ClassicVNet <br>
Przestrzeń adresowa = 10.0.0.0/24 <br>
Nazwa podsieci = Subnet-1 <br>
Zakres adresów podsieci = 10.0.0.0/27 <br>
Subskrypcja = subskrypcja, której chcesz użyć <br>
Grupa zasobów = ClassicRG <br>
Lokalizacja = zachodnie stany USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokacja lokalna = RMVNetLocal <br>

**Menedżer zasobów sieci wirtualnej**

Nazwa sieci wirtualnej = RMVNet <br>
Przestrzeń adresowa = 192.168.0.0/16 <br>
Grupa zasobów = RG1 <br>
Lokalizacja = Wschodnie stany USA <br>
Nazwa podsieci = Subnet-1 <br>
Zakres adresów = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nazwa bramy sieci wirtualnej = RMGateway <br>
Typ bramy = VPN <br>
Typ sieci VPN = oparta na trasach <br>
Jednostka SKU = VpnGw1 <br>
Lokalizacja = Wschodnie stany USA <br>
Virtual Network = RMVNet <br> (Skojarz bramę sieci VPN z tą siecią wirtualną) Konfiguracja pierwszego adresu IP = rmgwpip <br> (publiczny adres IP bramy) Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa połączenia = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Przegląd połączenia

W przypadku tej konfiguracji można utworzyć połączenie bramy sieci VPN za pośrednictwem tunelu sieci VPN protokołu IPsec/IKE między sieciami wirtualnymi. Upewnij się, że żaden z zakresów sieci wirtualnej nie nakłada się na siebie lub z dowolnymi z sieci lokalnych, z którymi się łączą.

W poniższej tabeli przedstawiono przykład definiowania przykładowych sieci wirtualnych i lokacji lokalnych:

| Virtual Network | Przestrzeń adresowa | Region (Region) | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Sekcja 1 — Konfigurowanie ustawień klasycznej sieci wirtualnej

W tej sekcji utworzysz klasyczną sieć wirtualną (lokację lokalną) i bramę sieci wirtualnej. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj, aby zastąpić wartości własnymi lub użyć [przykładowych](#values) wartości.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a> Tworzenie klasycznej sieci wirtualnej

Jeśli nie masz klasycznej sieci wirtualnej i uruchamiasz te kroki jako ćwiczenie, możesz utworzyć sieć wirtualną za pomocą [tego artykułu](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) oraz [przykładowych](#values) wartości ustawień z powyższych.

Jeśli masz już sieć wirtualną z bramą sieci VPN, sprawdź, czy brama jest dynamiczna. Jeśli jest ona statyczna, należy najpierw usunąć bramę sieci VPN przed kontynuowaniem [konfigurowania lokacji lokalnej](#local).

1. Otwórz witrynę [Azure Portal](https://ms.portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **+ Utwórz zasób** , aby otworzyć stronę "nowy".
3. W polu "Wyszukaj witrynę Marketplace" wpisz "Virtual Network". W takim przypadku wybierz pozycję Sieć — Virtual Network > nie będziesz mieć możliwości utworzenia klasycznej sieci wirtualnej.
4. Znajdź element "Virtual Network" na liście zwracanej i kliknij go, aby otworzyć stronę Virtual Network. 
5. Na stronie Sieć wirtualna wybierz opcję "klasyczny", aby utworzyć klasyczną sieć wirtualną. Jeśli w tym miejscu zostanie wprowadzona wartość domyślna, w zamian zostanie wystawiona Menedżer zasobów Sieć wirtualna.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a> Skonfiguruj lokację lokalną

1. Przejdź do **wszystkich zasobów** i Znajdź na liście **ClassicVNet** .
2. Kliknij pozycję **brama** w sekcji **Ustawienia** w menu, a następnie kliknij transparent, aby utworzyć bramę.
  ![Konfigurowanie bramy VPN Gateway](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurowanie bramy VPN Gateway")
3. Na stronie **nowe połączenie sieci VPN** w **polu Typ połączenia** wybierz pozycję **lokacja-lokacja**.
4. W obszarze **lokacja lokalna** kliknij pozycję **Skonfiguruj wymagane ustawienia**. Spowoduje to otwarcie strony **lokacja lokalna** .
5. Na stronie **lokacja lokalna** Utwórz nazwę, aby odwołać się do Menedżer zasobów sieci wirtualnej. Na przykład "RMVNetLocal".
6. Jeśli Brama sieci VPN dla Menedżer zasobów wirtualnej ma już publiczny adres IP, użyj wartości pola **adres IP bramy sieci VPN** . Jeśli wykonujesz te kroki jako ćwiczenie lub nie masz jeszcze bramy sieci wirtualnej dla sieci wirtualnej Menedżer zasobów, możesz utworzyć symbol zastępczy adresu IP. Upewnij się, że symbol zastępczy adresu IP ma prawidłowy format. Później zastępczy adres IP jest zastępowany publicznym adresem IP bramy sieci wirtualnej Menedżer zasobów.
7. W polu **przestrzeń adresowa klienta** Użyj [wartości](#connectoverview) przestrzeni adresów IP sieci wirtualnej dla Menedżer zasobów wirtualnej. To ustawienie służy do określania przestrzeni adresowych do skierowania do Menedżer zasobów sieci wirtualnej. W tym przykładzie używamy 192.168.0.0/16 zakresu adresów dla RMVNet.
8. Kliknij przycisk **OK** , aby zapisać wartości i powrócić do strony **nowe połączenie sieci VPN** .

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Utwórz bramę sieci wirtualnej

1. Na stronie **nowe połączenie sieci VPN** zaznacz pole wyboru **Utwórz bramę natychmiast** .
2. Kliknij pozycję **Opcjonalna konfiguracja bramy**, aby otworzyć stronę **Konfiguracja bramy**.

   ![Otwórz stronę konfiguracji bramy](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Otwórz stronę konfiguracji bramy")
3. Kliknij pozycję **podsieć — skonfiguruj wymagane ustawienia** , aby otworzyć stronę **Dodaj podsieć** . **Nazwa** jest już skonfigurowana przy użyciu wymaganej wartości: **GatewaySubnet**.
4. **Zakres adresów** odnosi się do zakresu podsieci bramy. Chociaż można utworzyć podsieć bramy z zakresem adresów/29 (3 adresy), zalecamy utworzenie podsieci bramy zawierającej więcej adresów IP. Będzie to dotyczyło przyszłych konfiguracji, które mogą wymagać większej liczby dostępnych adresów IP. Jeśli to możliwe, użyj/27 lub/28. Jeśli używasz tych kroków jako ćwiczenia, możesz odwołać się do [przykładowych wartości](#values). W tym przykładzie używamy "10.0.0.32/28". Kliknij przycisk **OK** , aby utworzyć podsieć bramy.
5. Na stronie **Konfiguracja bramy** **rozmiar** dotyczy jednostki SKU bramy. Wybierz jednostkę SKU bramy dla bramy sieci VPN.
6. Sprawdź, czy **Typ routingu** to **dynamiczny**, a następnie kliknij przycisk **OK** , aby powrócić do strony **nowe połączenie sieci VPN** .
7. Na stronie **nowe połączenie VPN** kliknij przycisk **OK** , aby rozpocząć tworzenie bramy sieci VPN. Tworzenie bramy sieci VPN może potrwać do 45 minut.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Skopiuj publiczny adres IP bramy sieci wirtualnej

Po utworzeniu bramy sieci wirtualnej można wyświetlić adres IP bramy. 

1. Przejdź do klasycznej sieci wirtualnej, a następnie kliknij pozycję **Przegląd**.
2. Kliknij pozycję **połączenia sieci VPN** , aby otworzyć stronę połączenia sieci VPN. Na stronie połączenia sieci VPN można wyświetlić publiczny adres IP. Jest to publiczny adres IP przypisany do bramy sieci wirtualnej. Zanotuj adres IP. Należy jej użyć w późniejszych krokach podczas pracy z ustawieniami konfiguracji bramy sieci lokalnej Menedżer zasobów. 
3. Możesz wyświetlić stan połączeń bramy. Zwróć uwagę na to, że utworzona lokacja sieci lokalnej znajduje się na liście "łączenie". Stan zmieni się po utworzeniu połączeń. Po zakończeniu wyświetlania stanu można zamknąć tę stronę.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Sekcja 2 — Konfigurowanie ustawień sieci wirtualnej Menedżer zasobów

W tej sekcji utworzysz bramę sieci wirtualnej i bramę sieci lokalnej dla sieci wirtualnej Menedżer zasobów. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj, aby zastąpić wartości własnymi lub użyć [przykładowych](#values) wartości.

### <a name="1-create-a-virtual-network"></a>1. Tworzenie sieci wirtualnej

**Przykładowe wartości:**

* Nazwa sieci wirtualnej = RMVNet <br>
* Przestrzeń adresowa = 192.168.0.0/16 <br>
* Grupa zasobów = RG1 <br>
* Lokalizacja = Wschodnie stany USA <br>
* Nazwa podsieci = Subnet-1 <br>
* Zakres adresów = 192.168.1.0/24 <br>

Jeśli nie masz sieci wirtualnej Menedżer zasobów i uruchamiasz te kroki jako ćwiczenie, Utwórz sieć wirtualną z instrukcjami w temacie [Tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md)przy użyciu przykładowych wartości.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Tworzenie bramy sieci wirtualnej

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Przykładowe wartości:**

* Nazwa bramy sieci wirtualnej = RMGateway <br>
* Typ bramy = VPN <br>
* Typ sieci VPN = oparta na trasach <br>
* Jednostka SKU = VpnGw1 <br>
* Lokalizacja = Wschodnie stany USA <br>
* Virtual Network = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Konfiguracja pierwszego adresu IP = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Tworzenie bramy sieci lokalnej

**Przykładowe wartości:** Brama sieci lokalnej = ClassicVNetLocal

| Virtual Network | Przestrzeń adresowa | Region (Region) | Nawiązuje połączenie z lokacją sieci lokalnej |Publiczny adres IP bramy|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |Publiczny adres IP przypisany do bramy ClassicVNet|
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |Publiczny adres IP przypisany do bramy RMVNet.|

Brama sieci lokalnej określa zakres adresów i publiczny adres IP skojarzony z klasyczną siecią wirtualną i bramą sieci wirtualnej. Jeśli wykonujesz te kroki jako ćwiczenie, zapoznaj się z przykładowymi wartościami.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Sekcja 3 — Modyfikowanie ustawień klasycznej lokacji lokalnej sieci wirtualnej

W tej sekcji zastąpisz symbol zastępczy adresu IP, który był używany podczas określania ustawień lokacji lokalnej, przy użyciu adresu IP bramy sieci VPN Menedżer zasobów. W tej sekcji są stosowane klasyczne (SM) polecenia cmdlet programu PowerShell.

1. W Azure Portal przejdź do klasycznej sieci wirtualnej.
2. Na stronie sieci wirtualnej kliknij pozycję **Przegląd**.
3. W sekcji **połączenia sieci VPN** kliknij nazwę lokalnej witryny w grafice.

   ![VPN — połączenia](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Połączenia VPN")
4. Na stronie **połączenia sieci VPN typu lokacja-lokacja** kliknij nazwę lokacji.

   ![Nazwa lokacji](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nazwa lokacji lokalnej")
5. Na stronie połączenie dla lokacji lokalnej kliknij nazwę lokacji lokalnej, aby otworzyć stronę **lokacja lokalna** .

   ![Lokacja otwarta lokalnie](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Otwórz lokację lokalną")
6. Na stronie **lokacja lokalna** Zastąp **adres IP bramy sieci VPN** adresem IP bramy Menedżer zasobów.

   ![Gateway-IP-Address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Adres IP bramy")
7. Kliknij przycisk **OK** , aby zaktualizować adres IP.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Sekcja 4 — Tworzenie klasycznego połączenia Menedżer zasobów

W tych krokach skonfigurujesz połączenie z Menedżer zasobów sieci wirtualnej do klasycznej sieci wirtualnej przy użyciu Azure Portal.

1. W obszarze **wszystkie zasoby** Znajdź bramę sieci lokalnej. W naszym przykładzie Brama sieci lokalnej to **ClassicVNetLocal**.
2. Kliknij pozycję **Konfiguracja** i sprawdź, czy wartość adresu IP jest bramą VPN dla klasycznej sieci wirtualnej. W razie konieczności zaktualizuj, a następnie kliknij przycisk **Zapisz**. Zamknij stronę.
3. W obszarze **wszystkie zasoby** kliknij pozycję Brama sieci lokalnej.
4. Kliknij pozycję **połączenia** , aby otworzyć stronę połączenia.
5. Na stronie **połączenia** kliknij, **+** Aby dodać połączenie.
6. Na stronie **Dodawanie połączenia** Nadaj nazwę połączenie. Na przykład "RMtoClassic".
7. Na tej stronie została już wybrana **lokacja-lokacja** .
8. Wybierz bramę sieci wirtualnej, która ma zostać skojarzona z tą lokacją.
9. Utwórz **klucz współużytkowany**. Ten klucz jest również używany w połączeniu, które tworzysz z klasycznej sieci wirtualnej do Menedżer zasobów sieci wirtualnej. Możesz wygenerować klucz lub utworzyć go. W naszym przykładzie używamy elementu "abc123", ale można używać bardziej złożonego elementu (i powinien).
10. Kliknij przycisk **OK** , aby utworzyć połączenie.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Sekcja 5 — Tworzenie klasycznego połączenia Menedżer zasobów

W tych krokach skonfigurujesz połączenie z klasycznej sieci wirtualnej do Menedżer zasobów sieci wirtualnej. Te kroki wymagają programu PowerShell. Nie można utworzyć tego połączenia w portalu. Upewnij się, że pobrano i zainstalowano Oba polecenia cmdlet programu PowerShell: klasyczne (SM) i Menedżer zasobów (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Nawiąż połączenie z kontem platformy Azure

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i zaloguj się na koncie platformy Azure. Po zalogowaniu się zostaną pobrane ustawienia konta, aby były dostępne do Azure PowerShell. Poniższe polecenie cmdlet poprosi o poświadczenia logowania dla konta platformy Azure dla Menedżer zasobów modelu wdrażania:

```powershell
Connect-AzAccount
```

Pobierz listę subskrypcji platformy Azure.

```powershell
Get-AzSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Następnie zaloguj się, aby użyć klasycznych poleceń cmdlet programu PowerShell (Service Management). Użyj następującego polecenia, aby dodać konto platformy Azure dla klasycznego modelu wdrażania:

```powershell
Add-AzureAccount
```

Pobierz listę subskrypcji. Ten krok może być konieczny podczas dodawania poleceń cmdlet zarządzania usługami, w zależności od instalacji modułu platformy Azure.

```powershell
Get-AzureSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Wyświetl wartości plików konfiguracji sieciowej

Podczas tworzenia sieci wirtualnej w Azure Portal pełna nazwa użyta przez platformę Azure nie jest widoczna w Azure Portal. Na przykład Sieć wirtualna o nazwie "ClassicVNet" w Azure Portal może mieć znacznie dłuższą nazwę w pliku konfiguracyjnym sieci. Nazwa może wyglądać następująco: "Group ClassicRG ClassicVNet". W tych krokach pobierzesz plik konfiguracji sieciowej i zobaczysz wartości.

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik z edytorem tekstu i Wyświetl nazwę klasycznej sieci wirtualnej. Podczas uruchamiania poleceń cmdlet programu PowerShell Użyj nazw w pliku konfiguracji sieci.

- Nazwy sieci wirtualnej są wyświetlane jako **VirtualNetworkSite nazwa =**
- Nazwy lokacji są wyświetlane jako **Nazwa LocalNetworkSite =**

### <a name="3-create-the-connection"></a>3. Utwórz połączenie

Ustaw klucz współużytkowany i Utwórz połączenie z klasycznej sieci wirtualnej do sieci wirtualnej Menedżer zasobów. Nie można ustawić klucza współużytkowanego przy użyciu portalu. Upewnij się, że uruchamiasz te kroki podczas logowania przy użyciu klasycznej wersji poleceń cmdlet programu PowerShell. Aby to zrobić, użyj polecenie **Add-AzureAccount**. W przeciwnym razie nie będzie można ustawić opcji "-AzureVNetGatewayKey".

- W tym przykładzie **-VNetName** jest nazwą klasycznej sieci wirtualnej, która została znaleziona w pliku konfiguracji sieciowej. 
- **-LocalNetworkSiteName** to nazwa określona dla lokacji lokalnej, która została znaleziona w pliku konfiguracyjnym sieci.
- **-SharedKey** jest wartością wygenerowaną i określoną przez użytkownika. W tym przykładzie użyto *abc123*, ale można wygenerować coś bardziej złożonego. Ważną kwestią jest to, że wartość określona w tym miejscu musi być taka sama, jak w przypadku tworzenia Menedżer zasobów do klasycznego połączenia.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Sekcja 6 — Weryfikowanie połączeń

Połączenia można zweryfikować przy użyciu Azure Portal lub programu PowerShell. Podczas sprawdzania, może być konieczne odczekanie minuty lub dwa po utworzeniu połączenia. Po pomyślnym nawiązaniu połączenia stan łączności zostanie zmieniony z "łączenie" na "Połączono".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby sprawdzić połączenie z klasycznej sieci wirtualnej z siecią wirtualną Menedżer zasobów

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby sprawdzić połączenie z sieci wirtualnej Menedżer zasobów do klasycznej sieci wirtualnej

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

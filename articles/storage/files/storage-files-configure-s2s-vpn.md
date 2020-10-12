---
title: Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z Azure Files | Microsoft Docs
description: Jak skonfigurować sieć VPN typu lokacja-lokacja (S2S) do użytku z usługą Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4d1d0f9e2a86da8213a9662b68c791a117dcc7fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515333"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurowanie sieci VPN typu lokacja-lokacja do użycia z usługą Azure Files
Za pomocą połączenia sieci VPN typu lokacja-lokacja (S2S) można instalować udziały plików platformy Azure za pośrednictwem protokołu SMB z sieci lokalnej bez konieczności otwierania portu 445. Sieć VPN typu lokacja-lokacja można skonfigurować przy użyciu usługi [azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md), która jest zasobem platformy Azure oferującym usług sieci VPN, i jest wdrażana w grupie zasobów obok kont magazynu lub innych zasobów platformy Azure.

![Wykres topologii przedstawiający topologię bramy sieci VPN platformy Azure łączącej udział plików platformy Azure z lokacją lokalną przy użyciu sieci VPN S2S](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Zdecydowanie zalecamy zapoznanie się z [omówieniem Azure Files sieci](storage-files-networking-overview.md) przed kontynuowaniem pracy z tym artykułem, aby uzyskać pełną dyskusję na temat opcji sieciowych dostępnych dla Azure Files.

W tym artykule szczegółowo opisano kroki konfigurowania sieci VPN typu lokacja-lokacja w celu zainstalowania udziałów plików platformy Azure bezpośrednio w środowisku lokalnym. Jeśli chcesz routować ruch związany z synchronizacją dla Azure File Sync za pośrednictwem sieci VPN typu lokacja-lokacja, zobacz [konfigurowanie Azure File Sync serwera proxy i ustawień zapory](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Wymagania wstępne
- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzanymi, które reprezentują udostępnioną pulę magazynów, w której można wdrożyć wiele udziałów plików, a także inne zasoby magazynu, takie jak kontenery obiektów blob lub kolejki. Więcej informacji na temat wdrażania udziałów plików platformy Azure i kont magazynu można znaleźć w temacie [Tworzenie udziału plików platformy Azure](storage-how-to-create-file-share.md).

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który ma zostać zainstalowany lokalnie. Aby dowiedzieć się więcej na temat tworzenia prywatnego punktu końcowego, zobacz [konfigurowanie Azure Files punktów końcowych sieci](storage-files-networking-endpoints.md?tabs=azure-portal). 

- Urządzenie sieciowe lub serwer w lokalnym centrum danych, który jest zgodny z usługą Azure VPN Gateway. Azure Files jest niezależny od wybranego urządzenia sieciowego, ale usługa Azure VPN Gateway zachowuje [listę przetestowanych urządzeń](../../vpn-gateway/vpn-gateway-about-vpn-devices.md). Różne urządzenia sieciowe oferują różne funkcje, cechy wydajności i funkcje zarządzania, dlatego należy wziąć pod uwagę te kwestie podczas wybierania urządzenia sieciowego.

    Jeśli nie masz istniejącego urządzenia sieciowego, system Windows Server zawiera wbudowaną rolę serwera, usługę Routing i dostęp zdalny (RRAS), która może być używana jako urządzenie sieci lokalnej. Aby dowiedzieć się więcej o konfigurowaniu usługi Routing i dostęp zdalny w systemie Windows Server, zobacz [brama ras](https://docs.microsoft.com/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Dodawanie konta magazynu do sieci wirtualnej
W Azure Portal przejdź do konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. W spisie treści konta magazynu wybierz pozycję **zapory i sieci wirtualne** . Jeśli podczas tworzenia nie dodano sieci wirtualnej do konta magazynu, w okienku wyników powinien znajdować się przycisk Zezwalaj na **dostęp z poziomu** przycisku radiowego dla **wszystkich wybranych sieci** .

Aby dodać konto magazynu do odpowiedniej sieci wirtualnej, wybierz opcję **wybrane sieci**. W obszarze podnagłówki **sieci wirtualnych** kliknij opcję **+ Dodaj istniejącą sieć wirtualną** lub **Dodaj nową sieć wirtualną** w zależności od żądanego stanu. Utworzenie nowej sieci wirtualnej spowoduje utworzenie nowego zasobu platformy Azure. Nowy lub istniejący zasób sieci wirtualnej nie musi znajdować się w tej samej grupie zasobów lub subskrypcji co konto magazynu, jednak musi znajdować się w tym samym regionie co konto magazynu, a grupa zasobów i subskrypcja, w której wdrażana jest sieć wirtualna, muszą być zgodne z tą, do której zostanie wdrożony VPN Gateway. 

![Zrzut ekranu przedstawiający Azure Portal umożliwiający dodanie istniejącej lub nowej sieci wirtualnej do konta magazynu](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Po dodaniu istniejącej sieci wirtualnej zostanie wyświetlony monit o wybranie co najmniej jednej podsieci tej sieci wirtualnej, do której należy dodać konto magazynu. W przypadku wybrania nowej sieci wirtualnej należy utworzyć podsieć w ramach tworzenia sieci wirtualnej. możesz później dodać ją za pomocą uzyskanego zasobu platformy Azure dla sieci wirtualnej.

Jeśli konto magazynu nie zostało wcześniej dodane do subskrypcji, należy dodać punkt końcowy usługi Microsoft. Storage do sieci wirtualnej. Może to potrwać trochę czasu i do momentu ukończenia tej operacji nie będzie można uzyskać dostępu do udziałów plików platformy Azure w ramach tego konta magazynu, w tym za pośrednictwem połączenia sieci VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Wdrażanie VPN Gateway platformy Azure
W spisie treści Azure Portal wybierz pozycję **Utwórz nowy zasób** i Wyszukaj *bramę sieci wirtualnej*. Brama sieci wirtualnej musi znajdować się w tej samej subskrypcji, regionie platformy Azure i grupie zasobów co sieć wirtualna wdrożona w poprzednim kroku (należy zauważyć, że grupa zasobów jest automatycznie wybierana podczas wybierania sieci wirtualnej). 

Na potrzeby wdrażania VPN Gateway platformy Azure należy wypełnić następujące pola:

- **Nazwa**: nazwa zasobu platformy Azure dla VPN Gateway. Ta nazwa może być dowolną nazwą, która jest przydatna dla Twojego zarządzania.
- **Region**: region, w którym zostanie wdrożona VPN Gateway.
- **Typ bramy**: na potrzeby wdrożenia sieci VPN typu lokacja-lokacja należy wybrać **Sieć VPN**.
- **Typ sieci VPN**: możesz wybrać opcję opartą na *trasach** lub **oparta na zasadach** w zależności od urządzenia sieci VPN. Sieci VPN oparte na trasach obsługują protokół IKEv2, natomiast sieci VPN oparte na zasadach obsługują tylko protokół IKEv1. Aby dowiedzieć się więcej o dwóch typach bram sieci VPN, zobacz [Informacje o bramach sieci VPN opartych na zasadach i trasie](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about)
- **Jednostka SKU**: jednostka SKU kontroluje liczbę dozwolonych tuneli między lokacjami i wymaganą wydajność sieci VPN. Aby wybrać odpowiednią jednostkę SKU dla przypadku użycia, zapoznaj się z listą [jednostek SKU bramy](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) . Jednostkę SKU VPN Gateway można zmienić w późniejszym czasie w razie potrzeby.
- **Sieć wirtualna**: Sieć wirtualna utworzona w poprzednim kroku.
- **Publiczny adres IP**: adres IP VPN Gateway, który zostanie uwidoczniony w Internecie. Prawdopodobnie konieczne będzie utworzenie nowego adresu IP, ale w razie potrzeby można również użyć istniejącego nieużywanego adresu IP. Jeśli wybierzesz opcję **utworzenia nowego**, nowy adres IP platformy Azure zostanie utworzony w tej samej grupie zasobów co VPN Gateway a  **publiczny adres IP** będzie nazwą nowo utworzonego adresu IP. W przypadku wybrania opcji **Użyj istniejącej**należy wybrać istniejący nieużywany adres IP.
- **Włącz tryb aktywny-aktywny**: zaznacz opcję **włączone** tylko wtedy, gdy tworzysz konfigurację bramy Active-Active. w przeciwnym razie pozostaw **wyłączone** zaznaczenie. Aby dowiedzieć się więcej o trybie aktywny-aktywny, zobacz [wiele lokalizacji i połączenia między sieciami wirtualnymi o wysokiej](../../vpn-gateway/vpn-gateway-highlyavailable.md)dostępności.
- **Skonfiguruj protokół BGP ASN**: wybierz opcję **włączone** tylko wtedy, gdy konfiguracja wymaga tego ustawienia. Aby dowiedzieć się więcej na temat tego ustawienia, zobacz [Informacje o protokole BGP z platformą Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).

Wybierz pozycję **Recenzja + Utwórz** , aby utworzyć VPN Gateway. Aby w pełni utworzyć i wdrożyć VPN Gateway, może upłynąć do 45 minut.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Tworzenie bramy sieci lokalnej dla bramy lokalnej 
Brama sieci lokalnej jest zasobem platformy Azure, który reprezentuje lokalne urządzenie sieciowe. W spisie treści Azure Portal wybierz pozycję **Utwórz nowy zasób** i Wyszukaj *bramę sieci lokalnej*. Brama sieci lokalnej to zasób platformy Azure, który zostanie wdrożony wraz z kontem magazynu, siecią wirtualną i VPN Gateway, ale nie musi znajdować się w tej samej grupie zasobów lub subskrypcji, co konto magazynu. 

W celu wdrożenia zasobu bramy sieci lokalnej należy wypełnić następujące pola:

- **Name**: nazwa zasobu platformy Azure dla bramy sieci lokalnej. Ta nazwa może być dowolną nazwą, która jest przydatna dla Twojego zarządzania.
- **Adres IP**: publiczny adres IP lokalnej bramy lokalnego.
- **Przestrzeń adresowa**: zakresy adresów dla sieci reprezentowane przez tę bramę sieci lokalnej. Można dodać wiele zakresów przestrzeni adresów, ale upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi chcesz nawiązać połączenie. 
- **Skonfiguruj ustawienia protokołu BGP**: Skonfiguruj ustawienia protokołu BGP tylko wtedy, gdy konfiguracja wymaga tego ustawienia. Aby dowiedzieć się więcej na temat tego ustawienia, zobacz [Informacje o protokole BGP z platformą Azure VPN Gateway](../../vpn-gateway/vpn-gateway-bgp-overview.md).
- **Subskrypcja**: żądana subskrypcja. Nie musi to być zgodne z subskrypcją używaną dla VPN Gateway lub konta magazynu.
- **Grupa zasobów**: żądana Grupa zasobów. Ta wartość nie musi być zgodna z grupą zasobów używaną dla VPN Gateway lub konta magazynu.
- **Lokalizacja**: region platformy Azure, w którym należy utworzyć zasób bramy sieci lokalnej. Ta wartość powinna być zgodna z regionem wybranym dla VPN Gateway i konta magazynu.

Wybierz pozycję **Utwórz** , aby utworzyć zasób bramy sieci lokalnej.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurowanie lokalnego urządzenia sieciowego
Konkretne kroki konfigurowania lokalnego urządzenia sieciowego zależą od wybranego przez organizację urządzenia sieciowego. W zależności od urządzenia, które zostało wybrane przez organizację, [Lista przetestowanych urządzeń](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) może mieć link do instrukcji dostawcy urządzenia na potrzeby konfigurowania za pomocą usługi Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Utwórz połączenie lokacja-lokacja
Aby ukończyć wdrażanie sieci VPN S2S, należy utworzyć połączenie między lokalnym urządzeniem sieciowym (reprezentowane przez zasób bramy sieci lokalnej) i VPN Gateway. W tym celu przejdź do utworzonego wcześniej VPN Gateway. W spisie treści VPN Gateway wybierz pozycję **połączenia**, a następnie kliknij przycisk **Dodaj**. W wyniku tego okienko **dodawania połączenia** wymagane są następujące pola:

- **Nazwa**: nazwa połączenia. VPN Gateway może obsługiwać wiele połączeń, dlatego należy wybrać nazwę przydatną dla zarządzania, która odróżni się od tego konkretnego połączenia.
- **Typ połączenia**: ponieważ to połączenie S2S, na liście rozwijanej wybierz pozycję **lokacja-lokacja (IPSec)** .
- **Brama sieci wirtualnej**: to pole jest wybierane do VPN Gateway, z którym nawiązywane jest połączenie, i nie można go zmienić.
- **Brama sieci lokalnej**: jest to Brama sieci lokalnej, która ma zostać podłączona do VPN Gateway. W wynikającym okienku wyboru powinna być nazwa bramy sieci lokalnej utworzonej powyżej.
- **Klucz współużytkowany (PSK)**: kombinacja liter i cyfr używana do nawiązywania szyfrowania dla połączenia. Ten sam klucz współużytkowany musi być używany zarówno w sieci wirtualnej, jak i w bramie sieci lokalnej. Jeśli urządzenie bramy nie oferuje takiego urządzenia, możesz je utworzyć i udostępnić urządzeniu.

Kliknij przycisk **OK**, aby utworzyć połączenie. Możesz sprawdzić, czy połączenie zostało nawiązane pomyślnie za pomocą strony **połączenia** .

## <a name="mount-azure-file-share"></a>Zainstaluj udział plików platformy Azure 
Ostatni krok konfigurowania sieci VPN S2S polega na sprawdzeniu, czy działa on w przypadku Azure Files. Możesz to zrobić, instalując udział plików platformy Azure w środowisku lokalnym przy użyciu preferowanego systemu operacyjnego. Zapoznaj się z instrukcjami dotyczącymi instalowania według systemu operacyjnego tutaj:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Zobacz też
- [Omówienie sieci Azure Files](storage-files-networking-overview.md)
- [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Windows do użytku z usługą Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z usługą Azure Files](storage-files-configure-p2s-vpn-linux.md)
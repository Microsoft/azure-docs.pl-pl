---
title: Konfigurowanie sieci VPN typu lokacja-lokacja (S2S) do użycia z Azure Files | Microsoft Docs
description: Jak skonfigurować sieć VPN typu lokacja-lokacja (S2S) do użycia z Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a08ca4142876a5a92adbe8b1c3fce9ec7953019
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778017"
---
# <a name="configure-a-site-to-site-vpn-for-use-with-azure-files"></a>Konfigurowanie sieci VPN typu lokacja-lokacja do użycia z Azure Files
Za pomocą połączenia sieci VPN typu lokacja-lokacja (S2S) można zainstalować udziały plików platformy Azure za pośrednictwem SMB z sieci lokalnej bez otwierania portu 445. Sieć VPN typu lokacja-lokacja można skonfigurować przy użyciu usługi [Azure VPN Gateway,](../../vpn-gateway/vpn-gateway-about-vpngateways.md)która jest zasobem platformy Azure oferującym usługi sieci VPN i jest wdrażana w grupie zasobów obok kont magazynu lub innych zasobów platformy Azure.

![Wykres topologii ilustrujący topologię bramy sieci VPN platformy Azure łączącej udział plików platformy Azure z lokacją lokalną przy użyciu sieci VPN typu lokacja-lokacja](media/storage-files-configure-s2s-vpn/s2s-topology.png)

Zdecydowanie zalecamy przeczytanie artykułu [Azure Files omówienie](storage-files-networking-overview.md) sieci przed kontynuowaniem pracy z tym artykułem, aby zapoznać się z pełną omówieniem opcji sieciowych dostępnych dla Azure Files.

W tym artykule szczegółowo o krokach konfigurowania sieci VPN typu lokacja-lokacja w celu instalacji udziałów plików platformy Azure bezpośrednio w środowisku lokalnym. Jeśli chcesz rozsyłać ruch synchronizacji dla sieci Azure File Sync za pośrednictwem sieci VPN typu lokacja-lokacja, zobacz konfigurowanie ustawień serwera [proxy Azure File Sync zapory.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Wymagania wstępne
- Udział plików platformy Azure, który chcesz zainstalować lokalnie. Udziały plików platformy Azure są wdrażane w ramach kont magazynu, które są konstrukcjami zarządzania reprezentującymi udostępnioną pulę magazynu, w której można wdrożyć wiele udziałów plików, a także innych zasobów magazynu, takich jak kontenery obiektów blob lub kolejki. Aby dowiedzieć się więcej na temat wdrażania udziałów plików i kont magazynu platformy Azure, [zobacz Tworzenie udziału plików platformy Azure.](storage-how-to-create-file-share.md)

- Prywatny punkt końcowy dla konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. Aby dowiedzieć się więcej na temat tworzenia prywatnego punktu końcowego, zobacz [Konfigurowanie Azure Files sieci.](storage-files-networking-endpoints.md?tabs=azure-portal) 

- Urządzenie sieciowe lub serwer w lokalnym centrum danych, który jest zgodny z usługą Azure VPN Gateway. Azure Files jest niezależny od wybranego lokalnego urządzenia sieciowego, ale usługa Azure VPN Gateway utrzymuje listę [przetestowanych urządzeń.](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) Różne urządzenia sieciowe oferują różne funkcje, charakterystyki wydajności i funkcje zarządzania, dlatego należy je wziąć pod uwagę podczas wybierania urządzenia sieciowego.

    Jeśli nie masz istniejącego urządzenia sieciowego, system Windows Server zawiera wbudowaną rolę serwera, routing i dostęp zdalny (RRAS), która może być używana jako lokalne urządzenie sieciowe. Aby dowiedzieć się więcej na temat konfigurowania routingu i dostępu zdalnego w systemie Windows Server, zobacz [bramy RAS](/windows-server/remote/remote-access/ras-gateway/ras-gateway).

## <a name="add-storage-account-to-vnet"></a>Dodawanie konta magazynu do sieci wirtualnej
W Azure Portal przejdź do konta magazynu zawierającego udział plików platformy Azure, który chcesz zainstalować lokalnie. W spisie treści konta magazynu wybierz wpis **Zapory i sieci** wirtualne. O ile sieć wirtualna nie została dodana do konta magazynu  podczas jej tworzenia, w wynikowym okienku powinien być wybrany przycisk radiowy **Zezwalaj** na dostęp z listy Wszystkich sieci.

Aby dodać konto magazynu do żądanej sieci wirtualnej, wybierz pozycję **Wybrane sieci.** W obszarze **Sieci wirtualne** kliknij pozycję **+ Dodaj** istniejącą sieć wirtualną lub **+Dodaj** nową sieć wirtualną w zależności od żądanego stanu. Utworzenie nowej sieci wirtualnej spowoduje utworzenie nowego zasobu platformy Azure. Nowy lub istniejący zasób sieci wirtualnej nie musi znajdować się w tej samej grupie zasobów lub subskrypcji co konto magazynu, jednak musi znajdować się w tym samym regionie co konto magazynu, a grupa zasobów i subskrypcja, w których wdrożysz sieć wirtualną, muszą być zgodne z tym, w VPN Gateway. 

![Zrzut ekranu Azure Portal z opcją dodania istniejącej lub nowej sieci wirtualnej do konta magazynu](media/storage-files-configure-s2s-vpn/add-vnet-1.png)

Jeśli dodasz istniejącą sieć wirtualną, zostanie poproszony o wybranie co najmniej jednej podsieci tej sieci wirtualnej, do której ma zostać dodane konto magazynu. Jeśli wybierzesz nową sieć wirtualną, utworzysz podsieć w ramach tworzenia sieci wirtualnej i możesz dodać więcej później za pośrednictwem wynikowego zasobu platformy Azure dla sieci wirtualnej.

Jeśli konto magazynu nie było wcześniej dodawane do subskrypcji, należy dodać punkt końcowy usługi Microsoft.Storage do sieci wirtualnej. Może to potrwać pewien czas, a do momentu ukończenia tej operacji nie będzie można uzyskać dostępu do udziałów plików platformy Azure w ramach tego konta magazynu, w tym za pośrednictwem połączenia sieci VPN. 

## <a name="deploy-an-azure-vpn-gateway"></a>Wdrażanie usługi Azure VPN Gateway
W spisie treści dla maszyny Azure Portal utwórz **nowy** zasób i wyszukaj pozycję *Brama sieci wirtualnej.* Brama sieci wirtualnej musi znajdować się w tej samej subskrypcji, regionie platformy Azure i grupie zasobów co sieć wirtualna wdrożona w poprzednim kroku (pamiętaj, że grupa zasobów jest wybierana automatycznie po wybraniu sieci wirtualnej). 

Na potrzeby wdrażania usługi Azure VPN Gateway należy wypełnić następujące pola:

- **Nazwa:** nazwa zasobu platformy Azure dla VPN Gateway. Ta nazwa może być dowolną nazwą, która może być przydatna do zarządzania.
- **Region:** region, w którym VPN Gateway zostanie wdrożony.
- **Typ bramy:** na potrzeby wdrażania sieci VPN typu lokacja-lokacja należy wybrać pozycję **SIEĆ VPN.**
- **Typ sieci VPN:** w zależności od  urządzenia sieci VPN możesz wybrać opcję Oparte na trasach *** lub Oparte na zasadach. Sieci VPN oparte na trasach obsługują tylko IKEv2, a sieci VPN oparte na zasadach obsługują tylko IKEv1. Aby dowiedzieć się więcej o dwóch typach bram sieci VPN, zobacz [About policy-based and route-based VPN gateways](../../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md#about) (Informacje o bramach sieci VPN opartych na zasadach i na trasach)
- **SKU:** ta sku steruje liczbą dozwolonych tuneli lokacja-lokacja i żądaną wydajnością sieci VPN. Aby wybrać odpowiednią dla Twojego przypadku użycia odpowiednią dla Twojego przypadku użycia, zapoznaj się z [listą pozycji Gateway SKU (SKU bramy).](../../vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) W razie potrzeby można później VPN Gateway zmienić jego VPN Gateway SKU.
- **Sieć wirtualna:** sieć wirtualna utworzona w poprzednim kroku.
- **Publiczny adres IP:** adres IP VPN Gateway, który zostanie ujawniony w Internecie. Prawdopodobnie konieczne będzie utworzenie nowego adresu IP, ale można również użyć istniejącego nieużywanego adresu IP, jeśli jest to odpowiednie. W przypadku wybrania opcji Utwórz nowy nowy adres IP zasób platformy Azure zostanie utworzony w tej samej grupie zasobów co grupa VPN Gateway, a nazwa publicznego adresu **IP** będzie nazwą nowo utworzonego adresu IP. W przypadku wybrania **opcji Użyj istniejącej** należy wybrać istniejący nieużywany adres IP.
- **Włącz tryb aktywny-aktywny:** wybierz opcję **Włączone** tylko w przypadku tworzenia konfiguracji bramy aktywne-aktywne. W przeciwnym razie pozostaw **zaznaczoną opcję** Wyłączone. Aby dowiedzieć się więcej na temat trybu aktywny-aktywny, zobacz Wysoko dostępna łączność między środowiskami lokalnymi i połączeniami między sieciami [wirtualnmi](../../vpn-gateway/vpn-gateway-highlyavailable.md).
- **Konfigurowanie asn BGP:** wybierz opcję **Włączone** tylko wtedy, gdy konfiguracja wymaga tego ustawienia. Aby dowiedzieć się więcej na temat tego ustawienia, zobacz About BGP with Azure VPN Gateway (Informacje [o BGP z usługą Azure VPN Gateway).](../../vpn-gateway/vpn-gateway-bgp-overview.md)

Wybierz **pozycję Przeglądanie + tworzenie,** aby utworzyć VPN Gateway. Pełne VPN Gateway może potrwać do 45 minut.

### <a name="create-a-local-network-gateway-for-your-on-premises-gateway"></a>Tworzenie bramy sieci lokalnej dla bramy lokalnej 
Brama sieci lokalnej to zasób platformy Azure, który reprezentuje lokalne urządzenie sieciowe. W spisie treści pliku wybierz Azure Portal **Utwórz** nowy zasób i wyszukaj *bramę sieci lokalnej.* Brama sieci lokalnej to zasób platformy Azure, który zostanie wdrożony wraz z kontem magazynu, siecią wirtualną i usługą VPN Gateway, ale nie musi być w tej samej grupie zasobów lub subskrypcji co konto magazynu. 

Na potrzeby wdrażania zasobu bramy sieci lokalnej należy wypełnić następujące pola:

- **Nazwa:** nazwa zasobu platformy Azure dla bramy sieci lokalnej. Ta nazwa może być dowolną nazwą, która może być przydatna do zarządzania.
- **Adres IP:** publiczny adres IP lokalnej bramy.
- **Przestrzeń adresowa:** zakresy adresów sieci, które reprezentuje ta brama sieci lokalnej. Możesz dodać wiele zakresów przestrzeni adresowej, ale upewnij się, że określone w tym miejscu zakresy nie nakładają się na zakresy innych sieci, z których chcesz nawiązać połączenie. 
- **Konfigurowanie ustawień protokołu BGP:** skonfiguruj ustawienia protokołu BGP tylko wtedy, gdy konfiguracja wymaga tego ustawienia. Aby dowiedzieć się więcej na temat tego ustawienia, zobacz About BGP with Azure VPN Gateway (Informacje [o bgp z usługą Azure VPN Gateway).](../../vpn-gateway/vpn-gateway-bgp-overview.md)
- **Subskrypcja:** żądana subskrypcja. Nie musi odpowiadać subskrypcji używanej dla konta VPN Gateway magazynu.
- **Grupa zasobów:** żądana grupa zasobów. Nie musi odpowiadać grupie zasobów używanej dla VPN Gateway lub konta magazynu.
- **Lokalizacja:** region świadczenia usługi Azure, w którym powinien zostać utworzony zasób bramy sieci lokalnej. Powinno to być zgodne z regionem wybranym dla VPN Gateway i kontem magazynu.

Wybierz **pozycję Utwórz,** aby utworzyć zasób bramy sieci lokalnej.  

## <a name="configure-on-premises-network-appliance"></a>Konfigurowanie lokalnego urządzenia sieciowego
Konkretne kroki konfigurowania lokalnego urządzenia sieciowego zależą od urządzenia sieciowego wybranego przez organizację. W zależności od urządzenia wybranego [](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) przez organizację lista przetestowanych urządzeń może zawierać link do instrukcji dotyczących konfigurowania przy użyciu usługi Azure VPN Gateway.

## <a name="create-the-site-to-site-connection"></a>Tworzenie połączenia lokacja-lokacja
Aby ukończyć wdrażanie sieci VPN typu lokacja-lokacja, należy utworzyć połączenie między lokalnym urządzeniem sieciowym (reprezentowanym przez zasób bramy sieci lokalnej) a VPN Gateway. W tym celu przejdź do utworzonej VPN Gateway powyżej. W spisie treści pliku wybierz pozycję VPN Gateway **i** kliknij przycisk **Dodaj**. Wynikowe **okienko Dodaj połączenie** wymaga następujących pól:

- **Nazwa:** nazwa połączenia. Komputer VPN Gateway hostować wiele połączeń, więc wybierz nazwę przydatną dla zarządzania, która będzie rozróżniać to konkretne połączenie.
- **Typ połączenia:** ponieważ jest to połączenie typu lokacja-lokacja, wybierz pozycję **Lokacja-lokacja (IPSec)** z listy rozwijanej.
- **Brama sieci wirtualnej:** to pole jest automatycznie wybierane VPN Gateway, z VPN Gateway z które jest nawiązaniu połączenia, i nie można go zmienić.
- **Brama sieci lokalnej:** jest to brama sieci lokalnej, którą chcesz połączyć z VPN Gateway. Wynikowe okienko wyboru powinno mieć nazwę utworzonej powyżej bramy sieci lokalnej.
- **Klucz wspólny (PSK):** połączenie jest używane do ustanawiania szyfrowania połączenia za pomocą różnych liter i cyfr. Ten sam klucz wspólny musi być używany zarówno w sieci wirtualnej, jak i bramach sieci lokalnej. Jeśli urządzenie bramy nie poda go, możesz je utworzyć w tym miejscu i udostępnić na urządzeniu.

Kliknij przycisk **OK**, aby utworzyć połączenie. Możesz sprawdzić, czy połączenie zostało nawiązaniu pomyślnie, za **pośrednictwem strony** Połączenia.

## <a name="mount-azure-file-share"></a>Zainstaluj udział plików platformy Azure 
Ostatnim krokiem konfigurowania sieci VPN S2S jest sprawdzenie, czy działa ona w Azure Files. Możesz to zrobić, ując udział plików platformy Azure w środowisku lokalnym przy użyciu preferowanego systemu operacyjnego. Zapoznaj się z instrukcjami instalacji przez system operacyjny tutaj:

- [Windows](storage-how-to-use-files-windows.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Linux](storage-how-to-use-files-linux.md)

## <a name="see-also"></a>Zobacz też
- [Azure Files omówienie sieci](storage-files-networking-overview.md)
- [Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Windows do użycia z Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurowanie sieci VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files](storage-files-configure-p2s-vpn-linux.md)
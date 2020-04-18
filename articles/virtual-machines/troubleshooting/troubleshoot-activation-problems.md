---
title: Rozwiązywanie problemów z aktywacją maszyny wirtualnej systemu Windows na platformie Azure| Dokumenty firmy Microsoft
description: Zawiera kroki rozwiązywania problemów z rozwiązywaniem problemów z aktywacją maszyny wirtualnej systemu Windows na platformie Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: fd38f646b8dfc58839cd2645f7fadf7332693854
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605984"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Rozwiązywanie problemów z aktywacją maszyny wirtualnej z systemem Windows na platformie Azure

Jeśli masz problemy podczas aktywacji maszyny wirtualnej systemu Azure Windows (VM), który jest tworzony na podstawie obrazu niestandardowego, można użyć informacji zawartych w tym dokumencie, aby rozwiązać problem. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Opis punktów końcowych usługi Azure KMS dotyczący aktywacji produktu systemu Windows maszyn wirtualnych platformy Azure

Platforma Azure używa różnych punktów końcowych dla aktywacji usług zarządzania kluczami (Usługi zarządzania kluczami) w zależności od regionu chmury, w którym znajduje się maszyna wirtualna. Korzystając z tego przewodnika rozwiązywania problemów, należy użyć odpowiedniego punktu końcowego usługi zarządzania kluczami, który ma zastosowanie do danego regionu.

* Regiony chmury publicznej platformy Azure: kms.core.windows.net:1688
* Regiony chmury krajowej Azure China 21Vianet: kms.core.chinacloudapi.cn:1688
* Regiony chmury krajowej azure w Niemczech: kms.core.cloudapi.de:1688
* Regiony chmury krajowej usługi Azure Gov w usłudze US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Objaw

Podczas próby aktywowania maszyny Wirtualnej systemu Windows platformy Azure pojawia się komunikat o błędzie podobny do następującego przykładu:

**Błąd: 0xC004F074 Usługa licencjonowania oprogramowania poinformowała, że nie można aktywować komputera. Nie można skontaktować się z usługą zarządzania kluczami (KMS). Dodatkowe informacje można znaleźć w dzienniku zdarzeń aplikacji.**

## <a name="cause"></a>Przyczyna

Ogólnie problemy z aktywacją maszyn wirtualnych platformy Azure występują, gdy maszyna wirtualna z systemem Windows nie jest skonfigurowana przy użyciu odpowiedniego klucza konfiguracji klienta usługi KMS lub gdy maszyna wirtualna z systemem Windows ma problem z połączeniem z usługą Azure KMS (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Rozwiązanie

>[!NOTE]
>Jeśli korzystasz z sieci VPN typu lokacja lokacja i wymuszonego tunelowania, zobacz Korzystanie z tras niestandardowych platformy Azure w [celu włączenia aktywacji usługi zarządzania kluczami z wymuszonym tunelowaniem.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) 
>
>Jeśli korzystasz z usługi ExpressRoute i opublikowano trasę domyślną, zobacz [Czy mogę zablokować połączenie z Internetem z sieciami wirtualnymi połączonymi z obwodami usługi ExpressRoute?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Krok 1 Konfigurowanie odpowiedniego klucza konfiguracji klienta usługi zarządzania kluczem

W przypadku maszyny Wirtualnej utworzonej na podstawie obrazu niestandardowego należy skonfigurować odpowiedni klucz konfiguracji klienta usługi zarządzania kluczem zarządzania kluczem zarządzania kluczem dla maszyny Wirtualnej.

1. Uruchom **slmgr.vbs /dlv** w wierszu polecenia z podwyższonym poziomem uprawnień. Sprawdź wartość Opis w danych wyjściowych, a następnie określ, czy została utworzona z nośnika licencji detalicznego (kanał DETALICZNY) czy nośnika licencji woluminu (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Jeśli po wpisaniu **slmgr.vbs /dlv** zostanie wyświetlony kanał RETAIL, uruchom następujące polecenia, aby ustawić [klucz konfiguracji klienta usługi KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) dla używanej wersji systemu Windows Server i wymusić ponowienie próby aktywacji: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Na przykład w centrum danych systemu Windows Server 2016 należy uruchomić następujące polecenie:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2 Sprawdź łączność między maszyną wirtualną a usługą Azure KMS

1. Pobierz i wyodrębnij narzędzie [PSping](https://docs.microsoft.com/sysinternals/downloads/psping) do folderu lokalnego na maszynie Wirtualnej, która nie jest aktywowana. 

2. Przejdź do ekranu startowego, wyszukaj w programie Windows PowerShell, kliknij prawym przyciskiem myszy pozycję Windows PowerShell, a następnie wybierz polecenie Uruchom jako administrator.

3. Upewnij się, że maszyna wirtualna jest skonfigurowana do używania właściwego serwera usługi Azure KMS. Aby to zrobić, uruchom następujące polecenie:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Polecenie powinno zwrócić: Nazwa komputera usługi zarządzania kluczami ustawiona na kms.core.windows.net:1688 pomyślnie.

4. Sprawdź przy użyciu narzędzia Psping, czy masz połączenie z serwerem usługi KMS. Przejdź do folderu, w którym wyodrębniono pobrany plik Pstools.zip, a następnie uruchom:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   W przedostatnim wierszu danych wyjściowych upewnij się, że widzisz: Wysłane = 4, Odebrane = 4, Utracone = 0 (0% straty).

   Jeśli Lost jest większa niż 0 (zero), maszyna wirtualna nie ma łączności z serwerem usługi zarządzania kluczami. W tej sytuacji jeśli maszyna wirtualna znajduje się w sieci wirtualnej i ma określony niestandardowy serwer DNS, należy upewnić się, że serwer DNS jest w stanie rozwiązać kms.core.windows.net. Możesz też zmienić serwer DNS na serwer, który rozwiązuje kms.core.windows.net.

   Należy zauważyć, że jeśli usuniesz wszystkie serwery DNS z sieci wirtualnej, maszyny wirtualne korzystają z wewnętrznej usługi DNS platformy Azure. Ta usługa może rozwiązać kms.core.windows.net.
  
    Upewnij się również, że wychodzący ruch sieciowy do punktu końcowego usługi 1688 z portem 1688 nie jest blokowany przez zaporę na maszynie Wirtualnej.

5. Sprawdź za pomocą [funkcji Kontrola sieci następnego przeskoku,](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) czy następny typ przeskoku z danej maszyny Wirtualnej do docelowej ip 23.102.135.246 (dla kms.core.windows.net) lub adres IP odpowiedniego punktu końcowego usługi zarządzania kluczami, który ma zastosowanie do danego regionu, to **Internet**.  Jeśli wynikiem jest VirtualAppliance lub VirtualNetworkGateway, jest prawdopodobne, że istnieje trasa domyślna.  Skontaktuj się z administratorem sieci i pracuj z nimi, aby określić prawidłowy kierunek działania.  Może to być [trasa niestandardowa,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) jeśli to rozwiązanie jest zgodne z zasadami organizacji.

6. Po zweryfikowaniu pomyślnego połączenia z maszyną wirtualną kms.core.windows.net uruchom poniższe polecenie w wierszu programu Windows PowerShell z podwyższonym poziomem uprawnień. To polecenie podejmuje próbę aktywacji wiele razy.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Pomyślna aktywacja zwraca informacje podobne do następujących:
    
    **Aktywacja systemu Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678) ...  Produkt został pomyślnie aktywowany.**

## <a name="faq"></a>Często zadawane pytania 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>System Windows Server 2016 został utworzony w portalu Azure Marketplace. Czy muszę skonfigurować klucz usługi zarządzania kluczem zarządzania kluczem do aktywacji systemu Windows Server 2016? 

 
Nie. Obraz w portalu Azure Marketplace ma już skonfigurowany klucz konfiguracji klienta usługi zarządzania kluczem zarządzania kluczem. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Czy aktywacja systemu Windows działa w ten sam sposób, niezależnie od tego, czy maszyna wirtualna korzysta z korzyści użycia hybrydowego platformy Azure (HUB), czy nie? 

 
Tak. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Co się stanie, jeśli upłynie okres aktywacji systemu Windows? 

 
Po upływie okresu prolongaty i nadal nie jest aktywowany system Windows Server 2008 R2 i nowsze wersje systemu Windows będą wyświetlać dodatkowe powiadomienia o aktywacji. Tapeta pulpitu pozostaje czarna, a usługa Windows Update zainstaluje tylko aktualizacje zabezpieczeń i aktualizacje krytyczne, ale nie są to aktualizacje opcjonalne. Zobacz sekcję Powiadomienia u dołu strony [Warunki licencjonowania.](https://technet.microsoft.com/library/ff793403.aspx)   

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

---
title: Rozwiązywanie problemów z aktywacją maszyn wirtualnych systemu Windows na platformie Azure | Microsoft Docs
description: Zawiera opis kroków rozwiązywania problemów z aktywacją maszyn wirtualnych systemu Windows na platformie Azure
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
ms.openlocfilehash: 3179324dd71ebf3bb44cb68f0fd84486bb88e2ce
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441054"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Rozwiązywanie problemów z aktywacją maszyny wirtualnej z systemem Windows na platformie Azure

Jeśli występują problemy podczas aktywowania maszyny wirtualnej systemu Windows Azure, która została utworzona na podstawie niestandardowego obrazu, możesz skorzystać z informacji podanych w tym dokumencie, aby rozwiązać problem. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Opis punktów końcowych usługi Azure KMS dotyczący aktywacji produktu systemu Windows maszyn wirtualnych platformy Azure

Platforma Azure używa różnych punktów końcowych dla aktywacji usługi KMS (usług zarządzania kluczami) w zależności od regionu w chmurze, w którym znajduje się maszyna wirtualna. Korzystając z tego przewodnika rozwiązywania problemów, należy użyć odpowiedniego punktu końcowego usługi KMS, który jest stosowany w Twoim regionie.

* Regiony chmury publicznej platformy Azure: kms.core.windows.net:1688
* Azure Chiny — narodowe regiony w chmurze 21Vianet: kms.core.chinacloudapi.cn:1688
* Regiony chmury krajowej platformy Azure (Niemcy): kms.core.cloudapi.de:1688
* Regiony chmury krajowej US Gov platformy Azure: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Objaw

Podczas próby aktywowania maszyny wirtualnej z systemem Windows Azure zostanie wyświetlony komunikat o błędzie podobny do następującego:

**Błąd: 0xC004F074 LicensingService oprogramowania zgłosił, że nie można aktywować komputera. Nie można skontaktować się z usługą Key ManagementService (KMS). Więcej informacji można znaleźć w dzienniku zdarzeń aplikacji.**

## <a name="cause"></a>Przyczyna

Ogólnie problemy z aktywacją maszyn wirtualnych platformy Azure występują, gdy maszyna wirtualna z systemem Windows nie jest skonfigurowana przy użyciu odpowiedniego klucza konfiguracji klienta usługi KMS lub gdy maszyna wirtualna z systemem Windows ma problem z połączeniem z usługą Azure KMS (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Rozwiązanie

>[!NOTE]
>Jeśli używasz sieci VPN typu lokacja-lokacja i wymuszonego tunelowania, zobacz [Używanie niestandardowych tras platformy Azure, aby włączyć aktywację usługi KMS z wymuszonym tunelowaniem](../../vpn-gateway/vpn-gateway-about-forced-tunneling.md). 
>
>Jeśli używasz programu ExpressRoute i masz opublikowaną trasę domyślną, zobacz, [czy można zablokować łączność internetową z sieciami wirtualnymi podłączonymi do obwodów usługi ExpressRoute?](../../expressroute/expressroute-faqs.md).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Krok 1. Konfigurowanie odpowiedniego klucza instalacji klienta usługi KMS

W przypadku maszyny wirtualnej utworzonej na podstawie obrazu niestandardowego należy skonfigurować odpowiedni klucz instalacji klienta usługi KMS dla maszyny wirtualnej.

1. Uruchom polecenie **slmgr. vbs/dlv** w wierszu polecenia z podwyższonym poziomem uprawnień. Sprawdź wartość Opis w danych wyjściowych, a następnie ustal, czy została ona utworzona na podstawie nośnika licencji detalicznej lub woluminu (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Jeśli po wpisaniu **slmgr.vbs /dlv** zostanie wyświetlony kanał RETAIL, uruchom następujące polecenia, aby ustawić [klucz konfiguracji klienta usługi KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys) dla używanej wersji systemu Windows Server i wymusić ponowienie próby aktywacji: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Na przykład w przypadku systemu Windows Server 2016 Datacenter można uruchomić następujące polecenie:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2. Weryfikowanie łączności między maszyną wirtualną a usługą Azure KMS

1. Pobierz i wyodrębnij narzędzie [PSping](/sysinternals/downloads/psping) do folderu lokalnego na maszynie wirtualnej, która nie jest aktywowana. 

2. Przejdź do menu Start, Wyszukaj w programie Windows PowerShell, kliknij prawym przyciskiem myszy program Windows PowerShell, a następnie wybierz polecenie Uruchom jako administrator.

3. Upewnij się, że maszyna wirtualna jest skonfigurowana do używania właściwego serwera usługi Azure KMS. Aby to zrobić, uruchom następujące polecenie:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Polecenie powinno zostać zwrócone: Nazwa maszyny usługi zarządzania kluczami ustawiona na kms.core.windows.net:1688 pomyślnie.

4. Sprawdź przy użyciu narzędzia Psping, czy masz połączenie z serwerem usługi KMS. Przejdź do folderu, w którym wyodrębniono pobrany plik Pstools.zip, a następnie uruchom:
  
    ```
    .\psping.exe kms.core.windows.net:1688
    ```
   W drugim wierszu danych wyjściowych upewnij się, że zobaczysz: wysłane = 4, odebrane = 4, utrata = 0 (0% straty).

   Jeśli wartość utracona jest większa niż 0 (zero), maszyna wirtualna nie ma łączności z serwerem usługi KMS. W takiej sytuacji, jeśli maszyna wirtualna znajduje się w sieci wirtualnej i ma określony niestandardowy serwer DNS, należy upewnić się, że serwer DNS jest w stanie rozpoznać kms.core.windows.net. Lub Zmień serwer DNS na taki, który rozwiązuje kms.core.windows.net.

   Zwróć uwagę, że w przypadku usunięcia wszystkich serwerów DNS z sieci wirtualnej maszyny wirtualne używają wewnętrznej usługi DNS platformy Azure. Ta usługa może rozwiązać kms.core.windows.net.
  
    Upewnij się również, że wychodzący ruch sieciowy do punktu końcowego usługi KMS z portem 1688 nie jest blokowany przez zaporę na maszynie wirtualnej.

5. Sprawdź przy użyciu [Network Watcher następnym przeskokiem](../../network-watcher/network-watcher-next-hop-overview.md) typ następnego przeskoku z maszyny wirtualnej, który ma być używany do docelowego adresu IP 23.102.135.246 (dla KMS.Core.Windows.NET) lub adres IP odpowiedniego punktu końcowego usługi KMS, który ma zastosowanie do Twojego regionu, to **Internet**.  Jeśli wynik to VirtualAppliance lub VirtualNetworkGateway, prawdopodobnie istnieje trasa domyślna.  Skontaktuj się z administratorem sieci i pracuj z nimi, aby określić poprawność działania.  Może to być [trasa niestandardowa](./custom-routes-enable-kms-activation.md) , jeśli to rozwiązanie jest zgodne z zasadami organizacji.

6. Po zweryfikowaniu pomyślnego połączenia z maszyną wirtualną kms.core.windows.net uruchom poniższe polecenie w wierszu programu Windows PowerShell z podwyższonym poziomem uprawnień. To polecenie podejmuje próbę aktywacji wiele razy.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Pomyślna aktywacja zwraca informacje podobne do następujących:
    
    **Aktywowanie systemu Windows (R), ServerDatacenter Edition (12345678-1234-1234-1234-12345678)...  Produkt został aktywowany pomyślnie.**

## <a name="faq"></a>Często zadawane pytania 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Po utworzeniu systemu Windows Server 2016 z witryny Azure Marketplace. Czy muszę skonfigurować klucz usługi KMS na potrzeby aktywowania systemu Windows Server 2016? 

 
Nie. Na obrazie w witrynie Azure Marketplace jest już skonfigurowany odpowiedni klucz instalacji klienta usługi KMS. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Czy aktywacja systemu Windows działa tak samo, niezależnie od tego, czy maszyna wirtualna korzysta z korzyści z używania hybrydowej platformy Azure (HUB) czy nie? 

 
Tak. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Co się stanie w przypadku wygaśnięcia okresu aktywacji systemu Windows? 

 
Po wygaśnięciu okresu prolongaty, gdy system Windows nadal nie jest aktywowany, w systemie Windows Server 2008 R2 i nowszych wersjach systemu Windows zostaną wyświetlone dodatkowe powiadomienia dotyczące aktywowania. Tapeta pulpitu pozostaje czarna, a Windows Update zainstaluje tylko zabezpieczenia i aktualizacje krytyczne, ale nie aktualizacje opcjonalne. Zobacz sekcję powiadomienia w dolnej części strony [Warunki licencjonowania](/previous-versions/tn-archive/ff793403(v=technet.10)) .   

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.

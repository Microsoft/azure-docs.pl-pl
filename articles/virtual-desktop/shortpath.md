---
title: Windows Virtual Desktop RDP shortpath (wersja zapoznawcza)
titleSuffix: Azure
description: Jak skonfigurować shortpath RDP (wersja zapoznawcza) dla pulpitu wirtualnego systemu Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: eef78ffefe8fe13e6f160e38a05405a80d6e46f8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660954"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Windows Virtual Desktop RDP shortpath (wersja zapoznawcza)

> [!IMPORTANT]
> Shortpath RDP jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

RDP shortpath to funkcja pulpitu wirtualnego systemu Windows, która tworzy bezpośrednią transport oparty na protokole UDP między klientem Pulpit zdalny a hostem sesji. Protokół RDP używa tego transportu do dostarczania Pulpit zdalny i usługi RemoteApp, a jednocześnie zapewnia lepszą niezawodność i spójne opóźnienia.

## <a name="key-benefits"></a>Najważniejsze korzyści

* Transport RDP shortpath jest oparty na standardzie wysoce wydajny  [Protokół uniwersalnej kontroli szybkości (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/). URCP rozszerza protokół UDP z aktywnym monitorowaniem warunków sieci i zapewnia sprawiedliwe i pełne wykorzystanie linków. URCP działa na poziomie małych opóźnień i strat zgodnie z wymaganiami Pulpit zdalny. URCP osiąga najlepszą wydajność przez dynamiczne uczenie parametrów sieci i zapewnianie protokołu przy użyciu mechanizmu kontroli szybkości.
* Protokół RDP shortpath ustanawia bezpośrednią łączność między klientem Pulpit zdalny a hostem sesji. Bezpośrednia łączność zmniejsza zależność od bram pulpitu wirtualnego systemu Windows, zwiększa niezawodność połączenia i zwiększa przepustowość dostępną dla każdej sesji użytkownika.
* Usunięcie dodatkowego przekaźnika zmniejsza czas błądzenia, co zwiększa komfort pracy użytkowników z aplikacjami zależnymi od opóźnienia i metodami wejściowymi.
* Shortpath RDP zapewnia obsługę konfigurowania priorytetu [Quality of Service (QoS)](./rdp-quality-of-service-qos.md) dla połączeń RDP za pomocą znaków DSCP (Differentiated Services Code Point).
* Transport shortpath RDP pozwala [ograniczyć ruch sieciowy wychodzący](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) przez określenie częstotliwości ograniczania dla każdej sesji.

## <a name="connection-security"></a>Zabezpieczenia połączeń

Shortpath RDP rozszerza możliwości wieloskładnikowego protokołu RDP. Nie zastępuje on transportu zwrotnego, ale uzupełnia go. Wszystkie wstępne Broker sesji są zarządzane za pośrednictwem infrastruktury pulpitów wirtualnych systemu Windows.

Port UDP 3390 jest używany tylko dla ruchu przychodzącego shortpath, który jest uwierzytelniany za pośrednictwem transportowego połączenia zwrotnego. Odbiornik shortpath RDP ignoruje wszystkie próby połączenia z odbiornikiem, chyba że są zgodne z sesją połączenia odwrotnego.

Protokół RDP shortpath korzysta z połączenia TLS między klientem i hostem sesji przy użyciu certyfikatów hosta sesji. Domyślnie certyfikat używany na potrzeby szyfrowania RDP jest generowany automatycznie przez system operacyjny podczas wdrażania. W razie potrzeby klienci mogą wdrażać centralne certyfikaty zarządzane przez urząd certyfikacji przedsiębiorstwa. Więcej informacji o konfiguracjach certyfikatów znajduje się w [dokumentacji systemu Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations.md).

## <a name="rdp-shortpath-connection-sequence"></a>Sekwencja połączenia RDP shortpath

Po zainstalowaniu [transportowego połączenia zwrotnego](./network-connectivity.md)klient i Host sesji nawiązują połączenie RDP i negocjują możliwości wielu transportów. Dodatkowe kroki opisane poniżej:

1. Host sesji wysyła do klienta listę prywatnych i publicznych adresów IPv4 i IPv6.
2. Klient uruchamia wątek w tle, aby ustanowić równoległy transport oparty na protokole UDP bezpośrednio do jednego z adresów IP hosta.
3. Gdy klient sonduje podane adresy IP, kontynuuje wstępne ustanowienie połączenia przez transportowe połączenie odwrotne, aby upewnić się, że połączenie użytkownika nie jest opóźnione.
4. Jeśli klient ma bezpośredni wgląd w szczegółowe informacje, a konfiguracja zapory jest poprawna, klient ustanowi bezpieczne połączenie TLS z hostem sesji.
5. Po ustanowieniu transportu shortpath protokół RDP przenosi wszystkie dynamiczne kanały wirtualne (DVCs), w tym zdalne grafiki, dane wejściowe i przekierowywanie urządzeń do nowego transportu.
6. Jeśli topologia zapory lub sieci uniemożliwia klientowi ustanowienie bezpośredniej łączności z protokołem UDP, protokół RDP kontynuuje transport odwrotny.

Na poniższym diagramie przedstawiono ogólne omówienie połączenia sieci shortpath RDP.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagram połączeń sieciowych shortpath RDP" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Wymagania

Aby zapewnić obsługę protokołu RDP shortpath, Klient pulpitu wirtualnego systemu Windows musi mieć bezpośredni wgląd w szczegółowe informacje na temat hosta sesji. Bezpośredni wgląd w szczegółowe informacje można uzyskać, korzystając z jednej z następujących technologii:

* [Prywatna Komunikacja równorzędna ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
* [Sieć VPN typu lokacja-lokacja (oparta na protokole IPsec)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Sieć VPN typu punkt-lokacja (oparta na protokole IPsec)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Przypisywanie publicznego adresu IP](../virtual-network/virtual-network-public-ip-address.md)

Jeśli używasz innych typów sieci VPN do łączenia się z siecią wirtualną platformy Azure, zalecamy użycie sieci VPN opartej na protokole UDP w celu uzyskania najlepszych wyników. Chociaż większość opartych na protokole TCP rozwiązań sieci VPN hermetyzuje wszystkie pakiety IP, w tym UDP, dodają one ponoszone obciążenie kontroli przeciążenia TCP, co zmniejsza wydajność protokołu RDP.

Bezpośrednia linia wglądu oznacza, że zapory nie blokują portu UDP 3390 i klient może połączyć się bezpośrednio z hostem sesji.

## <a name="enabling-rdp-shortpath-preview"></a>Włączanie wersji zapoznawczej protokołu RDP shortpath

Aby wziąć udział w wersji zapoznawczej shortpath protokołu RDP, należy włączyć odbiornik shortpath protokołu RDP na hoście sesji. Protokół RDP shortpath można włączyć na dowolnej liczbie hostów sesji używanych w danym środowisku. Nie jest wymagane włączenie protokołu RDP shortpath na wszystkich hostach w puli.
Aby włączyć odbiornik shortpath, należy skonfigurować następujące wartości rejestru:

> [!WARNING]
> W przypadku nieprawidłowego zmodyfikowania rejestru przy użyciu Edytora rejestru lub innej metody mogą wystąpić poważne problemy. Te problemy mogą wymagać ponownego zainstalowania systemu operacyjnego. Firma Microsoft nie gwarantuje, że te problemy będą możliwe do rozwiązania. Użytkownik modyfikuje rejestr na własne ryzyko.

1. Na hoście sesji Uruchom Regedit.exe, a następnie przejdź do następującej lokalizacji:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Utwórz nową wartość **DWORD** o nazwie **fUseUdpPortRedirector** i ustaw ją na **1** (dziesiętną)
3. Utwórz nową wartość **DWORD** o nazwie **UdpPortNumber** i ustaw ją na **3390** (Decimal)
4. Zamknij Edytor rejestru.
5. Uruchom ponownie hosta sesji

Możesz również uruchomić następujące polecenia cmdlet w oknie programu PowerShell z podwyższonym poziomem uprawnień, aby ustawić następujące wartości rejestru:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

Aby skonfigurować zasady grupy, można także użyć programu PowerShell

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "Default Domain Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Konfigurowanie zapory Windows Defender z zabezpieczeniami zaawansowanymi

Aby zezwolić na przychodzący ruch sieciowy dla shortpath RDP, Użyj węzła Zapora Windows Defender z zabezpieczeniami zaawansowanymi w przystawce programu MMC zasady grupy zarządzanie, aby utworzyć reguły zapory.

1. Otwórz Konsola zarządzania zasadami grupy do [zapory Windows Defender z zabezpieczeniami zaawansowanymi](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. W okienku nawigacji wybierz pozycję **reguły ruchu przychodzącego**.
3. Wybierz pozycję **Akcja**, a następnie wybierz pozycję **Nowa reguła**.
4. Na stronie **Typ reguły** kreatora Nowa reguła ruchu przychodzącego wybierz opcję **niestandardowy**, a następnie wybierz przycisk **dalej**.
5. Na stronie **program** wybierz **tę ścieżkę programu** i wpisz "% główny_katalog_systemowy% \system32\svchost.exe" a następnie wybierz **dalej**.
6. Na stronie **Protokół i porty** wybierz typ protokołu UDP. W polu **port lokalny** wybierz pozycję "określone porty" i wpisz wartość 3390.
7. Na stronie **zakres** możesz określić, że reguła ma zastosowanie tylko do ruchu sieciowego do lub z adresów IP wprowadzonych na tej stronie. Skonfiguruj odpowiednie dla swojego projektu, a następnie wybierz przycisk **dalej**.
8. Na stronie **Akcja** wybierz opcję **Zezwalaj na połączenie**, a następnie wybierz przycisk **dalej**.
9. Na stronie **profil** wybierz typy lokalizacji sieciowej, do których zostanie zastosowana ta reguła, a następnie wybierz przycisk **dalej**.
10. Na stronie **Nazwa** wpisz nazwę i opis reguły, a następnie wybierz pozycję **Zakończ**.

Możesz sprawdzić, czy Nowa reguła dopasowuje zrzuty ekranu poniżej: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="zrzut ekranu przedstawiający kartę Ogólne dla konfiguracji zapory dla połączeń sieci RDP shortpath" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Zrzut ekranu przedstawiający kartę Programy i usługi w celu skonfigurowania zapory dla połączeń sieciowych shortpath RDP" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Zrzut ekranu karty protokoły i porty w celu skonfigurowania zapory dla połączeń sieciowych shortpath RDP" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Aby skonfigurować zaporę systemu Windows, można także użyć programu PowerShell:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Konfigurowanie zapory Windows Defender przy użyciu programu PowerShell

Aby skonfigurować zasady grupy, można także użyć programu PowerShell

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "Default Domain Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

## <a name="configuring-azure-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń platformy Azure

Aby zezwolić na dostęp do odbiornika shortpath protokołu RDP w granicach zabezpieczeń sieci, należy skonfigurować grupę zabezpieczeń sieci platformy Azure, aby zezwalała na ruch przychodzący UDP 3390.
Postępuj zgodnie z [dokumentacją sieciowej grupy zabezpieczeń](../virtual-machines/windows/nsg-quickstart-portal.md) , aby utworzyć regułę zabezpieczeń dla ruchu przychodzącego zezwalającą na ruch z następującymi parametrami:

* **Źródło**  -  **Dowolny** lub zakres adresów IP, w których znajdują się klienci
* **Zakresy portów źródłowych** -* *\** _ _ dowolna **Lokalizacja docelowa**  -  **Any**
* **Docelowe zakresy portów**  -  **3390**
* **Protokół**  -  **Protokół UDP**
* **Akcja**  -  **Zezwalaj**
* Opcjonalnie można zmienić **priorytet**. Priorytet ma wpływ na kolejność stosowania reguł: mniejsza wartość liczbowa, przed zastosowaniem reguły.
* **Nazwa** — **shortpath RDP**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Wyłączanie shortpath protokołu RDP dla określonej podsieci

Jeśli zachodzi potrzeba blokowania określonych podsieci z używania protokołu RDP shortpath transport, można skonfigurować dodatkowe sieciowe grupy zabezpieczeń określające źródłowe zakresy adresów IP.

## <a name="verifying-the-connectivity"></a>Weryfikowanie łączności

### <a name="using-connection-information-dialog"></a>Korzystanie z okna dialogowego Informacje o połączeniu

Aby sprawdzić, czy połączenia korzystają z protokołu RDP shortpath, Otwórz okno dialogowe "informacje o połączeniu", klikając ikonę anteny na pasku narzędzi połączenia.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Obraz paska Podłączanie pulpitu zdalnego":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Obraz okna dialogowego Informacje o Podłączanie pulpitu zdalnego":::

### <a name="using-event-logs"></a>Korzystanie z dzienników zdarzeń

Aby sprawdzić, czy sesja używa protokołu RDP shortpath transport:

1. Nawiąż połączenie z pulpitem maszyny wirtualnej przy użyciu klienta pulpitu wirtualnego systemu Windows.
2. Uruchom Podgląd zdarzeń i przejdź do następującego węzła: **Dzienniki aplikacji i usług > Microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > operacyjnego**
3. Aby określić, czy używany jest transport RDP shortpath, poszukaj zdarzenia o IDENTYFIKATORze 131.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Weryfikowanie łączności shortpath przy użyciu Log Analytics

W przypadku korzystania z [usługi Azure log Analytics](./diagnostics-log-analytics.md)można monitorować połączenia, badając [tabelę WVDConnections](/azure/azure-monitor/reference/tables/wvdconnections). Kolumna o nazwie UdpUse wskazuje, czy stos protokołu RDP systemu Windows używa protokołu UDP w przypadku bieżącego połączenia użytkownika.
Możliwe wartości są następujące:

* **0** — połączenie użytkownika nie używa protokołu RDP shortpath
* połączenie **1** -użytkownika korzysta z protokołu RDP shortpath
  
Poniższa lista zapytań umożliwia przejrzenie informacji o połączeniu. To zapytanie można uruchomić w [Edytorze zapytań log Analytics](../azure-monitor/log-query/get-started-portal.md#write-and-run-basic-queries). Dla każdego zapytania Zastąp `userupn` nazwę UPN użytkownika, który chcesz wyszukać.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="verify-shortpath-listener"></a>Weryfikuj odbiornik shortpath

Aby sprawdzić, czy odbiornik UDP jest włączony, użyj następującego polecenia programu PowerShell na hoście sesji:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Jeśli ta funkcja jest włączona, zostaną wyświetlone dane wyjściowe podobne do następujących:

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

W przypadku konfliktu, można zidentyfikować proces zajmowany przez port przy użyciu następującego polecenia

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Wyłączanie shortpath RDP

W niektórych przypadkach może być konieczne wyłączenie transportu shortpath RDP. Shortpath RDP można wyłączyć za pomocą zasad grupy.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Wyłączanie shortpath RDP na kliencie

Aby wyłączyć shortpath RDP dla określonego klienta, można użyć następującego zasady grupy, aby wyłączyć obsługę protokołu UDP:

1. Uruchom program **gpedit. msc** na kliencie.
2. Przejdź do **Konfiguracja komputera > Szablony administracyjne > składniki systemu Windows > Usługi pulpitu zdalnego > Podłączanie pulpitu zdalnego klienta**.
3. Ustaw ustawienie **"Wyłącz protokół UDP na kliencie"** , aby **włączyć**

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Wyłączanie shortpath RDP na hoście sesji

Aby wyłączyć shortpath RDP dla określonego hosta sesji, można użyć następującego zasady grupy, aby wyłączyć obsługę protokołu UDP:

1. Na hoście sesji Uruchom **gpedit. msc**.
2. Przejdź do **Konfiguracja komputera > Szablony administracyjne > składniki systemu Windows > Usługi pulpitu zdalnego > Podłączanie pulpitu zdalnego Host > połączeń**.
3. Ustaw ustawienie **"Wybierz protokoły transportu RDP"** tylko na **TCP**

## <a name="feedback"></a>Opinia

Chcemy poznać Twoją opinię dotyczącą Twoich doświadczeń dzięki tej publicznej wersji zapoznawczej.
* Aby uzyskać odpowiedzi na pytania, żądania, komentarze i inne opinie, [Skorzystaj z tej formy opinii](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o łączności z siecią pulpitu wirtualnego systemu Windows, zobacz [Opis łączności z siecią pulpitu wirtualnego systemu Windows](network-connectivity.md).
* Aby rozpocząć pracę z usługą Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows, zobacz [implementacja Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows](rdp-quality-of-service-qos.md).

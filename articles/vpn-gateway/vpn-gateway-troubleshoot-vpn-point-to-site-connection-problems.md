---
title: Rozwiązywanie problemów z połączeniem punkt-lokacja platformy Azure
titleSuffix: Azure VPN Gateway
description: Informacje na temat rozwiązywania problemów i rozwiązywania typowych problemów z połączeniem punkt-lokacja oraz innych błędów i problemów z wirtualną siecią prywatną.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845037"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Rozwiązywanie problemów: problemy z połączeniem punkt-lokacja platformy Azure

W tym artykule wymieniono typowe problemy z połączeniem punkt-lokacja, które mogą wystąpić. Omówiono w nim również potencjalne przyczyny i rozwiązania tych problemów.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>Błąd klienta sieci VPN: nie można odnaleźć certyfikatu

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Nie można znaleźć certyfikatu, którego można użyć z tym rozszerzalnym protokołem uwierzytelniania. (Błąd 798)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli brakuje certyfikatu klienta w programie **Certificates-Current User\Personal\Certificates**.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Otwórz Menedżera certyfikatów: kliknij przycisk **Start**, wpisz **Zarządzanie certyfikatami komputerów**, a następnie kliknij przycisk **Zarządzaj certyfikatami komputera** w wyniku wyszukiwania.

2. Upewnij się, że następujące certyfikaty znajdują się w prawidłowej lokalizacji:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient. pfx  | Bieżący User\Personal\Certificates |
    | AzureRoot. cer    | Główne urzędy certyfikacji Computer\Trusted lokalnego|

3. Przejdź do \<UserName> \AppData\Roaming\Microsoft\Network\Connections\Cm C:\Users \<GUID> , ręcznie Zainstaluj certyfikat (plik *. cer) w sklepie użytkownika i komputera.

Więcej informacji o sposobie instalowania certyfikatu klienta znajduje się w temacie [generowanie i eksportowanie certyfikatów dla połączeń punkt-lokacja](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Podczas importowania certyfikatu klienta nie zaznaczaj opcji **Włącz silną ochronę klucza prywatnego** .

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Nie można nawiązać połączenia sieciowego między komputerem i serwerem sieci VPN, ponieważ serwer zdalny nie odpowiada

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z bramą sieci wirtualnej platformy Azure przy użyciu protokołu IKEv2 w systemie Windows zostanie wyświetlony następujący komunikat o błędzie:

**Nie można nawiązać połączenia sieciowego między komputerem i serwerem sieci VPN, ponieważ serwer zdalny nie odpowiada**

### <a name="cause"></a>Przyczyna
 
 Problem występuje, jeśli wersja systemu Windows nie obsługuje fragmentacji IKE
 
### <a name="solution"></a>Rozwiązanie

Protokół IKEv2 jest obsługiwany w systemach Windows 10 i Server 2016. Jednak aby można było używać protokołu IKEv2, należy zainstalować aktualizacje i lokalnie ustawić wartość klucza rejestru. Wersje systemu operacyjnego starsze niż Windows 10 nie są obsługiwane i mogą używać tylko protokołu SSTP.

Aby przygotowywać system Windows 10 lub Server 2016 pod kątem protokołu IKEv2:

1. Zainstaluj aktualizację.

   | Wersja systemu operacyjnego | Data | Numer/link |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 w wersji 1607 | 17 stycznia 2018 r. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 w wersji 1703 | 17 stycznia 2018 r. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 w wersji 1709 | 22 marca, 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Ustaw wartość klucza rejestru. Utwórz lub ustaw `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` klucz REG_DWORD w rejestrze na 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>Błąd klienta sieci VPN: odebrany komunikat był nieoczekiwany lub nieprawidłowo sformatowany

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Odebrany komunikat był nieoczekiwany lub nieprawidłowo sformatowany. (Błąd 0x80090326)**

### <a name="cause"></a>Przyczyna

Ten problem występuje, jeśli spełniony jest jeden z następujących warunków:

- Użycie tras zdefiniowanych przez użytkownika (UDR) z domyślną trasą w podsieci bramy jest ustawione nieprawidłowo.
- Klucz publiczny certyfikatu głównego nie został przekazany do bramy sieci VPN platformy Azure. 
- Klucz jest uszkodzony lub wygasł.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Usuń UDR w podsieci bramy. Upewnij się, że usługa UDR przekazuje cały ruch poprawnie.
2. Sprawdź stan certyfikatu głównego w Azure Portal, aby sprawdzić, czy został on odwołany. Jeśli nie jest ona odwołana, spróbuj usunąć certyfikat główny i przekazać go ponownie. Aby uzyskać więcej informacji, zobacz [Tworzenie certyfikatów](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>Błąd klienta sieci VPN: łańcuch certyfikatów przetworzony, ale zakończony 

### <a name="symptom"></a>Objaw 

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Łańcuch certyfikatów przetworzony, ale zakończony w certyfikacie głównym, który nie jest zaufany przez dostawcę zaufania.**

### <a name="solution"></a>Rozwiązanie

1. Upewnij się, że następujące certyfikaty znajdują się w prawidłowej lokalizacji:

    | Certyfikat | Lokalizacja |
    | ------------- | ------------- |
    | AzureClient. pfx  | Bieżący User\Personal\Certificates |
    | Azuregateway-*GUID*. cloudapp.NET  | Bieżące główne urzędy certyfikacji User\Trusted|
    | AzureGateway-*GUID*. cloudapp.NET, AzureRoot. cer    | Główne urzędy certyfikacji Computer\Trusted lokalnego|

2. Jeśli certyfikaty znajdują się już w tej lokalizacji, spróbuj je usunąć i zainstalować ponownie. Certyfikat **azuregateway-*GUID*. cloudapp.NET** znajduje się w pakiecie konfiguracji klienta sieci VPN pobranym z Azure Portal. Do wyodrębnienia plików z pakietu można użyć archiwów plików.

## <a name="file-download-error-target-uri-is-not-specified"></a>Błąd pobierania pliku: nie określono docelowego identyfikatora URI

### <a name="symptom"></a>Objaw

Zostanie wyświetlony następujący komunikat o błędzie:

**Błąd pobierania pliku. Nie określono docelowego identyfikatora URI.**

### <a name="cause"></a>Przyczyna 

Ten problem występuje z powodu nieprawidłowego typu bramy. 

### <a name="solution"></a>Rozwiązanie

Typem bramy sieci VPN musi być **Sieć VPN**, a typem sieci VPN musi być **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>Błąd klienta sieci VPN: niepowodzenie niestandardowego skryptu sieci VPN platformy Azure 

### <a name="symptom"></a>Objaw

Podczas próby nawiązania połączenia z siecią wirtualną platformy Azure przy użyciu klienta sieci VPN zostanie wyświetlony następujący komunikat o błędzie:

**Niepowodzenie skryptu niestandardowego (aby zaktualizować tabelę routingu). (Błąd 8007026f)**

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli próbujesz otworzyć połączenie sieci VPN typu lokacja-punkt przy użyciu skrótu.

### <a name="solution"></a>Rozwiązanie 

Otwórz pakiet sieci VPN bezpośrednio, zamiast otwierać go ze skrótu.

## <a name="cannot-install-the-vpn-client"></a>Nie można zainstalować klienta sieci VPN

### <a name="cause"></a>Przyczyna 

Do zaufania bramy sieci VPN dla sieci wirtualnej jest wymagany dodatkowy certyfikat. Certyfikat jest zawarty w pakiecie konfiguracji klienta sieci VPN, który jest generowany na podstawie Azure Portal.

### <a name="solution"></a>Rozwiązanie

Wyodrębnij pakiet konfiguracji klienta sieci VPN i Znajdź plik. cer. Aby zainstalować certyfikat, wykonaj następujące kroki:

1. Otwórz mmc.exe.
2. Dodaj przystawkę **Certyfikaty** .
3. Wybierz konto **komputera** dla komputera lokalnego.
4. Kliknij prawym przyciskiem myszy węzeł **Zaufane główne** urzędy certyfikacji. Kliknij pozycję **wszystko —**  >  **Importuj** zadanie i przejdź do pliku CER wyodrębnionego z pakietu konfiguracji klienta sieci VPN.
5. Uruchom ponownie komputer. 
6. Spróbuj zainstalować klienta sieci VPN.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Błąd Azure Portal: nie można zapisać bramy sieci VPN, a dane są nieprawidłowe

### <a name="symptom"></a>Objaw

Podczas próby zapisania zmian dla bramy sieci VPN w Azure Portal zostanie wyświetlony następujący komunikat o błędzie:

**Nie można zapisać &lt; *nazwy bramy* bramy sieci wirtualnej &gt; . Dane &lt; *identyfikatora certyfikatu* certyfikatu &gt; są nieprawidłowe.**

### <a name="cause"></a>Przyczyna 

Ten problem może wystąpić, jeśli przekazany klucz publiczny certyfikatu głównego zawiera nieprawidłowy znak, taki jak spacja.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że dane w certyfikacie nie zawierają nieprawidłowych znaków, takich jak podziały wierszy (znaki powrotu karetki). Cała wartość powinna być jedną długą linią. Następujący tekst jest przykładem certyfikatu:

```text
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
/0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
-----END CERTIFICATE-----
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Błąd Azure Portal: nie można zapisać bramy sieci VPN i nazwa zasobu jest nieprawidłowa

### <a name="symptom"></a>Objaw

Podczas próby zapisania zmian dla bramy sieci VPN w Azure Portal zostanie wyświetlony następujący komunikat o błędzie: 

**Nie można zapisać &lt; *nazwy bramy* bramy sieci wirtualnej &gt; . &lt; *Nazwa certyfikatu nazwy zasobu, którą próbujesz przekazać,* &gt; jest nieprawidłowa**.

### <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ nazwa certyfikatu zawiera nieprawidłowy znak, taki jak spacja. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Błąd Azure Portal: błąd pobierania pliku pakietu sieci VPN 503

### <a name="symptom"></a>Objaw

Podczas próby pobrania pakietu konfiguracji klienta sieci VPN pojawia się następujący komunikat o błędzie:

**Nie można pobrać pliku. Szczegóły błędu: błąd 503. Serwer jest zajęty.**
 
### <a name="solution"></a>Rozwiązanie

Ten błąd może być spowodowany przez tymczasowy problem z siecią. Spróbuj pobrać pakiet sieci VPN ponownie za kilka minut.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Uaktualnianie VPN Gateway platformy Azure: nie można nawiązać połączenia ze wszystkimi klientami punktu do lokacji

### <a name="cause"></a>Przyczyna

Jeśli certyfikat jest większy niż 50 procent w okresie istnienia, certyfikat jest rzutowany.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy ponownie pobrać i wdrożyć pakiet lokacji na wszystkich klientach.

## <a name="too-many-vpn-clients-connected-at-once"></a>Zbyt wielu klientów sieci VPN podłączonych jednocześnie

Osiągnięto maksymalną dozwoloną liczbę połączeń. Łączna liczba podłączonych klientów znajduje się w Azure Portal.

## <a name="vpn-client-cannot-access-network-file-shares"></a>Klient sieci VPN nie może uzyskać dostępu do sieciowych udziałów plików

### <a name="symptom"></a>Objaw

Klient sieci VPN nawiązał połączenie z siecią wirtualną platformy Azure. Jednak klient nie może uzyskać dostępu do udziałów sieciowych.

### <a name="cause"></a>Przyczyna

Protokół SMB służy do uzyskiwania dostępu do udziału plików. Po zainicjowaniu połączenia klient sieci VPN dodaje poświadczenia sesji i wystąpienie błędu. Po nawiązaniu połączenia klient musi używać poświadczeń pamięci podręcznej na potrzeby uwierzytelniania Kerberos. Ten proces inicjuje zapytania do centrum dystrybucji kluczy (kontroler domeny) w celu uzyskania tokenu. Ponieważ klient nawiązuje połączenie z Internetem, może nie być w stanie dotrzeć do kontrolera domeny. W związku z tym klient nie może przechodzić w tryb failover z protokołu Kerberos do NTLM. 

Jedyną prośbą o podanie poświadczeń przez klienta jest to, że ma on prawidłowy certyfikat (z siecią SAN = UPN) wystawiony przez domenę, do której jest przyłączony. Klient musi również być fizycznie połączony z siecią domeny. W takim przypadku klient próbuje użyć certyfikatu i zostanie do niego osiągnięty kontroler domeny. Następnie centrum dystrybucji kluczy zwraca błąd "KDC_ERR_C_PRINCIPAL_UNKNOWN". Klient jest zmuszony do przełączenia w tryb failover do protokołu NTLM. 

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, należy wyłączyć buforowanie poświadczeń domeny z następującego podklucza rejestru: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nie można znaleźć połączenia sieci VPN typu punkt-lokacja w systemie Windows po ponownym zainstalowaniu klienta sieci VPN

### <a name="symptom"></a>Objaw

Należy usunąć połączenie sieci VPN punkt-lokacja, a następnie ponownie zainstalować klienta sieci VPN. W takiej sytuacji połączenie sieci VPN nie zostało pomyślnie skonfigurowane. Połączenie sieci VPN nie jest widoczne w ustawieniach **połączeń sieciowych** w systemie Windows.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, usuń stare pliki konfiguracji klienta sieci VPN z **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>**, a następnie ponownie uruchom Instalatora klienta sieci VPN.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Klient sieci VPN typu punkt-lokacja nie może rozpoznać nazwy FQDN zasobów w domenie lokalnej

### <a name="symptom"></a>Objaw

Gdy klient łączy się z platformą Azure przy użyciu połączenia sieci VPN typu punkt-lokacja, nie może rozpoznać nazwy FQDN zasobów w domenie lokalnej.

### <a name="cause"></a>Przyczyna

Klient sieci VPN typu punkt-lokacja zwykle korzysta z serwerów Azure DNS skonfigurowanych w sieci wirtualnej platformy Azure. Serwery Azure DNS mają pierwszeństwo przed lokalnymi serwerami DNS skonfigurowanymi na kliencie (chyba że Metryka interfejsu Ethernet jest niższa), więc wszystkie zapytania DNS są wysyłane do serwerów Azure DNS. Jeśli serwery Azure DNS nie mają rekordów zasobów lokalnych, zapytanie kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, upewnij się, że serwery Azure DNS, które były używane w sieci wirtualnej platformy Azure, mogą rozpoznawać rekordy DNS dla zasobów lokalnych. W tym celu można użyć usług przesyłania dalej DNS lub usług przesyłania dalej. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Połączenie sieci VPN typu punkt-lokacja jest nawiązywane, ale nadal nie można nawiązać połączenia z zasobami platformy Azure 

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli klient sieci VPN nie uzyska tras z usługi Azure VPN Gateway.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, [Zresetuj usługę Azure VPN Gateway](./reset-gateway.md). Aby upewnić się, że nowe trasy są używane, klienci sieci VPN typu punkt-lokacja muszą zostać pobrani ponownie po pomyślnym skonfigurowaniu komunikacji równorzędnej sieci wirtualnej.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Błąd: "funkcja odwoływania nie mogła sprawdzić odwołania, ponieważ serwer odwołań był w trybie offline. (Błąd 0x80092013) "

### <a name="causes"></a>Przyczyny
Ten komunikat o błędzie występuje, gdy klient nie może uzyskać dostępu do programu http://crl3.digicert.com/ssca-sha2-g1.crl i http://crl4.digicert.com/ssca-sha2-g1.crl .  Sprawdzanie odwołania wymaga dostępu do tych dwóch lokacji.  Ten problem zwykle występuje na kliencie, na którym skonfigurowano serwer proxy. W niektórych środowiskach, jeśli żądania nie przechodzą przez serwer proxy, nastąpi odmowa w zaporze brzegowej.

### <a name="solution"></a>Rozwiązanie

Sprawdź ustawienia serwera proxy, upewnij się, że klient ma dostęp http://crl3.digicert.com/ssca-sha2-g1.crl i http://crl4.digicert.com/ssca-sha2-g1.crl .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>Błąd klienta sieci VPN: połączenie zostało uniemożliwione z powodu zasad skonfigurowanych na serwerze RAS/VPN. (Błąd 812)

### <a name="cause"></a>Przyczyna

Ten błąd występuje, gdy serwer RADIUS używany do uwierzytelniania klienta VPN ma nieprawidłowe ustawienia lub Brama platformy Azure nie może nawiązać połączenia z serwerem RADIUS.

### <a name="solution"></a>Rozwiązanie

Upewnij się, że serwer RADIUS jest prawidłowo skonfigurowany. Aby uzyskać więcej informacji, zobacz [integrowanie uwierzytelniania usługi RADIUS z usługą Azure AD serwer Multi-Factor Authentication](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Błąd 405" podczas pobierania certyfikatu głównego z VPN Gateway

### <a name="cause"></a>Przyczyna

Certyfikat główny nie został zainstalowany. Certyfikat główny jest instalowany w magazynie **zaufanych certyfikatów** klienta.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>Błąd klienta sieci VPN: połączenie zdalne nie zostało wykonane z powodu niepowodzenia podjętych tuneli VPN. (Błąd 800) 

### <a name="cause"></a>Przyczyna

Sterownik karty sieciowej jest nieaktualny.

### <a name="solution"></a>Rozwiązanie

Aktualizowanie sterownika karty sieciowej:

1. Kliknij przycisk **Start**, wpisz **Menedżer urządzeń** i wybierz go z listy wyników. Jeśli zostanie wyświetlony monit o hasło administratora lub potwierdzenie, wpisz hasło lub potwierdź.
2. W kategorii **karty sieciowe Znajdź kartę sieciową** , którą chcesz zaktualizować.  
3. Kliknij dwukrotnie nazwę urządzenia, wybierz pozycję **Aktualizuj sterownik**, a następnie pozycję **Wyszukaj automatycznie dla zaktualizowanego oprogramowania sterownika**.
4.  Jeśli system Windows nie znajduje nowego sterownika, możesz spróbować znaleźć go w witrynie internetowej producenta urządzenia i wykonać podane w niej instrukcje.
5. Uruchom ponownie komputer i spróbuj ponownie nawiązać połączenie.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>Błąd klienta sieci VPN: Nawiązywanie połączenia sieci VPN <VPN Connection Name> , stan = platforma sieci VPN nie wyzwolił połączenia

W Podgląd zdarzeń z RasClient może zostać wyświetlony następujący błąd: "użytkownik <User> wybiera połączenie o nazwie, <VPN Connection Name> które nie powiodło się. Kod błędu zwrócony w przypadku niepowodzenia to 1460. "

### <a name="cause"></a>Przyczyna

Klient sieci VPN platformy Azure nie ma włączonej uprawnienia aplikacji "aplikacje w tle" w ustawieniach aplikacji dla systemu Windows.

### <a name="solution"></a>Rozwiązanie

1. W systemie Windows przejdź do pozycji Ustawienia — > prywatność — > aplikacje w tle
2. Przełączenie "Zezwól aplikacji na działanie w tle" na Wł.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Błąd: "nie określono docelowego identyfikatora URI błędu pobierania pliku"

### <a name="cause"></a>Przyczyna

Jest to spowodowane tym, że skonfigurowano nieprawidłowy typ bramy.

### <a name="solution"></a>Rozwiązanie

Typem bramy sieci VPN platformy Azure musi być sieć VPN, a typem sieci VPN musi być **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>Instalator pakietu sieci VPN nie został ukończony

### <a name="cause"></a>Przyczyna

Ten problem może być spowodowany przez poprzednie instalacje klienta sieci VPN. 

### <a name="solution"></a>Rozwiązanie

Usuń stare pliki konfiguracji klienta VPN z usługi **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** i ponownie uruchom Instalatora klienta sieci VPN. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>Klient sieci VPN jest w stanie hibernacji lub uśpienia po pewnym czasie

### <a name="solution"></a>Rozwiązanie

Sprawdź ustawienia uśpienia i hibernacji na komputerze, na którym uruchomiono klienta sieci VPN.
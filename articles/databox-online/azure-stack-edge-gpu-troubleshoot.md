---
title: Użyj Azure Portal do rozwiązywania problemów z Azure Stack EDGE Pro z procesorem GPU | Microsoft Docs
description: Opisuje sposób rozwiązywania problemów z urządzeniami GPU w Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 87e75d771c2cc269eaae81c2433f445eb65a17a9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314154"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Rozwiązywanie problemów na urządzeniu z systemem Azure Stack Edge — procesor GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób rozwiązywania problemów z urządzeniem GPU Azure Stack Edge. 


## <a name="run-diagnostics"></a>Uruchamianie diagnostyki

Diagnozowanie i rozwiązywanie problemów związanych z błędami urządzenia jest możliwe dzięki testom diagnostycznym. Wykonaj następujące czynności w lokalnym internetowym interfejsie użytkownika, aby uruchomić testy diagnostyczne.

1. W lokalnym internetowym interfejsie użytkownika wybierz kolejno pozycje **Rozwiązywanie problemów > Testy diagnostyczne**. Wybierz test, który chcesz uruchomić, a następnie wybierz pozycję **Uruchom test**. Test diagnozuje wszelkie możliwe problemy z ustawieniami sieci, urządzeń, serwera proxy sieci Web, czasu lub chmury. Otrzymasz powiadomienie, że urządzenie wykonuje testy.

    ![Wybierz testy ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Po zakończeniu wykonywania testów zostaną wyświetlone wyniki. 

    ![Wyświetl wyniki testu](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Jeśli test zakończy się niepowodzeniem, zostanie wyświetlony adres URL zalecanej akcji. Wybierz adres URL, aby wyświetlić zalecaną akcję.
 
    ![Przejrzyj ostrzeżenia dotyczące testów zakończonych niepowodzeniem](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Zbieranie pakietu dla pomocy technicznej

Pakiet dziennika składa się ze wszystkich odpowiednich dzienników, które mogą okazać się przydatne dla pomocy technicznej firmy Microsoft podczas rozwiązywania różnego rodzaju problemów z urządzeniami. Pakiet dziennika można wygenerować za pomocą lokalnego internetowego interfejsu użytkownika.

Wykonaj następujące czynności, aby zebrać pakiet dla pomocy technicznej. 

1. W lokalnym internetowym interfejsie użytkownika wybierz kolejno pozycje **Rozwiązywanie problemów > Pomoc techniczna**. Wybierz pozycję **Utwórz pakiet pomocy technicznej**. System rozpoczyna zbieranie pakietu dla pomocy technicznej. Zbieranie pakietu może potrwać kilka minut.

    ![Wybierz pozycję Dodaj użytkownika](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Po utworzeniu pakietu dla pomocy technicznej wybierz pozycję **Pobierz pakiet pomocy technicznej**. Skompresowany pakiet zostanie pobrany w wybranej ścieżce. Można rozpakować pakiet i wyświetlić systemowe pliki dziennika.

    ![Wybierz pozycję Dodaj użytkownika 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Zbierz zaawansowane dzienniki zabezpieczeń

Zaawansowane dzienniki zabezpieczeń mogą być dziennikami programu wtargnięcia oprogramowania lub sprzętu dla urządzenia Azure Stack EDGE Pro.

### <a name="software-intrusion-logs"></a>Dzienniki włamania oprogramowania

W przypadku ruchu przychodzącego i wychodzącego są zbierane programowe lub domyślne Dzienniki zapory. 

- Po utworzeniu obrazu urządzenia w fabryce jest włączone domyślne rejestrowanie zapory. Te dzienniki są powiązane domyślnie z pakietem pomocy technicznej podczas tworzenia pakietu dla pomocy technicznej za pomocą lokalnego interfejsu użytkownika lub interfejsu programu Windows PowerShell na urządzeniu.

- Jeśli tylko Dzienniki zapory są potrzebne w pakiecie pomocy technicznej w celu przejrzenia wszelkich nieautoryzowanych oprogramowania w urządzeniu, użyj `-Include FirewallLog` opcji podczas tworzenia pakietu dla pomocy technicznej. 

- Jeśli nie zostanie podana żadna konkretna opcja include, Dziennik zapory jest domyślnie dołączany do pakietu dla pomocy technicznej.

- W pakiecie pomocy technicznej Dziennik zapory znajduje się i znajduje się `pfirewall.log` w folderze głównym. Oto przykład dziennika włamania oprogramowania dla urządzenia z Azure Stack Edge. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Dzienniki wtargnięcia sprzętowego

W celu wykrycia nieautoryzowanego sprzętu na urządzeniu są rejestrowane wszystkie zdarzenia obudów, takie jak otwieranie lub zamykanie obudowy. 

- Dziennik zdarzeń systemu z urządzenia jest odczytywany przy użyciu `racadm` polecenia cmdlet. Te zdarzenia są następnie filtrowane pod kątem zdarzenia dotyczącego obudów w `HWIntrusion.txt` pliku.

- Aby uzyskać dostęp do dziennika wtargnięcia sprzętowego w pakiecie pomocy technicznej, użyj `-Include HWSelLog` opcji podczas tworzenia pakietu dla pomocy technicznej. 

- Jeśli nie zostanie podana żadna konkretna opcja include, dziennik wtargnięcia sprzętowego jest domyślnie dołączany do pakietu dla pomocy technicznej.

- W pakiecie pomocy technicznej dziennik wtargnięcia sprzętowego jest i znajduje się `HWIntrusion.txt` w folderze głównym. Oto przykład dziennika nieautoryzowanego sprzętu dla urządzenia z Azure Stack Edge. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Rozwiązywanie problemów przy użyciu dzienników

Wszelkie błędy występujące podczas procesów przekazywania i odświeżania są uwzględniane w odpowiednich plikach błędów.

1. Aby wyświetlić pliki błędów, przejdź do udziału i wybierz udział, aby wyświetlić jego zawartość. 


2. Wybierz _folder Microsoft Data Box Edge_. Ten folder zawiera dwa podfoldery:

    - Folder przekazywania zawierający pliki dziennika w przypadku błędów przekazywania.
    - Folder odświeżania dla błędów podczas odświeżania.

    Oto przykładowy plik dziennika, który ma zostać odświeżony.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. W razie pojawienia się w tym pliku błędu (zaznaczonego w próbce), zanotuj jego kod — w tym przypadku jest to 16001. Sprawdź opis dla tego kodu błędu w odniesieniu do następujących informacji o błędzie.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Korzystanie z list błędów w celu rozwiązywania problemów

Listy błędów są kompilowane ze zidentyfikowanych scenariuszy i mogą być używane do samodiagnostyki i rozwiązywania problemów. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Poniżej przedstawiono błędy, które mogą pojawić się podczas konfigurowania Azure Resource Manager, aby uzyskać dostęp do urządzenia. 

| **Problem/błędy** |  **Rozwiązanie** | 
|------------|-----------------|
|Ogólne problemy|<li>[Upewnij się, że urządzenie brzegowe jest prawidłowo skonfigurowane](#verify-the-device-is-configured-properly).<li> [Sprawdź, czy klient jest prawidłowo skonfigurowany](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: Wystąpił błąd podczas wysyłania żądania.<br>W wierszu: 1 char: 1<br>+ Add-AzureRmEnvironment-Name Az3-ARMEndpoint " https://management.dbe ...|Ten błąd oznacza, że urządzenie Azure Stack EDGE Pro jest nieosiągalne lub nieprawidłowo skonfigurowane. Upewnij się, że urządzenie brzegowe i klient są poprawnie skonfigurowane. Aby uzyskać wskazówki, zobacz wiersz **Ogólne problemy** w tej tabeli.|
|Usługa zwróciła błąd. Sprawdź wyjątek wewnętrzny, aby uzyskać więcej szczegółów: Połączenie podstawowe zostało zamknięte: nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS. |   Ten błąd jest prawdopodobnie spowodowany niepoprawnym wykonaniem własnych kroków certyfikatu. Wskazówki można znaleźć [tutaj](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates). |
|Operacja zwróciła nieprawidłowy kod stanu "serviceniedostępny" <br> Kod stanu odpowiedzi nie wskazuje sukcesu: 503 (Usługa niedostępna). | Przyczyną tego błędu może być dowolny z tych warunków.<li>ArmStsPool jest w stanie zatrzymania.</li><li>Jedna z witryn sieci Web usług tokenów Azure Resource Manager/Security nie działa.</li><li>Zasób klastra Azure Resource Manager nie działa.</li><br><strong>Uwaga:</strong> Ponowne uruchomienie urządzenia może rozwiązać ten problem, ale należy zebrać pakiet pomocy technicznej, aby umożliwić jego dalsze debugowanie.|
|AADSTS50126: Nieprawidłowa nazwa użytkownika lub hasło.<br>Identyfikator śledzenia: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>Identyfikator korelacji: 1b9752c4-8cbf-4304-A714-8a16527410f4<br>Sygnatura czasowa: 2019-11-15 09:21:57Z: serwer zdalny zwrócił błąd: (400) złe żądanie.<br>W wierszu: 1 char: 1 |Przyczyną tego błędu może być dowolny z tych warunków.<li>W przypadku nieprawidłowej nazwy użytkownika i hasła Sprawdź, czy klient zmienił hasło z Azure Portal, wykonując kroki opisane w [tym miejscu](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password) , a następnie używając poprawnego hasła.<li>Dla nieprawidłowego identyfikatora dzierżawy identyfikator dzierżawy jest stałym identyfikatorem GUID i powinien być ustawiony na `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-AzureRmAccount: AADSTS90056: zasób jest wyłączony lub nie istnieje. Sprawdź kod aplikacji, aby upewnić się, że został określony dokładny adres URL zasobu dla zasobu, do którego próbujesz uzyskać dostęp.<br>Identyfikator śledzenia: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>Identyfikator korelacji: 75c8ef5a-830e-48b5-b039-595a96488ff9 sygnatura czasowa: 2019-11-18 07:00:51Z: serwer zdalny zwrócił błąd: (400) zły |Punkty końcowe zasobów użyte w `Add-AzureRmEnvironment` poleceniu są nieprawidłowe.|
|Nie można uzyskać punktów końcowych z chmury.<br>Upewnij się, że masz połączenie sieciowe. Szczegóły błędu: HTTPSConnectionPool (host = ' Management. dbg-of4k6suvm.microsoftdatabox.com ', port = 30005): przekroczono maksymalną liczbę ponownych prób przy użyciu adresu URL:/Metadata/Endpoints? API-Version = 2015-01-01 (spowodowany przez SSLError ("niewłaściwe uzgadnianie: błąd (" procedury SSL "," tls_process_server_certificate "," Weryfikowanie certyfikatu ")],)",) ") |Ten błąd występuje głównie w środowisku Mac/Linux i jest spowodowany następującymi problemami:<li>Certyfikat formatu PEM nie został dodany do magazynu certyfikatów języka Python.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Sprawdź, czy urządzenie jest prawidłowo skonfigurowane

1. W lokalnym interfejsie użytkownika Sprawdź, czy sieć urządzenia jest prawidłowo skonfigurowana.

2. Sprawdź, czy certyfikaty zostały zaktualizowane dla wszystkich punktów końcowych, jak wspomniano [tutaj](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

3. Pobierz Azure Resource Manager zarządzanie i punkt końcowy logowania ze strony **urządzenia** w lokalnym interfejsie użytkownika.

4. Sprawdź, czy urządzenie zostało aktywowane i zarejestrowane na platformie Azure.


### <a name="verify-the-client-is-configured-properly"></a>Sprawdź, czy klient jest prawidłowo skonfigurowany

1. Sprawdź, czy jest zainstalowana poprawna wersja programu PowerShell, jak wspomniano [tutaj](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client).

2. Sprawdź, czy poprawne moduły programu PowerShell są zainstalowane zgodnie z opisem w [tym miejscu](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client).

3. Sprawdź, czy Azure Resource Manager i punkty końcowe logowania są osiągalne. Możesz spróbować wysłać polecenie ping do punktów końcowych. Na przykład:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Jeśli nie są one dostępne, Dodaj wpisy pliku DNS/hosta, jak wspomniano [tutaj](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. Sprawdź, czy certyfikaty klienta zostały zainstalowane zgodnie z opisem w [tym miejscu](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell).

5. Jeśli klient korzysta z programu PowerShell, należy włączyć preferencję debugowania, aby wyświetlić szczegółowe komunikaty przez uruchomienie tego polecenia programu PowerShell. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Blob Storage na urządzeniu 

Poniżej przedstawiono błędy związane z usługą BLOB Storage na urządzeniu Azure Stack EDGE Pro/Data Box Gateway.

| **Problem/błędy** |  **Rozwiązanie** | 
|--------------------|-----------------|
|Nie można pobrać zasobów podrzędnych. Wartość jednego z nagłówków HTTP nie ma poprawnego formatu.| Z menu **Edycja** wybierz pozycję **docelowa Azure Stack interfejsy API**. Następnie ponownie uruchom Eksplorator usługi Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` została dodana do pliku hosts w tej ścieżce: `C:\Windows\System32\drivers\etc\hosts` w systemie Windows lub `/etc/hosts` w systemie Linux.|
|Nie można pobrać zasobów podrzędnych.<br> Szczegóły: certyfikat z podpisem własnym |Zaimportuj certyfikat SSL dla urządzenia do Eksplorator usługi Azure Storage: <ol><li>Pobierz certyfikat z Azure Portal. Aby uzyskać więcej informacji, zobacz [Pobieranie certyfikatu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Z menu **Edycja** wybierz pozycję Certyfikaty SSL, a następnie wybierz pozycję **Importuj certyfikaty**.</li></ol>|
|AzCopy polecenie wydaje się przestać odpowiadać przez minutę przed wyświetleniem tego błędu:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` została dodana do pliku hosts w lokalizacji: `C:\Windows\System32\drivers\etc\hosts` .|
|AzCopy polecenie wydaje się przestać odpowiadać przez minutę przed wyświetleniem tego błędu:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Zaimportuj certyfikat SSL dla urządzenia do magazynu certyfikatów systemu. Aby uzyskać więcej informacji, zobacz [Pobieranie certyfikatu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Polecenie AzCopy wydaje się przestać odpowiadać przez 20 minut przed wyświetleniem tego błędu:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` została dodana do pliku hosts w lokalizacji: `/etc/hosts` .|
|Polecenie AzCopy wydaje się przestać odpowiadać przez 20 minut przed wyświetleniem tego błędu:<br>`Error parsing source location… The SSL connection could not be established`. |Zaimportuj certyfikat SSL dla urządzenia do magazynu certyfikatów systemu. Aby uzyskać więcej informacji, zobacz [Pobieranie certyfikatu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Polecenie AzCopy wydaje się przestać odpowiadać przez 20 minut przed wyświetleniem tego błędu:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Sprawdź, czy nazwa punktu końcowego `<accountname>.blob.<serialnumber>.microsoftdatabox.com` została dodana do pliku hosts w lokalizacji: `/etc/hosts` .|
|Polecenie AzCopy wydaje się przestać odpowiadać przez 20 minut przed wyświetleniem tego błędu: `Error parsing source location… The SSL connection could not be established` .|Zaimportuj certyfikat SSL dla urządzenia do magazynu certyfikatów systemu. Aby uzyskać więcej informacji, zobacz [Pobieranie certyfikatu](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|Wartość jednego z nagłówków HTTP nie ma poprawnego formatu.|Zainstalowana wersja biblioteki Microsoft Azure Storage dla języka Python nie jest obsługiwana przez urządzenie Data Box. Zobacz Azure Data Box wymagania dotyczące magazynu obiektów BLOB dla obsługiwanych wersji.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| Przed uruchomieniem języka Python należy ustawić zmienną środowiskową REQUESTS_CA_BUNDLE na ścieżkę pliku certyfikatu SSL zakodowanego algorytmem Base64 (Zobacz artykuł jak [pobrać certyfikat](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Na przykład:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Alternatywnie Dodaj certyfikat do magazynu certyfikatów systemu, a następnie ustaw tę zmienną środowiskową na ścieżkę do tego magazynu. Na przykład na platformie Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|Upłynął limit czasu połączenia.|Zaloguj się do Azure Stack Edge, a następnie sprawdź, czy jest odblokowany. Gdy urządzenie zostanie ponownie uruchomione, pozostaje ono zablokowane, dopóki ktoś się nie zaloguje.|

## <a name="troubleshoot-iot-edge-errors"></a>Rozwiązywanie problemów z błędami IoT Edge

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [rozwiązywania problemów dotyczących aktywacji urządzeń](azure-stack-edge-gpu-troubleshoot-activation.md).

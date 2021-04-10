---
title: Rozwiązywanie problemów z zabezpieczeniami i kontrolami dostępu
description: Dowiedz się, jak rozwiązywać problemy z zabezpieczeniami i kontrolą dostępu w Azure Data Factory.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: 5e94ea989002d3d3c6d0e96123d5b8ddb5f078c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568039"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Rozwiązywanie problemów dotyczących zabezpieczeń Azure Data Factory i kontroli dostępu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów dotyczących zabezpieczeń i kontroli dostępu w programie Azure Data Factory.

## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Problem z łącznością w działaniu kopiowania magazynu danych w chmurze

#### <a name="symptoms"></a>Objawy

W przypadku wystąpienia problemów z łącznością w źródłowym lub ujścia danych magazynu mogą zostać zwrócone różne komunikaty o błędach.

#### <a name="cause"></a>Przyczyna 

Problem jest zwykle spowodowany przez jeden z następujących czynników:

* Ustawienie serwera proxy w węźle środowisko Integration Runtime (IR), jeśli używasz własnego środowiska IR.

* Ustawienie zapory w węźle samodzielnego środowiska IR, jeśli używasz własnego środowiska IR.

* Ustawienie zapory w magazynie danych w chmurze.

#### <a name="resolution"></a>Rozwiązanie

* Aby upewnić się, że jest to problem z połączeniem, sprawdź następujące kwestie:

   - Błąd jest zgłaszany z łączników źródła lub ujścia.
   - Awaria znajduje się na początku działania kopiowania.
   - Awaria jest spójna w przypadku Azure IR lub samodzielnego środowiska IR z jednym węzłem, ponieważ może to być błędna awaria w przypadku samodzielnego hosta z wieloma węzłami, jeśli problem występuje tylko w niektórych węzłach.

* Jeśli używasz własnego środowiska **IR**, sprawdź ustawienia serwera proxy, zapory i sieci, ponieważ połączenie z tym samym magazynem danych może się powieść, jeśli używasz Azure IR. Aby rozwiązać problem z tym scenariuszem, zobacz:

   * [Samoobsługowe porty i zapory IR](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Łącznik Azure Data Lake Storage](./connector-azure-data-lake-store.md)
  
* Jeśli używasz **Azure IR**, spróbuj wyłączyć ustawienie zapory dla magazynu danych. Takie podejście może rozwiązać problemy w następujących dwóch sytuacjach:
  
   * [Adresy IP Azure IR](./azure-integration-runtime-ip-addresses.md) nie znajdują się na liście dozwolonych.
   * Funkcja *Zezwalanie na dostęp do tego konta usługi Microsoft zaufane* są wyłączone dla [platformy Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) i [Azure Data Lake Storage generacji 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * Ustawienie *Zezwalaj na dostęp do usług platformy Azure* nie jest włączone dla Azure Data Lake Storage Gen1.

Jeśli żadna z powyższych metod nie działa, skontaktuj się z firmą Microsoft w celu uzyskania pomocy.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Nieprawidłowy lub pusty klucz uwierzytelniania po wyłączeniu dostępu do sieci publicznej

#### <a name="symptoms"></a>Objawy

Po wyłączeniu dostępu do sieci publicznej dla Data Factory środowisko Integration Runtime (własne) zgłasza następujący błąd: "klucz uwierzytelniania jest nieprawidłowy lub pusty".

#### <a name="cause"></a>Przyczyna

Problem jest najprawdopodobniej spowodowany przez problem z rozpoznawaniem systemu nazw domen (DNS), ponieważ wyłączenie łączności publicznej i ustanowienie prywatnego punktu końcowego uniemożliwia ponowne nawiązanie połączenia.

Aby sprawdzić, czy Data Factory w pełni kwalifikowana nazwa domeny (FQDN) jest rozpoznawana jako publiczny adres IP, wykonaj następujące czynności:

1. Upewnij się, że utworzono maszynę wirtualną platformy Azure w tej samej sieci wirtualnej co Data Factory prywatny punkt końcowy.

2. Uruchom PsPing i polecenie ping z maszyny wirtualnej platformy Azure do Data Factory FQDN:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Należy określić port dla polecenia PsPing. W tym miejscu jest wyświetlany port 443, ale nie jest to wymagane.

3. Sprawdź, czy oba polecenia rozwiązują Azure Data Factory publicznego adresu IP, który jest oparty na określonym regionie. Adres IP powinien mieć następujący format: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj następujące czynności:

- Jako opcję chcę zasugerować ręczne dodanie linku "Virtual Network" w obszarze Data Factory "prywatne łącze DNS strefy". Aby uzyskać szczegółowe informacje, zapoznaj się z artykułem [prywatnym usługi Azure dla Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints) artykułu. Instrukcja służy do konfigurowania prywatnej strefy DNS lub niestandardowego serwera DNS w celu rozpoznawania Data Factory nazwy FQDN na prywatny adres IP. 

- Jeśli jednak nie chcesz konfigurować prywatnej strefy DNS ani niestandardowego serwera DNS, wypróbuj następujące tymczasowe rozwiązanie:

  1. Zmień plik hosta w systemie Windows i Mapuj prywatny adres IP (Azure Data Factory prywatny punkt końcowy) na Azure Data Factory FQDN.
  
     Na maszynie wirtualnej platformy Azure przejdź do `C:\Windows\System32\drivers\etc` , a następnie otwórz plik *hosta* w Notatniku. Dodaj wiersz, który mapuje prywatny adres IP na nazwę FQDN na końcu pliku, a następnie Zapisz zmiany.
     
     ![Zrzut ekranu przedstawiający mapowanie prywatnego adresu IP na host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Uruchom ponownie te same polecenia jak w poprzednich krokach weryfikacji, aby sprawdzić odpowiedź, która powinna zawierać prywatny adres IP.

  1. Zarejestruj ponownie środowisko Integration Runtime, a problem powinien zostać rozwiązany.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nie można zarejestrować klucza uwierzytelniania IR na własnych maszynach wirtualnych z powodu prywatnego linku

#### <a name="symptoms"></a>Objawy

Nie można zarejestrować klucza uwierzytelniania IR na samohostowanej maszynie wirtualnej, ponieważ link prywatny jest włączony. Zostanie wyświetlony następujący komunikat o błędzie:

"Nie można pobrać tokenu usługi z usługi ADF z kluczem * * * * * * * * * * * * * * * i kosztem czasu: 0,1250079 sekunda kod błędu to: InvalidGatewayKey, activityId to: XXXXXXX i szczegółowy komunikat o błędzie to adres IP klienta nie jest prawidłowym prywatnym adresem IP, ponieważ Fabryka danych nie może uzyskać dostępu do sieci publicznej, dlatego nie można skontaktować się z chmurą w celu pomyślnego nawiązania połączenia".

#### <a name="cause"></a>Przyczyna

Problem może być spowodowany przez maszynę wirtualną, w której próbujesz zainstalować własne środowisko IR. Aby nawiązać połączenie z chmurą, upewnij się, że jest włączony dostęp do sieci publicznej.

#### <a name="resolution"></a>Rozwiązanie

**Rozwiązanie 1**
 
Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Przejdź do strony [fabryki — aktualizacja](/rest/api/datafactory/Factories/Update) .

1. W prawym górnym rogu wybierz przycisk **Wypróbuj** .
1. W obszarze **Parametry** wykonaj wymagane informacje. 
1. W obszarze **treść** wklej następującą właściwość:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Wybierz pozycję **Uruchom** , aby uruchomić funkcję. 

1. W obszarze **Parametry** wykonaj wymagane informacje. 

1. W obszarze **treść** wklej następującą właściwość:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Wybierz pozycję **Uruchom** , aby uruchomić funkcję. 
1. Upewnij się, że jest wyświetlany **kod odpowiedzi: 200** . Wklejona właściwość powinna również być wyświetlana w definicji JSON.

1. Ponownie Dodaj klucz uwierzytelniania IR w środowisku Integration Runtime.

**Rozwiązanie 2**

Aby rozwiązać ten problem, przejdź do [prywatnego linku platformy Azure dla Azure Data Factory](./data-factory-private-link.md).

Spróbuj włączyć dostęp do sieci publicznej w interfejsie użytkownika, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający kontrolkę "Enabled" dla "Zezwalaj na dostęp do sieci publicznej" w okienku sieć.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>Strefa prywatna DNS usługi ADF przesłania Azure Resource Manager rozpoznawania nazw DNS powodujących błąd "nie znaleziono"

#### <a name="cause"></a>Przyczyna
Zarówno Azure Resource Manager, jak i ADF używają tej samej strefy prywatnej tworzącej potencjalne konflikty w prywatnym systemie DNS klienta w scenariuszu, w którym nie można znaleźć rekordów Azure Resource Manager.

#### <a name="solution"></a>Rozwiązanie
1. Znajdź Prywatna strefa DNS strefy **privatelink.Azure.com** w Azure Portal.
![Zrzut ekranu przedstawiający Znajdowanie stref Prywatna strefa DNS.](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. Sprawdź, czy jest dostępny **podajnik APD**.
![Zrzut ekranu przedstawiający rekord.](media/security-access-control-troubleshoot-guide/a-record.png)
3.  Przejdź do pozycji **linki sieci wirtualnej**, a następnie usuń wszystkie rekordy.
![Zrzut ekranu przedstawiający łącze sieci wirtualnej.](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Przejdź do fabryki danych w Azure Portal i Utwórz ponownie prywatny punkt końcowy dla Azure Data Factory portalu.
![Zrzut ekranu przedstawiający odtwarzanie prywatnego punktu końcowego.](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  Wróć do obszaru Prywatna strefa DNS strefy i sprawdź, czy istnieje Nowa prywatna strefa DNS **privatelink.ADF.Azure.com**.
![Zrzut ekranu przedstawiający nowy rekord DNS.](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>Błąd połączenia w publicznym punkcie końcowym

#### <a name="symptoms"></a>Objawy

W przypadku kopiowania danych za pomocą konta usługi Azure Blob Storage dostęp publiczny, uruchomienie potoku losowo kończy się niepowodzeniem z powodu następującego błędu.

Na przykład: obiekt ujścia Blob Storage platformy Azure używa Azure IR (publiczna, niezarządzana Sieć wirtualna), a źródło Azure SQL Database korzysta z zarządzanego środowiska IR sieci wirtualnej. Lub źródło/ujścia używaj zarządzanej sieci wirtualnej IR tylko z dostępem publicznym magazynu.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Przyczyna

Funkcja ADF może nadal używać zarządzanej sieci wirtualnej, ale można napotkać ten błąd, ponieważ publiczny punkt końcowy na platformie Azure Blob Storage w zarządzanej sieci wirtualnej nie jest niezawodny w oparciu o wynik testu, a usługi Azure Blob Storage i Azure Data Lake Gen2 nie są obsługiwane przez publiczny punkt końcowy w przypadku zarządzanych przez usługę ADF Virtual Network z zarządzaną [siecią wirtualną & zarządzanych prywatnych punktów końcowych](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network).

#### <a name="solution"></a>Rozwiązanie

- Posiadanie prywatnego punktu końcowego z włączonym źródłem, a także po stronie ujścia przy użyciu zarządzanego środowiska IR sieci wirtualnej.
- Jeśli nadal chcesz używać publicznego punktu końcowego, możesz przełączyć się do publicznego środowiska IR, a nie przy użyciu środowiska IR zarządzanej sieci wirtualnej dla źródła i ujścia. Nawet jeśli przełączysz się z powrotem do publicznego środowiska IR, ADF może nadal korzystać z zarządzanego środowiska IR sieci wirtualnej, jeśli nadal istnieje zarządzana Sieć wirtualna sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Prywatny link do Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&stronie](/answers/topics/azure-data-factory.html)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
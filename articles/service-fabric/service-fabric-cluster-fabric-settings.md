---
title: Zmień ustawienia klastra Service Fabric platformy Azure
description: W tym artykule opisano ustawienia sieci szkieletowej oraz zasady uaktualniania sieci szkieletowej, które można dostosować.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: a83d24b4badd78750756a3cb4564b1e53fd30593
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648229"
---
# <a name="customize-service-fabric-cluster-settings"></a>Dostosowywanie ustawień klastra usługi Service Fabric
W tym artykule opisano różne ustawienia sieci szkieletowej dla klastra Service Fabric, które można dostosować. W przypadku klastrów hostowanych na platformie Azure można dostosować ustawienia za pomocą [Azure Portal](https://portal.azure.com) lub szablonu Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [uaktualnianie konfiguracji klastra platformy Azure](service-fabric-cluster-config-upgrade-azure.md). W przypadku klastrów autonomicznych można dostosować ustawienia przez aktualizację *ClusterConfig.jsw* pliku i przeprowadzanie uaktualnienia konfiguracji w klastrze. Aby uzyskać więcej informacji, zobacz [uaktualnianie konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).

Istnieją trzy różne zasady uaktualniania:

- **Dynamiczne** — zmiany w konfiguracji dynamicznej nie powodują ponownego uruchomienia procesu Service Fabric procesów lub procesów hosta usługi. 
- **Statyczny** — zmiany w konfiguracji statycznej spowodują ponowne uruchomienie węzła Service Fabric w celu użycia zmiany. Usługi w węzłach zostaną ponownie uruchomione.
- **Nołój** — nie można modyfikować tych ustawień. Zmiana tych ustawień wymaga zniszczenia klastra i utworzenia nowego klastra. 

Poniżej znajduje się lista ustawień sieci szkieletowej, które można dostosować, zorganizowane według sekcji.

## <a name="applicationgatewayhttp"></a>Elementu applicationgateway/http

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|ciąg, wartość domyślna to "Brak"|Static| Nie powoduje to zweryfikowania certyfikatu serwera; żądanie zostało zakończone pomyślnie. Zapoznaj się z konfiguracją ServiceCertificateThumbprints dla oddzielonych przecinkami list odcisków palca certyfikatów zdalnych, które może ufać zwrotnego serwera proxy. Zapoznaj się z tematem Konfiguracja ServiceCommonNameAndIssuer dla nazwy podmiotu i odcisku palca certyfikatów zdalnych, które może ufać zwrotnego serwera proxy. Aby dowiedzieć się więcej, zobacz [bezpieczny połączenia zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, wartość domyślna to 16384 |Dynamiczny| Zwraca rozmiar fragmentu w bajtach używany do odczytywania treści. |
|CrlCheckingFlag|uint, wartość domyślna to 0x40000000 |Dynamiczny| Flagi weryfikacji łańcucha certyfikatów aplikacji/usług; na przykład sprawdzanie listy CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY ustawieniem 0 wyłącza sprawdzanie listy CRL pełna lista obsługiwanych wartości jest udokumentowana przez flagiDW CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Czas w sekundach. wartość domyślna to 120 |Dynamiczny|Określ wartość TimeSpan w sekundach.  Zwraca domyślny limit czasu żądania dla żądań HTTP przetwarzanych w bramie aplikacji protokołu HTTP. |
|ForwardClientCertificate|bool, wartość domyślna to FALSE|Dynamiczny|Po ustawieniu na wartość false, zwrotny serwer proxy nie będzie żądać certyfikatu klienta. Po ustawieniu na wartość true zwrotny serwer proxy wyśle żądanie certyfikatu klienta podczas uzgadniania protokołu TLS i przekazuje ciąg formatu PEM szyfrowany algorytmem Base64 do usługi w nagłówku o nazwie X-Client-Certificate. po sprawdzeniu danych certyfikatu usługa może zakończyć żądanie przy użyciu odpowiedniego kodu stanu. Jeśli jest to prawdziwe, a klient nie zaprezentuje certyfikatu, zwrotny serwer proxy przejdzie do pustego nagłówka i zezwoli usłudze na obsługę tego przypadku. Zwrotny serwer proxy będzie pełnić funkcję warstwy przezroczystej. Aby dowiedzieć się więcej, zobacz [Konfigurowanie uwierzytelniania certyfikatu klienta](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |ciąg, wartość domyślna to "Brak" |Static| Wskazuje typ poświadczeń zabezpieczeń do użycia w prawidłowych wartościach punktu końcowego bramy aplikacji protokołu HTTP to None/x509. |
|GatewayX509CertificateFindType |ciąg, wartość domyślna to "FindByThumbprint" |Dynamiczny| Wskazuje, w jaki sposób wyszukiwać certyfikat w magazynie określonym przez GatewayX509CertificateStoreName obsługiwaną wartość: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | ciąg, wartość domyślna to "" |Dynamiczny| Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy App Gateway. Ten certyfikat jest skonfigurowany w punkcie końcowym https i może służyć do weryfikowania tożsamości aplikacji, jeśli jest to konieczne przez usługi. FindValue jest najpierw wyszukiwany. i jeśli nie istnieje; FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy App Gateway. Ten certyfikat jest skonfigurowany w punkcie końcowym https i może służyć do weryfikowania tożsamości aplikacji, jeśli jest to konieczne przez usługi. FindValue jest najpierw wyszukiwany. i jeśli nie istnieje; FindValueSecondary.|
|GatewayX509CertificateStoreName |ciąg, wartość domyślna to "my" |Dynamiczny| Nazwa magazynu certyfikatów X. 509 zawierającego certyfikat dla usługi http App Gateway. |
|HttpRequestConnectTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (5)|Dynamiczny|Określ wartość TimeSpan w sekundach.  Zwraca limit czasu połączenia dla żądań HTTP wysyłanych z bramy aplikacji http.  |
|IgnoreCrlOfflineError|bool, wartość domyślna to TRUE|Dynamiczny|Czy zignorować błąd offline listy CRL dla weryfikacji certyfikatu aplikacji/usługi. |
|IsEnabled |Bool, wartość domyślna to false |Static| Włącza/wyłącza HttpApplicationGateway. HttpApplicationGateway jest domyślnie wyłączona i należy ustawić tę konfigurację, aby ją włączyć. |
|NumberOfParallelOperations | Uint, wartość domyślna to 5000 |Static|Liczba odczytów do opublikowania w kolejce serwera http. Określa liczbę równoczesnych żądań, które mogą być spełnione przez HttpGateway. |
|RemoveServiceResponseHeaders|ciąg, wartość domyślna to "Date; Server|Static|Rozdzielana średnikami/rozdzielana przecinkami lista nagłówków odpowiedzi, które zostaną usunięte z odpowiedzi usługi; przed przesłaniem ich do klienta. Jeśli ta wartość jest ustawiona na pusty ciąg; Przekaż wszystkie nagłówki zwrócone przez usługę jako-is. co nie zastępuj daty i serwera |
|ResolveServiceBackoffInterval |Czas w sekundach, wartość domyślna to 5 |Dynamiczny|Określ wartość TimeSpan w sekundach.  Zwraca domyślny interwał wycofywania przed ponowną próbą wykonania nieudanej operacji rozwiązania. |
|SecureOnlyMode|bool, wartość domyślna to FALSE|Dynamiczny| SecureOnlyMode: true: zwrotny serwer proxy będzie przesyłany tylko do usług, które publikują bezpieczne punkty końcowe. false: zwrotny serwer proxy może przesyłać żądania do bezpiecznych/niezabezpieczonych punktów końcowych. Aby dowiedzieć się więcej, zobacz [logika wyboru zwrotnego punktu końcowego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|ciąg, wartość domyślna to ""|Dynamiczny|Rozdzielana przecinkami lista odcisków palców zdalnych certyfikatów, które może ufać zwrotnego serwera proxy. Aby dowiedzieć się więcej, zobacz [bezpieczny połączenia zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>Elementu applicationgateway/http/ServiceCommonNameAndIssuer

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, wartość domyślna to None|Dynamiczny| Nazwa podmiotu i odcisk palca certyfikatów zdalnych, które może ufać zwrotnego serwera proxy. Aby dowiedzieć się więcej, zobacz [bezpieczny połączenia zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, wartość domyślna to 0|Static|MinReplicaSetSize dla BackupRestoreService |
|PlacementConstraints|ciąg, wartość domyślna to ""|Static|    Usługa PlacementConstraints dla BackupRestore |
|SecretEncryptionCertThumbprint|ciąg, wartość domyślna to ""|Dynamiczny|Odcisk palca certyfikatu x509 szyfrowania klucza tajnego |
|SecretEncryptionCertX509StoreName|ciąg, Zalecana wartość to "my" (brak wartości domyślnej) |    Dynamiczny|    Wskazuje certyfikat używany do szyfrowania i odszyfrowywania nazwy poświadczeń w magazynie certyfikatów X. 509, który jest używany do szyfrowania poświadczeń magazynu, używanych przez usługę przywracania kopii zapasowych |
|Wartość targetreplicasetsize|int, wartość domyślna to 0|Static| Wartość targetreplicasetsize dla BackupRestoreService |

## <a name="clustermanager"></a>Clustermanager

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, wartość domyślna to false |Dynamiczny|Czy są dozwolone niestandardowe zasady sortowania uaktualnień. Jest to używane do przeprowadzania uaktualnień 2-fazowych, włączając tę funkcję. Service Fabric 6,5 dodaje obsługę określania zasad sortowania dla domen uaktualnienia podczas uaktualniania klastra lub aplikacji. Obsługiwane zasady to numeryczne, lexicographical, ReverseNumeric i ReverseLexicographical. Wartość domyślna to Numeric. Aby można było korzystać z tej funkcji, dla ustawienia Clustermanager/AllowCustomUpgradeSortPolicies musi być ustawiona wartość true jako druga czynność uaktualnienia konfiguracji po zakończeniu uaktualniania kodu SF 6,5. Należy pamiętać, że ta czynność odbywa się w dwóch fazach. w przeciwnym razie uaktualnienie kodu może być mylone z kolejnością uaktualniania podczas pierwszego uaktualnienia.|
|EnableDefaultServicesUpgrade | Bool, wartość domyślna to false |Dynamiczny|Włącz uaktualnianie usług domyślnych podczas uaktualniania aplikacji. Domyślne opisy usług zostaną nadpisane po uaktualnieniu. |
|FabricUpgradeHealthCheckInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość sprawdzania stanu kondycji podczas monitorowania monitorowanej sieci szkieletowej |
|FabricUpgradeStatusPollInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość sondowania stanu uaktualnienia sieci szkieletowej. Ta wartość określa częstotliwość aktualizacji dla dowolnego wywołania GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Czas w sekundach, wartość domyślna to 3 |Dynamiczny|Określ wartość TimeSpan w sekundach. Ilość czasu zezwalająca na powrót błędów limitu czasu dla określonego konstruktora obrazu do klienta programu. Jeśli bufor jest za mały; Następnie klient przekroczy limit czasu, zanim serwer wyjdzie z ogólnego błędu. |
|InfrastructureTaskHealthCheckRetryTimeout | Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ wartość TimeSpan w sekundach. Czas poświęcany na ponowienie nieudanych testów kondycji podczas przetwarzania zadania infrastruktury. Zaobserwowanie przekazaną kontrolę kondycji spowoduje zresetowanie tego czasomierza. |
|InfrastructureTaskHealthCheckStableDuration | Czas w sekundach, wartość domyślna to 0|Dynamiczny| Określ wartość TimeSpan w sekundach. Czas, przez który należy obserwować kolejne testy kondycji przed przetworzeniem zadania infrastruktury zakończone powodzeniem. Obserwowanie nieudanych kontroli kondycji spowoduje zresetowanie tego czasomierza. |
|InfrastructureTaskHealthCheckWaitDuration |Czas w sekundach, wartość domyślna to 0|Dynamiczny| Określ wartość TimeSpan w sekundach. Czas oczekiwania przed rozpoczęciem kontroli kondycji po wykonaniu zadania infrastruktury po zakończeniu przetwarzania. |
|InfrastructureTaskProcessingInterval | Czas w sekundach, wartość domyślna to 10 |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał przetwarzania używany przez maszynę stanu przetwarzania zadania infrastruktury. |
|MaxCommunicationTimeout |Czas w sekundach, wartość domyślna to 600 |Dynamiczny|Określ wartość TimeSpan w sekundach. Maksymalny limit czasu komunikacji wewnętrznej między klastrami i innymi usługami systemowymi (tj.; Usługa nazewnictwa; Menedżer trybu failover i itp.). Ten limit czasu powinien być mniejszy niż globalny MaxOperationTimeout (ponieważ może istnieć wiele komunikacji między składnikami systemowymi dla każdej operacji klienta). |
|MaxDataMigrationTimeout |Czas w sekundach, wartość domyślna to 600 |Dynamiczny|Określ wartość TimeSpan w sekundach. Maksymalny limit czasu operacji odzyskiwania migracji danych po przeprowadzeniu uaktualnienia sieci szkieletowej. |
|MaxOperationRetryDelay |Czas w sekundach, wartość domyślna to 5|Dynamiczny| Określ wartość TimeSpan w sekundach. Maksymalne opóźnienie wewnętrznych ponownych prób w przypadku wystąpienia błędów. |
|MaxOperationTimeout |Czas w sekundach, wartość domyślna to MaxValue |Dynamiczny| Określ wartość TimeSpan w sekundach. Maksymalny globalny limit czasu dla wewnętrznych operacji przetwarzania na serwerze Clustermanager. |
|MaxTimeoutRetryBuffer | Czas w sekundach, wartość domyślna to 600 |Dynamiczny|Określ wartość TimeSpan w sekundach. Maksymalny limit czasu operacji podczas wewnętrznego ponawiania próby z powodu przekroczeń limitu czasu wynosi `<Original Time out> + <MaxTimeoutRetryBuffer>` . Dodatkowy limit czasu jest dodawany w przyrostach MinOperationTimeout. |
|MinOperationTimeout | Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ wartość TimeSpan w sekundach. Minimalny limit czasu globalnego operacji przetwarzania wewnętrznego w programie Clustermanager. |
|MinReplicaSetSize |Int, wartość domyślna to 3 |Niedozwolone|MinReplicaSetSize dla Klastramanager. |
|PlacementConstraints | ciąg, wartość domyślna to "" |Niedozwolone|PlacementConstraints dla Klastramanager. |
|QuorumLossWaitDuration |Czas w sekundach, wartość domyślna to MaxValue |Niedozwolone| Określ wartość TimeSpan w sekundach. QuorumLossWaitDuration dla Klastramanager. |
|ReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to (60,0 \* 30)|Niedozwolone|Określ wartość TimeSpan w sekundach. ReplicaRestartWaitDuration dla Klastramanager. |
|ReplicaSetCheckTimeoutRollbackOverride |Czas w sekundach, wartość domyślna to 1200 |Dynamiczny| Określ wartość TimeSpan w sekundach. Jeśli ReplicaSetCheckTimeout jest ustawiona na wartość maksymalną wartości DWORD; następnie zostanie zastąpiona wartością tej konfiguracji na potrzeby wycofywania. Wartość użyta do przewrócenia do przodu nigdy nie jest zastępowana. |
|SkipRollbackUpdateDefaultService | Bool, wartość domyślna to false |Dynamiczny|Program CM pominie przywrócenie zaktualizowanych usług domyślnych podczas wycofywania uaktualniania aplikacji. |
|StandByReplicaKeepDuration | Czas w sekundach, wartość domyślna to (3600,0 \* 2)|Niedozwolone|Określ wartość TimeSpan w sekundach. StandByReplicaKeepDuration dla Klastramanager. |
|Wartość targetreplicasetsize |Int, wartość domyślna to 7 |Niedozwolone|Wartość targetreplicasetsize dla Klastramanager. |
|UpgradeHealthCheckInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość sprawdzania stanu kondycji podczas uaktualniania monitorowanych aplikacji |
|UpgradeStatusPollInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość sondowania stanu uaktualnienia aplikacji. Ta wartość określa częstotliwość aktualizacji dla dowolnego wywołania GetApplicationUpgradeProgress |
|CompleteClientRequest | Bool, wartość domyślna to false |Dynamiczny| Ukończ żądanie klienta po jego zaakceptowaniu przez CM. |

## <a name="common"></a>Wspólne

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Czas w sekundach, wartość domyślna to 1 |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał monitorowania wydajności. Ustawienie wartości 0 lub ujemnej powoduje wyłączenie monitorowania. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, wartość domyślna to None|Dynamiczny|Określa poniższą defragmentację zasad podczas opróżniania węzłów. Dla danej metryki 0 wskazuje, że SF powinien próbować defragmentować węzły równomiernie w poprzek i domenami błędów; 1 wskazuje tylko, że węzły muszą być pofragmentowane |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, wartość domyślna to None|Dynamiczny|Określa zestaw metryk, które mają być używane do defragmentacji, a nie do równoważenia obciążenia. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, wartość domyślna to None|Dynamiczny|Określa liczbę wolnych węzłów, które są konieczne do uwzględnienia defragmentacji klastra przez określenie procentu z zakresu [0,0-1,0] lub liczby pustych węzłów jako liczby >= 1,0 |

## <a name="diagnostics"></a>Diagnostyka

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, wartość domyślna to true | Dynamiczny | Wyklucz żądania HTTP, które nie mają wpływu na stan klastra z inspekcji. Aktualne tylko żądania typu "GET" są wykluczone; może to jednak ulec zmianie. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, wartość domyślna to true | Dynamiczny |Określa, czy personifikacja jest wymagana podczas uzyskiwania dostępu do magazynów diagnostycznych w imieniu aplikacji. |
|AppEtwTraceDeletionAgeInDays |Int, wartość domyślna to 3 | Dynamiczny |Liczba dni, po usunięciu starych plików ETL zawierających ślady ETW aplikacji. |
|ApplicationLogsFormatVersion |Int, wartość domyślna to 0 | Dynamiczny |Wersja formatu dzienników aplikacji. Obsługiwane wartości to 0 i 1. Wersja 1 zawiera więcej pól z rekordu zdarzenia ETW niż wersja 0. |
|AuditHttpRequests |Bool, wartość domyślna to false | Dynamiczny | Włącz lub Wyłącz inspekcję HTTP. Celem inspekcji jest wyświetlenie działań, które zostały wykonane względem klastra; dołączenie do osoby, która zainicjowała żądanie. Należy zauważyć, że jest to Najlepsza próba zarejestrowania; mogą wystąpić utracone wyniki. Żądania HTTP z uwierzytelnianiem użytkownika nie są rejestrowane. |
|CaptureHttpTelemetry|Bool, wartość domyślna to true | Dynamiczny | Włączać lub wyłączać dane telemetryczne protokołu HTTP. Celem telemetrii jest umożliwienie Service Fabric przechwycenia danych telemetrycznych w celu zaplanowania przyszłej pracy i zidentyfikowania obszarów problemów. Telemetrię nie rejestruje żadnych danych osobowych ani treści żądania. Dane telemetryczne przechwytują wszystkie żądania HTTP, o ile nie zostały skonfigurowane inaczej |
|ClusterId |String | Dynamiczny |Unikatowy identyfikator klastra. Ta wartość jest generowana podczas tworzenia klastra. |
|ConsumerInstances |String | Dynamiczny |Lista wystąpień konsumentów DCA. |
|DiskFullSafetySpaceInMB |Int, wartość domyślna to 1024 | Dynamiczny |Pozostała ilość miejsca na dysku (w MB) do ochrony przed użyciem przez DCA. |
|EnableCircularTraceSession |Bool, wartość domyślna to false | Static |Flaga wskazuje, czy należy używać cyklicznych sesji śledzenia. |
|EnablePlatformEventsFileSink |Bool, wartość domyślna to false | Static |Włączanie/wyłączanie zdarzeń platformy zapisywanych na dysku |
|EnableTelemetry |Bool, wartość domyślna to true | Dynamiczny |Spowoduje to włączenie lub wyłączenie telemetrii. |
|FailuresOnlyHttpTelemetry | Bool, wartość domyślna to false | Dynamiczny | Jeśli funkcja przechwytywania telemetrii HTTP jest włączona; Przechwyć tylko Nieudane żądania. Jest to pomocne w zmniejszeniu liczby zdarzeń wygenerowanych na potrzeby telemetrii. |
|HttpTelemetryCapturePercentage | int, wartość domyślna to 50 | Dynamiczny | Jeśli funkcja przechwytywania telemetrii HTTP jest włączona; Przechwyć tylko losową wartość procentową żądań. Jest to pomocne w zmniejszeniu liczby zdarzeń wygenerowanych na potrzeby telemetrii. |
|MaxDiskQuotaInMB |Int, wartość domyślna to 65536 | Dynamiczny |Przydział dysku w MB dla plików dziennika Windows Fabric. |
|ProducerInstances |String | Dynamiczny |Lista wystąpień producentów DCA. |

## <a name="dnsservice"></a>DnsService
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, wartość domyślna to FALSE|Static|Flaga umożliwiająca obsługę zapytań DNS dla usług podzielonych na partycje. Ta funkcja jest domyślnie wyłączona. Aby uzyskać więcej informacji, zobacz [Service Fabric usługi DNS.](service-fabric-dnsservice.md)|
|InstanceCount|int, wartość domyślna to-1|Static|Wartość domyślna to-1, co oznacza, że DnsService jest uruchomiona w każdym węźle. Jednopunktowy musi być ustawiona na 1, ponieważ DnsService używa dobrze znanego portu 53, dlatego nie może mieć wielu wystąpień na tym samym komputerze.|
|IsEnabled|bool, wartość domyślna to FALSE|Static|Włącza/wyłącza DnsService. DnsService jest domyślnie wyłączona i należy ustawić tę konfigurację, aby ją włączyć. |
|PartitionPrefix|ciąg, wartość domyślna to "--"|Static|Kontroluje wartość ciągu prefiksu partycji w zapytaniach DNS dla usług partycjonowanych. Wartość: <ul><li>Powinna być zgodna ze specyfikacją RFC, ponieważ będzie częścią zapytania DNS.</li><li>Nie powinna zawierać kropki ".", ponieważ kropka zakłóca zachowanie sufiksu DNS.</li><li>Nie może być dłuższa niż 5 znaków.</li><li>Nie może być pustym ciągiem.</li><li>Jeśli ustawienie PartitionPrefix jest zastępowane, PartitionSuffix musi być zastąpione i na odwrót.</li></ul>Aby uzyskać więcej informacji, zobacz [Service Fabric usługi DNS.](service-fabric-dnsservice.md).|
|PartitionSuffix|ciąg, wartość domyślna to ""|Static|Steruje wartością ciągu sufiksu partycji w zapytaniach DNS dla usług partycjonowanych. Wartość: <ul><li>Powinna być zgodna ze specyfikacją RFC, ponieważ będzie częścią zapytania DNS.</li><li>Nie powinna zawierać kropki ".", ponieważ kropka zakłóca zachowanie sufiksu DNS.</li><li>Nie może być dłuższa niż 5 znaków.</li><li>Jeśli ustawienie PartitionPrefix jest zastępowane, PartitionSuffix musi być zastąpione i na odwrót.</li></ul>Aby uzyskać więcej informacji, zobacz [Service Fabric usługi DNS.](service-fabric-dnsservice.md). |

## <a name="eventstoreservice"></a>EventStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, wartość domyślna to 0|Static|Usługa MinReplicaSetSize dla EventStore |
|PlacementConstraints|ciąg, wartość domyślna to ""|Static|    Usługa PlacementConstraints dla EventStore |
|Wartość targetreplicasetsize|int, wartość domyślna to 0|Static| Usługa wartość targetreplicasetsize dla EventStore |

## <a name="fabricclient"></a>FabricClient

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Czas w sekundach, wartość domyślna to 2 |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał limitu czasu połączenia dla każdego klienta próbuje otworzyć połączenie z bramą.|
|HealthOperationTimeout |Czas w sekundach, wartość domyślna to 120 |Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu wiadomości raportu wysyłanej do Menedżera kondycji. |
|HealthReportRetrySendInterval |Czas w sekundach, wartość domyślna to 30, minimum to 1 |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał, w którym składnik raportowania ponownie wysyła skumulowane raporty kondycji do Menedżera kondycji. |
|HealthReportSendInterval |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał, z jakim składnik raportowania wysyła skumulowane raporty kondycji do Menedżera kondycji. |
|KeepAliveIntervalInSeconds |Int, wartość domyślna to 20 |Static|Interwał, w którym transport FabricClient wysyła komunikaty Keep-Alive do bramy. Dla 0; Utrzymywanie aktywności jest wyłączone. Musi być wartością dodatnią. |
|MaxFileSenderThreads |Uint, wartość domyślna to 10 |Static|Maksymalna liczba plików, które są przesyłane równolegle. |
|NodeAddresses |ciąg, wartość domyślna to "" |Static|Kolekcja adresów (parametrów połączeń) w różnych węzłach, które mogą być używane do komunikacji z Usługa nazewnictwa. Początkowo klient łączy wybór jednego z adresów losowo. Jeśli podano więcej niż jeden ciąg połączenia i połączenie nie powiedzie się z powodu błędu komunikacji lub przekroczenia limitu czasu; Klient przełącza się do użycia następnego adresu sekwencyjnie. Zobacz sekcję ponowna próba adresu Usługa nazewnictwa, aby uzyskać szczegółowe informacje o semantyce ponownych prób. |
|PartitionLocationCacheLimit |Int, wartość domyślna to 100000 |Static|Liczba partycji w pamięci podręcznej w celu rozpoznania usługi (wartość 0 nie ma limitu). |
|RetryBackoffInterval |Czas w sekundach, wartość domyślna to 3 |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał wycofywania przed ponowną próbą wykonania operacji. |
|ServiceChangePollInterval |Czas w sekundach, wartość domyślna to 120 |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał między kolejnymi sondowami w przypadku zmian w usługach z klienta do bramy dla zarejestrowanego wywołania zwrotnego powiadomień o zmianach usługi. |

## <a name="fabrichost"></a>Elemencie fabrichost określono

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, wartość domyślna to 10 |Dynamiczny|Jest to maksymalna liczba, dla której system podejmie ponowną próbę niepowodzenia aktywacji przed pokazaniem. |
|ActivationMaxRetryInterval |Czas w sekundach, wartość domyślna to 300 |Dynamiczny|Określ wartość TimeSpan w sekundach. Maksymalna długość interwału ponawiania próby aktywacji. W przypadku każdego ciągłego błędu interwał ponawiania jest obliczany jako minimum (ActivationMaxRetryInterval; Liczba błędów ciągłych * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Czas w sekundach, wartość domyślna to 5 |Dynamiczny|Określ wartość TimeSpan w sekundach. Wycofywania interwał dla każdej awarii aktywacji; w przypadku każdej ciągłej awarii aktywacji system podejmie ponowną próbę aktywacji do MaxActivationFailureCount. Interwał ponawiania prób przy każdej próbie to produkt o ciągłej awarii aktywacji oraz interwał wycofywania aktywacji. |
|EnableRestartManagement |Bool, wartość domyślna to false |Dynamiczny|Ma to na celu włączenie ponownego uruchomienia serwera. |
|EnableServiceFabricAutomaticUpdates |Bool, wartość domyślna to false |Dynamiczny|Jest to włączenie automatycznej aktualizacji sieci szkieletowej za pośrednictwem Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, wartość domyślna to false |Dynamiczny|Jest to włączenie aktualizacji podstawowej dla serwera programu. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, wartość domyślna to true | Static | Umożliwia szybsze przekazywanie w DCA, gdy elemencie fabrichost określono jest w trybie raportowania błędów. |
|FailureReportingTimeout | TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (60) | Static |Określ wartość TimeSpan w sekundach. Przekroczenie limitu czasu dla raportowania błędów DCA w przypadku elemencie fabrichost określono napotka błąd uruchamiania wczesnego etapu. | 
|RunDCAOnStartupFailure | Bool, wartość domyślna to true | Static |Określa, czy uruchamiać DCA do przekazywania dzienników w przypadku problemów z uruchamianiem w elemencie fabrichost określono. | 
|StartTimeout |Czas w sekundach, wartość domyślna to 300 |Dynamiczny|Określ wartość TimeSpan w sekundach. Przekroczono limit czasu podczas uruchamiania usługi fabricactivationmanager. |
|StopTimeout |Czas w sekundach, wartość domyślna to 300 |Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu aktywacji usługi hostowanej; Dezaktywacja i uaktualnienie. |

## <a name="fabricnode"></a>FabricNode

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |ciąg, wartość domyślna to "FindByThumbprint" |Dynamiczny|Wskazuje, w jaki sposób wyszukiwać certyfikat w magazynie określonym przez ClientAuthX509StoreName obsługiwaną wartość: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |ciąg, wartość domyślna to "" | Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli administratora FabricClient. |
|ClientAuthX509FindValueSecondary |ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli administratora FabricClient. |
|ClientAuthX509StoreName |ciąg, wartość domyślna to "my" |Dynamiczny|Nazwa magazynu certyfikatów X. 509 zawierającego certyfikat dla domyślnej roli administratora FabricClient. |
|ClusterX509FindType |ciąg, wartość domyślna to "FindByThumbprint" |Dynamiczny|Wskazuje, jak wyszukiwać certyfikat klastra w magazynie określonym przez ClusterX509StoreName obsługiwane wartości: "FindByThumbprint"; "FindBySubjectName" z "FindBySubjectName"; Jeśli istnieje wiele dopasowań; zostanie użyta taka z najdalej wygaśnięciem. |
|ClusterX509FindValue |ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|ClusterX509FindValueSecondary |ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|ClusterX509StoreName |ciąg, wartość domyślna to "my" |Dynamiczny|Nazwa magazynu certyfikatów X. 509 zawierającego certyfikat klastra służący do zabezpieczania komunikacji wewnątrz klastra. |
|EndApplicationPortRange |Int, wartość domyślna to 0 |Static|Zakończenie (nie włącznie) portów aplikacji zarządzanych przez podsystem hostingu. Wymagane, jeśli EndpointFilteringEnabled ma wartość true w hostingu. |
|ServerAuthX509FindType |ciąg, wartość domyślna to "FindByThumbprint" |Dynamiczny|Wskazuje, w jaki sposób wyszukiwać certyfikat serwera w magazynie określonym przez ServerAuthX509StoreName obsługiwaną wartość: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|ServerAuthX509FindValueSecondary |ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|ServerAuthX509StoreName |ciąg, wartość domyślna to "my" |Dynamiczny|Nazwa magazynu certyfikatów X. 509 zawierającego certyfikat serwera dla usługi bezboleśnie. |
|StartApplicationPortRange |Int, wartość domyślna to 0 |Static|Początek portów aplikacji zarządzanych przez podsystem hostingu. Wymagane, jeśli EndpointFilteringEnabled ma wartość true w hostingu. |
|StateTraceInterval |Czas w sekundach, wartość domyślna to 300 |Static|Określ wartość TimeSpan w sekundach. Interwał śledzenia stanu węzła w każdym węźle i w górę w węzłach FM/FMM. |
|UserRoleClientX509FindType |ciąg, wartość domyślna to "FindByThumbprint" |Dynamiczny|Wskazuje, w jaki sposób wyszukiwać certyfikat w magazynie określonym przez UserRoleClientX509StoreName obsługiwaną wartość: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli użytkownika FabricClient. |
|UserRoleClientX509FindValueSecondary |ciąg, wartość domyślna to "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli użytkownika FabricClient. |
|UserRoleClientX509StoreName |ciąg, wartość domyślna to "my" |Dynamiczny|Nazwa magazynu certyfikatów X. 509 zawierającego certyfikat dla domyślnej roli użytkownika FabricClient. |

## <a name="failovermanager"></a>Tryb failovermanager

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, wartość domyślna to FALSE |Dynamiczny|Flaga wskazująca, czy można usunąć stan węzła dla węzła inicjatora |
|BuildReplicaTimeLimit|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (3600)|Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu tworzenia repliki stanowej; po upływie którego zostanie zainicjowany Raport kondycji ostrzegawczej |
|ClusterPauseThreshold|int, wartość domyślna to 1|Dynamiczny|Jeśli liczba węzłów w systemie znajduje się poniżej tej wartości, rozmieszczenie; Równoważenie obciążenia; i tryb failover jest zatrzymany. |
|CreateInstanceTimeLimit|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (300)|Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu tworzenia wystąpienia bezstanowego; po upływie którego zostanie zainicjowany Raport kondycji ostrzegawczej |
|ExpectedClusterSize|int, wartość domyślna to 1|Dynamiczny|Gdy klaster jest początkowo uruchamiany; Radio będzie czekać, aż wiele węzłów zgłosi się przed rozpoczęciem umieszczania innych usług. łącznie z usługami systemowymi, takimi jak nazewnictwo. Zwiększenie tej wartości wydłuża czas uruchomienia klastra; Zapobiega to przeciążeniu wczesnych węzłów, a także dodatkowe przeniesienia, które będą potrzebne, gdy więcej węzłów przejdzie do trybu online. Ta wartość powinna być zwykle ustawiona na niewielką część początkowego rozmiaru klastra. |
|ExpectedNodeDeactivationDuration|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (60.0 \* 30)|Dynamiczny|Określ wartość TimeSpan w sekundach. Jest to oczekiwany czas trwania dezaktywacji węzła w programie. |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (60.0 \* 30)|Dynamiczny|Określ wartość TimeSpan w sekundach. Jest to oczekiwany czas, który ma zostać uaktualniony przez węzeł podczas uaktualnienia Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (60.0 \* 30)|Dynamiczny|Określ wartość TimeSpan w sekundach. Jest to oczekiwany czas trwania uaktualnienia wszystkich replik w węźle podczas uaktualniania aplikacji. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, wartość domyślna to TRUE|Dynamiczny|Jeśli wartość jest równa true; repliki z docelowym zestawem replik o rozmiarze 1 będą dozwolone podczas uaktualniania. |
|MinReplicaSetSize|int, wartość domyślna to 3|Niedozwolone|Jest to minimalny rozmiar zestawu replik dla FM. Jeśli liczba aktywnych replik FM spadnie poniżej tej wartości; FM spowoduje odrzucenie zmian w klastrze do momentu odzyskania co najmniej minimalnej liczby replik |
|PlacementConstraints|ciąg, wartość domyślna to ""|Niedozwolone|Wszystkie ograniczenia umieszczania dla replik Menedżera trybu failover |
|PlacementTimeLimit|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (600)|Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu osiągnięcia docelowej liczby replik. po upływie którego zostanie zainicjowany Raport kondycji ostrzegawczej |
|QuorumLossWaitDuration |Czas w sekundach, wartość domyślna to MaxValue |Dynamiczny|Określ wartość TimeSpan w sekundach. Jest to maksymalny czas trwania obowiązywania partycji w stanie utraty kworum. Jeśli partycja nadal znajduje się w utracie kworum po tym czasie trwania; partycja jest odzyskiwana po utracie kworum, rozważając repliki w dół jako utracone. Należy pamiętać, że może to spowodować utratę danych. |
|ReconfigurationTimeLimit|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (300)|Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu dla ponownej konfiguracji; po upływie którego zostanie zainicjowany Raport kondycji ostrzegawczej |
|ReplicaRestartWaitDuration|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (60.0 \* 30)|Niedozwolone|Określ wartość TimeSpan w sekundach. Jest to ReplicaRestartWaitDuration dla FMService |
| SeedNodeQuorumAdditionalBufferNodes | int, wartość domyślna to 0 | Dynamiczny | Bufor węzłów inicjatora, które muszą być spełnione (wraz z kworum węzłów inicjatora) FM powinien zezwalać na przechodzenie między węzłami inicjatora (totalNumSeedNodes-(seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)). |
|StandByReplicaKeepDuration|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (3600.0 \* 24 \* 7)|Niedozwolone|Określ wartość TimeSpan w sekundach. Jest to StandByReplicaKeepDuration dla FMService |
|Wartość targetreplicasetsize|int, wartość domyślna to 7|Niedozwolone|Jest to docelowa liczba replik FM, które będą obsługiwane przez Windows Fabric. Wyższa liczba powoduje wyższą niezawodność danych FM; z małą wadą wydajności. |
|UserMaxStandByReplicaCount |Int, wartość domyślna to 1 |Dynamiczny|Domyślna maksymalna liczba replik rezerwowych zachowywanych przez system dla usług użytkownika. |
|UserReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to 60,0 \* 30 |Dynamiczny|Określ wartość TimeSpan w sekundach. Gdy trwała replika ulegnie awarii; Windows Fabric czeka na ten czas trwania repliki, aby utworzyć kopię zapasową przed utworzeniem nowych replik zamiennych (co wymagałoby kopii stanu). |
|UserStandByReplicaKeepDuration |Czas w sekundach, wartość domyślna to 3600,0 \* 24 \* 7 |Dynamiczny|Określ wartość TimeSpan w sekundach. Gdy utrwalona replika wraca ze stanu Down; być może został już zastąpiony. Ten czasomierz określa, jak długo Radio będzie utrzymywać replikę rezerwową przed odpisaniem. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, wartość domyślna to 604800 |Static| To przybliżenie, jak długo mają być przechowywane akcje w stanie terminalu. Jest to również zależne od StoredActionCleanupIntervalInSeconds; ponieważ zadania do oczyszczenia są wykonywane tylko w tym interwale. 604800 wynosi 7 dni. |
|DataLossCheckPollIntervalInSeconds|int, wartość domyślna to 5|Static|Jest to czas między sprawdzeniami wykonywanym przez system podczas oczekiwania na utratę danych. Liczba przypadków, gdy liczba utraconych danych zostanie sprawdzona na iterację wewnętrzną, to DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, wartość domyślna to 25|Static|Łączny czas; w sekundach; że system będzie oczekiwał na utratę danych. Jest on używany wewnętrznie podczas wywoływania interfejsu API StartPartitionDataLossAsync (). |
|MinReplicaSetSize |Int, wartość domyślna to 0 |Static|MinReplicaSetSize dla FaultAnalysisService. |
|PlacementConstraints | ciąg, wartość domyślna to ""|Static| PlacementConstraints dla FaultAnalysisService. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Static|Określ wartość TimeSpan w sekundach. QuorumLossWaitDuration dla FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, wartość domyślna to 600|Static|Ten parametr jest używany, gdy wywoływany jest interfejs API utraty danych. Określa, jak długo system będzie oczekiwał na porzucenie przez replikę po wywołaniu wewnętrznie usuwania repliki. |
|ReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna to 60 minut|Static|Określ wartość TimeSpan w sekundach. ReplicaRestartWaitDuration dla FaultAnalysisService. |
|StandByReplicaKeepDuration| Czas w sekundach, wartość domyślna to (60 *24* 7) min |Static|Określ wartość TimeSpan w sekundach. StandByReplicaKeepDuration dla FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, wartość domyślna to 3600 |Static|Dzieje się tak, jak często trwa czyszczenie magazynu. Tylko akcje w stanie terminalu; i zakończone co najmniej CompletedActionKeepDurationInSeconds temu zostaną usunięte. |
|StoredChaosEventCleanupIntervalInSeconds | Int, wartość domyślna to 3600 |Static|Tak, jak często magazyn będzie monitorowany do czyszczenia; Jeśli liczba zdarzeń przekracza 30000; Czyszczenie zostanie rozpoczęte w programie. |
|Wartość targetreplicasetsize |Int, wartość domyślna to 0 |Static|NOT_PLATFORM_UNIX_START wartość targetreplicasetsize dla FaultAnalysisService. |

## <a name="federation"></a>Federacja

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|LeaseDuration |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy między węzłem i jego sąsiadami. |
|LeaseDurationAcrossFaultDomain |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy między węzłem i jego sąsiadami w domenach błędów. |

## <a name="filestoreservice"></a>FileStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, wartość domyślna to TRUE|Dynamiczny|Konfiguracja w celu określenia, czy usługa Magazyn plików akceptuje przekazywanie plików na podstawie fragmentów, czy nie podczas kopiowania pakietu aplikacji. |
|AnonymousAccessEnabled | Bool, wartość domyślna to true |Static|Włącz/Wyłącz dostęp anonimowy do udziałów FileStoreService. |
|CommonName1Ntlmx509CommonName|ciąg, wartość domyślna to ""|Static| Nazwa pospolita certyfikatu x509 używanego do generowania HMAC na CommonName1NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName1Ntlmx509StoreLocation|ciąg, wartość domyślna to "LocalMachine"|Static|Lokalizacja magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na CommonName1NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName1Ntlmx509StoreName|ciąg, wartość domyślna to "MY"| Static|Nazwa magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na CommonName1NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName2Ntlmx509CommonName|ciąg, wartość domyślna to ""|Static|Nazwa pospolita certyfikatu x509 używanego do generowania HMAC na CommonName2NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName2Ntlmx509StoreLocation|ciąg, wartość domyślna to "LocalMachine"| Static|Lokalizacja magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na CommonName2NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonName2Ntlmx509StoreName|ciąg, wartość domyślna to "MY"|Static| Nazwa magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na CommonName2NtlmPasswordSecret w przypadku korzystania z uwierzytelniania NTLM |
|CommonNameNtlmPasswordSecret|SecureString, wartość domyślna to common:: SecureString ("")| Static|Hasło tajne, które zostało użyte jako inicjator do wygenerowania tego samego hasła podczas korzystania z uwierzytelniania NTLM |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (5)|Dynamiczny|Określ wartość TimeSpan w sekundach. Przedział czasu między sprawdzaniem miejsca na dysku dla zdarzenia kondycji raportowania, gdy ilość wolnego miejsca na dysku jest bliska. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (15)|Dynamiczny|Określ wartość TimeSpan w sekundach. Przedział czasu między sprawdzaniem miejsca na dysku dla zdarzenia kondycji raportowania, gdy na dysku jest wystarczająca ilość miejsca. |
|EnableImageStoreHealthReporting |bool, wartość domyślna to TRUE    |Static|Konfiguracja w celu ustalenia, czy usługa magazynu plików powinna raportować swoją kondycję. |
|FreeDiskSpaceNotificationSizeInKB|Int64, wartość domyślna to 25 \* 1024 |Dynamiczny|Rozmiar wolnego miejsca na dysku, poniżej którego może wystąpić ostrzeżenie o kondycji. Minimalna wartość tej konfiguracji i konfiguracji FreeDiskSpaceNotificationThresholdPercentage są używane do określania wysyłania ostrzeżenia kondycji. |
|FreeDiskSpaceNotificationThresholdPercentage|Double, wartość domyślna to 0,02 |Dynamiczny|Wartość procentowa wolnego miejsca na dysku, poniżej którego może wystąpić ostrzeżenie o kondycji. Minimalna wartość tej konfiguracji i konfiguracji FreeDiskSpaceNotificationInMB są używane do określania wysyłania ostrzeżenia kondycji. |
|GenerateV1CommonNameAccount| bool, wartość domyślna to TRUE|Static|Określa, czy ma zostać wygenerowane konto z algorytmem generowania nazwy użytkownika w wersji 1. Począwszy od Service Fabric w wersji 6,1; tworzone jest konto z generacjami w wersji 2. Konto w wersji 1 jest wymagane w przypadku uaktualnień z/do wersje, które nie obsługują generacji v2 (przed 6,1).|
|MaxCopyOperationThreads | Uint, wartość domyślna to 0 |Dynamiczny| Maksymalna liczba plików równoległych, które pomocnicze mogą kopiować z elementu podstawowego. "0" = = liczba rdzeni. |
|MaxFileOperationThreads | Uint, wartość domyślna to 100 |Static| Maksymalna liczba wątków równoległych, które mogą wykonać FileOperations (Kopiowanie/przenoszenie) w podstawowym. "0" = = liczba rdzeni. |
|MaxRequestProcessingThreads | Uint, wartość domyślna to 200 |Static|Maksymalna liczba wątków równoległych, które mogą przetwarzać żądania w podstawowym. "0" = = liczba rdzeni. |
|MaxSecondaryFileCopyFailureThreshold | Uint, wartość domyślna to 25|Dynamiczny|Maksymalna liczba ponownych prób kopiowania plików na serwerze pomocniczym przed pokazaniem. |
|MaxStoreOperations | Uint, wartość domyślna to 4096 |Static|Maksymalna liczba operacji transakcji magazynu równoległego dozwolonych na serwerze podstawowym. "0" = = liczba rdzeni. |
|NamingOperationTimeout |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu wykonywania operacji nazewnictwa. |
|PrimaryAccountNTLMPasswordSecret | SecureString, wartość domyślna jest pusta |Static| Klucz tajny hasła, który jest używany jako inicjator do wygenerowania tego samego hasła podczas korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509StoreLocation | ciąg, wartość domyślna to "LocalMachine"|Static| Lokalizacja magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509StoreName | ciąg, wartość domyślna to "MY"|Static| Nazwa magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountNTLMX509Thumbprint | ciąg, wartość domyślna to ""|Static|Odcisk palca certyfikatu x509 używanego do generowania algorytmu HMAC na PrimaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|PrimaryAccountType | ciąg, wartość domyślna to "" |Static|Podstawowy rodzaj konta podmiotu zabezpieczeń do listy ACL udziały FileStoreService. |
|PrimaryAccountUserName | ciąg, wartość domyślna to "" |Static|Główna nazwa użytkownika konta podmiotu zabezpieczeń dla udziałów FileStoreService. |
|PrimaryAccountUserPassword | SecureString, wartość domyślna jest pusta |Static|Podstawowe hasło do konta podmiotu zabezpieczeń dla udziałów FileStoreService. |
|QueryOperationTimeout | Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu wykonywania operacji zapytania. |
|SecondaryAccountNTLMPasswordSecret | SecureString, wartość domyślna jest pusta |Static| Klucz tajny hasła, który jest używany jako inicjator do wygenerowania tego samego hasła podczas korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509StoreLocation | ciąg, wartość domyślna to "LocalMachine" |Static|Lokalizacja magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509StoreName | ciąg, wartość domyślna to "MY" |Static|Nazwa magazynu certyfikatu x509 użyta do wygenerowania algorytmu HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509Thumbprint | ciąg, wartość domyślna to ""| Static|Odcisk palca certyfikatu x509 używanego do generowania algorytmu HMAC na SecondaryAccountNTLMPasswordSecret w przypadku korzystania z uwierzytelniania NTLM. |
|SecondaryAccountType | ciąg, wartość domyślna to ""|Static| Pomocniczy rodzaj konta podmiotu zabezpieczeń do listy ACL udziały FileStoreService. |
|SecondaryAccountUserName | ciąg, wartość domyślna to ""| Static|Nazwa użytkownika konta pomocniczego podmiotu zabezpieczeń listy ACL udziały FileStoreService. |
|SecondaryAccountUserPassword | SecureString, wartość domyślna jest pusta |Static|Hasło konta pomocniczego podmiotu zabezpieczeń do listy ACL udziały FileStoreService. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, wartość domyślna to 500|Dynamiczny|Opóźnienie ponawiania operacji kopiowania plików (w milisekundach).|
|UseChunkContentInTransportMessage|bool, wartość domyślna to TRUE|Dynamiczny|Flaga używania nowej wersji protokołu przekazywania wprowadzonej w v 6.4. Ta wersja protokołu korzysta z transportu usługi Service Fabric w celu przekazywania plików do magazynu obrazów, co zapewnia lepszą wydajność niż protokół SMB używany w poprzednich wersjach. |

## <a name="healthmanager"></a>Stan kondycji

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, wartość domyślna to false |Static|Zasady oceny kondycji klastra: Włącz dla oceny kondycji typu aplikacji. |
|MaxSuggestedNumberOfEntityHealthReports|Int, wartość domyślna to 100 |Dynamiczny|Maksymalna liczba raportów o kondycji, które może mieć jednostka przed zwiększeniem wątpliwości dotyczących logiki raportowania kondycji licznika danych. Każda jednostka kondycji powinna mieć stosunkowo małą liczbę raportów kondycji. Jeśli liczba raportów przekracza tę liczbę; mogą wystąpić problemy z implementacją licznika alarmowego. Jednostka z zbyt dużą liczbą raportów jest oflagowana przy użyciu raportu kondycji ostrzegawczej, gdy jednostka jest szacowana. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, wartość domyślna to false |Static|Zasady oceny kondycji klastra: ostrzeżenia są traktowane jako błędy. |
|MaxPercentUnhealthyApplications | Int, wartość domyślna to 0 |Static|Zasady oceny kondycji klastra: maksymalny procent aplikacji w złej kondycji, które mogą być w stanie dobrać klaster. |
|MaxPercentUnhealthyNodes | Int, wartość domyślna to 0 |Static|Zasady oceny kondycji klastra: maksymalny procent węzłów w złej kondycji, które mogą być prawidłowe w przypadku kondycji klastra. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, wartość domyślna to 10|Static|Zasady oceny kondycji uaktualniania klastra: maksymalna wartość procentowa różnic węzłów w złej kondycji dozwolona w przypadku kondycji klastra |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, wartość domyślna to 15|Static|Zasady oceny kondycji uaktualniania klastra: maksymalny procent różnicy węzłów w złej kondycji w domenie uaktualnienia dozwolony przez klaster w dobrej kondycji |

## <a name="hosting"></a>Hosting

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Liczba całkowita, wartość domyślna to 10 |Dynamiczny|Liczba nieudanych prób aktywacji systemu przed pokazaniem |
|ActivationMaxRetryInterval |Czas w sekundach, wartość domyślna to 300 |Dynamiczny|W przypadku każdej awarii ciągłej aktywacji system ponawia próbę aktywacji do ActivationMaxFailureCount. ActivationMaxRetryInterval określa interwał czasu oczekiwania przed ponowną próbą po każdym błędzie aktywacji |
|ActivationRetryBackoffInterval |Czas w sekundach, wartość domyślna to 5 |Dynamiczny|Wycofywania interwał dla każdej awarii aktywacji; W przypadku każdej awarii ciągłej aktywacji system ponawia próbę aktywacji do MaxActivationFailureCount. Interwał ponawiania prób przy każdej próbie to produkt o ciągłej awarii aktywacji oraz interwał wycofywania aktywacji. |
|ActivationTimeout| TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (180)|Dynamiczny| Określ wartość TimeSpan w sekundach. Limit czasu aktywacji aplikacji; Dezaktywacja i uaktualnienie. |
|ApplicationHostCloseTimeout| TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (120)|Dynamiczny| Określ wartość TimeSpan w sekundach. Po wykryciu wyjścia sieci szkieletowej w procesach samoaktywowanych; FabricRuntime zamyka wszystkie repliki w procesie hosta użytkownika (ApplicationHost). Jest to limit czasu dla operacji zamknięcia. |
| CnsNetworkPluginCnmUrlPort | wstring, wartość domyślna to L "48080" | Static | Port URL interfejsu API usługi Azure CNM |
| CnsNetworkPluginCnsUrlPort | wstring, wartość domyślna to L "10090" | Static | Port URL CNS platformy Azure |
|ContainerServiceArguments|ciąg, wartość domyślna to "-H localhost: 2375-H npipe://"|Static|Service Fabric (SF) zarządza demonem platformy Docker (z wyjątkiem komputerów klienckich z systemem Windows, takich jak Win10). Ta konfiguracja umożliwia użytkownikowi Określanie niestandardowych argumentów, które powinny być przekazane do demona platformy Docker podczas jej uruchamiania. W przypadku określenia argumentów niestandardowych Service Fabric nie przekazywać żadnego innego argumentu do aparatu platformy Docker, z wyjątkiem argumentu "--pidfile". W związku z tym użytkownicy nie powinni określać argumentu "--pidfile" jako części argumentów klienta. Ponadto argumenty niestandardowe powinny zapewnić, że demon Docker nasłuchuje w domyślnym potoku nazw w systemie Windows (lub w gnieździe domeny systemu UNIX w systemie Linux), aby Service Fabric mógł z nich komunikować się.|
|ContainerServiceLogFileMaxSizeInKb|int, wartość domyślna to 32768|Static|Maksymalny rozmiar pliku dziennika wygenerowanego przez kontenery platformy Docker.  Tylko system Windows.|
|ContainerImageDownloadTimeout|int, liczba sekund, wartość domyślna to 1200 (20 minut)|Dynamiczny|Liczba sekund przed upływem limitu czasu pobierania obrazu.|
|ContainerImagesToSkip|ciąg znaków, nazwy obrazów oddzielone znakami pionowymi, wartość domyślna to ""|Static|Nazwa co najmniej jednego obrazu kontenera, który nie powinien zostać usunięty.  Używane z parametrem PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|ciąg, wartość domyślna to "sfcontainerlogs"|Static|Prefiks nazwy pliku dla plików dziennika generowanych przez kontenery platformy Docker.  Tylko system Windows.|
|ContainerServiceLogFileRetentionCount|int, wartość domyślna to 10|Static|Liczba plików dziennika wygenerowanych przez kontenery platformy Docker przed zastąpieniem plików dziennika.  Tylko system Windows.|
|CreateFabricRuntimeTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (120)|Dynamiczny| Określ wartość TimeSpan w sekundach. Wartość limitu czasu dla wywołania synchronizacji FabricCreateRuntime |
|DefaultContainerRepositoryAccountName|ciąg, wartość domyślna to ""|Static|Poświadczenia domyślne używane zamiast poświadczeń określonych w ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|ciąg, wartość domyślna to ""|Static|Używane są domyślne poświadczenia hasła zamiast poświadczeń określonych w ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|ciąg, wartość domyślna to ""|Static|W przypadku braku pustego ciągu wartość może być "zaszyfrowana" lub "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|bool, wartość domyślna to FALSE|Static|Domyślnie nazwa usługi jest dołączana do nazwy DNS SF dla usług kontenerów. Ta funkcja zatrzyma to zachowanie, tak aby nic nie zostało dołączone do nazwy DNS SF domyślnie w ramach ścieżki rozpoznawania.|
|DeploymentMaxFailureCount|int, wartość domyślna to 20| Dynamiczny|Ponowna próba wdrożenia aplikacji zostanie ponowiona dla DeploymentMaxFailureCount czasów przed niepowodzeniem wdrożenia tej aplikacji w węźle.| 
|DeploymentMaxRetryInterval| TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (3600)|Dynamiczny| Określ wartość TimeSpan w sekundach. Maksymalny interwał ponawiania prób dla wdrożenia. W przypadku każdego ciągłego błędu interwał ponawiania jest obliczany jako minimum (DeploymentMaxRetryInterval; Liczba błędów ciągłych * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (10)|Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał wycofywania dla błędu wdrożenia. Na każdym ciągłym błędzie wdrożenia system podejmie ponowną próbę wdrożenia do MaxDeploymentFailureCount. Interwał ponawiania prób to iloczyn ciągłego niepowodzeń wdrażania oraz interwału wycofywania wdrożenia. |
|DisableContainers|bool, wartość domyślna to FALSE|Static|Konfiguracja do wyłączania kontenerów — używana zamiast DisableContainerServiceStartOnContainerActivatorOpen, która jest przestarzałą konfiguracją |
|DisableDockerRequestRetry|bool, wartość domyślna to FALSE |Dynamiczny| Domyślnie SF komunikuje się z DD (Docker Dameon) o limicie czasu DockerRequestTimeout ' dla każdego wysłanego żądania HTTP. Jeśli DD nie odpowiada w tym okresie; SF ponownie wysyła żądanie, jeśli operacja najwyższego poziomu nadal ma pozostały czas.  Z kontenerem HyperV; DD czasami Poświęć więcej czasu na przełączenie kontenera lub jego dezaktywowanie. W takich przypadkach DD żądanie, które upłynął od perspektywa SF i SF ponawia operację. Czasami wydaje się, że zwiększa nacisk na DD. Ta konfiguracja pozwala wyłączyć tę ponowną próbę i poczekać na odpowiedź DD. |
|DnsServerListTwoIps | Bool, wartość domyślna to FALSE | Static | Ta flaga dodaje dwa razy lokalny serwer DNS, aby pomóc w zmniejszeniu sporadycznych problemów. |
| DoNotInjectLocalDnsServer | bool, wartość domyślna to FALSE | Static | Zapobiega wprowadzaniu przez środowisko uruchomieniowe lokalnego adresu IP jako serwera DNS dla kontenerów. |
|EnableActivateNoWindow| bool, wartość domyślna to FALSE|Dynamiczny| Aktywowany proces jest tworzony w tle bez żadnej konsoli. |
|EnableContainerServiceDebugMode|bool, wartość domyślna to TRUE|Static|Włącz/Wyłącz rejestrowanie kontenerów platformy Docker.  Tylko system Windows.|
|EnableDockerHealthCheckIntegration|bool, wartość domyślna to TRUE|Static|Włącza integrację zdarzeń platformy Docker HEALTHCHECK z raportem kondycji systemu Service Fabric |
|EnableProcessDebugging|bool, wartość domyślna to FALSE|Dynamiczny| Włącza uruchamianie hostów aplikacji w debugerze |
|EndpointProviderEnabled| bool, wartość domyślna to FALSE|Static| Umożliwia zarządzanie zasobami punktu końcowego według sieci szkieletowej. Wymaga specyfikacji zakresu portów aplikacji początkowej i końcowej w FabricNode. |
|FabricContainerAppsEnabled| bool, wartość domyślna to FALSE|Static| |
|FirewallPolicyEnabled|bool, wartość domyślna to FALSE|Static| Włącza otwieranie portów zapory dla zasobów punktów końcowych z jawnymi portami określonymi w servicemanifest |
|GetCodePackageActivationContextTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (120)|Dynamiczny|Określ wartość TimeSpan w sekundach. Wartość limitu czasu dla wywołań CodePackageActivationContext. Nie dotyczy to usług ad hoc. |
|GovernOnlyMainMemoryForProcesses|bool, wartość domyślna to FALSE|Static|Domyślne zachowanie funkcji zarządzania zasobami polega na umieszczeniu w MemoryInMB limitu całkowitej ilości pamięci (pamięć RAM + wymiana), która jest stosowana przez proces. W przypadku przekroczenia limitu; proces otrzyma wyjątek OutOfMemory. Jeśli ten parametr ma wartość true; limit zostanie zastosowany tylko do ilości pamięci RAM, która będzie używana przez proces. W przypadku przekroczenia tego limitu; a jeśli to ustawienie ma wartość PRAWDA; następnie system operacyjny spowoduje zamianę głównej pamięci na dysk. |
|IPProviderEnabled|bool, wartość domyślna to FALSE|Static|Umożliwia zarządzanie adresami IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, wartość domyślna to FALSE|Static|Czy DefaultContainerRepositoryPassword jest szyfrowany, czy nie.|
|LinuxExternalExecutablePath|ciąg, wartość domyślna to "/usr/bin/" |Static|Podstawowy katalog zewnętrznych poleceń wykonywalnych w węźle.|
|NTLMAuthenticationEnabled|bool, wartość domyślna to FALSE|Static| Umożliwia obsługę protokołu NTLM przez pakiety kodu uruchomione jako inni użytkownicy, dzięki czemu procesy między maszynami mogą bezpiecznie komunikować się. |
|NTLMAuthenticationPasswordSecret|SecureString, wartość domyślna to common:: SecureString ("")|Static|Jest zaszyfrowana, który jest używany do generowania hasła dla użytkowników protokołu NTLM. Musi być ustawiona, jeśli NTLMAuthenticationEnabled ma wartość true. Zweryfikowane przez program wdrażania. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (3)|Dynamiczny|Określ wartość TimeSpan w sekundach. Ustawienia specyficzne dla środowiska okresowo, przy którym hosty skanuje nowe certyfikaty, aby można było używać go na potrzeby konfiguracji NTLM FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (4)|Dynamiczny| Określ wartość TimeSpan w sekundach. Limit czasu konfigurowania użytkowników NTLM przy użyciu nazw pospolitych certyfikatów. Użytkownicy protokołu NTLM są musieli uzyskać udziały FileStoreService. |
|PruneContainerImages|bool, wartość domyślna to FALSE|Dynamiczny| Usuń nieużywane obrazy kontenera aplikacji z węzłów. Po wyrejestrowaniu programu ApplicationType z klastra Service Fabric obrazy kontenerów używane przez tę aplikację zostaną usunięte w węzłach, które zostały pobrane przez Service Fabric. Oczyszczanie jest uruchamiane co godzinę, więc może upłynąć do 1 godziny (plus czas na oczyszczenie obrazu) w przypadku obrazów do usunięcia z klastra.<br>Service Fabric nigdy nie pobiera ani nie usuwa obrazów niezwiązanych z aplikacją.  Niepowiązane obrazy, które zostały pobrane ręcznie lub w inny sposób, muszą zostać usunięte jawnie.<br>W parametrze ContainerImagesToSkip nie można określić obrazów, które nie powinny być usuwane.| 
|RegisterCodePackageHostTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (120)|Dynamiczny| Określ wartość TimeSpan w sekundach. Wartość limitu czasu dla wywołania synchronizacji FabricRegisterCodePackageHost. Ma to zastosowanie tylko do hostów aplikacji z wielojęzycznym pakietem kodu, takich jak FWP |
|RequestTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (30)|Dynamiczny| Określ wartość TimeSpan w sekundach. Oznacza to przekroczenie limitu czasu komunikacji między hostem aplikacji użytkownika a procesem sieci szkieletowej dla różnych operacji związanych z hostingiem, takich jak rejestracja fabryki; Rejestracja w czasie wykonywania. |
|RunAsPolicyEnabled| bool, wartość domyślna to FALSE|Static| Włącza uruchamianie pakietów kodu jako użytkownika lokalnego innego niż użytkownik, na którym jest uruchomiony proces sieci szkieletowej. Aby można było włączyć tę sieć szkieletową zasad, musi działać jako SYSTEM lub użytkownik, który ma SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (120)|Dynamiczny|Określ wartość TimeSpan w sekundach. Wartość limitu czasu dla wywołania servicefactory rejestru synchronizacji (bezstanowe/stanowe) |
|ServiceTypeDisableFailureThreshold |Liczba całkowita, wartość domyślna to 1 |Dynamiczny|Jest to próg liczby niepowodzeń, po którym zostanie powiadomiony obiekt trybu failover (FM) w celu wyłączenia typu usługi w tym węźle i wypróbowania innego węzła. |
|ServiceTypeDisableGraceInterval|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (30)|Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał czasowy, po którym można wyłączyć typ usługi |
|ServiceTypeRegistrationTimeout |Czas w sekundach, wartość domyślna to 300 |Dynamiczny|Maksymalny dozwolony czas rejestrowania dla elementu ServiceType w sieci szkieletowej |
|UseContainerServiceArguments|bool, wartość domyślna to TRUE|Static|Ta konfiguracja informuje host o pominięciu przekazywania argumentów (określonych w konfiguracji ContainerServiceArguments) do demona platformy Docker.|

## <a name="httpgateway"></a>HttpGateway

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, wartość domyślna to 50 |Static| Liczba odczytów do opublikowania w kolejce serwera http. Określa liczbę równoczesnych żądań, które mogą być spełnione przez HttpGateway. |
|HttpGatewayHealthReportSendInterval |Czas w sekundach, wartość domyślna to 30 |Static|Określ wartość TimeSpan w sekundach. Interwał, w którym Brama http wysyła do Menedżera kondycji skumulowane raporty kondycji. |
|HttpStrictTransportSecurityHeader|ciąg, wartość domyślna to ""|Dynamiczny| Określ wartość nagłówka zabezpieczeń protokołu HTTP Strict Transport, która ma zostać uwzględniona w każdej odpowiedzi wysyłanej przez HttpGateway. Po ustawieniu na pusty ciąg; ten nagłówek nie zostanie uwzględniony w odpowiedzi bramy.|
|IsEnabled|Bool, wartość domyślna to false |Static| Włącza/wyłącza HttpGateway. HttpGateway jest domyślnie wyłączona. |
|MaxEntityBodySize |Uint, wartość domyślna to 4194304 |Dynamiczny|Zapewnia maksymalny rozmiar treści, który może być oczekiwany w żądaniu HTTP. Wartość domyślna to 4 MB. Httpgateway nie wyśle żądania, jeśli ma treść o rozmiarze > tej wartości. Minimalny rozmiar fragmentu odczytu to 4096 bajtów. Musi to być >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|Enabled (Włączony) |Bool, wartość domyślna to false |Static|Flaga włączenia dla ImageStoreService. Wartość domyślna: FAŁSZ |
|MinReplicaSetSize | Int, wartość domyślna to 3 |Static|MinReplicaSetSize dla ImageStoreService. |
|PlacementConstraints | ciąg, wartość domyślna to "" |Static| PlacementConstraints dla ImageStoreService. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Static| Określ wartość TimeSpan w sekundach. QuorumLossWaitDuration dla ImageStoreService. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to 60,0 \* 30 |Static|Określ wartość TimeSpan w sekundach. ReplicaRestartWaitDuration dla ImageStoreService. |
|StandByReplicaKeepDuration | Czas w sekundach, wartość domyślna to 3600,0 \* 2 |Static| Określ wartość TimeSpan w sekundach. StandByReplicaKeepDuration dla ImageStoreService. |
|Wartość targetreplicasetsize | Int, wartość domyślna to 7 |Static|Wartość targetreplicasetsize dla ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, wartość domyślna to 1 |Dynamiczny|Flaga wskazująca, czy ustawienia pamięci mają być automatycznie i konfigurowane dynamicznie. Jeśli wartość jest równa zero, ustawienia konfiguracji pamięci są używane bezpośrednio i nie są zmieniane w oparciu o warunki systemowe. Jeśli jedna następnie ustawienia pamięci zostaną skonfigurowane automatycznie i mogą ulec zmianie w zależności od warunków systemu. |
|MaximumDestagingWriteOutstandingInKB | Int, wartość domyślna to 0 |Dynamiczny|Liczba KB umożliwiająca przejściu dziennika udostępnionego z wyprzedzeniem dedykowanego dziennika. Wartość 0 oznacza brak limitu.
|SharedLogId |ciąg, wartość domyślna to "" |Static|Unikatowy identyfikator GUID dla udostępnionego kontenera dzienników. Użyj "", jeśli ścieżka domyślna zostanie użyta w obszarze Katalog główny danych sieci szkieletowej. |
|SharedLogPath |ciąg, wartość domyślna to "" |Static|Ścieżka i nazwa pliku do lokalizacji, w której ma zostać umieszczony kontener udostępnionego dziennika. Użyj "", aby użyć domyślnej ścieżki w obszarze Katalog główny danych sieci szkieletowej. |
|SharedLogSizeInMB |Int, wartość domyślna to 8192 |Static|Liczba MB do przydzielenia w kontenerze dzienników udostępnionych. |
|SharedLogThrottleLimitInPercentUsed|int, wartość domyślna to 0 | Static | Procent użycia udostępnionego dziennika, który będzie powodować ograniczenie przepustowości. Wartość musi należeć do zakresu od 0 do 100. Wartość 0 oznacza użycie domyślnej wartości procentowej. Wartość 100 oznacza brak ograniczenia. Wartość z zakresu od 1 do 99 określa procent użycia dziennika, powyżej którego nastąpi ograniczenie przepustowości; Jeśli na przykład dziennik udostępniony to 10 GB, a wartość to 90, ograniczenie zostanie wykonane, gdy 9 GB jest w użyciu. Zalecane jest użycie wartości domyślnej.|
|WriteBufferMemoryPoolMaximumInKB | Int, wartość domyślna to 0 |Dynamiczny|Liczba KB zezwalająca na zwiększenie puli pamięci buforu zapisu. Wartość 0 oznacza brak limitu. |
|WriteBufferMemoryPoolMinimumInKB |Int, wartość domyślna to 8388608 |Dynamiczny|Liczba KB do wstępnego przydzielenia dla puli pamięci buforu zapisu. Wartość 0 oznacza, że wartość domyślna nie powinna być zgodna z SharedLogSizeInMB poniżej. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|IsEnabled|bool, wartość domyślna to FALSE|Static|Oflaguj kontrolowanie obecności i stanu usługi Managed Identity Tokens w klastrze; jest to wymaganie wstępne w przypadku korzystania z funkcji tożsamości zarządzanej Service Fabric aplikacji.|

## <a name="management"></a>Zarządzanie

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (5)|Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał oczyszczania dozwolony dla wyrejestrowania typu aplikacji podczas automatycznego czyszczenia typu aplikacji.|
|AzureStorageMaxConnections | Int, wartość domyślna to 5000 |Dynamiczny|Maksymalna liczba jednoczesnych połączeń z usługą Azure Storage. |
|AzureStorageMaxWorkerThreads | Int, wartość domyślna to 25 |Dynamiczny|Jednocześnie Maksymalna liczba wątków roboczych. |
|AzureStorageOperationTimeout | Czas w sekundach, wartość domyślna to 6000 |Dynamiczny|Określ wartość TimeSpan w sekundach. Przekroczono limit czasu na zakończenie operacji xstore. |
|CleanupApplicationPackageOnProvisionSuccess|bool, wartość domyślna to FALSE |Dynamiczny|Włącza lub wyłącza automatyczne czyszczenie pakietu aplikacji po pomyślnym zainicjowaniu obsługi administracyjnej.<br/> *Najlepszym rozwiązaniem jest użycie `true` .*
|CleanupUnusedApplicationTypes|Bool, wartość domyślna to FALSE |Dynamiczny|Ta konfiguracja umożliwia automatyczne Wyrejestrowanie nieużywanych wersji typu aplikacji z pominięciem najnowszych trzech nieużywanych wersji, a tym samym przycinanie miejsca na dysku zajmowanego przez magazyn obrazów. Automatyczne czyszczenie zostanie wyzwolone po zakończeniu pomyślnego zainicjowania obsługi dla tego określonego typu aplikacji, a także będzie uruchamiane okresowo raz dziennie dla wszystkich typów aplikacji. Liczba nieużywanych wersji do pominięcia jest konfigurowalna przy użyciu parametru "MaxUnusedAppTypeVersionsToKeep". <br/> *Najlepszym rozwiązaniem jest użycie `true` .*
|DisableChecksumValidation | Bool, wartość domyślna to false |Static| Ta konfiguracja umożliwia włączenie lub wyłączenie walidacji sumy kontrolnej podczas aprowizacji aplikacji. |
|DisableServerSideCopy | Bool, wartość domyślna to false |Static|Ta konfiguracja włącza lub wyłącza kopie pakietu aplikacji po stronie serwera na magazynu ImageStore podczas aprowizacji aplikacji. |
|ImageCachingEnabled | Bool, wartość domyślna to true |Static|Ta konfiguracja umożliwia włączenie lub wyłączenie buforowania. |
|ImageStoreConnectionString |SecureString |Static|Parametry połączenia z katalogiem głównym dla magazynu ImageStore. |
|ImageStoreMinimumTransferBPS | Int, wartość domyślna to 1024 |Dynamiczny|Minimalna szybkość transferu między klastrem i magazynu ImageStore. Ta wartość służy do określania limitu czasu podczas uzyskiwania dostępu do zewnętrznego magazynu ImageStore. Tę wartość należy zmienić tylko wtedy, gdy opóźnienie między klastrem i magazynu ImageStore jest wysokie, aby umożliwić pobieranie klastra z zewnętrznego magazynu ImageStore. |
|MaxUnusedAppTypeVersionsToKeep | Int, wartość domyślna to 3 |Dynamiczny|Ta konfiguracja określa liczbę nieużywanych wersji typu aplikacji do pominięcia na potrzeby oczyszczania. Ten parametr ma zastosowanie tylko wtedy, gdy parametr CleanupUnusedApplicationTypes jest włączony. <br/>*Najlepszym rozwiązaniem jest użycie domyślnego ( `3` ). Wartości mniejsze niż 1 są nieprawidłowe.*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, wartość domyślna to None|Dynamiczny|Określa zestaw MetricActivityThresholds dla metryk w klastrze. Równoważenie będzie działało, jeśli maxNodeLoad jest większa niż MetricActivityThresholds. W przypadku metryk defragmentacji definiuje ilość obciążenia równą lub poniżej, która Service Fabric będzie uwzględniać węzeł pusty |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, wartość domyślna to None|Dynamiczny|Określa zestaw MetricBalancingThresholds dla metryk w klastrze. Równoważenie będzie działało, jeśli wartość maxNodeLoad/minNodeLoad jest większa niż MetricBalancingThresholds. Defragmentacja będzie działała, jeśli maxNodeLoad/minNodeLoad w co najmniej jednym FD lub UD jest mniejszy niż MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, wartość domyślna to None|Dynamiczny|Określa część obciążenia, która jest dołączana z repliką, gdy jest zamieniony, przyjmuje wartość z zakresu od 0 (obciążenie nie jest nalepki z repliką) i 1 (Załaduj nowe wartości z repliką domyślnie) |

## <a name="namingservice"></a>NamingService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, wartość domyślna to 0 |Static|Maksymalna liczba wpisów przechowywanych w pamięci podręcznej Opis usługi LRU w ramach bramy nazewnictwa (wartość 0 powoduje brak limitu). |
|MaxClientConnections |Int, wartość domyślna to 1000 |Dynamiczny|Maksymalna dozwolona liczba połączeń klienta na bramę. |
|MaxFileOperationTimeout |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ wartość TimeSpan w sekundach. Maksymalny limit czasu dozwolony dla operacji usługi magazynu plików. Żądania określające większy limit czasu zostaną odrzucone. |
|MaxIndexedEmptyPartitions |Int, wartość domyślna to 1000 |Dynamiczny|Maksymalna liczba pustych partycji, które pozostaną indeksowane w pamięci podręcznej powiadomień w celu synchronizowania ponownie połączonych klientów. Wszystkie puste partycje powyżej tej liczby zostaną usunięte z indeksu w kolejności wersji wyszukiwania rosnącego. Ponowne łączenie klientów nadal może synchronizować i odbierać brakujące aktualizacje pustej partycji; jednak protokół synchronizacji jest droższy. |
|MaxMessageSize |Int, wartość domyślna to 4 \* 1024 \* 1024 |Static|Maksymalny rozmiar komunikatu dla komunikacji węzła klienta podczas korzystania z nazw. System DOS — łagodzenie ataków; wartość domyślna to 4 MB. |
|MaxNamingServiceHealthReports | Int, wartość domyślna to 10 |Dynamiczny|Maksymalna liczba wolnych operacji, w przypadku których nazwy raportów usługi magazynu są w złej kondycji. Jeśli 0; wszystkie wolne operacje są wysyłane. |
|MaxOperationTimeout |Czas w sekundach, wartość domyślna to 600 |Dynamiczny|Określ wartość TimeSpan w sekundach. Maksymalny limit czasu dozwolony dla operacji klienta. Żądania określające większy limit czasu zostaną odrzucone. |
|MaxOutstandingNotificationsPerClient |Int, wartość domyślna to 1000 |Dynamiczny|Maksymalna liczba oczekujących powiadomień przed zamknięciem rejestracji klienta przez bramę. |
|MinReplicaSetSize | Int, wartość domyślna to 3 |Niedozwolone| Minimalna liczba replik Usługa nazewnictwa wymaganych do zapisu w celu ukończenia aktualizacji. Jeśli w systemie jest mniej replik niż ta aktywna w systemie, system niezawodności nie zezwala na aktualizacje magazynu Usługa nazewnictwa do momentu przywrócenia replik. Ta wartość nigdy nie powinna być większa niż wartość targetreplicasetsize. |
|PartitionCount |Int, wartość domyślna to 3 |Niedozwolone|Liczba partycji magazynu Usługa nazewnictwa, które mają zostać utworzone. Każda partycja jest właścicielem jednego klucza partycji, który odpowiada jego indeksowi; klucze partycji [0; PartitionCount] istnieje. Zwiększenie liczby partycji Usługa nazewnictwa zwiększa skalę, w której Usługa nazewnictwa może wykonywać, zmniejszając średnią ilość danych przechowywanych przez wszystkie zapasowe zestawy replik; kosztem zwiększonego wykorzystania zasobów (ponieważ PartitionCount * ReplicaSetSize usługi muszą być utrzymywane).|
|PlacementConstraints | ciąg, wartość domyślna to "" |Niedozwolone| Ograniczenie umieszczania dla Usługa nazewnictwa. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Niedozwolone| Określ wartość TimeSpan w sekundach. Gdy Usługa nazewnictwa do utracie kworum; ten czasomierz rozpocznie się. Gdy wygaśnie, Radio będzie traktować repliki w dół jako utracone; i podjęto próbę odzyskania kworum. Nie oznacza to, że może to spowodować utratę danych. |
|RepairInterval | Czas w sekundach, wartość domyślna to 5 |Static| Określ wartość TimeSpan w sekundach. Interwał, w którym rozpocznie się naprawianie niespójności nazw między właścicielem urzędu i właścicielem nazwy. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to (60,0 * 30)|Niedozwolone| Określ wartość TimeSpan w sekundach. Gdy replika Usługa nazewnictwa ulegnie awarii; ten czasomierz rozpocznie się. Po wygaśnięciu FM rozpocznie się zamiana replik, które nie są jeszcze uwzględniane. |
|ServiceDescriptionCacheLimit | Int, wartość domyślna to 0 |Static| Maksymalna liczba wpisów przechowywanych w pamięci podręcznej Opis usługi LRU w usłudze Magazyn nazewnictwa (wartość 0 nie ma limitu). |
|ServiceNotificationTimeout |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ wartość TimeSpan w sekundach. Limit czasu używany podczas dostarczania powiadomień usługi do klienta. |
|StandByReplicaKeepDuration | Czas w sekundach, wartość domyślna to 3600,0 * 2 |Niedozwolone| Określ wartość TimeSpan w sekundach. Gdy Usługa nazewnictwa replika wraca ze stanu Down; być może został już zastąpiony. Ten czasomierz określa, jak długo Radio będzie utrzymywać replikę rezerwową przed odpisaniem. |
|Wartość targetreplicasetsize |Int, wartość domyślna to 7 |Niedozwolone|Liczba zestawów replik dla każdej partycji magazynu Usługa nazewnictwa. Zwiększenie liczby zestawów replik zwiększa poziom niezawodności informacji w magazynie Usługa nazewnictwa. zmniejszenie zmiany, że informacje zostaną utracone w wyniku awarii węzła; koszt zwiększonego obciążenia Windows Fabric i czas potrzebny do wykonania aktualizacji danych nazewnictwa.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, wartość domyślna to None|Dynamiczny|Wartość procentowa pojemności węzła na nazwę metryki; używany jako bufor, aby zachować pewne wolne miejsce w węźle dla przypadku pracy w trybie failover. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Static|Kolekcja pojemności węzłów dla różnych metryk. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Static|Opisuje domeny błędów, do których należy węzeł. Domena błędów jest definiowana za pomocą identyfikatora URI, który opisuje lokalizację węzła w centrum danych.  Identyfikatory URI domeny błędów mają format FD:/FD/, po którym następuje segment ścieżki URI.|
|UpgradeDomainId |ciąg, wartość domyślna to "" |Static|Opisuje domenę uaktualnienia, do której należy węzeł. |

## <a name="nodeproperties"></a>NodeProperties

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Static|Kolekcja par klucz-wartość dla właściwości węzła. |

## <a name="paas"></a>PaaS

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ClusterId |ciąg, wartość domyślna to "" |Niedozwolone|Magazyn certyfikatów x509 używany przez sieć szkieletową do ochrony konfiguracji. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|Liczniki |String | Dynamiczny |Rozdzielana przecinkami lista liczników wydajności do zbierania. |
|IsEnabled |Bool, wartość domyślna to true | Dynamiczny |Flaga wskazuje, czy jest włączona kolekcja liczników wydajności w węźle lokalnym. |
|MaxCounterBinaryFileSizeInMB |Int, wartość domyślna to 1 | Dynamiczny |Maksymalny rozmiar (w MB) dla każdego pliku binarnego licznika wydajności. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, wartość domyślna to 10 | Dynamiczny |Maksymalny interwał (w sekundach), po którym tworzony jest nowy plik binarny licznika wydajności. |
|SamplingIntervalInSeconds |Int, wartość domyślna to 60 | Dynamiczny |Interwał próbkowania zbieranych liczników wydajności. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia koligacji: 0: twarda; 1: nietrwałe; wartość ujemna: Ignoruj. |
|ApplicationCapacityConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia pojemności: 0: twarda; 1: nietrwałe; wartość ujemna: Ignoruj. |
|AutoDetectAvailableResources|bool, wartość domyślna to TRUE|Static|Ta konfiguracja spowoduje wyzwolenie automatycznego wykrywania dostępnych zasobów w węźle (procesor i pamięć), gdy ta konfiguracja zostanie ustawiona na wartość true — będziemy odczytywać rzeczywiste pojemności i poprawiać je, jeśli użytkownik określił nieprawidłowe pojemności węzłów lub nie zdefiniowano ich w ogóle, jeśli ta konfiguracja zostanie ustawiona na wartość false — zostanie wyświetlone ostrzeżenie informujące o tym, że użytkownik określił nieprawidłowe pojemności węzłów; ale nie zostaną one poprawione. oznacza to, że użytkownik chce mieć pojemności określone jako > niż w rzeczywistości czy w przypadku niezdefiniowania pojemności; przyjmie ona nieograniczoną pojemność |
|BalancingDelayAfterNewNode | Czas w sekundach, wartość domyślna to 120 |Dynamiczny|Określ wartość TimeSpan w sekundach. Nie uruchamiaj działań równoważenia obciążenia w tym okresie po dodaniu nowego węzła. |
|BalancingDelayAfterNodeDown | Czas w sekundach, wartość domyślna to 120 |Dynamiczny|Określ wartość TimeSpan w sekundach. Nie uruchamiaj działań równoważenia obciążenia w tym okresie po zdarzeniu w dół węzła. |
|CapacityConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia pojemności: 0: twarda; 1: nietrwałe; wartość ujemna: Ignoruj. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, wartość domyślna to 20 | Dynamiczny|Określa liczbę kolejnych porzuconych przesunięć ResourceBalancer przed wykonaniem diagnostyki i wyemitowaniu ostrzeżeń dotyczących kondycji. Wartość ujemna: żadne ostrzeżenia nie są emitowane w tym stanie. |
|ConstraintFixPartialDelayAfterNewNode | Czas w sekundach, wartość domyślna to 120 |Dynamiczny| Określ wartość TimeSpan w sekundach. DDo nie naprawiaj naruszeń ograniczenia FaultDomain i UpgradeDomain w tym okresie po dodaniu nowego węzła. |
|ConstraintFixPartialDelayAfterNodeDown | Czas w sekundach, wartość domyślna to 120 |Dynamiczny| Określ wartość TimeSpan w sekundach. Nie należy usuwać naruszeń ograniczenia FaultDomain i UpgradeDomain w tym okresie po zdarzeniu w dół węzła. |
|ConstraintViolationHealthReportLimit | Int, wartość domyślna to 50 |Dynamiczny| Określa, ile razy ograniczenie repliki ma być trwale nienaprawione przed przeprowadzeniem diagnostyki i Emituj raporty kondycji. |
|DetailedConstraintViolationHealthReportLimit | Int, wartość domyślna to 200 |Dynamiczny| Określa, ile razy naruszenie repliki ma być trwałe nienaprawione przed przeprowadzeniem diagnostyki, a szczegółowe raporty kondycji są emitowane. |
|DetailedDiagnosticsInfoListLimit | Int, wartość domyślna to 15 |Dynamiczny| Definiuje liczbę wpisów diagnostycznych (ze szczegółowymi informacjami) na ograniczenie do uwzględnienia przed obcinaniem w diagnostyce.|
|DetailedNodeListLimit | Int, wartość domyślna to 15 |Dynamiczny| Definiuje liczbę węzłów na ograniczenie do uwzględnienia przed obcinaniem w raportach nieumieszczonych replik. |
|DetailedPartitionListLimit | Int, wartość domyślna to 15 |Dynamiczny| Definiuje liczbę partycji na wpis diagnostyczny dla ograniczenia, które mają zostać uwzględnione przed obcinaniem w diagnostyce. |
|DetailedVerboseHealthReportLimit | Int, wartość domyślna to 200 | Dynamiczny|Określa, ile razy ma być trwała nieumieszczona replika, zanim szczegółowe raporty kondycji zostaną wyemitowane. |
|EnforceUserServiceMetricCapacities|bool, wartość domyślna to FALSE | Static |Włącza ochronę usług sieci szkieletowej. Wszystkie usługi użytkownika znajdują się w jednym obiekcie zadania/cgroup i są ograniczone do określonej ilości zasobów. Musi to być statyczne (wymaga ponownego uruchomienia elemencie fabrichost określono) jako tworzenia/usuwania obiektu zadania użytkownika i limitów ustawień wykonywanych podczas otwierania hosta sieci szkieletowej. |
|FaultDomainConstraintPriority | Int, wartość domyślna to 0 |Dynamiczny| Określa priorytet ograniczenia domeny błędów: 0: twarda; 1: nietrwałe; wartość ujemna: Ignoruj. |
|GlobalMovementThrottleCountingInterval | Czas w sekundach, wartość domyślna to 600 |Static| Określ wartość TimeSpan w sekundach. Wskazuje długość przeszłego interwału, dla którego ma być śledzone przesunięcia repliki domeny (używane razem z GlobalMovementThrottleThreshold). Można ustawić na 0, aby całkowicie zignorować globalne ograniczenie przepustowości. |
|GlobalMovementThrottleThreshold | Uint, wartość domyślna to 1000 |Dynamiczny| Maksymalna liczba przesunięć dozwolona w fazie równoważenia w poprzednim interwale wskazywanym przez GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, wartość domyślna to 0 | Dynamiczny|Maksymalna liczba przesunięć dozwolona w fazie równoważenia w przeszłości interwał wskazany przez GlobalMovementThrottleCountingInterval. wartość 0 oznacza brak limitu. |
|GlobalMovementThrottleThresholdForPlacement | Uint, wartość domyślna to 0 |Dynamiczny| Maksymalna liczba przesunięć dozwolona w fazie umieszczania w przeszłości interwału wskazywanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu.|
|GlobalMovementThrottleThresholdPercentage|Double, wartość domyślna to 0|Dynamiczny|Maksymalna liczba dozwolonych ruchów w fazach równoważenia i umieszczania (wyrażona jako procent łącznej liczby replik w klastrze) w przeszłości interwał wskazywany przez GlobalMovementThrottleCountingInterval. wartość 0 oznacza brak limitu. Jeśli określono oba te i GlobalMovementThrottleThreshold; następnie zostanie użyty bardziej ostrożny limit.|
|GlobalMovementThrottleThresholdPercentageForBalancing|Double, wartość domyślna to 0|Dynamiczny|Maksymalna liczba przesunięć dozwolona w fazie równoważenia (wyrażona jako procent całkowitej liczby replik w PLB) w przeszłości interwał wskazywany przez GlobalMovementThrottleCountingInterval. wartość 0 oznacza brak limitu. Jeśli określono oba te i GlobalMovementThrottleThresholdForBalancing; następnie zostanie użyty bardziej ostrożny limit.|
|InBuildThrottlingAssociatedMetric | ciąg, wartość domyślna to "" |Static| Skojarzona Nazwa metryki dla tego ograniczenia. |
|InBuildThrottlingEnabled | Bool, wartość domyślna to false |Dynamiczny| Ustal, czy włączono ograniczenie w kompilacji. |
|InBuildThrottlingGlobalMaxValue | Int, wartość domyślna to 0 |Dynamiczny|Maksymalna liczba replik w kompilacji, które są dozwolone globalnie. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, wartość domyślna to false | Dynamiczny|Określa, czy dowolny typ aktualizacji jednostki trybu failover powinien przerwać szybkie lub wolne równoważenie. Po podaniu określonego "false" przebiegu równoważenia zostanie przerwany, jeśli jednostka failover Unit: zostanie utworzony/usunięty; Brak replik; Zmieniono lokalizację podstawowej repliki lub zmieniono liczbę replik. Przebieg równoważenia nie zostanie przerwany w innych przypadkach — Jeśli jednostka failover Unit: ma dodatkowe repliki; Zmieniono jakąkolwiek flagę repliki; Zmieniono tylko wersję partycji lub dowolną inną wielkość liter. |
|MinConstraintCheckInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ wartość TimeSpan w sekundach. Określa minimalną ilość czasu, który musi upłynąć przed upływem dwóch kolejnych kontroli ograniczeń. |
|MinLoadBalancingInterval | Czas w sekundach, wartość domyślna to 5 |Dynamiczny| Określ wartość TimeSpan w sekundach. Określa minimalny czas, który musi upłynąć przed upływem dwóch kolejnych operacji równoważenia obciążenia. |
|MinPlacementInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ wartość TimeSpan w sekundach. Określa minimalny czas, który musi upłynąć przed dwoma kolejnymi zaokrąglami umieszczania. |
|MoveExistingReplicaForPlacement | Bool, wartość domyślna to true |Dynamiczny|Ustawienie określające, czy przenieść istniejącą replikę podczas umieszczania. |
|MovementPerPartitionThrottleCountingInterval | Czas w sekundach, wartość domyślna to 600 |Static| Określ wartość TimeSpan w sekundach. Wskazuje długość przeszłego interwału, dla którego mają być śledzone przesunięcia replik dla każdej partycji (używane razem z MovementPerPartitionThrottleThreshold). |
|MovementPerPartitionThrottleThreshold | Uint, wartość domyślna to 50 |Dynamiczny| Brak przenoszenia związanego z równoważeniem obciążenia dla partycji, jeśli liczba przepływów związanych z równoważeniem dla replik tej partycji osiągnęła lub przekroczyła MovementPerFailoverUnitThrottleThreshold w ciągu ostatnich interwałów wskazywanych przez MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, wartość domyślna to false |Dynamiczny| Ustawienie określające, czy można przenosić repliki nadrzędne w celu naprawy ograniczeń koligacji.|
|PartiallyPlaceServices | Bool, wartość domyślna to true |Dynamiczny| Określa, czy wszystkie repliki usługi w klastrze mają być umieszczone "wszystkie, czy nie", w odniesieniu do nich są ograniczone odpowiednie węzły.|
|PlaceChildWithoutParent | Bool, wartość domyślna to true | Dynamiczny|Ustawienie określające, czy można umieścić replikę podrzędną usługi w przypadku braku repliki nadrzędnej. |
|PlacementConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia położenia: 0: twarda; 1: nietrwałe; wartość ujemna: Ignoruj. |
|PlacementConstraintValidationCacheSize | Int, wartość domyślna to 10000 |Dynamiczny| Ogranicza rozmiar tabeli używanej do szybkiej walidacji i buforowania wyrażeń ograniczeń umieszczania. |
|PlacementSearchTimeout | Czas w sekundach, wartość domyślna to 0,5 |Dynamiczny| Określ wartość TimeSpan w sekundach. Podczas umieszczania usług; Wyszukaj o największej długości przed zwróceniem wyniku. |
|PLBRefreshGap | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ wartość TimeSpan w sekundach. Określa minimalny czas, który musi upłynąć przed ponownym odświeżaniem PLB. |
|PreferredLocationConstraintPriority | Int, wartość domyślna to 2| Dynamiczny|Określa priorytet ograniczenia preferowanej lokalizacji: 0: twarda; 1: nietrwałe; 2: Optymalizacja; wartość ujemna: Ignoruj |
|PreferredPrimaryDomainsConstraintPriority| Int, wartość domyślna to 1 | Dynamiczny| Określa priorytet preferowanego ograniczenia domeny podstawowej: 0: twarda; 1: nietrwałe; wartość ujemna: Ignoruj |
|PreferUpgradedUDs|bool, wartość domyślna to FALSE|Dynamiczny|Włącza i wyłącza logikę, która preferuje przeniesienie do już uaktualnionej wersji. Począwszy od SF 7,0, wartość domyślna dla tego parametru jest zmieniana z TRUE na FALSE.|
|PreventTransientOvercommit | Bool, wartość domyślna to false | Dynamiczny|Określa, czy PLB od razu liczą zasoby, które zostaną zwolnione przez zainicjowane przeniesienia. Domyślnie; PLB może inicjować przenoszenie i poruszać się w tym samym węźle, który może tworzyć przejściowe nadmiarowe. Ustawienie tego parametru na wartość true uniemożliwi wyłączenie tych rodzajów zatwierdzeń i defragmentacji na żądanie (placementWithMove). |
|ScaleoutCountConstraintPriority | Int, wartość domyślna to 0 |Dynamiczny| Określa priorytet ograniczenia liczby skalowania: 0: twarda; 1: nietrwałe; wartość ujemna: Ignoruj. |
|SubclusteringEnabled|Bool, wartość domyślna to FALSE | Dynamiczny |Potwierdź podklastry podczas obliczania odchylenia standardowego na potrzeby równoważenia |
|SubclusteringReportingPolicy| Int, wartość domyślna to 1 |Dynamiczny|Definiuje, w jaki sposób i czy raporty kondycji klastra są wysyłane: 0: nie zgłaszaj; 1: Ostrzeżenie; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | ciąg, wartość domyślna to ""|Static| Skojarzona Nazwa metryki dla tego ograniczenia. |
|SwapPrimaryThrottlingEnabled | Bool, wartość domyślna to false|Dynamiczny| Ustal, czy jest włączone ograniczenie wymiany. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, wartość domyślna to 0 |Dynamiczny| Maksymalna liczba replik podstawowych, które są dozwolone globalnie. |
|TraceCRMReasons |Bool, wartość domyślna to true |Dynamiczny|Określa, czy mają być śledzone przyczyny przesunięć przez programu CRM do kanału zdarzeń operacyjnych. |
|UpgradeDomainConstraintPriority | Int, wartość domyślna to 1| Dynamiczny|Określa priorytet ograniczenia domeny uaktualnienia: 0: twardy; 1: nietrwałe; wartość ujemna: Ignoruj. |
|UseMoveCostReports | Bool, wartość domyślna to false | Dynamiczny|Instruuje LB, aby zignorować element Cost funkcji oceniania; to, że potencjalnie duża liczba przeniesień do lepszego zrównoważonego umieszczania. |
|UseSeparateSecondaryLoad | Bool, wartość domyślna to true | Dynamiczny|Ustawienie określające, czy do replik pomocniczych należy używać oddzielnego obciążenia. |
|UseSeparateSecondaryMoveCost | Bool, wartość domyślna to false | Dynamiczny|Ustawienie określające, czy do replik pomocniczych należy używać osobnego kosztu przenoszenia. |
|ValidatePlacementConstraint | Bool, wartość domyślna to true |Dynamiczny| Określa, czy wyrażenie PlacementConstraint dla usługi jest sprawdzane podczas aktualizowania ServiceDescription usługi. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, wartość domyślna to TRUE |Dynamiczny|Określa, czy wyrażenie PlacementConstraint dla usługi jest oceniane dla preferencji głównych w trybie failover. |
|VerboseHealthReportLimit | Int, wartość domyślna to 20 | Dynamiczny|Określa, ile razy należy umieścić replikę, zanim zostanie zgłoszone ostrzeżenie o kondycji (jeśli jest włączone pełne Raportowanie kondycji). |
|NodeLoadsOperationalTracingEnabled | Bool, wartość domyślna to true |Dynamiczny|Konfiguracja, która umożliwia śledzenie operacji w ramach obciążenia węzła w magazynie zdarzeń. |
|NodeLoadsOperationalTracingInterval | TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (20) | Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał, z jakim śledzenie węzłów jest ładowane do magazynu zdarzeń dla każdej domeny usługi. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Czas w sekundach, wartość domyślna to 900 |Dynamiczny|Określ wartość TimeSpan w sekundach. Czas oczekiwania systemu przed przerwaniem hostów usługi, które mają repliki, które są zamykane podczas uaktualniania aplikacji.|
|FabricUpgradeMaxReplicaCloseDuration | Czas w sekundach, wartość domyślna to 900 |Dynamiczny| Określ wartość TimeSpan w sekundach. Czas oczekiwania systemu przed przerwaniem hostów usługi, które mają repliki, które są zablokowane podczas uaktualniania sieci szkieletowej. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (120)|Dynamiczny|Określ wartość TimeSpan w sekundach. Czas oczekiwania systemu przed przerwaniem hostów usługi, które mają repliki, które są zablokowane. Jeśli ta wartość jest równa 0, repliki nie będą nakazuje zamknięcia.|
|NodeDeactivationMaxReplicaCloseDuration | Czas w sekundach, wartość domyślna to 900 |Dynamiczny|Określ wartość TimeSpan w sekundach. Czas oczekiwania systemu przed przerwaniem hostów usługi, które mają repliki, które są zamykane podczas dezaktywacji węzła. |
|PeriodicApiSlowTraceInterval | Czas w sekundach, wartość domyślna to 5 minut. |Dynamiczny| Określ wartość TimeSpan w sekundach. PeriodicApiSlowTraceInterval definiuje interwał, w jakim wolne wywołania interfejsu API zostaną prześledzone przez Monitor interfejsu API. |
|ReplicaChangeRoleFailureRestartThreshold|int, wartość domyślna to 10|Dynamiczny| Całkowitą. Określ liczbę błędów interfejsu API podczas podwyższania poziomu, po których zostanie zastosowana akcja autołagodzenia (ponowne uruchomienie repliki). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, wartość domyślna to 2147483647|Dynamiczny| Całkowitą. Określ liczbę błędów interfejsu API podczas podwyższania poziomu, po których zostanie zgłoszony Raport kondycji ostrzeżenia.|
|ServiceApiHealthDuration | Czas w sekundach, wartość domyślna to 30 minut. |Dynamiczny| Określ wartość TimeSpan w sekundach. ServiceApiHealthDuration definiuje, jak długo czekamy na uruchomienie interfejsu API usługi przed zgłoszeniem nieprawidłowej kondycji. |
|ServiceReconfigurationApiHealthDuration | Czas w sekundach, wartość domyślna to 30 |Dynamiczny| Określ wartość TimeSpan w sekundach. ServiceReconfigurationApiHealthDuration określa, jak długo czekamy na uruchomienie interfejsu API usługi przed zgłoszeniem złej kondycji. Dotyczy to wywołań interfejsu API, które mają wpływ na dostępność.|

## <a name="replication"></a>Replikacja
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, wartość domyślna to common:: TimeSpan:: FromMilliseconds (15)|Static|Określ wartość TimeSpan w sekundach. Określa czas oczekiwania przez replikatora po odebraniu operacji przed wysłaniem potwierdzenia do końca. Inne operacje odebrane w tym okresie będą musiały zostać przesłane z powrotem w jednej wiadomości — > zmniejszenie ruchu sieciowego, ale może to zmniejszyć przepływność replikatora.|
|MaxCopyQueueSize|uint, wartość domyślna to 1024|Static|Jest to maksymalna wartość definiująca początkowy rozmiar kolejki, która zachowuje operacje replikacji. Należy pamiętać, że musi być potęgą liczby 2. Jeśli w czasie wykonywania Kolejka powiększa się do tej operacji, ta operacja zostanie ograniczona między replikacją podstawową i pomocniczą.|
|MaxPrimaryReplicationQueueMemorySize|uint, wartość domyślna to 0|Static|Jest to maksymalna wartość głównej kolejki replikacji w bajtach.|
|MaxPrimaryReplicationQueueSize|uint, wartość domyślna to 1024|Static|Jest to maksymalna liczba operacji, które mogą istnieć w podstawowej kolejce replikacji. Należy pamiętać, że musi być potęgą liczby 2.|
|MaxReplicationMessageSize|uint, wartość domyślna to 52428800|Static|Maksymalny rozmiar komunikatu operacji replikacji. Wartość domyślna to 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, wartość domyślna to 0|Static|Jest to maksymalna wartość kolejki replikacji pomocniczej w bajtach.|
|MaxSecondaryReplicationQueueSize|uint, wartość domyślna to 2048|Static|Jest to maksymalna liczba operacji, które mogą istnieć w pomocniczej kolejce replikacji. Należy pamiętać, że musi być potęgą liczby 2.|
|QueueHealthMonitoringInterval|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (30)|Static|Określ wartość TimeSpan w sekundach. Ta wartość określa czas używany przez Replikator do monitorowania wszystkich zdarzeń kondycji ostrzeżeń/błędów w kolejkach operacji replikacji. Wartość "0" powoduje wyłączenie monitorowania kondycji |
|QueueHealthWarningAtUsagePercent|uint, wartość domyślna to 80|Static|Ta wartość określa użycie kolejki replikacji (w procentach), po której raportowane jest ostrzeżenie o wysokim użyciu kolejki. Należy to zrobić po upływie interwału prolongaty QueueHealthMonitoringInterval. Jeśli użycie kolejki spadnie poniżej tego procentu w interwale prolongaty|
|ReplicatorAddress|ciąg, wartość domyślna to "localhost: 0"|Static|Punkt końcowy w formie ciągu "IP: Port", który jest używany przez Replikator Windows Fabric do nawiązywania połączeń z innymi replikami w celu wysyłania/odbierania operacji.|
|ReplicatorListenAddress|ciąg, wartość domyślna to "localhost: 0"|Static|Punkt końcowy w postaci ciągu "IP: Port", który jest używany przez Replikator Windows Fabric do odbierania operacji z innych replik.|
|ReplicatorPublishAddress|ciąg, wartość domyślna to "localhost: 0"|Static|Punkt końcowy w postaci ciągu "IP: Port", który jest używany przez Replikator Windows Fabric do wysyłania operacji do innych replik.|
|RetryInterval|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (5)|Static|Określ wartość TimeSpan w sekundach. Gdy operacja zostanie utracona lub odrzucona, ten czasomierz decyduje o tym, jak często Replikator ponowi próbę wysłania operacji.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|IsEnabled|bool, wartość domyślna to FALSE |Static|Kontroluje, czy usługa jest włączona w klastrze, czy nie. |

## <a name="runas"></a>Uruchamia

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje nazwę konta RunAs. Ta wartość jest wymagana tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena \ użytkownik" lub " user@domain ". |
|RunAsAccountType|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne w przypadku wszystkich prawidłowych wartości sekcji RunAs to "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Ta wartość jest wymagana tylko dla typu konta "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje nazwę konta RunAs. Ta wartość jest wymagana tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena \ użytkownik" lub " user@domain ". |
|RunAsAccountType|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne w przypadku wszystkich prawidłowych wartości sekcji RunAs to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Ta wartość jest wymagana tylko dla typu konta "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje nazwę konta RunAs. Ta wartość jest wymagana tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena \ użytkownik" lub " user@domain ". |
|RunAsAccountType|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne w przypadku wszystkich prawidłowych wartości sekcji RunAs to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Ta wartość jest wymagana tylko dla typu konta "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|RunAsAccountName |ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje nazwę konta RunAs. Ta wartość jest wymagana tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena \ użytkownik" lub " user@domain ". |
|RunAsAccountType|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne w przypadku wszystkich prawidłowych wartości sekcji RunAs to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|ciąg, wartość domyślna to "" |Dynamiczny|Wskazuje hasło do konta Uruchom jako. Ta wartość jest wymagana tylko dla typu konta "DomainUser". |

## <a name="security"></a>Zabezpieczenia
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|ciąg, wartość domyślna to ""|Static|Format punktu końcowego certyfikatu usługi AAD, domyślny komercyjny dla platformy Azure, określony dla środowiska innego niż domyślne, takie jak Azure Government "https: \/ /login.microsoftonline.us/ {0} /federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|ciąg, wartość domyślna to ""|Static|Nazwa lub identyfikator natywnej aplikacji klienckiej reprezentującej klientów sieci szkieletowej |
|AADClusterApplication|ciąg, wartość domyślna to ""|Static|Nazwa lub identyfikator aplikacji internetowego interfejsu API reprezentujący klaster |
|AADLoginEndpoint|ciąg, wartość domyślna to ""|Static|Punkt końcowy logowania usługi AAD, domyślna komercyjna platforma Azure określona dla środowiska innego niż domyślne, takie jak Azure Government "https: \/ /login.microsoftonline.us" |
|AADTenantId|ciąg, wartość domyślna to ""|Static|Identyfikator dzierżawy (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, wartość domyślna to FALSE|Dynamiczny|Flaga oznaczająca, czy akceptować wygasłe certyfikaty klastrów zadeklarowane przez odcisk palca mają zastosowanie tylko do certyfikatów klastra; tak, aby zachować aktywność klastra. |
|AdminClientCertThumbprints|ciąg, wartość domyślna to ""|Dynamiczny|Odciski palca certyfikatów używanych przez klientów w roli administratora. Jest to lista nazw rozdzielonych przecinkami. |
|AADTokenEndpointFormat|ciąg, wartość domyślna to ""|Static|Punkt końcowy tokenu usługi AAD, domyślny komercyjny dla platformy Azure, określony dla środowiska innego niż domyślne, takie jak Azure Government "https: \/ /login.microsoftonline.us/ {0} " |
|AdminClientClaims|ciąg, wartość domyślna to ""|Dynamiczny|Wszystkie możliwe oświadczenia oczekiwane przez klientów administracyjnych; ten sam format co ClientClaims; Ta lista jest wewnętrznie dodawana do ClientClaims; nie trzeba również dodawać tych samych wpisów do ClientClaims. |
|AdminClientIdentities|ciąg, wartość domyślna to ""|Dynamiczny|Tożsamości systemu Windows klientów sieci szkieletowej w roli administratora; służy do autoryzacji uprzywilejowanych operacji sieci szkieletowej. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwą konta domeny lub grupą. Dla wygody; konto, na którym działa fabric.exe jest automatycznie przypisana rola administratora; jest to grupa ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|ciąg, wartość domyślna to/Home/sfuser/sfusercerts |Static|Folder, w którym znajdują się AppRunAsAccountGroup certyfikaty x509 i klucze prywatne |
|CertificateExpirySafetyMargin|TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (43200)|Static|Określ wartość TimeSpan w sekundach. Margines bezpieczeństwa dla wygaśnięcia certyfikatu; stan raportu kondycji certyfikatu zmienia się z OK na ostrzeżenie, gdy okres ważności jest bliższy. Wartość domyślna to 30 dni. |
|CertificateHealthReportingInterval|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (3600 * 8)|Static|Określ wartość TimeSpan w sekundach. Określ interwał raportowania kondycji certyfikatu; Domyślnie 8 godzin; ustawienie wartości 0 powoduje wyłączenie raportowania kondycji certyfikatów |
|ClientCertThumbprints|ciąg, wartość domyślna to ""|Dynamiczny|Odciski palców certyfikatów używanych przez klientów do komunikacji z klastrem; klaster używa tego autoryzowanego połączenia przychodzącego. Jest to lista nazw rozdzielonych przecinkami. |
|ClientClaimAuthEnabled|bool, wartość domyślna to FALSE|Static|Wskazuje, czy na klientach jest włączone uwierzytelnianie oparte na żądaniach. ustawienie tego ustawienia prawda niejawnie ustawia ClientRoleEnabled. |
|ClientClaims|ciąg, wartość domyślna to ""|Dynamiczny|Wszystkie możliwe oświadczenia oczekiwane od klientów na potrzeby łączenia się z bramą. To jest lista "OR": ClaimsEntry \| \| ClaimsEntry \| \| ClaimsEntry... Każdy ClaimsEntry jest listą "i": ClaimType = ClaimValue && Claim = ClaimValue && Claimname = ClaimValue... |
|ClientIdentities|ciąg, wartość domyślna to ""|Dynamiczny|Tożsamości systemu Windows FabricClient; Brama nazw używa tego do autoryzacji połączeń przychodzących. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwą konta domeny lub grupą. Dla wygody; konto, na którym działa fabric.exe jest automatycznie dozwolone; są to grupy ServiceFabricAllowedUsers i ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, wartość domyślna to FALSE|Static|Wskazuje, czy rola klienta jest włączona; Po ustawieniu na wartość true; Klienci mają przypisane role na podstawie ich tożsamości. W wersji 2; Włączenie tego oznacza, że klient nie w AdminClientCommonNames/AdminClientIdentities może wykonywać tylko operacje tylko do odczytu. |
|ClusterCertThumbprints|ciąg, wartość domyślna to ""|Dynamiczny|Odciski palca certyfikatów, które mogą dołączać do klastra; rozdzielana przecinkami lista nazw. |
|ClusterCredentialType|ciąg, wartość domyślna to "Brak"|Niedozwolone|Wskazuje typ poświadczeń zabezpieczeń do użycia w celu zabezpieczenia klastra. Prawidłowe wartości to "none/x509/Windows" |
|ClusterIdentities|ciąg, wartość domyślna to ""|Dynamiczny|Tożsamości systemu Windows węzłów klastra; używany do autoryzacji członkostwa w klastrze. Jest to rozdzielana przecinkami lista; Każdy wpis jest nazwą konta domeny lub nazwą grupy |
|ClusterSpn|ciąg, wartość domyślna to ""|Niedozwolone|Nazwa główna usługi klastra; gdy sieć szkieletowa działa jako pojedynczy użytkownik domeny (gMSA/konto użytkownika domeny). Jest to nazwa SPN odbiornika i odbiorników dzierżawy w fabric.exe: detektory federacyjne; odbiorniki replikacji wewnętrznej; odbiornik usługi środowiska uruchomieniowego i odbiornik bramy nazewnictwa. Ta strona powinna pozostać pusta, gdy sieć szkieletowa działa jako konta komputera; w takim przypadku łączenie SPN usługi obliczeniowej z adresem transportu odbiornika. |
|CrlCheckingFlag|uint, wartość domyślna to 0x40000000|Dynamiczny|Flaga walidacji domyślnego łańcucha certyfikatów; może być zastąpiona przez flagę specyficzną dla składnika; np. Federacji/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY ustawieniem 0 wyłącza sprawdzanie listy CRL pełna lista obsługiwanych wartości jest udokumentowana przez flagiDW CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (15)|Dynamiczny|Określ wartość TimeSpan w sekundach. Jak długo sprawdzanie listy CRL jest wyłączone dla danego certyfikatu po napotkaniu błędu offline; Jeśli błąd offline listy CRL można zignorować. |
|CrlOfflineHealthReportTtl|TimeSpan, wartość domyślna to common:: TimeSpan:: FromMinutes (1440)|Dynamiczny|Określ wartość TimeSpan w sekundach. |
|DisableFirewallRuleForDomainProfile| bool, wartość domyślna to TRUE |Static| Wskazuje, czy reguła zapory nie powinna być włączona dla profilu domeny |
|DisableFirewallRuleForPrivateProfile| bool, wartość domyślna to TRUE |Static| Wskazuje, czy reguła zapory nie powinna być włączona dla profilu prywatnego | 
|DisableFirewallRuleForPublicProfile| bool, wartość domyślna to TRUE | Static|Wskazuje, czy reguła zapory nie powinna być włączona dla profilu publicznego |
| EnforceLinuxMinTlsVersion | bool, wartość domyślna to FALSE | Static | Jeśli wartość jest równa true; Obsługiwane są tylko protokoły TLS w wersji 1.2 +.  Jeśli wartość jest równa false; Obsługa wcześniejszych wersji protokołu TLS. Dotyczy tylko systemu Linux |
| EnforcePrevalidationOnSecurityChanges | bool, wartość domyślna to FALSE| Dynamiczny | Flaga kontrolująca zachowanie uaktualniania klastra podczas wykrywania zmian ustawień zabezpieczeń. Jeśli ustawiono wartość "true", uaktualnienie klastra podejmie próbę upewnienia się, że co najmniej jeden certyfikat pasujący do dowolnej z reguł prezentacji może przekazać odpowiednią regułę walidacji. Przed zainstalowaniem nowych ustawień do dowolnego węzła zostanie wykonane wstępne sprawdzanie poprawności, ale jest ono uruchamiane tylko w węźle hostującym podstawową replikę usługi Cluster Manager w momencie inicjowania uaktualnienia. Wartość domyślna jest obecnie ustawiona na wartość "false"; począwszy od wersji 7,1, ustawienie zostanie ustawione na wartość "true" dla nowych klastrów usługi Azure Service Fabric.|
|FabricHostSpn| ciąg, wartość domyślna to "" |Static| Nazwa główna usługi elemencie fabrichost określono; gdy sieć szkieletowa działa jako pojedynczy użytkownik domeny (gMSA/konto użytkownika domeny) i elemencie fabrichost określono działa w ramach konta komputera. Jest to nazwa SPN odbiornika IPC dla elemencie fabrichost określono; Domyślnie powinno pozostać puste, ponieważ elemencie fabrichost określono jest uruchamiany w ramach konta komputera |
|IgnoreCrlOfflineError|bool, wartość domyślna to FALSE|Dynamiczny|Czy ignorowanie błędów offline listy CRL, gdy po stronie serwera sprawdza przychodzące certyfikaty klienta |
|IgnoreSvrCrlOfflineError|bool, wartość domyślna to TRUE|Dynamiczny|Czy ignorowanie błędów offline listy CRL, gdy po stronie klienta sprawdza przychodzące certyfikaty serwera; wartość domyślna to true. Ataki z odwołanymi certyfikatami serwera wymagają naruszenia systemu DNS; trudniejsze niż w przypadku odwołanych certyfikatów klienta. |
|ServerAuthCredentialType|ciąg, wartość domyślna to "Brak"|Static|Wskazuje typ poświadczeń zabezpieczeń, które mają być używane w celu zabezpieczenia komunikacji między FabricClient i klastrem. Prawidłowe wartości to "none/x509/Windows" |
|ServerCertThumbprints|ciąg, wartość domyślna to ""|Dynamiczny|Odciski palców certyfikatów serwera używanych przez klaster do komunikowania się z klientami; Klienci używają tego do uwierzytelniania klastra. Jest to lista nazw rozdzielonych przecinkami. |
|SettingsX509StoreName| ciąg, wartość domyślna to "MY"| Dynamiczny|Magazyn certyfikatów x509 używany przez sieć szkieletową do ochrony konfiguracji |
|UseClusterCertForIpcServerTlsSecurity|bool, wartość domyślna to FALSE|Static|Czy używać certyfikatu klastra do zabezpieczania jednostki transportowej TLS serwera IPC |
|X509Folder|ciąg, wartość domyślna to/var/lib/waagent|Static|Folder, w którym znajdują się certyfikaty x509 i klucze prywatne |
|TLS1_2_CipherList| ciąg| Static|Jeśli ustawiono na niepusty ciąg; zastępuje obsługiwaną listę szyfrową dla protokołu TLS 1.2 i poniżej. Zobacz dokumentację "OpenSSL-ciphers", aby pobrać obsługiwaną listę szyfrową i przykład formatu listy mocnej szyfrowej listy dla protokołu TLS 1.2: "ECDHE-ECDSA-AES256-GCM-SHA384: ECDHE-ECDSA-AES128-GCM-SHA256: ECDHE-RSA-AES256-GCM-SHA384: ECDHE-RSA-AES-128-GCM-SHA256: ECDHE-ECDSA-AES256-CBC-SHA384: ECDHE-ECDSA-AES128-CBC-SHA256: ECDHE-RSA-AES256-CBC-SHA384: ECDHE-RSA-AES128-CBC-SHA256" dotyczy tylko systemu Linux. |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, wartość domyślna to None|Dynamiczny|Jest to lista par "name" i "value". Każdy "name" ma wspólną nazwę podmiotu lub DnsName certyfikaty x509 autoryzowane dla operacji klienta administratora. Dla danej "name", "value" jest oddzielną listą odcisków palców certyfikatów dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów klienta administratora musi znajdować się na liście. |

## <a name="securityclientaccess"></a>Security/ClientAccess

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ActivateNode |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla aktywacji węzła. |
|CancelTestCommand |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Anuluje określony TestCommand — jeśli jest w locie. |
|CodePackageControl |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń służąca do ponownego uruchamiania pakietów kodu. |
|Operacji CreateApplication |ciąg, wartość domyślna to "Administrator" | Dynamiczny|Konfiguracja zabezpieczeń dla tworzenia aplikacji. |
|CreateComposeDeployment|ciąg, wartość domyślna to "Administrator"| Dynamiczny|Tworzy wdrożenie redagowania opisane przez redagowanie plików |
|CreateGatewayResource|ciąg, wartość domyślna to "Administrator"| Dynamiczny|Tworzenie zasobu bramy |
|Nazwa |ciąg, wartość domyślna to "Administrator" |Dynamiczny|Konfiguracja zabezpieczeń dla tworzenia nazw identyfikatorów URI. |
|Sieć|ciąg, wartość domyślna to "Administrator" |Dynamiczny|Tworzy sieć kontenera |
|CreateService |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla tworzenia usług. |
|CreateServiceFromTemplate |ciąg, wartość domyślna to "Administrator" |Dynamiczny|Konfiguracja zabezpieczeń dla tworzenia usługi z szablonu. |
|Wolumin|ciąg, wartość domyślna to "Administrator"|Dynamiczny|Tworzy wolumin |
|Operacja deactivatenode |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla dezaktywowania węzła. |
|DeactivateNodesBatch |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla dezaktywowania wielu węzłów. |
|Usuń |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracje zabezpieczeń dla operacji usuwania klienta magazynu obrazów. |
|Operacji deleteapplication |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń do usunięcia aplikacji. |
|DeleteComposeDeployment|ciąg, wartość domyślna to "Administrator"| Dynamiczny|Usuwa wdrożenie redagowania |
|DeleteGatewayResource|ciąg, wartość domyślna to "Administrator"| Dynamiczny|Usuwa zasób bramy |
|Usuńname |ciąg, wartość domyślna to "Administrator" |Dynamiczny|Konfiguracja zabezpieczeń dotycząca usuwania nazw URI. |
|DeleteNetwork|ciąg, wartość domyślna to "Administrator" |Dynamiczny|Usuwa sieć kontenera |
|DeleteService |ciąg, wartość domyślna to "Administrator" |Dynamiczny|Konfiguracja zabezpieczeń do usunięcia usługi. |
|DeleteVolume|ciąg, wartość domyślna to "Administrator"|Dynamiczny|Usuwa woluminu.| 
|EnumerateProperties |ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń dla wyliczania właściwości nazewnictwa. |
|EnumerateSubnames |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla wyliczania identyfikatorów URI. |
|FileContent |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń transferu plików klienta na potrzeby magazynu obrazów (zewnętrzna z klastrem). |
|FileDownload |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla inicjowania pliku klienta z magazynem obrazów (zewnętrzna z klastrem). |
|FinishInfrastructureTask |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń służąca do kończenia zadań związanych z infrastrukturą. |
|Raporcie getchaosreport | ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Pobiera stan chaos w danym przedziale czasu. |
|GetClusterConfiguration | ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Wywołuje GetClusterConfiguration na partycji. |
|GetClusterConfigurationUpgradeStatus | ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Wywołuje GetClusterConfigurationUpgradeStatus na partycji. |
|GetFabricUpgradeStatus |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń do sondowania stanu uaktualnienia klastra. |
|GetFolderSize |ciąg, wartość domyślna to "Administrator" |Dynamiczny|Konfiguracja zabezpieczeń dla rozmiaru folderu pobierania FileStoreService |
|GetNodeDeactivationStatus |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń służąca do sprawdzania stanu dezaktywacji. |
|GetNodeTransitionProgress | ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń do uzyskiwania postępu dla polecenia przejścia węzła. |
|GetPartitionDataLossProgress | ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Pobiera postęp wywołania interfejsu API utraty danych. |
|GetPartitionQuorumLossProgress | ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Pobiera postęp wywołania interfejsu API utraty kworum. |
|GetPartitionRestartProgress | ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Pobiera postęp wywołania interfejsu API ponownego uruchamiania partycji. |
|Getsecrets|ciąg, wartość domyślna to "Administrator"|Dynamiczny|Pobierz wartości klucza tajnego |
|GetServiceDescription |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla powiadomień o usłudze w przypadku długich sondowań i odczytywanie opisów usług. |
|GetStagingLocation |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla pobierania lokalizacji tymczasowej klienta magazynu obrazów. |
|GetStoreLocation |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla pobierania lokalizacji magazynu klienta magazynu obrazów. |
|GetUpgradeOrchestrationServiceState|ciąg, wartość domyślna to "Administrator"| Dynamiczny|Wywołuje GetUpgradeOrchestrationServiceState na partycji |
|GetUpgradesPendingApproval |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Wywołuje GetUpgradesPendingApproval na partycji. |
|GetUpgradeStatus |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń do sondowania stanu uaktualnienia aplikacji. |
|InternalList |ciąg, wartość domyślna to "Administrator" | Dynamiczny|Konfiguracja zabezpieczeń dla operacji na liście plików klienta magazynu obrazów (wewnętrzna). |
|InvokeContainerApi|ciąg, wartość domyślna to "Administrator"|Dynamiczny|Wywołaj interfejs API kontenera |
|InvokeInfrastructureCommand |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla poleceń zarządzania zadaniami infrastruktury. |
|InvokeInfrastructureQuery |ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń do wykonywania zapytań dotyczących zadań infrastruktury. |
|Lista |ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń dla operacji na liście plików klienta magazynu obrazów. |
|MoveNextFabricUpgradeDomain |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń służąca do wznawiania uaktualnień klastra z jawną domeną uaktualnienia. |
|MoveNextUpgradeDomain |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń służąca do wznawiania uaktualnień aplikacji z jawną domeną uaktualnienia. |
|MoveReplicaControl |ciąg, wartość domyślna to "Administrator" | Dynamiczny|Przenieś replikę. |
|NameExists |ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń dla sprawdzania istnienia identyfikatorów URI. |
|NodeControl |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń do uruchomienia; zatrzymanie i ponownie uruchamiając węzły. |
|Elementu nodestateremoved |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Usunięto konfigurację zabezpieczeń dla stanu węzła raportowania. |
|Ping |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla poleceń ping klienta. |
|PredeployPackageToNode |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Interfejs API preinstalacji. |
|PrefixResolveService |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla rozpoznawania prefiksów usługi opartej na skargach. |
|PropertyReadBatch |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla operacji odczytu właściwości. |
|PropertyWriteBatch |ciąg, wartość domyślna to "Administrator" |Dynamiczny|Konfiguracje zabezpieczeń do nazewnictwa operacji zapisu właściwości. |
|ProvisionApplicationType |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla aprowizacji typu aplikacji. |
|ProvisionFabric |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla inicjowania obsługi manifestu MSI i/lub klastra. |
|Zapytanie |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla zapytań. |
|RecoverPartition |ciąg, wartość domyślna to "Administrator" | Dynamiczny|Konfiguracja zabezpieczeń na potrzeby odzyskiwania partycji. |
|RecoverPartitions |ciąg, wartość domyślna to "Administrator" | Dynamiczny|Konfiguracja zabezpieczeń na potrzeby odzyskiwania partycji. |
|RecoverServicePartitions |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń na potrzeby odzyskiwania partycji usługi. |
|RecoverSystemPartitions |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń na potrzeby odzyskiwania partycji usługi systemowej. |
|RemoveNodeDeactivations |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń na potrzeby wycofywania dezaktywacji na wielu węzłach. |
|ReportFabricUpgradeHealth |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń służąca do wznawiania uaktualnień klastra z bieżącym postępem uaktualniania. |
|ReportFault |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla błędu raportowania. |
|ReportHealth |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla kondycji raportowania. |
|ReportUpgradeHealth |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń służąca do wznawiania uaktualnień aplikacji z bieżącym postępem uaktualniania. |
|ResetPartitionLoad |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń służąca do resetowania obciążenia dla elementu jednostka failover Unit. |
|ResolveNameOwner |ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń służąca do rozpoznawania właściciela nazewnictwa URI. |
|ResolvePartition |ciąg, wartość domyślna to "Administrator \| \| użytkownika" | Dynamiczny|Konfiguracja zabezpieczeń służąca do rozpoznawania usług systemowych. |
|ResolveService |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla rozpoznawania usługi opartej na skargach. |
|ResolveSystemService|ciąg, wartość domyślna to "Administrator \| \| użytkownika"|Dynamiczny| Konfiguracja zabezpieczeń służąca do rozpoznawania usług systemowych |
|RollbackApplicationUpgrade |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń do wycofywania uaktualnień aplikacji. |
|RollbackFabricUpgrade |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń do wycofywania uaktualnień klastra. |
|Powiadomienia dotyczące servicenotifications |ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń dla powiadomień dotyczących usług opartych na zdarzeniach. |
|SetUpgradeOrchestrationServiceState|ciąg, wartość domyślna to "Administrator"| Dynamiczny|Wywołuje SetUpgradeOrchestrationServiceState na partycji |
|StartApprovedUpgrades |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Wywołuje StartApprovedUpgrades na partycji. |
|StartChaos |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Uruchamia chaos — Jeśli nie jest jeszcze uruchomiona. |
|StartClusterConfigurationUpgrade |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Wywołuje StartClusterConfigurationUpgrade na partycji. |
|StartInfrastructureTask |ciąg, wartość domyślna to "Administrator" | Dynamiczny|Konfiguracja zabezpieczeń służąca do uruchamiania zadań związanych z infrastrukturą. |
|StartNodeTransition |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń do uruchamiania przejścia węzła. |
|StartPartitionDataLoss |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Wywołuje utratę danych w partycji. |
|StartPartitionQuorumLoss |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Wywołuje utratę kworum w partycji. |
|StartPartitionRestart |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Jednocześnie uruchamia ponownie niektóre lub wszystkie repliki partycji. |
|StopChaos |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Program przestaje chaos — jeśli został uruchomiony. |
|ToggleVerboseServicePlacementHealthReporting | ciąg, wartość domyślna to "Administrator \| \| użytkownika" |Dynamiczny| Konfiguracja zabezpieczeń służąca do przełączania pełnego HealthReporting usługi serviceplace. |
|UnprovisionApplicationType |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń dla typu aplikacji anulowania aprowizacji. |
|UnprovisionFabric |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń na potrzeby anulowania aprowizacji manifestu MSI i/lub klastra. |
|UnreliableTransportControl |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Zawodny transport służący do dodawania i usuwania zachowań. |
|UpdateService |ciąg, wartość domyślna to "Administrator" |Dynamiczny|Konfiguracja zabezpieczeń dla aktualizacji usługi. |
|UpgradeApplication |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń na potrzeby uruchamiania lub przerywania uaktualnień aplikacji. |
|UpgradeComposeDeployment|ciąg, wartość domyślna to "Administrator"| Dynamiczny|Uaktualnia wdrożenie redagowania |
|UpgradeFabric |ciąg, wartość domyślna to "Administrator" |Dynamiczny| Konfiguracja zabezpieczeń do uruchamiania uaktualnień klastra. |
|Przekazywanie |ciąg, wartość domyślna to "Administrator" | Dynamiczny|Konfiguracja zabezpieczeń dla operacji przekazywania klienta magazynu obrazów. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, wartość domyślna to None |Dynamiczny|Magazyny certyfikatów wystawcy x509 dla certyfikatów klienta; Nazwa = clientIssuerCN; Wartość = rozdzielona przecinkami lista sklepów |

## <a name="securityclientx509names"></a>Security/ClientX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, wartość domyślna to None|Dynamiczny|Jest to lista par "name" i "value". Każdy "name" ma wspólną nazwę podmiotu lub DnsName certyfikaty x509 autoryzowane dla operacji klienta. Dla danej "name", "value" jest oddzielną listą odcisków palców certyfikatów dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów klienta musi znajdować się na liście.|

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, wartość domyślna to None |Dynamiczny|Magazyny certyfikatów wystawcy x509 dla certyfikatów klastra; Nazwa = clusterIssuerCN; Wartość = rozdzielona przecinkami lista sklepów |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, wartość domyślna to None|Dynamiczny|Jest to lista par "name" i "value". Każdy "name" ma wspólną nazwę podmiotu lub DnsName certyfikaty x509 autoryzowane na potrzeby operacji klastra. Dla danej "name", "value" jest oddzielną listą odcisków palców certyfikatów dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów klastra musi znajdować się na liście.|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, wartość domyślna to None |Dynamiczny|Magazyny certyfikatów wystawcy x509 dla certyfikatów serwera; Nazwa = serverIssuerCN; Wartość = rozdzielona przecinkami lista sklepów |

## <a name="securityserverx509names"></a>Security/ServerX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, wartość domyślna to None|Dynamiczny|Jest to lista par "name" i "value". Każdy "name" ma wspólną nazwę podmiotu lub DnsName certyfikaty x509 autoryzowane dla operacji na serwerze. Dla danej "name", "value" jest oddzielną listą odcisków palców certyfikatów dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów serwera musi znajdować się na liście.|

## <a name="setup"></a>Konfigurowanie

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ContainerNetworkName|ciąg, wartość domyślna to ""| Static |Nazwa sieci do użycia podczas konfigurowania sieci kontenera.|
|ContainerNetworkSetup|bool, wartość domyślna to FALSE (Linux), a wartość domyślna to TRUE (Windows)| Static |Określa, czy należy skonfigurować sieć kontenera.|
|FabricDataRoot |String | Niedozwolone |Katalog główny danych Service Fabric. Wartość domyślna dla platformy Azure to d:\svcfab |
|FabricLogRoot |String | Niedozwolone |Katalog główny dziennika usługi Service Fabric. Jest to miejsce, w którym są umieszczane dzienniki i ślady SF. |
|NodesToBeRemoved|ciąg, wartość domyślna to ""| Dynamiczny |Węzły, które powinny zostać usunięte w ramach uaktualnienia konfiguracji. (Tylko w przypadku wdrożeń autonomicznych)|
|ServiceRunAsAccountName |String | Niedozwolone |Nazwa konta, pod którym zostanie uruchomiona usługa hosta sieci szkieletowej. |
|SkipContainerNetworkResetOnReboot|bool, wartość domyślna to FALSE|NotAllowed|Czy pominąć Resetowanie sieci kontenera przy ponownym uruchomieniu.|
|SkipFirewallConfiguration |Bool, wartość domyślna to false | Niedozwolone |Określa, czy ustawienia zapory muszą być ustawiane przez system, czy nie. Ma to zastosowanie tylko wtedy, gdy używasz zapory systemu Windows. Jeśli są używane zapory innych firm, należy otworzyć porty dla systemu i aplikacji do użycia |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|Dostawcy |ciąg, wartość domyślna to "DSTS" |Static|Rozdzielana przecinkami lista dostawców weryfikacji tokenu do włączenia (Prawidłowi dostawcy: DSTS; AAD). Obecnie tylko jeden dostawca można włączyć w dowolnym momencie. |

## <a name="traceetw"></a>Śledzenie/ETW

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|Poziom |Int, wartość domyślna to 4 | Dynamiczny |Poziom ETW śledzenia może przyjmować wartości 1, 2, 3, 4. W celu zapewnienia obsługi należy zachować poziom śledzenia na 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Czas w sekundach, wartość domyślna to 0,015 | Static | Określ wartość TimeSpan w sekundach. Określa czas oczekiwania przez replikatora po odebraniu operacji przed wysłaniem potwierdzenia do końca. Inne operacje odebrane w tym okresie będą musiały zostać przesłane z powrotem w jednej wiadomości — > zmniejszenie ruchu sieciowego, ale może to zmniejszyć przepływność replikatora. |
|MaxCopyQueueSize |Uint, wartość domyślna to 16384 | Static |Jest to maksymalna wartość definiująca początkowy rozmiar kolejki, która zachowuje operacje replikacji. Należy pamiętać, że musi być potęgą liczby 2. Jeśli w czasie wykonywania Kolejka powiększa się do tej operacji, ta operacja zostanie ograniczona między replikacją podstawową i pomocniczą. |
|MaxPrimaryReplicationQueueMemorySize |Uint, wartość domyślna to 0 | Static |Jest to maksymalna wartość głównej kolejki replikacji w bajtach. |
|MaxPrimaryReplicationQueueSize |Uint, wartość domyślna to 8192 | Static |Jest to maksymalna liczba operacji, które mogą istnieć w podstawowej kolejce replikacji. Należy pamiętać, że musi być potęgą liczby 2. |
|MaxReplicationMessageSize |Uint, wartość domyślna to 52428800 | Static | Maksymalny rozmiar komunikatu operacji replikacji. Wartość domyślna to 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, wartość domyślna to 0 | Static |Jest to maksymalna wartość kolejki replikacji pomocniczej w bajtach. |
|MaxSecondaryReplicationQueueSize |Uint, wartość domyślna to 16384 | Static |Jest to maksymalna liczba operacji, które mogą istnieć w pomocniczej kolejce replikacji. Należy pamiętać, że musi być potęgą liczby 2. |
|ReplicatorAddress |ciąg, wartość domyślna to "localhost: 0" | Static | Punkt końcowy w formie ciągu "IP: Port", który jest używany przez Replikator Windows Fabric do nawiązywania połączeń z innymi replikami w celu wysyłania/odbierania operacji. |

## <a name="transport"></a>Transport
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania** |**Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (60)|Static|Określ wartość TimeSpan w sekundach. Limit czasu dla konfiguracji połączenia na stronie przychodzącej i akceptującej (w tym negocjowanie zabezpieczeń w trybie zabezpieczonym) |
|FrameHeaderErrorCheckingEnabled|bool, wartość domyślna to TRUE|Static|Domyślne ustawienie sprawdzania błędów w nagłówku ramki w trybie niezabezpieczonym; ustawienie składnika zastępuje ten element. |
|MessageErrorCheckingEnabled|bool, wartość domyślna to FALSE|Static|Ustawienie domyślne do sprawdzania błędów w nagłówku komunikatu i treści w trybie niezabezpieczonym; ustawienie składnika zastępuje ten element. |
|ResolveOption|ciąg, wartość domyślna to "nieokreślone"|Static|Określa sposób rozwiązywania nazwy FQDN.  Prawidłowe wartości to "nieokreślone/IPv4/IPv6". |
|Właściwości SendTimeout|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (300)|Dynamiczny|Określ wartość TimeSpan w sekundach. Wysłano limit czasu podczas wykrywania zablokowanego połączenia. Raporty o błędach TCP nie są niezawodne w niektórych środowiskach. Może być konieczne dostosowanie w zależności od dostępnej przepustowości sieci i rozmiaru danych wychodzących ( \* MaxMessageSize \/ \* SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, wartość domyślna to true |Static| Automatyczna akcja sondowania i uaktualniania oparta na pliku stanu celu. |
|AutoupgradeInstallEnabled|Bool, wartość domyślna to FALSE|Static|Automatyczne sondowanie, Inicjowanie obsługi administracyjnej i Instalowanie akcji uaktualniania kodu na podstawie pliku stanu celu.|
|GoalStateExpirationReminderInDays|int, wartość domyślna to 30|Static|Ustawia liczbę pozostałych dni, po których ma być wyświetlane przypomnienie o stanie celu.|
|MinReplicaSetSize |Int, wartość domyślna to 0 |Static |MinReplicaSetSize dla UpgradeOrchestrationService.|
|PlacementConstraints | ciąg, wartość domyślna to "" |Static| PlacementConstraints dla UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Czas w sekundach, wartość domyślna to MaxValue |Static| Określ wartość TimeSpan w sekundach. QuorumLossWaitDuration dla UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Czas w sekundach, wartość domyślna to 60 minut|Static| Określ wartość TimeSpan w sekundach. ReplicaRestartWaitDuration dla UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Czas w sekundach, wartość domyślna to 60 *24* 7 minut |Static| Określ wartość TimeSpan w sekundach. StandByReplicaKeepDuration dla UpgradeOrchestrationService. |
|Wartość targetreplicasetsize |Int, wartość domyślna to 0 |Static |Wartość targetreplicasetsize dla UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, wartość domyślna to false | Static|Ustawienie do wykonania uaktualnienia kodu wymaga zatwierdzenia przez administratora przed kontynuowaniem. |

## <a name="upgradeservice"></a>UpgradeService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|BaseUrl | ciąg, wartość domyślna to "" |Static|BaseUrl dla UpgradeService. |
|ClusterId | ciąg, wartość domyślna to "" |Static|ClusterId dla UpgradeService. |
|Koordynatortype | ciąg, wartość domyślna to "WUTest"|Niedozwolone|Koordynatortype dla UpgradeService. |
|MinReplicaSetSize | Int, wartość domyślna to 2 |Niedozwolone| MinReplicaSetSize dla UpgradeService. |
|OnlyBaseUpgrade | Bool, wartość domyślna to false |Dynamiczny|OnlyBaseUpgrade dla UpgradeService. |
|PlacementConstraints |ciąg, wartość domyślna to "" |Niedozwolone|Usługa PlacementConstraints for Upgrade. |
|PollIntervalInSeconds|TimeSpan, wartość domyślna to common:: TimeSpan:: FromSeconds (60) |Dynamiczny|Określ wartość TimeSpan w sekundach. Interwał między UpgradeService sondowaniem operacji zarządzania ARM. |
|Wartość targetreplicasetsize | Int, wartość domyślna to 3 |Niedozwolone| Wartość targetreplicasetsize dla UpgradeService. |
|TestCabFolder | ciąg, wartość domyślna to "" |Static| TestCabFolder dla UpgradeService. |
|X509FindType | ciąg, wartość domyślna to ""|Dynamiczny| X509FindType dla UpgradeService. |
|X509FindValue | ciąg, wartość domyślna to "" |Dynamiczny| X509FindValue dla UpgradeService. |
|X509SecondaryFindValue | ciąg, wartość domyślna to "" |Dynamiczny| X509SecondaryFindValue dla UpgradeService. |
|X509StoreLocation | ciąg, wartość domyślna to "" |Dynamiczny| X509StoreLocation dla UpgradeService. |
|X509StoreName | ciąg, wartość domyślna to "my"|Dynamiczny|X509StoreName dla UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub Krótki opis** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, wartość domyślna to None | Static | Zbiór limitów nadzoru zasobów usług użytkownika musi być statyczny, ponieważ wpływa na logikę autowykrywania |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [uaktualnianie konfiguracji klastra platformy Azure](service-fabric-cluster-config-upgrade-azure.md) i [uaktualnianie konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).

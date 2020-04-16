---
title: Zmienianie ustawień klastra sieci szkieletowej usług Azure
description: W tym artykule opisano ustawienia sieci szkieletowej i zasady uaktualniania sieci szkieletowej, które można dostosować.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: c2e280af814a3e10ad84c5ba07fc376868fcd851
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416241"
---
# <a name="customize-service-fabric-cluster-settings"></a>Dostosowywanie ustawień klastra usługi Service Fabric
W tym artykule opisano różne ustawienia sieci szkieletowej klastra sieci szkieletowej usług, które można dostosować. W przypadku klastrów hostowanych na platformie Azure można dostosować ustawienia za pośrednictwem [witryny Azure portal](https://portal.azure.com) lub przy użyciu szablonu usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [Uaktualnianie konfiguracji klastra platformy Azure](service-fabric-cluster-config-upgrade-azure.md). W przypadku klastrów autonomicznych można dostosować ustawienia, aktualizując plik *ClusterConfig.json* i wykonując uaktualnienie konfiguracji w klastrze. Aby uzyskać więcej informacji, zobacz [Uaktualnianie konfiguracji autonomicznego klastra](service-fabric-cluster-config-upgrade-windows-server.md).

Istnieją trzy różne zasady uaktualniania:

- **Dynamiczne** — zmiany w konfiguracji dynamicznej nie powodują ponownego uruchamiania procesów sieci szkieletowej usług ani procesów hosta usług. 
- **Statyczne** — zmiany w konfiguracji statycznej spowoduje, że węzeł sieci szkieletowej usług, aby korzystać ze zmiany. Usługi w węzłach zostaną ponownie uruchomione.
- **Niedozwolone** — tych ustawień nie można modyfikować. Zmiana tych ustawień wymaga zniszczenia klastra i utworzenia nowego klastra. 

Poniżej znajduje się lista ustawień sieci szkieletowej, które można dostosować, uporządkowane według sekcji.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|ciąg, domyślnie jest "Brak"|Statyczny| Nie sprawdza poprawności certyfikatu serwera; zakończyć się pomyślnie. Zobacz config ServiceCertificateThumbprints dla oddzielonej przecinkami listy odcisków palców zdalnych certyfikatów, którym może ufać odwrotny serwer proxy. Zobacz config ServiceCommonNameAndIssuer dla nazwy podmiotu i odcisk palca wystawcy zdalnych certyfikatów, którym może ufać odwrotny serwer proxy. Aby dowiedzieć się więcej, zobacz [Odwróć bezpieczne połączenie serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|Rozmiar bodyChunkSize |Uint, wartość domyślna to 16384 |Dynamiczny| Podaje rozmiar fragmentu w bajtach używanych do odczytu treści. |
|Żużel w crlchecking|uint, domyślnie jest 0x40000000 |Dynamiczny| Flagi do sprawdzania poprawności łańcucha certyfikatów aplikacji/usługi; na przykład sprawdzanie crl 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Ustawienie 0 wyłącza sprawdzanie CRL Pełna lista obsługiwanych wartości jest udokumentowana przez dwFlags certGetCertificateChain:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|Domyślny limit czasuhttprequest |Czas w sekundach. domyślnie wynosi 120 |Dynamiczny|Określ czas w sekundach.  Daje domyślny limit czasu żądania dla żądań http przetwarzanych w bramie aplikacji http. |
|Certyfikat ForwardClientCertificate|bool, domyślnie jest FALSE|Dynamiczny|Po ustawieniu na false, reverse proxy nie zażąda certyfikatu klienta. Po ustawieniu na wartość true, reverse proxy zażąda certyfikatu klienta podczas uzgadniania TLS i przekazuje ciąg formatu PEM zakodowanego base64 do usługi w nagłówku o nazwie X-Client-Certificate.Usługa może zakończyć się niepowodzeniem żądania z odpowiednim kodem stanu po sprawdzeniu danych certyfikatu. Jeśli jest to prawda, a klient nie przedstawia certyfikatu, odwrócony serwer proxy prześli pusty nagłówek i pozwoli serwisowi obsłużyć sprawę. Odwrócony serwer proxy będzie działał jako warstwa przezroczysta. Aby dowiedzieć się więcej, zobacz [Konfigurowanie uwierzytelniania certyfikatów klienta](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |ciąg, domyślnie jest "Brak" |Statyczny| Wskazuje typ poświadczeń zabezpieczeń do użycia w punkcie końcowym bramy aplikacji http Prawidłowe wartości to Brak/X509. |
|GatewayX509CertificateFindType |ciąg, domyślnie jest "FindByThumbprint" |Dynamiczny| Wskazuje sposób wyszukiwania certyfikatu w magazynie określonym przez GatewayX509CertificateStoreName obsługiwana wartość: FindByThumbprint; ZnajdźNazjectName. |
|GatewayX509CertificateFindValue | ciąg, domyślnie jest "" |Dynamiczny| Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy aplikacji http. Ten certyfikat jest skonfigurowany w punkcie końcowym https i może również służyć do weryfikacji tożsamości aplikacji, jeśli jest to potrzebne przez usługi. Najpierw wyszukuje się wartość FindValue; a jeśli tak nie jest; FindValueSecondary jest spojrzał w górę. |
|GatewayX509CertificateFindValueSecondary | ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu bramy aplikacji http. Ten certyfikat jest skonfigurowany w punkcie końcowym https i może również służyć do weryfikacji tożsamości aplikacji, jeśli jest to potrzebne przez usługi. Najpierw wyszukuje się wartość FindValue; a jeśli tak nie jest; FindValueSecondary jest spojrzał w górę.|
|GatewayX509CertificateStoreName |ciąg, domyślnie jest "Mój" |Dynamiczny| Nazwa magazynu certyfikatów X.509 zawierającego certyfikat bramy aplikacji http. |
|Funkcja HttpRequestConnectTimeout|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(5)|Dynamiczny|Określ czas w sekundach.  Daje limit czasu połączenia dla żądań http wysyłanych z bramy aplikacji http.  |
|IgnoreCrlOfflineError|bool, domyślnie jest PRAWDA|Dynamiczny|Czy zignorować błąd programu CRL w trybie offline dla weryfikacji certyfikatu aplikacji/usługi. |
|IsEnabled |Bool, wartość domyślna to false |Statyczny| Włącza/wyłącza HttpApplicationGateway. HttpApplicationGateway jest domyślnie wyłączona i ten config musi być ustawiony, aby go włączyć. |
|NumberOfParallelOperations | Uint, domyślnie jest 5000 |Statyczny|Liczba odczytów do opublikowania w kolejce serwera http. Służy to do sterowania liczbą równoczesnych żądań, które mogą być spełnione przez HttpGateway. |
|Usuń Pozycję Powiernicy|ciąg, domyślnie jest "Data; Serwer"|Statyczny|Semi colon/ przecinek oddzielone listy nagłówków odpowiedzi, które zostaną usunięte z odpowiedzi usługi; przed przekazaniem go do klienta. Jeśli jest to ustawione na pusty ciąg; przekazać wszystkie nagłówki zwrócone przez usługę jako — jest. Tj nie zastępuj daty i serwera |
|Rozwiązanie ResolveServiceBackoffInterval |Czas w sekundach, wartość domyślna to 5 |Dynamiczny|Określ czas w sekundach.  Podaje domyślny interwał wycofywania przed ponowieniem próby nieudanej operacji uswalenia usługi. |
|Tryb SecureOnlyMode|bool, domyślnie jest FALSE|Dynamiczny| SecureOnlyMode: true: Reverse Proxy będzie przesyłać dalej tylko do usług, które publikują bezpieczne punkty końcowe. false: Reverse Proxy może przesyłać dalej żądania do bezpiecznych/niezabezpieczonych punktów końcowych. Aby dowiedzieć się więcej, zobacz [Logika wyboru punktu końcowego serwera proxy odwrotnie](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|ciąg, domyślnie jest ""|Dynamiczny|Oddzielona przecinkami lista odcisków palców zdalnych certyfikatów, którym może ufać odwrotny serwer proxy. Aby dowiedzieć się więcej, zobacz [Odwróć bezpieczne połączenie serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, domyślnie jest Brak|Dynamiczny| Nazwa podmiotu i odcisk palca wystawcy zdalnych certyfikatów, którym może ufać odwrotny serwer proxy. Aby dowiedzieć się więcej, zobacz [Odwróć bezpieczne połączenie serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService (Usługa backuprestore)

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Rozmiar MinReplicaSetSize|int, wartość domyślna to 0|Statyczny|Rozmiar MinReplicaSetSize dla backuprestoreservice |
|Miejsca docelowe|ciąg, domyślnie jest ""|Statyczny|    Usługa PlacementConstraints for BackupRestore |
|Drukarka SecretEncryptionCertThumbprint|ciąg, domyślnie jest ""|Dynamiczny|Odcisk palca certyfikatu szyfrowania tajnego X509 |
|SecretEncryptionCertX509StoreName|ciąg, domyślnie jest "Mój"|    Dynamiczny|    Wskazuje to certyfikat używany do szyfrowania i odszyfrowywania creds Nazwa magazynu certyfikatów X.509, który jest używany do szyfrowania poświadczeń magazynu odszyfrowywania używanych przez usługę przywracania kopii zapasowej |
|Rozmiar zestawu TargetReplicaSetSize|int, wartość domyślna to 0|Statyczny| Rozmiar zestawu TargetReplicaSetSize dla backuprestoreservice |

## <a name="clustermanager"></a>Menedżer klastra

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, wartość domyślna to false |Dynamiczny|Określa, czy niestandardowe zasady sortowania uaktualnień są dozwolone. Służy do wykonywania aktualizacji 2-fazowej, włączając tę funkcję. Usługa Sieci szkieletowej 6.5 dodaje obsługę określania zasad sortowania dla domen uaktualniania podczas uaktualniania klastra lub aplikacji. Obsługiwane zasady to numeryczne, leksykograficzne, odwrócone liczenione i odwróconelexikograficzne. Wartość domyślna to Numeryczne. Aby móc korzystać z tej funkcji, ustawienie manifestu klastra ClusterManager/ AllowCustomUpgradeSortPolicies musi być ustawione na True jako drugi krok uaktualnienia konfiguracji po zakończeniu uaktualnienia kodu SF 6.5. Ważne jest, aby odbywało się to w dwóch fazach, w przeciwnym razie uaktualnienie kodu może zostać pomylone z zamówieniem uaktualnienia podczas pierwszego uaktualnienia.|
|EnableDefaultServicesUpgrade | Bool, wartość domyślna to false |Dynamiczny|Włącz uaktualnianie usług domyślnych podczas uaktualniania aplikacji. Domyślne opisy usług zostaną zastąpione po uaktualnieniu. |
|TkaninaUpgradeHealthInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość sprawdzania stanu zdrowia podczas monitorowania aktualizacji sieci szkieletowej |
|FabricUpgradeStatusPollInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość sondowania stanu uaktualnienia sieci szkieletowej. Ta wartość określa szybkość aktualizacji dla każdego wywołania GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Czas w sekundach, wartość domyślna to 3 |Dynamiczny|Określ czas w sekundach. Czas umożliwiający błędy limitu czasu określonego w konstruktorze obrazów do zwrócenia do klienta. Jeśli ten bufor jest za mały; następnie limit czasu klienta przed serwerem i pobiera ogólny błąd limitu czasu. |
|InfrastrukturaTaskHealthCheckRetryTimeout | Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ czas w sekundach. Ilość czasu do ponowienia prób nie powiodło się kontroli kondycji podczas przetwarzania końcowego zadania infrastruktury. Obserwując przeszedł kontroli kondycji spowoduje zresetowanie tego timera. |
|InfrastrukturaTaskHealthCheckStableDuration | Czas w sekundach, wartość domyślna to 0|Dynamiczny| Określ czas w sekundach. Czas, aby obserwować kolejne przeszedł kontroli kondycji przed zakończeniem przetwarzania końcowego zadania infrastruktury. Obserwowanie nieudanej kontroli kondycji spowoduje zresetowanie tego timera. |
|InfrastrukturaTaskHealthCheckWaitDuration |Czas w sekundach, wartość domyślna to 0|Dynamiczny| Określ czas w sekundach. Czas oczekiwania przed rozpoczęciem kontroli kondycji po zakończeniu przetwarzania zadania infrastruktury. |
|InfrastrukturaTaskProcessingInterval | Czas w sekundach, wartość domyślna to 10 |Dynamiczny|Określ czas w sekundach. Interwał przetwarzania używany przez maszynę stanu przetwarzania zadań infrastruktury. |
|Maksymalny limit czasu komunikacji |Czas w sekundach, domyślnie to 600 |Dynamiczny|Określ czas w sekundach. Maksymalny limit czasu komunikacji wewnętrznej między ClusterManager a innymi usługami systemowymi (tj. Usługa nazewnictwa; Menedżer pracy awaryjnej itp.). Ten limit czasu powinien być mniejszy niż globalny MaxOperationTimeout (ponieważ może istnieć wiele komunikacji między składnikami systemu dla każdej operacji klienta). |
|Maksymalny limit czasu |Czas w sekundach, domyślnie to 600 |Dynamiczny|Określ czas w sekundach. Maksymalny limit czasu dla operacji odzyskiwania migracji danych po uaktualnieniu sieci szkieletowej miało miejsce. |
|MaxOperationRetryDelay |Czas w sekundach, wartość domyślna to 5|Dynamiczny| Określ czas w sekundach. Maksymalne opóźnienie wewnętrznych ponownych prób po napotkaniu awarii. |
|Maksymalny limit czasu operacji |Czas w sekundach, domyślnie jest MaxValue |Dynamiczny| Określ czas w sekundach. Maksymalny globalny limit czasu dla operacji przetwarzania wewnętrznego na ClusterManager. |
|MaxTimeoutRetryBuffer (MaxTimeoutRetryBuffer) | Czas w sekundach, domyślnie to 600 |Dynamiczny|Określ czas w sekundach. Maksymalny limit czasu operacji podczas ponowiania prób `<Original Time out> + <MaxTimeoutRetryBuffer>`wewnętrznych z powodu przekroczenie limitu czasu wynosi . Dodatkowy limit czasu jest dodawany w przyrostach MinOperationTimeout. |
|MinCzas operacji | Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ czas w sekundach. Minimalny globalny limit czasu dla operacji przetwarzania wewnętrznie na ClusterManager. |
|Rozmiar MinReplicaSetSize |Int, wartość domyślna to 3 |Niedozwolone|Rozmiar MinReplicaSetSize dla clustermanagera. |
|Miejsca docelowe | ciąg, domyślnie jest "" |Niedozwolone|MieszczanieStawy dla programu ClusterManager. |
|KworumLossWaitDuration |Czas w sekundach, domyślnie jest MaxValue |Niedozwolone| Określ czas w sekundach. KworumLossWaitDuration dla ClusterManager. |
|ReplicaRestartWaitDuration |Czas w sekundach, wartość domyślna \* to (60,0 30)|Niedozwolone|Określ czas w sekundach. ReplicaRestartWaitDuration dla clustermanager. |
|ReplicaSetCheckTimeoutRollbackOverride |Czas w sekundach, wartość domyślna to 1200 |Dynamiczny| Określ czas w sekundach. Jeśli ReplicaSetCheckTimeout jest ustawiona na maksymalną wartość DWORD; następnie jest zastępowane wartością tego configu do celów wycofywania. Wartość używana do przekazywania do przodu nigdy nie jest zastępowane. |
|SkipRollbackUpdateDefaultService | Bool, wartość domyślna to false |Dynamiczny|Cm pominie przywracanie zaktualizowanych usług domyślnych podczas wycofywania uaktualnienia aplikacji. |
|StandByReplicaOdwekczenie | Czas w sekundach, wartość domyślna to \* (3600.0 2)|Niedozwolone|Określ czas w sekundach. StandByReplicaKeepDuration dla clustermanager. |
|Rozmiar zestawu TargetReplicaSetSize |Int, wartość domyślna to 7 |Niedozwolone|Rozmiar zestawu TargetReplicaSetSize dla zestawu ClusterManager. |
|UpgradeHealthCheckInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość kontroli stanu kondycji podczas monitorowania uaktualnień aplikacji |
|UpgradeStatusPollInterval |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Częstotliwość sondowania stanu uaktualnienia aplikacji. Ta wartość określa szybkość aktualizacji dla każdego wywołania GetApplicationUpgradeProgress |
|CompleteClientRequest (Kompletna prośba oclgospoda) | Bool, wartość domyślna to false |Dynamiczny| Kompletne żądanie klienta po zaakceptowaniu przez CM. |

## <a name="common"></a>Wspólne

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Czas w sekundach, wartość domyślna to 1 |Dynamiczny|Określ czas w sekundach. Interwał monitorowania wydajności. Ustawienie wartości 0 lub ujemnej powoduje wyłączenie monitorowania. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentacjaNaptyNodeDistributionPolicy
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|KeyIntegerValueMap, domyślnie jest Brak|Dynamiczny|Określa defragmentację zasad podczas opróżniania węzłów. Dla danej metryki 0 wskazuje, że SF należy spróbować defragmentacji węzłów równomiernie w uds i fds; 1 oznacza tylko, że węzły muszą zostać zdefragmentowane |

## <a name="defragmentationmetrics"></a>DefragmentacjaMetria
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|KeyBoolValueMap, domyślnie jest Brak|Dynamiczny|Określa zestaw metryk, które powinny być używane do defragmentacji, a nie do równoważenia obciążenia. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentacjaMetricsPercentLubNumberOfEmptyNodesTriggeringThreshold
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, domyślnie jest Brak|Dynamiczny|Określa liczbę wolnych węzłów, które są potrzebne do rozważenia defragmentacji klastra, określając procent w zakresie [0,0 - 1,0] lub liczbę pustych węzłów jako liczbę >= 1,0 |

## <a name="diagnostics"></a>Diagnostyka

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, wartość domyślna jest true | Dynamiczny | Wyklucz żądania HTTP, które nie mają wpływu na stan klastra z inspekcji. Obecnie; wykluczane są tylko żądania typu "GET"; ale może to ulec zmianie. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, wartość domyślna jest true | Dynamiczny |Czy personifikacja jest wymagana podczas uzyskiwania dostępu do magazynów diagnostycznych w imieniu aplikacji. |
|AppEtwTraceDeletionAgeInDays |Int, wartość domyślna to 3 | Dynamiczny |Liczba dni, po których usuwamy stare pliki ETL zawierające ślady ETW aplikacji. |
|ApplicationLogsFormatVersion |Int, wartość domyślna to 0 | Dynamiczny |Wersja dla formatu dzienników aplikacji. Obsługiwane wartości to 0 i 1. Wersja 1 zawiera więcej pól z rekordu zdarzenia ETW niż w wersji 0. |
|InspekcjaHttpRequests |Bool, wartość domyślna to false | Dynamiczny | Włączanie lub wyłączanie inspekcji HTTP. Celem inspekcji jest wyświetlenie działań, które zostały wykonane względem klastra; w tym kto zainicjował wniosek. Należy zauważyć, że jest to najlepsza próba rejestrowania; może wystąpić utrata śladu. Żądania HTTP z uwierzytelnianiem "Użytkownik" nie są rejestrowane. |
|PrzechwytywaniehttpTelemetria|Bool, wartość domyślna jest true | Dynamiczny | Włączanie lub wyłączanie danych telemetrycznych HTTP. Celem telemetrii jest dla sieci szkieletowej usług, aby móc przechwytywać dane telemetryczne, aby pomóc zaplanować przyszłe prace i zidentyfikować obszary problemów. Telemetria nie rejestruje żadnych danych osobowych ani treści żądania. Telemetria przechwytuje wszystkie żądania HTTP, chyba że skonfigurowano inaczej. |
|Ład klastra |Ciąg | Dynamiczny |Unikatowy identyfikator klastra. Jest to generowane podczas tworzenia klastra. |
|ConsumerInstances (Niesłańce konsumpcyjne) |Ciąg | Dynamiczny |Lista wystąpień konsumenta DCA. |
|DyskFullSafetySpaceInMB |Int, wartość domyślna to 1024 | Dynamiczny |Pozostała ilość miejsca na dysku w MB w celu ochrony przed użyciem przez DCA. |
|EnableCircularTraceSession (EnableCircularTraceSession) |Bool, wartość domyślna to false | Statyczny |Flaga wskazuje, czy powinny być używane sesje śledzenia kołowe. |
|EnablePlatformEventsFileSink |Bool, wartość domyślna to false | Statyczny |Włączanie/wyłączanie zdarzeń platformy zapisywanych na dysku |
|EnableTelemetry |Bool, wartość domyślna jest true | Dynamiczny |Spowoduje to włączenie lub wyłączenie telemetrii. |
|BłędyTylkohttpTelemetria | Bool, wartość domyślna to false | Dynamiczny | Jeśli przechwytywanie telemetrii HTTP jest włączone; przechwytywania tylko nieudanych żądań. Ma to pomóc zmniejszyć liczbę zdarzeń generowanych dla telemetrii. |
|HttpTelemetryCapturePercentage | int, domyślnie jest to 50 | Dynamiczny | Jeśli przechwytywanie telemetrii HTTP jest włączone; przechwytywania tylko losowego odsetka żądań. Ma to pomóc zmniejszyć liczbę zdarzeń generowanych dla telemetrii. |
|MaxDiskQuotaInMB |Int, wartość domyślna to 65536 | Dynamiczny |Przydział dysku w MB dla plików dziennika sieci szkieletowej systemu Windows. |
|Informacje o producentach |Ciąg | Dynamiczny |Lista wystąpień producenta DCA. |

## <a name="dnsservice"></a>Usługa Dns
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|WłączParekwerowaniepartitioned|bool, domyślnie jest FALSE|Statyczny|Flaga umożliwiająca obsługę zapytań DNS dla usług podzielonych na partycje. Funkcja jest domyślnie wyłączona. Aby uzyskać więcej informacji, zobacz [Usługa DNS sieci szkieletowej usług.](service-fabric-dnsservice.md)|
|Liczba wystąpień|int, wartość domyślna to -1|Statyczny|Wartość domyślna to -1, co oznacza, że usługa DnsService jest uruchomiona w każdym węźle. OneBox musi to ustawić na 1, ponieważ DnsService używa dobrze znanego portu 53, więc nie może mieć wielu wystąpień na tym samym komputerze.|
|IsEnabled|bool, domyślnie jest FALSE|Statyczny|Włącza/wyłącza usługę DnsService. DnsService jest domyślnie wyłączona i ten config musi być ustawiony, aby go włączyć. |
|Poprawka partycji|ciąg, domyślnie jest "--"|Statyczny|Steruje wartością ciągu prefiksu partycji w kwerendach DNS dla usług podzielonych na partycje. Wartość: <ul><li>Powinien być zgodny z RFC, ponieważ będzie częścią kwerendy DNS.</li><li>Nie powinien zawierać kropki ,,', ponieważ kropka zakłóca zachowanie sufiksu DNS.</li><li>Nie powinien być dłuższy niż 5 znaków.</li><li>Nie może być pustym ciągiem.</li><li>Jeśli partitionPrefix ustawienie jest zastępowane, a następnie PartitionSuffix musi zostać zastąpiony i na odwrót.</li></ul>Aby uzyskać więcej informacji, zobacz [Usługa DNS sieci szkieletowej usług.](service-fabric-dnsservice.md).|
|PartitionSuffix (sufiks partycji)|ciąg, domyślnie jest ""|Statyczny|Steruje wartością ciągu sufiksu partycji w kwerendach DNS dla usług podzielonych na partycje. Wartość: <ul><li>Powinien być zgodny z RFC, ponieważ będzie częścią kwerendy DNS.</li><li>Nie powinien zawierać kropki ,,', ponieważ kropka zakłóca zachowanie sufiksu DNS.</li><li>Nie powinien być dłuższy niż 5 znaków.</li><li>Jeśli partitionPrefix ustawienie jest zastępowane, a następnie PartitionSuffix musi zostać zastąpiony i na odwrót.</li></ul>Aby uzyskać więcej informacji, zobacz [Usługa DNS sieci szkieletowej usług.](service-fabric-dnsservice.md). |

## <a name="eventstoreservice"></a>Usługa EventStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Rozmiar MinReplicaSetSize|int, wartość domyślna to 0|Statyczny|Usługa MinReplicaSetSize for EventStore |
|Miejsca docelowe|ciąg, domyślnie jest ""|Statyczny|    Usługa PlacementConstraints for EventStore |
|Rozmiar zestawu TargetReplicaSetSize|int, wartość domyślna to 0|Statyczny| Usługa TargetReplicaSetSize for EventStore |

## <a name="fabricclient"></a>TkaninaClient

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Czas w sekundach, wartość domyślna to 2 |Dynamiczny|Określ czas w sekundach. Interwał limitu czasu połączenia dla każdego klienta próbuje otworzyć połączenie z bramą.|
|HealthOperationTimeout |Czas w sekundach, domyślnie to 120 |Dynamiczny|Określ czas w sekundach. Limit czasu dla wiadomości raportu wysłanej do Menedżera kondycji. |
|HealthReportRetrySendInterval (Raport kondycji) |Czas w sekundach, domyślnie 30, minimum to 1 |Dynamiczny|Określ czas w sekundach. Interwał, w którym składnik raportowania ponownie wysunął skumulowane raporty dotyczące kondycji do menedżera kondycji. |
|HealthReportSendInterval (Raportowanie kondycji) |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ czas w sekundach. Interwał, w którym składnik raportowania wysyła skumulowane raporty kondycji do Health Manager. |
|KeepAliveIntervalInSekundy |Int, wartość domyślna to 20 |Statyczny|Interwał, w którym Transport FabricClient wysyła komunikaty keep-alive do bramy. Dla 0; keepAlive jest wyłączona. Musi być wartością dodatnią. |
|MaxFileSenderWątry |Uint, wartość domyślna to 10 |Statyczny|Maksymalna liczba plików, które są przesyłane równolegle. |
|NodeAddresses (Adresy węzłów) |ciąg, domyślnie jest "" |Statyczny|Kolekcja adresów (parametry połączenia) w różnych węzłach, które mogą służyć do komunikowania się z usługą nazewnictwa. Początkowo Klient łączy wybierając jeden z adresów losowo. Jeśli dostarczony jest więcej niż jeden parametry połączenia, a połączenie nie powiedzie się z powodu błędu komunikacji lub limitu czasu; klient przełącza się do korzystania z następnego adresu sekwencyjnie. Zobacz ponawianie adresu usługi nazewnictwa, aby uzyskać szczegółowe informacje na temat semantyki ponownych prób. |
|PartitionLocationCacheLimit |Int, wartość domyślna to 100000 |Statyczny|Liczba partycji buforowanych dla rozdzielczości usługi (ustawiona na 0 dla bez limitu). |
|RetryBackoffInterval |Czas w sekundach, wartość domyślna to 3 |Dynamiczny|Określ czas w sekundach. Interwał wycofywania przed ponowieniem próby wykonania operacji. |
|UsługaCzanglInterval |Czas w sekundach, domyślnie to 120 |Dynamiczny|Określ czas w sekundach. Interwał między kolejnymi ankietami dla zmian usługi z klienta do bramy dla zarejestrowanych powiadomień o zmianie usługi wywołania zwrotne. |

## <a name="fabrichost"></a>Hosta

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AktywacjaMaxFailureCount |Int, wartość domyślna to 10 |Dynamiczny|Jest to maksymalna liczba, dla której system ponowi próbę aktywacji nie powiodło się przed rezygnacją. |
|AktywacjaMaxRetryInterval |Czas w sekundach, domyślnie to 300 |Dynamiczny|Określ czas w sekundach. Maksymalny interwał ponawiania próby aktywacji. Przy każdej ciągłej awarii interwał ponawiania jest obliczany jako Min( ActivationMaxRetryInterval; Liczba ciągłych awarii * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Czas w sekundach, wartość domyślna to 5 |Dynamiczny|Określ czas w sekundach. Interwał wycofywania przy każdym niepowodzeniu aktywacji;Przy każdym ciągłym niepowodzeniu aktywacji system ponowi próbę aktywacji do maxActivationFailureCount. Interwał ponawiania przy każdej próbie jest wynikiem ciągłego niepowodzenia aktywacji i interwału wycofywania aktywacji. |
|EnableRestartManagement (Umożliwianie uruchamiania) |Bool, wartość domyślna to false |Dynamiczny|Ma to umożliwić ponowne uruchomienie serwera. |
|EnableServiceFabricAutomaticUpdates (Włącz UsługęFabricAutomaticUpdates) |Bool, wartość domyślna to false |Dynamiczny|Ma to umożliwić automatyczną aktualizację sieci szkieletowej za pośrednictwem witryny Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, wartość domyślna to false |Dynamiczny|Ma to umożliwić aktualizację podstawową dla serwera. |
|Błąd RaportowanieWydajanie RaportowanieIntervalValenabled | Bool, wartość domyślna jest true | Statyczny | Umożliwia szybsze przesyłanie szybkości w dca, gdy FabricHost jest w trybie raportowania awarii. |
|Błąd Raportowanie Limit | TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(60) | Statyczny |Określ czas w sekundach. Limit czasu dla raportowania awarii DCA w przypadku FabricHost napotyka niepowodzenie uruchamiania na wczesnym etapie. | 
|RunDCAOnStartupFailure | Bool, wartość domyślna jest true | Statyczny |Określa, czy uruchomić dca do przekazywania dzienników w obliczu problemów z uruchamianiem w FabricHost. | 
|Czas rozpoczęcia |Czas w sekundach, domyślnie to 300 |Dynamiczny|Określ czas w sekundach. Limit czasu dla uruchamiania fabricactivationmanager. |
|StopTimeout (Czas zatrzymania) |Czas w sekundach, domyślnie to 300 |Dynamiczny|Określ czas w sekundach. Limit czasu aktywacji usługi hosta; dezaktywacji i uaktualnienia. |

## <a name="fabricnode"></a>TkaninaNoda

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Typ klienta AuthX509FindType |ciąg, domyślnie jest "FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu w magazynie określonym przez ClientAuthX509StoreName obsługiwane wartości: FindByThumbprint; ZnajdźNazjectName. |
|ClientAuthX509Znajdujecvalue |ciąg, domyślnie jest "" | Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli administratora FabricClient. |
|KlientAuthX509ZnajdujeValueSecondary |ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli administratora FabricClient. |
|ClientAuthX509SamestoreName |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatów X.509 zawierającego certyfikat dla domyślnej roli administratora FabricClient. |
|Typ odnajdowania klastraX509 |ciąg, domyślnie jest "FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu klastra w magazynie określonym przez obsługiwane wartości ClusterX509StoreName: "FindByThumbprint"; "FindBySubjectName" z "FindBySubjectName"; gdy istnieje wiele dopasowań; ten, który ma najdalej wydech. |
|ClusterX509Znajdowanie odnaleźnia |ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|ClusterX509FindValueSecondary |ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu klastra. |
|Nazwa magazynu ClusterX509 |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatów X.509 zawierającego certyfikat klastra do zabezpieczania komunikacji wewnątrz klastra. |
|EndApplicationPortRange (EndApplicationPortRange) |Int, wartość domyślna to 0 |Statyczny|Koniec (bez włącznie) portów aplikacji zarządzanych przez podsystem hostingowy. Wymagane, jeśli EndpointFilteringEnabled jest true w hostingu. |
|Typ odnajdowania serweraAuthX509 |ciąg, domyślnie jest "FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu serwera w magazynie określonym przez ServerAuthX509StoreName obsługiwane wartości: FindByThumbprint; ZnajdźNazjectName. |
|SerwerAuthX509Znajduje się |ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|SerwerAuthX509FindValueSecondary |ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu serwera. |
|Nazwa sklepu ServerAuthX509 |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatów X.509 zawierającego certyfikat serwera dla usługi entrée. |
|StartApplicationPortRange |Int, wartość domyślna to 0 |Statyczny|Uruchamianie portów aplikacji zarządzanych przez podsystem hostingowy. Wymagane, jeśli EndpointFilteringEnabled jest true w hostingu. |
|StateTraceInterval |Czas w sekundach, domyślnie to 300 |Statyczny|Określ czas w sekundach. Interwał śledzenia stanu węzła w każdym węźle i w górę węzłów w FM/FMM. |
|UżytkownikRoleClientX509FindType |ciąg, domyślnie jest "FindByThumbprint" |Dynamiczny|Wskazuje sposób wyszukiwania certyfikatu w magazynie określonym przez UserRoleClientX509StoreName obsługiwane wartości: FindByThumbprint; ZnajdźNazjectName. |
|Nazwa użytkownikaClientX509Znawość doznania |ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli użytkownika FabricClient. |
|UżytkownikRoleClientX509FindValueSecondary |ciąg, domyślnie jest "" |Dynamiczny|Wartość filtru wyszukiwania używana do lokalizowania certyfikatu dla domyślnej roli użytkownika FabricClient. |
|Nazwa użytkownikaRoleClientX509StoreName |ciąg, domyślnie jest "Mój" |Dynamiczny|Nazwa magazynu certyfikatów X.509 zawierającego certyfikat dla domyślnej roli użytkownika FabricClient. |

## <a name="failovermanager"></a>Menedżer trybu failover

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, domyślnie jest FALSE |Dynamiczny|Flaga wskazująca, czy jest dozwolone usunięcie stanu węzła dla węzła źródłowego |
|BuildReplicaTimeLimit|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(3600)|Dynamiczny|Określ czas w sekundach. Termin tworzenia repliki stanowej; po którym zostanie zainicjowany raport ostrzegawczy o stanie zdrowia |
|ClusterPauseThreshold|int, wartość domyślna to 1|Dynamiczny|Jeśli liczba węzłów w systemie zejdzie poniżej tej wartości, a następnie umieszczenie; równoważenie obciążenia; i pracy awaryjnej jest zatrzymany. |
|Utwórz pozycjęCzaswzamitość|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(300)|Dynamiczny|Określ czas w sekundach. Termin tworzenia wystąpienia bezstanowego; po którym zostanie zainicjowany raport ostrzegawczy o stanie zdrowia |
|Rozmiar expectedclustersize|int, wartość domyślna to 1|Dynamiczny|Po uruchomieniu klastra; FM będzie czekać na to wiele węzłów, aby zgłosić się przed rozpoczęciem wprowadzania innych usług; w tym usługi systemowe, takie jak nazewnictwo. Zwiększenie tej wartości zwiększa czas potrzebny do uruchomienia klastra; ale zapobiega wczesne węzły staje się przeciążony, a także dodatkowe ruchy, które będą niezbędne, jak więcej węzłów są online. Ta wartość powinna być zazwyczaj ustawiona na niewielki ułamek początkowego rozmiaru klastra. |
|Oczekiwana DeaktywacjaDuracacja|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(60.0 \* 30)|Dynamiczny|Określ czas w sekundach. Jest to oczekiwany czas trwania węzła, aby zakończyć dezaktywację w. |
|Oczekiwana Wartość NiefabricUpgradeDuration|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(60.0 \* 30)|Dynamiczny|Określ czas w sekundach. Jest to oczekiwany czas trwania węzła, który ma zostać uaktualniony podczas uaktualniania sieci szkieletowej systemu Windows. |
|OczekiwanaReplicaUpgradeDuration|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(60.0 \* 30)|Dynamiczny|Określ czas w sekundach. Jest to oczekiwany czas trwania dla wszystkich replik, które mają zostać uaktualnione w węźle podczas uaktualniania aplikacji. |
|IsSingletonReplicaMoveAllowedPodszurowanie|bool, domyślnie jest PRAWDA|Dynamiczny|Jeśli ustawiona na true; repliki o docelowym rozmiarze zestawu replik 1 będą mogły być przesuniętye podczas uaktualniania. |
|Rozmiar MinReplicaSetSize|int, wartość domyślna to 3|Niedozwolone|Jest to minimalny rozmiar zestawu replik dla FM. Jeśli liczba aktywnych replik FM spadnie poniżej tej wartości; FM odrzuci zmiany w klastrze, dopóki nie zostanie odzyskana co najmniej minimalna liczba replik |
|Miejsca docelowe|ciąg, domyślnie jest ""|Niedozwolone|Wszelkie ograniczenia umieszczania replik menedżera trybu failover |
|Miejsce DoceloweGrajmit|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(600)|Dynamiczny|Określ czas w sekundach. Limit czasu osiągnięcia docelowej liczby replik; po którym zostanie zainicjowany raport ostrzegawczy o stanie zdrowia |
|KworumLossWaitDuration |Czas w sekundach, domyślnie jest MaxValue |Dynamiczny|Określ czas w sekundach. Jest to maksymalny czas trwania, dla którego zezwalamy partycji być w stanie utraty kworum. Jeśli partycja jest nadal w utracie kworum po tym czasie trwania; partycja jest odzyskiwana z utraty kworum, biorąc pod uwagę repliki w dół jako utracone. Należy zauważyć, że może to potencjalnie spowodować utratę danych. |
|RekonfiguracjaTimeLimit|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(300)|Dynamiczny|Określ czas w sekundach. Termin ponownej konfiguracji; po którym zostanie zainicjowany raport ostrzegawczy o stanie zdrowia |
|ReplicaRestartWaitDuration|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(60.0 \* 30)|Niedozwolone|Określ czas w sekundach. Jest to replicarestartWaitDuration dla FMService |
| SeedNodeQuorumAdodatkoweBufferNodes | int, wartość domyślna to 0 | Dynamiczny | Bufor węzłów źródłowych, który jest potrzebny do w górę (wraz z kworum węzłów źródłowych) FM powinien umożliwić maksymalnie (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) węzłów źródłowych, aby przejść w dół. |
|StandByReplicaOdwekczenie|Timespan, domyślnie jest wspólny::TimeSpan::FromSeconds(3600.0 \* 24 \* 7)|Niedozwolone|Określ czas w sekundach. Jest to standbyreplicaKeepDuration dla FMService |
|Rozmiar zestawu TargetReplicaSetSize|int, wartość domyślna to 7|Niedozwolone|Jest to docelowa liczba replik FM, które będą utrzymywać sieci szkieletowej systemu Windows. Większa liczba skutkuje większą niezawodnością danych FM; z niewielkim kompromisem wydajności. |
|UżytkownikMaxStandByReplicaCount |Int, wartość domyślna to 1 |Dynamiczny|Domyślna maksymalna liczba replik w trybie wstrzymania, które system przechowuje dla usług użytkownika. |
|UserReplicaRestartWaitDuration |Czas w sekundach, domyślnie 60,0 \* 30 |Dynamiczny|Określ czas w sekundach. Gdy utrwalona replika ustępuje; Sieci szkieletowej systemu Windows czeka na ten czas trwania repliki wrócić przed utworzeniem nowych replik zastępczych (co wymagałoby kopii stanu). |
|UserStandByReplicaKeepDuration |Czas w sekundach, domyślnie 3600.0 \* 24 \* 7 |Dynamiczny|Określ czas w sekundach. Gdy utrwalona replika wrócić ze stanu w dół; być może został już wymieniony. Ten czasomierz określa, jak długo fm będzie przechowywać replikę w trybie gotowości przed odrzuceniem go. |

## <a name="faultanalysisservice"></a>Usługa analizy błędów

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSekundy | Int, wartość domyślna to 604800 |Statyczny| Jest to w przybliżeniu, jak długo, aby zachować akcje, które są w stanie terminala. Zależy to również od StoredActionCleanupIntervalInSeconds; ponieważ praca do czyszczenia odbywa się tylko w tym przedziale. 604800 to 7 dni. |
|DataLossCheckPollIntervalInSekundy|int, domyślnie jest 5|Statyczny|Jest to czas między sprawdza, czy system wykonuje podczas oczekiwania na utratę danych się zdarzyć. Liczba razy numer utraty danych zostanie sprawdzona na wewnętrznej iteracji jest DataLossCheckWaitDurationInSeconds/this. |
|DataLossCheckWaitDurationInSeconds|int, domyślnie jest to 25|Statyczny|Całkowita ilość czasu; w kilka sekund; że system będzie czekać na utratę danych się zdarzyć. Jest to używane wewnętrznie, gdy wywoływany jest interfejs API StartPartitionDataLossAsync(). |
|Rozmiar MinReplicaSetSize |Int, wartość domyślna to 0 |Statyczny|Rozmiar MinReplicaSetSize dla usługi FaultAnalysisService. |
|Miejsca docelowe | ciąg, domyślnie jest ""|Statyczny| MieszczanieConstraints dla FaultAnalysisService. |
|KworumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Statyczny|Określ czas w sekundach. Usługa Analizy błędów. |
|ReplicaDropWaitDurationInSeconds|int, domyślnie jest to 600|Statyczny|Ten parametr jest używany, gdy wywoływany jest interfejs API utraty danych. Określa, jak długo system będzie czekać na replikę, aby uzyskać usunięte po usunięciu repliki jest wewnętrznie wywoływane na nim. |
|ReplicaRestartWaitDuration |Czas w sekundach, domyślnie to 60 minut|Statyczny|Określ czas w sekundach. ReplicaRestartWaitDuration dla FaultAnalysisService. |
|StandByReplicaOdwekczenie| Czas w sekundach, domyślnie (60*24*7) minut |Statyczny|Określ czas w sekundach. StandByReplicaKeepDuration dla FaultAnalysisService. |
|StoredActionCleanupIntervalInSekundy | Int, wartość domyślna to 3600 |Statyczny|Jest to, jak często sklep zostanie oczyszczony. Tylko akcje w stanie terminala; i że zakończone co najmniej CompletedActionKeepDurationInSeconds temu zostaną usunięte. |
|StoredChaosEventCleanupIntervalInSekds | Int, wartość domyślna to 3600 |Statyczny|Jest to, jak często magazyn będzie poddana inspekcji w celu czyszczenia; jeżeli liczba zdarzeń jest większa niż 30000; rozpocznie się oczyszczanie. |
|Rozmiar zestawu TargetReplicaSetSize |Int, wartość domyślna to 0 |Statyczny|NOT_PLATFORM_UNIX_START TargetReplicaSetSize dla FaultAnalysisService. |

## <a name="federation"></a>Federacja

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|DzierżawaDuration |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy trwa między węzłem a jego sąsiadami. |
|DzierżawaDurationAcrossFaultDomain |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Czas trwania dzierżawy trwa między węzłem a jego sąsiadami w domenach błędów. |

## <a name="filestoreservice"></a>Usługa FileStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AcceptChunkUpload (AkceptujCzunkUpload)|Bool, domyślnie jest PRAWDA|Dynamiczny|Config, aby ustalić, czy usługa magazynu plików akceptuje przekazywanie plików na podstawie fragmentu, czy nie podczas kopiowania pakietu aplikacji. |
|AnonimowyAccessEnabled | Bool, wartość domyślna jest true |Statyczny|Włącz/Wyłącz dostęp anonimowy do udziałów FileStoreService. |
|Nazwa wspólna1Ntlmx509Namekompoznana|ciąg, domyślnie jest ""|Statyczny| Nazwa potoczna certyfikatu X509 używana do generowania protokołu HMAC na commonname1NtlmPasswordSecret podczas korzystania z uwierzytelniania NTLM |
|CommonName1Ntlmx509Lokalizacja magazynów|ciąg, domyślnie jest "LocalMachine"|Statyczny|Lokalizacja magazynu certyfikatu X509 używanego do generowania HMAC na commonname1NtlmPasswordSecret podczas korzystania z uwierzytelniania NTLM |
|Nazwa CommonName1Ntlmx509StoreName|ciąg, domyślnie jest "MY"| Statyczny|Nazwa sklepu certyfikatu X509 używanego do generowania protokołu HMAC na commonname1NtlmPasswordSecret podczas korzystania z uwierzytelniania NTLM |
|Nazwa wspólna2Ntlmx509Namekompoznana|ciąg, domyślnie jest ""|Statyczny|Nazwa potoczna certyfikatu X509 używana do generowania protokołu HMAC na commonname2NtlmPasswordSecret podczas korzystania z uwierzytelniania NTLM |
|CommonName2Ntlmx509Lokalizacja magazynów|ciąg, domyślnie jest "LocalMachine"| Statyczny|Lokalizacja magazynu certyfikatu X509 używanego do generowania HMAC na commonname2NtlmPasswordSecret podczas korzystania z uwierzytelniania NTLM |
|Nazwa CommonName2Ntlmx509StoreName|ciąg, domyślnie jest "MY"|Statyczny| Nazwa sklepu certyfikatu X509 używanego do generowania HMAC na commonname2NtlmPasswordSecret podczas korzystania z uwierzytelniania NTLM |
|CommonNameNtlmPasswordSecret|SecureString, domyślnie jest wspólny::SecureString(")| Statyczny|Klucz tajny hasła, który używany jako materiał siewny wygenerował to samo hasło podczas korzystania z uwierzytelniania NTLM |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(5)|Dynamiczny|Określ czas w sekundach. Przedział czasu między sprawdzaniem miejsca na dysku do raportowania zdarzenia kondycji, gdy dysk jest bliski braku miejsca. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(15)|Dynamiczny|Określ czas w sekundach. Przedział czasu między sprawdzaniem miejsca na dysku do raportowania zdarzenia kondycji, gdy na dysku jest wystarczająco dużo miejsca. |
|EnableImageStoreHealthReporting (Raportowanie o stanie zdrowia w sieci) |bool, domyślnie jest PRAWDA    |Statyczny|Config, aby ustalić, czy usługa magazynu plików powinna zgłaszać jego kondycję. |
|FreeDiskSpaceNotificationSizeInKB|int64, domyślnie jest\*to 25 1024 |Dynamiczny|Rozmiar wolnego miejsca na dysku, poniżej którego może wystąpić ostrzeżenie o kondycji. Minimalna wartość tego config i FreeDiskSpaceNotificationThresholdPercentage config są używane do określenia wysyłania ostrzeżenia o kondycji. |
|FreeDiskSpaceNotificationThresholdPercentage|podwójne, domyślnie jest to 0,02 |Dynamiczny|Procent wolnego miejsca na dysku, poniżej którego może wystąpić ostrzeżenie o kondycji. Minimalna wartość tego config i FreeDiskSpaceNotificationInMB config są używane do określenia wysyłania ostrzeżenia o kondycji. |
|Generowanie konta 1Klinamuname| bool, domyślnie jest PRAWDA|Statyczny|Określa, czy konto ma być generowane z algorytmem generowania nazwy użytkownika V1. Począwszy od sieci szkieletowej usług w wersji 6.1; konto z generowaniem v2 jest zawsze tworzone. Konto V1 jest niezbędne w przypadku uaktualnień z/do wersji, które nie obsługują generacji V2 (przed wersją 6.1).|
|MaxCopyOperationThreads | Uint, wartość domyślna to 0 |Dynamiczny| Maksymalna liczba równoległych plików, które pomocnicze można skopiować z podstawowego. '0' == liczba rdzeni. |
|MaxFileOperationWątry | Uint, wartość domyślna to 100 |Statyczny| Maksymalna liczba równoległych wątków dozwolonych do wykonywania operacji plików (Kopiuj/Przenieś) w podstawowym. '0' == liczba rdzeni. |
|MaxRequestProcessingThreads | Uint, wartość domyślna to 200 |Statyczny|Maksymalna liczba równoległych wątków dozwolonych do przetwarzania żądań w podstawowym. '0' == liczba rdzeni. |
|MaxSecondaryFileCopyFailureThreshold | Uint, wartość domyślna to 25|Dynamiczny|Maksymalna liczba kopii pliku ponawia ponowną ponawianie się w pomocniczym przed rezygnacją. |
|MaxStoreOperations (MaxStoreOperations) | Uint, wartość domyślna to 4096 |Statyczny|Maksymalna liczba operacji transakcji magazynu równoległego dozwolona w podstawowych. '0' == liczba rdzeni. |
|Czas działania nazewnictwa nazewnictwa |Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ czas w sekundach. Limit czasu wykonywania operacji nazewnictwa. |
|PodstawowyAktomatNTLMPasswordSecret | SecureString, domyślnie jest pusty |Statyczny| Klucz tajny hasła, który używany jako materiał siewny wygenerował to samo hasło podczas korzystania z uwierzytelniania NTLM. |
|Lokalizacja magazynu PrimaryAccountNTLMX509 | ciąg, domyślnie jest "LocalMachine"|Statyczny| Lokalizacja magazynu certyfikatu X509 używanego do generowania HMAC w PrimaryAccountNTLMPasswordSecret podczas korzystania z uwierzytelniania NTLM. |
|Nazwa sieci podstawowej | ciąg, domyślnie jest "MY"|Statyczny| Nazwa magazynu certyfikatu X509 używanego do generowania HMAC w PrimaryAccountNTLMPasswordSecret podczas korzystania z uwierzytelniania NTLM. |
|Drukarka podstawowaNTLMX509Thumbprint | ciąg, domyślnie jest ""|Statyczny|Odcisk palca certyfikatu X509 używany do generowania pamięci HMAC w primaryaccountNTLMPasswordSecret podczas korzystania z uwierzytelniania NTLM. |
|Typ konta podstawowego | ciąg, domyślnie jest "" |Statyczny|Podstawowy AccountType podmiotu do listy ACL udziałów FileStoreService. |
|Nazwa użytkownika podstawowego | ciąg, domyślnie jest "" |Statyczny|Nazwa konta podstawowego podmiotu głównego do listy ACL udziałów FileStoreService. |
|Klucz podstawowyWzdniesznik użytkownika | SecureString, domyślnie jest pusty |Statyczny|Hasło konta podstawowego podmiotu zabezpieczeń do listy ACL udziałów FileStoreService. |
|Czas działania programu QueryOperation | Czas w sekundach, wartość domyślna to 60 |Dynamiczny|Określ czas w sekundach. Limit czasu wykonywania operacji kwerendy. |
|WtórnyAktomatNTLMPasswordSecret | SecureString, domyślnie jest pusty |Statyczny| Klucz tajny hasła, który używany jako materiał siewny wygenerował to samo hasło podczas korzystania z uwierzytelniania NTLM. |
|SecondaryAccountNTLMX509Lokalizacja magazynów | ciąg, domyślnie jest "LocalMachine" |Statyczny|Lokalizacja magazynu certyfikatu X509 używanego do generowania HMAC w SecondaryAccountNTLMPasswordSecret podczas korzystania z uwierzytelniania NTLM. |
|Drugorzędna nazwa magazynuNTLMX509 | ciąg, domyślnie jest "MY" |Statyczny|Nazwa magazynu certyfikatu X509 używanego do generowania HMAC w secondaryaccountNTLMPasswordSecret podczas korzystania z uwierzytelniania NTLM. |
|Drukarka wtórnaNTLMX509Thumbprint | ciąg, domyślnie jest ""| Statyczny|Odcisk palca certyfikatu X509 używany do generowania HMAC w SecondaryAccountNTLMPasswordSecret podczas korzystania z uwierzytelniania NTLM. |
|Typ konta pomocniczego | ciąg, domyślnie jest ""|Statyczny| Pomocniczy AccountType podmiotu do listy ACL udziałów FileStoreService. |
|Nazwa użytkownika wtórnego | ciąg, domyślnie jest ""| Statyczny|Drugie konto Nazwa użytkownika podmiotu głównego do ACL udziałów FileStoreService. |
|Drugorzędowe KontoUserPassword | SecureString, domyślnie jest pusty |Statyczny|Hasło konta pomocniczego podmiotu zabezpieczeń do listy ACL udziałów FileStoreService. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, domyślnie jest to 500|Dynamiczny|Opóźnienie ponowienia próby kopiowania pliku (w milisekundach).|
|Użyj Funkcji ChunkContentInTransportMessage|bool, domyślnie jest PRAWDA|Dynamiczny|Flaga do korzystania z nowej wersji protokołu przekazywania wprowadzonego w wersji 6.4. Ta wersja protokołu używa transportu sieci szkieletowej usług do przekazywania plików do magazynu obrazów, który zapewnia lepszą wydajność niż protokół SMB używany w poprzednich wersjach. |

## <a name="healthmanager"></a>Healthmanager

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Wartość EnableApplicationTypeHealthEvaluation |Bool, wartość domyślna to false |Statyczny|Zasady oceny kondycji klastra: włącz ocenę kondycji typu aplikacji. |
|MaxSuggestedNumberOfEntityHealthReports|Int, wartość domyślna to 100 |Dynamiczny|Maksymalna liczba raportów kondycji, które jednostka może mieć przed zgłoszeniem obaw dotyczących logiki raportowania kondycji watchdog.The maximum number of health reports that an entity can have before raising concerns about the watchdog's health reporting logic. Każda jednostka kondycji ma mieć stosunkowo niewielką liczbę raportów dotyczących kondycji. Jeśli liczba raportów przekroczy tę liczbę; mogą występować problemy z implementacją watchdoga. Jednostka z zbyt wielu raportów jest oflagowana za pośrednictwem raportu kondycji ostrzeżenie, gdy jednostka jest oceniana. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|RozważwarningAsError |Bool, wartość domyślna to false |Statyczny|Zasady oceny kondycji klastra: ostrzeżenia są traktowane jako błędy. |
|MaxPercentNiezdrowezastosacje | Int, wartość domyślna to 0 |Statyczny|Zasady oceny kondycji klastra: maksymalny procent aplikacji w złej kondycji dozwolone dla klastra w dobrej kondycji. |
|MaxPercentUnhealthyNodes | Int, wartość domyślna to 0 |Statyczny|Zasady oceny kondycji klastra: maksymalny procent węzłów w złej kondycji dozwolone dla klastra w dobrej kondycji. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, domyślnie jest to 10|Statyczny|Zasady oceny kondycji uaktualnienia klastra: maksymalny procent węzłów w złej kondycji delta, które mogą być w dobrej kondycji klastra |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, domyślnie jest to 15|Statyczny|Zasady oceny kondycji uaktualnienia klastra: maksymalny procent różnicy węzłów w złej kondycji w domenie uaktualnienia, co pozwala na to, aby klaster był w dobrej kondycji |

## <a name="hosting"></a>Hosting

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AktywacjaMaxFailureCount |Liczba całkowita, wartość domyślna to 10 |Dynamiczny|Liczba ponownych prób systemu nie powiodła się aktywacja przed rezygnacją |
|AktywacjaMaxRetryInterval |Czas w sekundach, domyślnie to 300 |Dynamiczny|Przy każdym ciągłym niepowodzeniu aktywacji system ponawia ponowną aktywację do aktywacjiMaxFailureCount. ActivationMaxRetryInterval określa interwał oczekiwania przed ponawianiem próby po każdym niepowodzeniu aktywacji |
|ActivationRetryBackoffInterval |Czas w sekundach, wartość domyślna to 5 |Dynamiczny|Interwał wycofywania przy każdym niepowodzeniu aktywacji; Przy każdym ciągłym niepowodzeniu aktywacji system ponawia ponowną aktywację do maxActivationFailureCount. Interwał ponawiania przy każdej próbie jest wynikiem ciągłego niepowodzenia aktywacji i interwału wycofywania aktywacji. |
|Limit czasu aktywacji| TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(180)|Dynamiczny| Określ czas w sekundach. Limit czasu aktywacji aplikacji; dezaktywacji i uaktualnienia. |
|Limit czasu aplikacji| TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(120)|Dynamiczny| Określ czas w sekundach. Po wykryciu wyjścia sieci szkieletowej w procesach samoaktywnych; FabricRuntime zamyka wszystkie repliki w procesie hosta użytkownika (hosta aplikacji). Jest to limit czasu dla operacji zamknięcia. |
| CnsNetworkPluginCnmUrlPort | wstring, domyślnie jest L"48080" | Statyczny | Port adresu URL interfejsu api platformy Azure |
| CnsNetworkPluginCnsUrlPort | wstring, domyślnie jest L"10090" | Statyczny | Port adresu URL platformy Azure |
|KonteneryArgumenty|ciąg, domyślnie jest "-H localhost:2375 -H npipe://"|Statyczny|Usługa Service Fabric (SF) zarządza demonem docker (z wyjątkiem komputerów klienckich systemu Windows, takich jak Win10). Ta konfiguracja umożliwia użytkownikowi określenie argumentów niestandardowych, które powinny być przekazywane do demona docker podczas uruchamiania go. Po określeniu argumentów niestandardowych sieci szkieletowej usług nie przekazuje żadnych innych argumentów do aparatu platformy Docker z wyjątkiem argumentu "--pidfile". W związku z tym użytkownicy nie powinni określać argumentu "--pidfile" jako część argumentów klienta. Ponadto argumenty niestandardowe powinny zapewnić, że demon docker nasłuchuje domyślnego potoku nazw w systemie Windows (lub uniksowym gnieździe domeny w systemie Linux), aby sieci szkieletowej usług mogły się z nim komunikować.|
|ContainerServiceLogFileMaxSizeInKb|int, domyślnie jest 32768|Statyczny|Maksymalny rozmiar pliku dziennika generowanego przez kontenery docker.  Tylko system Windows.|
|ContainerImageDownloadTimeout|int, liczba sekund, domyślnie 1200 (20 min)|Dynamiczny|Liczba sekund przed pobraniem czasu pobierania obrazu.|
|KontenerImagesToSkip|ciąg, nazwy obrazów oddzielone pionowym znakiem linii, domyślnie jest ""|Statyczny|Nazwa jednego lub więcej obrazów kontenerów, które nie powinny zostać usunięte.  Używany z parametrem PruneContainerImages.|
|ContainerServiceLogFileNamePozrost|ciąg, domyślnie jest "sfcontainerlogs"|Statyczny|Prefiks nazwy pliku dla plików dziennika generowanych przez kontenery docker.  Tylko system Windows.|
|ContainerServiceLogFileRetentionCount|int, domyślnie jest to 10|Statyczny|Liczba plików dziennika generowanych przez kontenery platformy docker przed nadpisywane pliki dziennika.  Tylko system Windows.|
|CreateFabricRuntimeTimeout|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(120)|Dynamiczny| Określ czas w sekundach. Wartość limitu czasu dla wywołania sync FabricCreateRuntime |
|Nazwa konta domyślnego|ciąg, domyślnie jest ""|Statyczny|Domyślne poświadczenia używane zamiast poświadczeń określonych w pliku ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|ciąg, domyślnie jest ""|Statyczny|Domyślne poświadczenia hasła używane zamiast poświadczeń określonych w pliku ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|ciąg, domyślnie jest ""|Statyczny|Gdy nie jest pusty ciąg, wartość może być "Zaszyfrowane" lub "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|bool, domyślnie jest FALSE|Statyczny|Domyślnie nazwa usługi jest dołączana do nazwy DNS SF dla usług kontenera. Ta funkcja zatrzymuje to zachowanie, dzięki czemu nic nie jest domyślnie dołączane do nazwy DNS SF w ścieżce rozpoznawania.|
|Licznik DeploymentMaxFailure|int, domyślnie jest to 20| Dynamiczny|Wdrożenie aplikacji zostanie ponowione dla DeploymentMaxFailureCount razy przed niepowodzeniem wdrożenia tej aplikacji w węźle.| 
|DeploymentMaxRetryInterval| TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(3600)|Dynamiczny| Określ czas w sekundach. Maksymalny interwał ponawiania próby dla wdrożenia. Przy każdym ciągłym niepowodzeniu interwał ponawiania jest obliczany jako Min( DeploymentMaxRetryInterval; Liczba ciągłych awarii * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(10)|Dynamiczny|Określ czas w sekundach. Interwał wycofywania dla niepowodzenia wdrożenia. Przy każdej awarii ciągłego wdrażania system ponowi próbę wdrożenia do MaxDeploymentFailureCount. Interwał ponawiania jest wynikiem ciągłego niepowodzenia wdrażania i interwału wycofywania wdrożenia. |
|Wyłączniki|bool, domyślnie jest FALSE|Statyczny|Config do wyłączania kontenerów — używany zamiast disableContainerServiceStartOnContainerActivatorOpen, który jest przestarzały config |
|WyłączDockerRequestRetry|bool, domyślnie jest FALSE |Dynamiczny| Domyślnie SF komunikuje się z DD (docker dameon) z limitem czasu "DockerRequestTimeout" dla każdego żądania http wysłane do niego. Jeśli DD nie odpowiada w tym okresie; SF ponownie wyśmia żądanie, jeśli operacja najwyższego poziomu nadal ma pozostały czas.  Z pojemnikiem hyperv; DD czasami zająć dużo więcej czasu, aby przywołać kontenera lub wyłączyć go. W takich przypadkach DD żądanie razy przesunie się z punktu widzenia SF i SF ponawia operację. Czasami wydaje się to zwiększać presję na DD. Ten config pozwala wyłączyć tę próbę i czekać na DD odpowiedzieć. |
|DnsServerListTwoIps | Bool, domyślnie jest FALSE | Statyczny | Ta flaga dodaje lokalny serwer dns dwa razy, aby pomóc złagodzić sporadyczne rozwiązywanie problemów. |
| Serwer DoNotInjectLocalDnsServer | bool, domyślnie jest FALSE | Statyczny | Zapobiega wstrzykuje się lokalnego adresu IP jako serwera DNS dla kontenerów. |
|Włącz funkcję Aktywujnowindow| bool, domyślnie jest FALSE|Dynamiczny| Aktywowany proces jest tworzony w tle bez żadnej konsoli. |
|Tryb EnableContainerServiceDebugMode|bool, domyślnie jest PRAWDA|Statyczny|Włącz/wyłącz rejestrowanie kontenerów dokowych.  Tylko system Windows.|
|Integracja enabledockerHealthCheck|bool, domyślnie jest PRAWDA|Statyczny|Umożliwia integrację zdarzeń docker HEALTHCHECK z raportem kondycji systemu sieci szkieletowej usług |
|Włącz ProcesOwanieDebugging|bool, domyślnie jest FALSE|Dynamiczny| Umożliwia uruchamianie hostów aplikacji w debugerze |
|EndpointProviderEnabled| bool, domyślnie jest FALSE|Statyczny| Umożliwia zarządzanie zasobami punktu końcowego przez sieci szkieletowej. Wymaga specyfikacji zakresu portów aplikacji początkowej i końcowej w FabricNode. |
|TkaninaContainerAppsEnabled| bool, domyślnie jest FALSE|Statyczny| |
|FirewallPolicyEnabled|bool, domyślnie jest FALSE|Statyczny| Umożliwia otwieranie portów zapory dla zasobów punktu końcowego z jawnymi portami określonymi w ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(120)|Dynamiczny|Określ czas w sekundach. Wartość limitu czasu dla wywołań CodePackageActivationContext. Nie dotyczy to usług ad hoc. |
|GovernOnlyMainMemoryForProcesses|bool, domyślnie jest FALSE|Statyczny|Domyślne zachowanie zarządzania zasobami jest umieścić limit określony w MemoryInMB na ilość pamięci całkowitej (RAM + swap), który używa procesu. W przypadku przekroczenia limitu; proces otrzyma wyjątek OutOfMemory. Jeśli ten parametr jest ustawiony na true; limit zostanie zastosowany tylko do ilości pamięci RAM, z której będzie korzystać proces. W przypadku przekroczenia tego limitu; a jeśli to ustawienie jest prawdziwe; następnie system operacyjny zamieni pamięć główną na dysk. |
|IpProviderEnabled|bool, domyślnie jest FALSE|Statyczny|Umożliwia zarządzanie adresami IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, domyślnie jest FALSE|Statyczny|Czy DefaultContainerRepositoryPassword jest szyfrowany, czy nie.|
|LinuxExternalExecutablePath|ciąg, domyślnie jest "/usr/bin/" |Statyczny|Katalog podstawowy zewnętrznych poleceń wykonywalnych w węźle.|
|NTLMAuthenticationEnabled|bool, domyślnie jest FALSE|Statyczny| Umożliwia obsługę przy użyciu NTLM przez pakiety kodu, które są uruchomione jako inni użytkownicy, dzięki czemu procesy między komputerami mogą komunikować się bezpiecznie. |
|NTLMAuthenticationPasswordSecret|SecureString, domyślnie jest wspólny::SecureString(")|Statyczny|Jest zaszyfrowanym ma, który jest używany do generowania hasła dla użytkowników NTLM. Należy ustawić, jeśli NTLMAuthenticationEnabled jest true. Zweryfikowane przez wdrażacza. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(3)|Dynamiczny|Określ czas w sekundach. Ustawienia specyficzne dla środowiska Okresowy interwał, w którym hosting skanuje w poszukiwaniu nowych certyfikatów, które mają być używane dla konfiguracji NTLM usługi FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(4)|Dynamiczny| Określ czas w sekundach. Limit czasu konfigurowania użytkowników NTLM przy użyciu nazw pospolitych certyfikatów. Użytkownicy NTLM są potrzebne dla udziałów FileStoreService. |
|PruneContainerImages (Zdjęcia wstępne)|bool, domyślnie jest FALSE|Dynamiczny| Usuń nieużywane obrazy kontenerów aplikacji z węzłów. Gdy ApplicationType jest wyrejestrowany z klastra sieci szkieletowej usług, obrazy kontenerów, które były używane przez tę aplikację zostaną usunięte w węzłach, w których został pobrany przez sieci szkieletowej usług. Przycinanie jest uruchamiane co godzinę, więc usunięcie obrazów z klastra może potrwać do jednej godziny (plus czas) dla usunięcia obrazów z klastra.<br>Sieci szkieletowej usług nigdy nie będzie pobierać ani usuwać obrazów niezwiązanym z aplikacją.  Niepowiązane obrazy, które zostały pobrane ręcznie lub w inny sposób, muszą zostać usunięte jawnie.<br>Obrazy, które nie powinny być usuwane można określić w ContainerImagesToSkip parametru.| 
|Zarejestruj Pakiet Kodu PakietOTimeout|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(120)|Dynamiczny| Określ czas w sekundach. Wartość limitu czasu dla wywołania synchronizacji FabricRegisterCodePackageHost. Dotyczy to tylko hostów aplikacji pakietu wielodyskowego, takich jak FWP |
|Czas składania wniosków|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(30)|Dynamiczny| Określ czas w sekundach. Reprezentuje limit czasu komunikacji między hostem aplikacji użytkownika i procesem sieci szkieletowej dla różnych operacji związanych z hostingiem, takich jak rejestracja fabryczna; rejestracji w czasie wykonywania. |
|RunAsPolicyEnabled| bool, domyślnie jest FALSE|Statyczny| Włącza uruchamianie pakietów kodu jako użytkownika lokalnego innego niż użytkownik, w którym jest uruchomiony proces sieci szkieletowej. Aby włączyć tę strukturę szkieletową zasad musi być uruchomiony jako system lub jako użytkownik, który ma SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(120)|Dynamiczny|Określ czas w sekundach. Wartość limitu czasu dla wywołania rejestru synchronizacji(bezstanowe/stanowe)ServiceFactory |
|UsługaTypeDisableFailureThreshold |Liczba całkowita, wartość domyślna to 1 |Dynamiczny|Jest to próg liczby błędów, po którym funkcja FailoverManager (FM) jest powiadamiana o wyłączeniu typu usługi w tym węźle i wypróbowaniu innego węzła do umieszczenia. |
|UsługaTypeDisableGraceInterval|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(30)|Dynamiczny|Określ czas w sekundach. Przedział czasu, po którym można wyłączyć typ usługi |
|Limit rejestracji typu usługi |Czas w sekundach, wartość domyślna to 300 |Dynamiczny|Maksymalny czas rejestracji typu ServiceType w sieci szkieletowej |
|UżyjContainerServiceArgument|bool, domyślnie jest PRAWDA|Statyczny|Ta config mówi hosting pominąć przekazywanie argumentów (określone w konfiguracji ContainerServiceArguments) do demona docker.|

## <a name="httpgateway"></a>Strona HttpGateway

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AktywneListenery |Uint, wartość domyślna to 50 |Statyczny| Liczba odczytów do opublikowania w kolejce serwera http. Służy to do sterowania liczbą równoczesnych żądań, które mogą być spełnione przez HttpGateway. |
|Protokół HttpGatewayHealthReportSendInterval |Czas w sekundach, wartość domyślna to 30 |Statyczny|Określ czas w sekundach. Interwał, w którym brama http wysyła skumulowane raporty kondycji do Menedżera kondycji. |
|HttpStrictTransportSecurityHeader (HttpStrictTransportSecurityHeader)|ciąg,domyślnie jest ""|Dynamiczny| Określ wartość nagłówka HTTP Strict Transport Security, która ma zostać uwzględniona w każdej odpowiedzi wysłanej przez httpgateway. Po ustawieniu na pusty ciąg; ten nagłówek nie zostanie uwzględniony w odpowiedzi bramy.|
|IsEnabled|Bool, wartość domyślna to false |Statyczny| Włącza/wyłącza httpgateway. Protokół HttpGateway jest domyślnie wyłączony. |
|MaxEntityBodySize (Rozmiar) |Uint, domyślnie 4194304 |Dynamiczny|Daje maksymalny rozmiar treści, których można oczekiwać od żądania http. Wartość domyślna to 4 MB. Httpgateway zakończy się niepowodzeniem żądania, jeśli ma treść rozmiaru > tej wartości. Minimalny rozmiar fragmentu odczytu to 4096 bajtów. Więc to musi być >= 4096. |

## <a name="imagestoreservice"></a>Usługa ImageStoreService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Enabled (Włączony) |Bool, wartość domyślna to false |Statyczny|Flaga Enabled dla usługi ImageStoreService. Domyślnie: false |
|Rozmiar MinReplicaSetSize | Int, wartość domyślna to 3 |Statyczny|Rozmiar MinReplicaSetSize dla usługi ImageStoreService. |
|Miejsca docelowe | ciąg, domyślnie jest "" |Statyczny| Usługi PlacementConstraints dla usługi ImageStoreService. |
|KworumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Statyczny| Określ czas w sekundach. Usługa ImageStoreStoreService. |
|ReplicaRestartWaitDuration | Czas w sekundach, domyślnie 60,0 \* 30 |Statyczny|Określ czas w sekundach. ReplicaRestartWaitDuration dla ImageStoreService. |
|StandByReplicaOdwekczenie | Czas w sekundach, domyślnie 3600.0 \* 2 |Statyczny| Określ czas w sekundach. StandByReplicaKeepDuration dla ImageStoreService. |
|Rozmiar zestawu TargetReplicaSetSize | Int, wartość domyślna to 7 |Statyczny|TargetReplicaSetSize dla ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger ( KtlLogger )

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Konfiguracja automatyczna |Int, wartość domyślna to 1 |Dynamiczny|Flaga wskazująca, czy ustawienia pamięci powinny być skonfigurowane automatycznie i dynamicznie. Jeśli zero, ustawienia konfiguracji pamięci są używane bezpośrednio i nie zmieniają się w zależności od warunków systemowych. Jeśli ktoś wtedy ustawienia pamięci są konfigurowane automatycznie i mogą ulec zmianie w zależności od warunków systemowych. |
|MaximumDestagingWriteOutstandingInKB | Int, wartość domyślna to 0 |Dynamiczny|Liczba KB, aby umożliwić udostępnionego dziennika do góry przed dedykowanym dzienniku. Użyj 0, aby wskazać brak limitu.
|SharedLogId (ida użytkownika sharedlogid) |ciąg, domyślnie jest "" |Statyczny|Unikatowy identyfikator guid dla udostępnionego kontenera dziennika. Użyj "", jeśli używasz domyślnej ścieżki w katalogu głównym danych sieci szkieletowej. |
|Ścieżka sharedlogpath |ciąg, domyślnie jest "" |Statyczny|Ścieżka i nazwa pliku do lokalizacji, aby umieścić udostępniony kontener dziennika. Użyj "" do używania domyślnej ścieżki w katalogu głównym danych sieci szkieletowej. |
|SharedLogSizeInMB |Int, wartość domyślna to 8192 |Statyczny|Liczba MB do przydzielenia w kontenerze dziennika udostępnionego. |
|SharedLogThrottleLimitInProcentUuuuuuuuu|int, wartość domyślna to 0 | Statyczny | Procent użycia udostępnionego dziennika, który będzie wywoływać ograniczanie przepustowości. Wartość powinna wynosić od 0 do 100. Wartość 0 oznacza użycie domyślnej wartości procentowej. Wartość 100 oznacza, że nie dławienie w ogóle. Wartość z 1 do 99 określa procent użycia dziennika, powyżej którego nastąpi ograniczanie przepustowości; na przykład, jeśli udostępniony dziennik jest 10GB i wartość jest 90 następnie ograniczanie nastąpi po 9GB jest w użyciu. Zaleca się użycie wartości domyślnej.|
|WriteBufferMemoryPoolMaximumInKB | Int, wartość domyślna to 0 |Dynamiczny|Liczba KB, aby umożliwić puli pamięci buforu zapisu do dorastania do. Użyj 0, aby wskazać brak limitu. |
|WriteBufferMemoryPoolMinimumInKB |Int, wartość domyślna to 8388608 |Dynamiczny|Liczba KB początkowo przydzielić dla puli pamięci buforu zapisu. Użyj 0, aby wskazać brak limitu Domyślne powinny być zgodne z SharedLogSizeInMB poniżej. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService (Usługa ManagedIdentityToken)
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|IsEnabled|bool, domyślnie jest FALSE|Statyczny|Flaga kontrolowanie obecności i stanu usługi tokenu tożsamości zarządzanej w klastrze;jest to warunek wstępny do korzystania z funkcji tożsamości zarządzanej aplikacji sieci szkieletowej usług.|

## <a name="management"></a>Zarządzanie

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(5)|Dynamiczny|Określ czas w sekundach. Interwał oczyszczania dla dozwolonych dla wyrejestrowania typu aplikacji podczas automatycznego oczyszczania typu aplikacji.|
|AzureStorageMaxConnections | Int, domyślnie jest 5000 |Dynamiczny|Maksymalna liczba równoczesnych połączeń z magazynem azure. |
|AzureStorageMaxWorkerThreads | Int, wartość domyślna to 25 |Dynamiczny|Maksymalna liczba wątków roboczych równolegle. |
|AzureStorageOperationTimeout | Czas w sekundach, wartość domyślna to 6000 |Dynamiczny|Określ czas w sekundach. Limit czasu na wykonanie operacji xstore. |
|CleanupApplicationPackageOnProvisionSukces|bool, domyślnie jest FALSE |Dynamiczny|Włącza lub wyłącza automatyczne oczyszczanie pakietu aplikacji w pomyślnym aprowizowaniu. |
|CleanupUnusedApplicationTypes|Bool, domyślnie jest FALSE |Dynamiczny|Ta konfiguracja, jeśli jest włączona, umożliwia automatyczne wyrejestrowanie nieużywanych wersji typu aplikacji pomijając najnowsze trzy nieużywane wersje, przycinając w ten sposób miejsce na dysku zajmowane przez magazyn obrazów. Automatyczne oczyszczanie zostanie wyzwolone po zakończeniu pomyślnego udostępnienia dla tego określonego typu aplikacji, a także będzie uruchamiane okresowo raz dziennie dla wszystkich typów aplikacji. Liczba nieużywanych wersji do pominięcia można skonfigurować za pomocą parametru "MaxUnusedAppTypeVersionsToKeep". |
|Wyłączujprawżąwprawniejem | Bool, wartość domyślna to false |Statyczny| Ta konfiguracja umożliwia nam włączenie lub wyłączenie sprawdzania poprawności sumy kontrolnej podczas inicjowania obsługi administracyjnej aplikacji. |
|DisableServerSideCopy | Bool, wartość domyślna to false |Statyczny|Ta konfiguracja włącza lub wyłącza kopię pakietu aplikacji po stronie serwera w magazynie imagestore podczas inicjowania obsługi administracyjnej aplikacji. |
|ImageCachingEnabled (Podpisywanie) | Bool, wartość domyślna jest true |Statyczny|Ta konfiguracja pozwala nam włączyć lub wyłączyć buforowanie. |
|ImageStoreConnectionString |Securestring |Statyczny|Ciąg połączenia z katalogiem głównym dla magazynu obrazów. |
|ImageStoreMinimumTransferBPS | Int, wartość domyślna to 1024 |Dynamiczny|Minimalna szybkość transferu między klastrem a Magazynem obrazów. Ta wartość jest używana do określenia limitu czasu podczas uzyskiwania dostępu do zewnętrznego ImageStore. Zmień tę wartość tylko wtedy, gdy opóźnienie między klastrem i ImageStore jest wysoki, aby dać więcej czasu dla klastra do pobrania z zewnętrznego ImageStore. |
|MaxUnusedAppTypeVersionsToKeep | Int, wartość domyślna to 3 |Dynamiczny|Ta konfiguracja definiuje liczbę nieużywanych wersji typu aplikacji, które mają zostać pominięte w celu oczyszczenia. Ten parametr ma zastosowanie tylko wtedy, gdy parametr CleanupUnusedApplicationTypes jest włączony. |


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|KeyIntegerValueMap, domyślnie jest Brak|Dynamiczny|Określa zestaw MetricActivityThresholds dla metryk w klastrze. Równoważenie będzie działać, jeśli maxNodeLoad jest większa niż MetricActivityThresholds. W przypadku metryk defragmentacji określa ona ilość obciążenia równą lub poniżej której usługa Service Fabric uzna węzeł za pusty |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, domyślnie jest Brak|Dynamiczny|Określa zestaw MetricBalancingThresholds dla metryk w klastrze. Równoważenie będzie działać, jeśli maxNodeLoad/minNodeLoad jest większa niż MetricBalancingThresholds. Defragmentacja będzie działać, jeśli maxNodeLoad/minNodeLoad w co najmniej jeden FD lub UD jest mniejsza niż MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, domyślnie jest Brak|Dynamiczny|Określa część obciążenia, która przykleja się do repliki po wymianie Przyjmuje wartość między 0 (obciążenie nie przykleja się do repliki) i 1 (wczytywki obciążenia z repliką - domyślnie) |

## <a name="namingservice"></a>Usługa nazewnictwa

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Usługa GatewayServiceDescriptionCacheLimit |Int, wartość domyślna to 0 |Statyczny|Maksymalna liczba wpisów zachowanych w pamięci podręcznej opisu usługi LRU w bramie nazewnictwa (ustawiona na 0 dla bez limitu). |
|MaxClientConnections (MaxClientConnections) |Int, wartość domyślna to 1000 |Dynamiczny|Maksymalna dozwolona liczba połączeń klientów na bramę. |
|Maksymalny limit czasu operacji |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ czas w sekundach. Maksymalny limit czasu dozwolony dla obsługi usługi magazynu plików. Żądania określające większy limit czasu zostaną odrzucone. |
|MaxIndexedEmptyPartitions |Int, wartość domyślna to 1000 |Dynamiczny|Maksymalna liczba pustych partycji, które pozostaną indeksowane w pamięci podręcznej powiadomień do synchronizacji ponownie łącząc klientów. Wszystkie puste partycje powyżej tego numeru zostaną usunięte z indeksu w kolejności rosnącej wersji wyszukiwania. Ponowne łączenie klientów może nadal synchronizować i odbierać nieodebrane aktualizacje pustych partycji; ale protokół synchronizacji staje się droższy. |
|Rozmiar MaxMessage |Int, wartość domyślna to 4\*1024\*1024 |Statyczny|Maksymalny rozmiar wiadomości dla komunikacji węzła klienta podczas używania nazewnictwa. Łagodzenie ataków DOS; wartość domyślna wynosi 4 MB. |
|MaxNamingServiceHealthReports (Raporty dotyczące usług zdrowotnych maxnaming) | Int, wartość domyślna to 10 |Dynamiczny|Maksymalna liczba powolnych operacji, które usługa magazynu nazewnictwa zgłasza w złej kondycji w tym samym czasie. Jeśli 0; wszystkie powolne operacje są wysyłane. |
|Maksymalny limit czasu operacji |Czas w sekundach, domyślnie to 600 |Dynamiczny|Określ czas w sekundach. Maksymalny limit czasu dozwolony dla operacji klienta. Żądania określające większy limit czasu zostaną odrzucone. |
|MaxOutstandingNotificationsPerClient |Int, wartość domyślna to 1000 |Dynamiczny|Maksymalna liczba zaległych powiadomień przed rejestracją klienta jest przymusowo zamykana przez bramę. |
|Rozmiar MinReplicaSetSize | Int, wartość domyślna to 3 |Niedozwolone| Minimalna liczba replik usługi nazewnictwa wymaganych do zapisania do ukończenia aktualizacji. Jeśli w systemie jest mniej replik niż ta aktywna, system niezawodności odmawia aktualizacji magazynu usług nazewnictwa do czasu przywrócenia replik. Ta wartość nigdy nie powinna być większa niż TargetReplicaSetSize. |
|PartitionCount |Int, wartość domyślna to 3 |Niedozwolone|Liczba partycji magazynu usługi nazewnictwa, który ma zostać utworzony. Każda partycja jest właścicielem klucza partycji, który odpowiada jego indeksu; więc klucze partycji [0; PartitionCount] istnieje. Zwiększenie liczby partycji usługi nazewnictwa zwiększa skalę, jaką usługa nazewnictwa może wykonać, zmniejszając średnią ilość danych przechowywanych przez dowolny zapas repliki; kosztem zwiększonego wykorzystania zasobów (ponieważ repliki usługi PartitionCount*ReplicaSetSize muszą być utrzymywane).|
|Miejsca docelowe | ciąg, domyślnie jest "" |Niedozwolone| Ograniczenie umieszczania usługi nazewnictwa. |
|KworumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Niedozwolone| Określ czas w sekundach. Gdy usługa nazewnictwa przechodzi w utratę kworum; ten zegar się uruchamia. Po wygaśnięciu FM uzna repliki w dół za utracone; i podjąć próbę odzyskania kworum. Nie, że może to spowodować utratę danych. |
|NaprawaInterwal | Czas w sekundach, wartość domyślna to 5 |Statyczny| Określ czas w sekundach. Interwał, w którym rozpocznie się naprawa niespójności nazewnictwa między właścicielem urzędu a właścicielem nazwy. |
|ReplicaRestartWaitDuration | Czas w sekundach, domyślnie (60,0 * 30)|Niedozwolone| Określ czas w sekundach. Gdy replika usługi nazewnictwa ustępuje; ten zegar się uruchamia. Po wygaśnięciu FM zacznie wymieniać repliki, które są w dół (nie uważa jeszcze je za utracone). |
|ServiceDescriptionCacheLimit | Int, wartość domyślna to 0 |Statyczny| Maksymalna liczba wpisów zachowanych w pamięci podręcznej opisu usługi LRU w usłudze magazynu nazewnictwa (ustawiona na 0 dla bez limitu). |
|Limit transakcji z doraszczeniem usługi |Czas w sekundach, wartość domyślna to 30 |Dynamiczny|Określ czas w sekundach. Limit czasu używany podczas dostarczania powiadomień usługi do klienta. |
|StandByReplicaOdwekczenie | Czas w sekundach, domyślnie to 3600.0 * 2 |Niedozwolone| Określ czas w sekundach. Gdy replika usługi nazewnictwa wrócić ze stanu w dół; być może został już wymieniony. Ten czasomierz określa, jak długo fm będzie przechowywać replikę w trybie gotowości przed odrzuceniem go. |
|Rozmiar zestawu TargetReplicaSetSize |Int, wartość domyślna to 7 |Niedozwolone|Liczba zestawów replik dla każdej partycji magazynu usługi nazewnictwa. Zwiększenie liczby zestawów replik zwiększa poziom niezawodności informacji w magazynie usług nazewnictwa; zmniejszenie zmiany, że informacje zostaną utracone w wyniku awarii węzła; kosztem zwiększonego obciążenia sieci szkieletowej systemu Windows i czasu potrzebnym do wykonywania aktualizacji danych nazewnictwa.|

## <a name="nodebufferpercentage"></a>NodeBufferPrzestja wstęgę
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|KeyDoubleValueMap, domyślnie jest Brak|Dynamiczny|Procent pojemności węzła na nazwę metryki; używany jako bufor w celu zachowania wolnego miejsca w węźle dla sprawy trybu failover. |

## <a name="nodecapacities"></a>Zdolności węzłów

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup |NodeCapacityCollectionMap |Statyczny|Kolekcja pojemności węzłów dla różnych metryk. |

## <a name="nodedomainids"></a>Identyfikatory domen węzła

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup |NodeFaultDomainIdCollection (Dopełnienie węzła) |Statyczny|Zawiera opis domen błędów, do których należy węzeł. Domena błędów jest definiowana za pomocą identyfikatora URI, który opisuje lokalizację węzła w centrum danych.  Identyfikatory URI domeny błędów są w formacie fd:/fd/, po którym następuje segment ścieżki identyfikatora URI.|
|UaktualnienieDojąd |ciąg, domyślnie jest "" |Statyczny|Zawiera opis domeny uaktualnienia, do której należy węzeł. |

## <a name="nodeproperties"></a>Właściwości nodeproperties

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup |NodePropertyCollectionMap |Statyczny|Kolekcja par klucza-wartość ciągu dla właściwości węzła. |

## <a name="paas"></a>Paas

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Ład klastra |ciąg, domyślnie jest "" |Niedozwolone|Magazyn certyfikatów X509 używany przez fabrykę do ochrony konfiguracji. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Liczniki |Ciąg | Dynamiczny |Oddzielona przecinkami lista liczników wydajności do zebrania. |
|IsEnabled |Bool, wartość domyślna jest true | Dynamiczny |Flaga wskazuje, czy kolekcja licznika wydajności w węźle lokalnym jest włączona. |
|MaxCounterBinaryFileSizeInMB |Int, wartość domyślna to 1 | Dynamiczny |Maksymalny rozmiar (w MB) dla każdego pliku binarnego licznika wydajności. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, wartość domyślna to 10 | Dynamiczny |Maksymalny interwał (w sekundach), po którym tworzony jest nowy plik binarny licznika wydajności. |
|Pobieranie próbekIntervalInSekundy |Int, wartość domyślna to 60 | Dynamiczny |Interwał próbkowania dla liczników wydajności, które są zbierane. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing (Rozmieszczenie i ładowanie)

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia koligacji: 0: Twardy; 1: Miękki; negatywne: Ignoruj. |
|ZastosowanieCapacityConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia pojemności: 0: Twardy; 1: Miękki; negatywne: Ignoruj. |
|Źródła autodetectavailableresources|bool, domyślnie jest PRAWDA|Statyczny|Ten config wyzwoli automatyczne wykrywanie dostępnych zasobów w węźle (CPU i pamięci) Gdy ten config jest ustawiony na true - będziemy czytać rzeczywiste pojemności i poprawić je, jeśli użytkownik określił złe pojemności węzła lub nie zdefiniował ich w ogóle Jeśli ten config jest ustawiony na false - będziemy śledzić ostrzeżenie, że użytkownik określił złe pojemności węzła; ale nie poprawimy ich; oznacza, że użytkownik chce mieć zdolności określone jako > niż węzeł naprawdę ma lub jeśli zdolności są niezdefiniowane; będzie zakładać nieograniczoną pojemność |
|BalancingDelayPoNowajna | Czas w sekundach, domyślnie to 120 |Dynamiczny|Określ czas w sekundach. Nie należy rozpoczynać równoważenia działań w tym okresie po dodaniu nowego węzła. |
|BalancingDelayPonodeDown | Czas w sekundach, domyślnie to 120 |Dynamiczny|Określ czas w sekundach. Nie należy uruchamiać równoważenia działań w tym okresie po zdarzeniu węzła w dół. |
|PojemnośćConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia pojemności: 0: Twardy; 1: Miękki; negatywne: Ignoruj. |
|KolejneDroppedMovementsHealthReportLimit | Int, wartość domyślna to 20 | Dynamiczny|Określa liczbę kolejnych razy, że ResourceBalancer-wydane ruchy są odrzucane przed diagnostyki są wykonywane i ostrzeżenia zdrowotne są emitowane. Negatywne: Brak ostrzeżeń emitowanych w tym stanie. |
|ConstraintFixPartialDelayPonowa Niedymaśna | Czas w sekundach, domyślnie to 120 |Dynamiczny| Określ czas w sekundach. DDo nie naprawić FaultDomain i UpgradeDomain naruszenia ograniczeń w tym okresie po dodaniu nowego węzła. |
|ConstraintFixPartialDelayPonoczesieDown | Czas w sekundach, domyślnie to 120 |Dynamiczny| Określ czas w sekundach. Nie należy naprawiać błędówDomena i uaktualnieniauwłau ograniczenia w tym okresie po zdarzeniu węzła w dół. |
|Ograniczenia OgraniczeniaHealthReportLimit | Int, domyślnie jest 50 |Dynamiczny| Określa, ile razy ograniczenie naruszenie repliki musi być uporczywie nieustalone przed przeprowadzeniem diagnostyki i raporty dotyczące kondycji są emitowane. |
|SzczegółoweConstraintViolationHealthReportLimit | Int, wartość domyślna to 200 |Dynamiczny| Określa, ile razy ograniczenie naruszenie repliki musi być uporczywie nieustalone przed przeprowadzeniem diagnostyki i szczegółowe raporty kondycji są emitowane. |
|Szczegółowy diagnozaInfolistLimit | Int, wartość domyślna to 15 |Dynamiczny| Definiuje liczbę wpisów diagnostycznych (ze szczegółowymi informacjami) na ograniczenie, które należy uwzględnić przed obcięciem w diagnostyce.|
|SzczegółoweNodeListLimit | Int, wartość domyślna to 15 |Dynamiczny| Definiuje liczbę węzłów na ograniczenie, które mają być uwzględnione przed obcięciem, w raportach Replika nieumieszczona. |
|Szczegółowy listapartitionLimit | Int, wartość domyślna to 15 |Dynamiczny| Definiuje liczbę partycji na wpis diagnostyczny dla ograniczenia do uwzględnienia przed obcięciem w diagnostyce. |
|SzczegółoweVerboseHealthReportLimit | Int, wartość domyślna to 200 | Dynamiczny|Określa, ile razy replika nieumieszczona musi być trwale nieumieszczona przed emitowanie szczegółowych raportów o kondycji. |
|EnforceUserServiceMetricCapacities (WymuszanieuserServiceMetricCapacities)|bool, domyślnie jest FALSE | Statyczny |Umożliwia ochronę usług sieci szkieletowej. Wszystkie usługi użytkownika znajdują się w ramach jednego obiektu zadania/grupy c i są ograniczone do określonej ilości zasobów. Musi to być statyczne (wymaga ponownego uruchomienia FabricHost) jako tworzenie/usuwanie obiektu zadania użytkownika i ustawianie limitów w wykonaniu podczas otwierania hosta sieci szkieletowej. |
|BłądDomainConstraintPriority | Int, wartość domyślna to 0 |Dynamiczny| Określa priorytet ograniczenia domeny błędów: 0: Hard; 1: Miękki; negatywne: Ignoruj. |
|GlobalMovementThrottleCountingInterval | Czas w sekundach, domyślnie to 600 |Statyczny| Określ czas w sekundach. Wskazać długość przeszłego interwału, dla którego mają być śledzone ruchy repliki domeny (używane wraz z GlobalMovementThrottleThreshold). Można ustawić na 0, aby całkowicie zignorować globalne ograniczanie przepustowości. |
|GlobalMovementThrottleThreshold | Uint, wartość domyślna to 1000 |Dynamiczny| Maksymalna liczba ruchów dozwolonych w fazie równoważenia w poprzednim przedziale wskazanym przez GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, wartość domyślna to 0 | Dynamiczny|Maksymalna liczba ruchów dozwolonych w fazie równoważenia w poprzednim przedziale wskazanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. |
|GlobalMovementThrottleThresholdForPlacement | Uint, wartość domyślna to 0 |Dynamiczny| Maksymalna liczba ruchów dozwolonych w fazie umieszczania w poprzednim przedziale wskazanym przez GlobalMovementThrottleCountingInterval.0 wskazuje brak limitu.|
|GlobalMovementThrottleThresholdPercentage|podwójne, wartość domyślna to 0|Dynamiczny|Maksymalna liczba całkowitych ruchów dozwolonych w fazach równoważenia i umieszczania (wyrażona jako procent całkowitej liczby replik w klastrze) w poprzednim przedziale wskazanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. Jeśli określono zarówno to, jak i GlobalMovementThrottleThreshold; następnie stosuje się bardziej konserwatywny limit.|
|GlobalMovementThrottleThresholdPercentageForBalancing|podwójne, wartość domyślna to 0|Dynamiczny|Maksymalna liczba ruchów dozwolonych w fazie równoważenia (wyrażona jako procent całkowitej liczby replik w PLB) w poprzednim przedziale wskazanym przez GlobalMovementThrottleCountingInterval. 0 oznacza brak limitu. Jeśli określono zarówno to, jak i GlobalMovementThrottleThresholdForBalancing; następnie stosuje się bardziej konserwatywny limit.|
|InBuildThrottlingAssociatedMetric | ciąg, domyślnie jest "" |Statyczny| Skojarzona nazwa metryki dla tej ograniczania przepustowości. |
|InBuildThrottlingEnabled | Bool, wartość domyślna to false |Dynamiczny| Określ, czy ograniczanie przepustowości w kompilacji jest włączone. |
|InBuildThrottlingGlobalMaxValue | Int, wartość domyślna to 0 |Dynamiczny|Maksymalna liczba replik wbudowanych dozwolonych globalnie. |
|InterruptBalancingForAllFailoverUnitUpdates InterruptBalancingForAllFailoverUnitUpdates InterruptBalancingForAllFailoverUnitUpdates InterruptBal | Bool, wartość domyślna to false | Dynamiczny|Określa, czy dowolny typ aktualizacji jednostki trybu failover powinien przerywać szybkie lub powolne uruchamianie równoważenia. Z określonym przebiegiem równoważenia "false" zostanie przerwane, jeśli zostanie utworzona/usunięta w uliczce failoverUnit: zostanie utworzona/usunięta; brakuje replik; zmieniono lokalizację repliki podstawowej lub zmienioną liczbę replik. Uruchamianie równoważenia NIE zostanie przerwane w innych przypadkach - jeśli usługa FailoverUnit: ma dodatkowe repliki; zmieniono dowolną flagę repliki; zmieniono tylko wersję partycji lub inny przypadek. |
|MinConstraintInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ czas w sekundach. Określa minimalną ilość czasu, który musi upłynąć przed dwoma kolejnymi rundami sprawdzania ograniczeń. |
|MinLoadBalancingInterval | Czas w sekundach, wartość domyślna to 5 |Dynamiczny| Określ czas w sekundach. Określa minimalną ilość czasu, który musi upłynąć przed dwoma kolejnymi rundami równoważenia. |
|MinPlacementInterval | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ czas w sekundach. Określa minimalną ilość czasu, który musi upłynąć przed dwoma kolejnymi rundami umieszczania. |
|MoveExistingReplicaForPlacement | Bool, wartość domyślna jest true |Dynamiczny|Ustawienie, które określa, czy przenieść istniejącą replikę podczas umieszczania. |
|RuchPerPartitionPoliczaniePoliczanieInterwalne | Czas w sekundach, domyślnie to 600 |Statyczny| Określ czas w sekundach. Wskazać długość przeszłego interwału, dla którego można śledzić ruchy repliki dla każdej partycji (używane wraz z MovementPerPartitionThrottleThreshold). |
|RuchPerPartitionThrottleThreshold | Uint, wartość domyślna to 50 |Dynamiczny| Nie ruch związany z równoważeniem wystąpi dla partycji, jeśli liczba równoważenia powiązanych ruchów dla replik tej partycji osiągnęła lub przekroczyła MovementPerFailoverUnitThrottleThreshold w poprzednim przedziale wskazanym przez MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityWiększość | Bool, wartość domyślna to false |Dynamiczny| Ustawienie, które określa, czy repliki nadrzędne mogą być przenoszone w celu naprawienia ograniczeń koligacji.|
|Usługi częściowo zawarte | Bool, wartość domyślna jest true |Dynamiczny| Określa, czy wszystkie repliki usługi w klastrze zostaną umieszczone "wszystko lub nic" biorąc pod uwagę ograniczone odpowiednie węzły dla nich.|
|PlaceChildWithoutParent | Bool, wartość domyślna jest true | Dynamiczny|Ustawienie, które określa, czy replika usługi podrzędnej mogą być umieszczone, jeśli nie replika nadrzędna jest w górę. |
|RozmieszczenieConstraintPriority | Int, wartość domyślna to 0 | Dynamiczny|Określa priorytet ograniczenia umieszczania: 0: Twardy; 1: Miękki; negatywne: Ignoruj. |
|Miejsce doceloweConstraintValidationCacheSize | Int, wartość domyślna to 10000 |Dynamiczny| Ogranicza rozmiar tabeli używanej do szybkiej sprawdzania poprawności i buforowania wyrażeń ograniczenia umieszczania. |
|Czas owania Wyszukiwania | Czas w sekundach, wartość domyślna to 0,5 |Dynamiczny| Określ czas w sekundach. Podczas składania usług; szukaj co najwyżej na tak długo przed zwróceniem wyniku. |
|PLBRefreshGap (PlBRefreshGap) | Czas w sekundach, wartość domyślna to 1 |Dynamiczny| Określ czas w sekundach. Określa minimalną ilość czasu, który musi upłynąć przed ponownym odświeżaniem sterownika PLB. |
|PreferredLocationConstraintPriority | Int, wartość domyślna to 2| Dynamiczny|Określa priorytet ograniczenia preferowanej lokalizacji: 0: Twardy; 1: Miękki; 2: Optymalizacja; negatywne: Ignoruj |
|PreferowanaPrimaryDomainsConstraintPriority| Int, wartość domyślna to 1 | Dynamiczny| Określa priorytet preferowanego ograniczenia domeny podstawowej: 0: Hard; 1: Miękki; negatywne: Ignoruj |
|PreferUpgradedUDs|bool, domyślnie jest FALSE|Dynamiczny|Włącza i wyłącza logikę, która preferuje przenoszenie do już uaktualnionych identyfikatorów UD. Począwszy od SF 7.0, domyślna wartość tego parametru jest zmieniana z TRUE na FALSE.|
|PreventTransientOvercommit | Bool, wartość domyślna to false | Dynamiczny|Określa, czy PLB natychmiast liczyć na zasoby, które zostaną uwolnione przez zainicjowane ruchy. Domyślnie; PLB może inicjować wyprowadzanie i poruszać się w tym samym węźle, który może tworzyć przejściowe nadmierne przywiązywanie. Ustawienie tego parametru na true uniemożliwi wyłączenie tego rodzaju nadmiernych nakładów i defragmentacji na żądanie (aka placementWithMove). |
|ScaleoutCountConstraintPriority | Int, wartość domyślna to 0 |Dynamiczny| Określa priorytet ograniczenia liczby skalowania: 0: Twardy; 1: Miękki; negatywne: Ignoruj. |
|SubclusteringEnabled (SubclusteringEnabled)|Bool, domyślnie jest FALSE | Dynamiczny |Potwierdzanie podklugusy podczas obliczania odchylenia standardowego dla bilansowania |
|SubkluzydowanieReportingPolicy| Int, wartość domyślna to 1 |Dynamiczny|Określa, jak i czy subklastrujące raporty kondycji są wysyłane: 0: Nie zgłaszaj; 1: Ostrzeżenie; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | ciąg, domyślnie jest ""|Statyczny| Skojarzona nazwa metryki dla tej ograniczania przepustowości. |
|SwapPrimaryThrottlingEnabled | Bool, wartość domyślna to false|Dynamiczny| Określ, czy ograniczenie podstawowe wymiany jest włączone. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, wartość domyślna to 0 |Dynamiczny| Maksymalna liczba replik swap-primary dozwolonych globalnie. |
|TraceCRMReasons |Bool, wartość domyślna jest true |Dynamiczny|Określa, czy mają być śledzeni przyczyny ruchów wystawionych przez program CRM do kanału zdarzeń operacyjnych. |
|UaktualnienieDomainConstraintPriority | Int, wartość domyślna to 1| Dynamiczny|Określa priorytet ograniczenia domeny uaktualnienia: 0: Hard; 1: Miękki; negatywne: Ignoruj. |
|Raporty UseMoveCostReports | Bool, wartość domyślna to false | Dynamiczny|Nakazuje LB zignorować element kosztu funkcji oceniania; potencjalnie dużą liczbę ruchów w celu uzyskania lepszego wyważonego rozmieszczenia. |
|UżyjSeparateSecondaryLoad | Bool, wartość domyślna jest true | Dynamiczny|Ustawienie, które określa, czy oddzielne obciążenie powinno być używane dla replik pomocniczych. |
|UżyjSeparateSecondaryMoveCost | Bool, wartość domyślna to false | Dynamiczny|Ustawienie, które określa, czy oddzielny koszt przeniesienia powinien być używany dla replik pomocniczych. |
|Poprawność poprawnościzaceptujconstraint | Bool, wartość domyślna jest true |Dynamiczny| Określa, czy wyrażenie PlacementConstraint dla usługi jest sprawdzane podczas aktualizowaniem service's ServiceDescription. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, domyślnie jest PRAWDA |Dynamiczny|Określa, czy wyrażenie PlacementConstraint dla usługi jest oceniane pod kątem preferencji podstawowych w trybie failover. |
|Pełny Raport ZdrowiaJętnik | Int, wartość domyślna to 20 | Dynamiczny|Określa, ile razy replika musi pozostać nieumieszczona przed zgłoszeniem ostrzeżenia o kondycji (jeśli jest włączone pełne raportowanie kondycji). |
|NodeLoadsOperationalTracingEnabled | Bool, wartość domyślna jest true |Dynamiczny|Config, który umożliwia parametru Parametr Load operacyjne śledzenia strukturalnego w magazynie zdarzeń. |
|NodeLoadsOperationalTracingInterval | TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(20) | Dynamiczny|Określ czas w sekundach. Interwał, z którym można śledzić obciążenia węzłów do magazynu zdarzeń dla każdej domeny usługi. |

## <a name="reconfigurationagent"></a>RekonfiguracjaGent

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Czas w sekundach, wartość domyślna to 900 |Dynamiczny|Określ czas w sekundach. Czas trwania, na który system będzie czekać przed zakończeniem hostów usługi, które mają repliki, które utknęły w pobliżu podczas uaktualniania aplikacji.|
|FabricUpgradeMaxReplicaKrułaknokrąceodowanie | Czas w sekundach, wartość domyślna to 900 |Dynamiczny| Określ czas w sekundach. Czas trwania, na który system będzie czekać przed zakończeniem hostów usługi, które mają repliki, które utknęły w pobliżu podczas uaktualniania sieci szkieletowej. |
|WdziękuReplicaShutdownMaxDuration|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(120)|Dynamiczny|Określ czas w sekundach. Czas trwania, na który system będzie czekać przed zakończeniem hostów usługi, które mają repliki, które są zablokowane w pobliżu. Jeśli ta wartość jest ustawiona na 0, repliki nie zostaną poinstruowane, aby zamknąć.|
|NodeDeactivationMaxReplicaKrułaczaswalność | Czas w sekundach, wartość domyślna to 900 |Dynamiczny|Określ czas w sekundach. Czas trwania, na który system będzie czekać przed zakończeniem hostów usługi, które mają repliki, które utknęły w pobliżu podczas dezaktywacji węzła. |
|Okresowe ApiSlowTraceInterval | Czas w sekundach, domyślnie to 5 minut |Dynamiczny| Określ czas w sekundach. PeriodicApiSlowTraceInterval definiuje interwał, w którym powolne wywołania interfejsu API będą ponownie odnajdowane przez monitor interfejsu API. |
|ReplicaChangeRoleFailureRestartThreshold|int, domyślnie jest to 10|Dynamiczny| Liczba całkowita. Określ liczbę błędów interfejsu API podczas promocji podstawowej, po której zostaną zastosowane działania automatycznego ograniczania ryzyka (ponowne uruchomienie repliki). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, domyślnie jest 2147483647|Dynamiczny| Liczba całkowita. Określ liczbę błędów interfejsu API podczas promocji podstawowej, po której zostanie podniesiony raport o kondycji ostrzeżenia.|
|ServiceApiHealthDuration | Czas w sekundach, domyślnie to 30 minut |Dynamiczny| Określ czas w sekundach. ServiceApiHealthDuration określa, jak długo czekamy na interfejs API usługi do uruchomienia, zanim zgłosimy go w złej kondycji. |
|UsługaRekonfiguracjaPiHealthDuration | Czas w sekundach, wartość domyślna to 30 |Dynamiczny| Określ czas w sekundach. ServiceReconfigurationApiHealthDuration definiuje, jak długo czekamy na interfejs API usługi do uruchomienia, zanim zgłosimy złej kondycji. Dotyczy to wywołań interfejsu API, które mają wpływ na dostępność.|

## <a name="replication"></a>Replikacja
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval (W przypadku danych: BatchAcknowledgementInterval)|TimeSpan, domyślnie jest wspólny::TimeSpan::FromMilliseconds(15)|Statyczny|Określ czas w sekundach. Określa czas oczekiwania replikatora po otrzymaniu operacji przed wysłaniem potwierdzenia. Inne operacje odebrane w tym okresie będą miały swoje potwierdzenia wysyłane z powrotem w jednej > wiadomości zmniejszając ruch sieciowy, ale potencjalnie zmniejszając przepływność replikatora.|
|Rozmiar maksymalnej wielkości|uint, domyślnie jest 1024|Statyczny|Jest to maksymalna wartość definiuje rozmiar początkowy dla kolejki, która utrzymuje operacje replikacji. Należy pamiętać, że musi to być moc 2. Jeśli w czasie wykonywania kolejka rośnie do tej operacji rozmiaru zostanie ograniczona między replikatorami podstawowymi i pomocniczymi.|
|MaxPrimaryReplicationQueueMemorySize|uint, domyślnie jest to 0|Statyczny|Jest to maksymalna wartość kolejki replikacji podstawowej w bajtach.|
|Maksymalna liczba aplikacji MaxPrimaryReplicationQueueSize|uint, domyślnie jest 1024|Statyczny|Jest to maksymalna liczba operacji, które mogą istnieć w kolejce replikacji podstawowej. Należy pamiętać, że musi to być moc 2.|
|Rozmiar MaxReplicationMessageSize|uint, domyślnie jest 52428800|Statyczny|Maksymalny rozmiar operacji replikacji. Wartość domyślna to 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, domyślnie jest to 0|Statyczny|Jest to maksymalna wartość kolejki replikacji pomocniczej w bajtach.|
|MaxSecondaryReplicationQueueSize|uint, domyślnie jest 2048|Statyczny|Jest to maksymalna liczba operacji, które mogą istnieć w kolejce replikacji pomocniczej. Należy pamiętać, że musi to być moc 2.|
|QueueHealthMonitoringInterval (Monitorowanie kondycji kolejki)|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(30)|Statyczny|Określ czas w sekundach. Ta wartość określa okres używany przez replikatora do monitorowania zdarzeń kondycji ostrzeżenia/błędu w kolejkach operacji replikacji. Wartość "0" wyłącza monitorowanie kondycji |
|QueueHealthWarningAtUsagePercent|uint, domyślnie jest to 80|Statyczny|Ta wartość określa użycie kolejki replikacji (w procentach), po czym firma Firma Firma My zgłosimy ostrzeżenie o wysokim użyciu kolejki. Robimy to po przerwie prolongaty QueueHealthMonitoringInterval. Jeśli użycie kolejki spadnie poniżej tej wartości procentowej w interwale własności|
|ReplikatorAddress|ciąg, domyślnie jest "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu -'IP:Port' który jest używany przez replikatora sieci szkieletowej systemu Windows do ustanawiania połączeń z innymi replikami w celu wysyłania/odbierania operacji.|
|ReplicatorListenAddress|ciąg, domyślnie jest "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu -'IP:Port', który jest używany przez replikatora sieci szkieletowej systemu Windows do odbierania operacji z innych replik.|
|ReplikatorPublishAddress|ciąg, domyślnie jest "localhost:0"|Statyczny|Punkt końcowy w postaci ciągu -'IP:Port' który jest używany przez replikatora sieci szkieletowej systemu Windows do wysyłania operacji do innych replik.|
|Ponowne próbyInterwalne|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(5)|Statyczny|Określ czas w sekundach. Gdy operacja zostanie utracona lub odrzucona, ten czasomierz określa, jak często replikator ponowi próbę wysłania operacji.|

## <a name="resourcemonitorservice"></a>Usługa ResourceMonitorService
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|IsEnabled|bool, domyślnie jest FALSE |Statyczny|Określa, czy usługa jest włączona w klastrze, czy nie. |

## <a name="runas"></a>Runas

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Nazwa konta RunAsAccount |ciąg, domyślnie jest "" |Dynamiczny|Wskazuje nazwę konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType (Typ konta runAsaccount)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne dla każdej sekcji RunAs Prawidłowe wartości to "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword (Hasło RunAs)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje hasło do konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Nazwa konta RunAsAccount |ciąg, domyślnie jest "" |Dynamiczny|Wskazuje nazwę konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType (Typ konta runAsaccount)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne dla każdej sekcji RunAs Prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword (Hasło RunAs)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje hasło do konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Nazwa konta RunAsAccount |ciąg, domyślnie jest "" |Dynamiczny|Wskazuje nazwę konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType (Typ konta runAsaccount)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne dla każdej sekcji RunAs Prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword (Hasło RunAs)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje hasło do konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Nazwa konta RunAsAccount |ciąg, domyślnie jest "" |Dynamiczny|Wskazuje nazwę konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser" lub "ManagedServiceAccount". Prawidłowe wartości to "domena\użytkownik" lub "user@domain". |
|RunAsAccountType (Typ konta runAsaccount)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje typ konta RunAs. Jest to potrzebne dla każdej sekcji RunAs Prawidłowe wartości to "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword (Hasło RunAs)|ciąg, domyślnie jest "" |Dynamiczny|Wskazuje hasło do konta RunAs. Jest to potrzebne tylko dla typu konta "DomainUser". |

## <a name="security"></a>Zabezpieczenia
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania**| **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|ciąg, domyślnie jest ""|Statyczny|AAD Cert Endpoint Format, domyślna platforma Azure Commercial, określona dla\/środowiska innego{0}niż domyślne, takiego jak Azure Government "https: /login.microsoftonline.us/ /federationmetadata/2007-06/federationmetadata.xml" |
|AADClientAplikacja|ciąg, domyślnie jest ""|Statyczny|Nazwa lub identyfikator aplikacji klienta macierzystego reprezentujący klientów sieci szkieletowej |
|AADClusterApplication (AADClusterApplication)|ciąg, domyślnie jest ""|Statyczny|Nazwa lub identyfikator aplikacji interfejsu API sieci Web reprezentujący klaster |
|Punkt AADLoginEndpoint|ciąg, domyślnie jest ""|Statyczny|Punkt końcowy logowania AAD, domyślna usługa Azure Commercial, określona dla\/środowiska innego niż domyślne, takiego jak Azure Government "https: /login.microsoftonline.us" |
|Identyfikator AADTenantId|ciąg, domyślnie jest ""|Statyczny|Identyfikator dzierżawy (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, domyślnie jest FALSE|Dynamiczny|Flaga wskazująca, czy zaakceptować wygasłe certyfikaty klastra zadeklarowane przez odcisk palca Dotyczy tylko certyfikatów klastra; tak, aby utrzymać klaster przy życiu. |
|AdminClientCertThumbprints|ciąg, domyślnie jest ""|Dynamiczny|Odciski palców certyfikatów używanych przez klientów w roli administratora. Jest to lista nazw oddzielonych przecinkami. |
|AADTokenEndpointFormat|ciąg, domyślnie jest ""|Statyczny|Punkt końcowy tokenu AAD, domyślna usługa Azure Commercial, określona\/dla środowiska innego{0}niż domyślne, takiego jak Azure Government "https: /login.microsoftonline.us/ " |
|AdminClientClaims|ciąg, domyślnie jest ""|Dynamiczny|Wszystkie możliwe roszczenia oczekiwane od klientów administracyjnych; w tym samym formacie co ClientClaims; ta lista wewnętrznie zostanie dodana do ClientClaims; więc nie ma potrzeby, aby również dodać te same wpisy do ClientClaims. |
|Identyfikowalności AdminClient|ciąg, domyślnie jest ""|Dynamiczny|Tożsamości systemu Windows klientów sieci szkieletowej w roli administratora; służy do autoryzowania uprzywilejowanych operacji sieci szkieletowej. Jest to lista oddzielona przecinkami; każdy wpis jest nazwą konta domeny lub nazwą grupy. Dla wygody; konto, na które działa plik fabric.exe, jest automatycznie przypisywane roli administratora; tak jest grupa ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|ciąg, domyślnie jest /home/sfuser/sfusercerts |Statyczny|Folder, w którym znajdują się certyfikaty i klucze prywatne AppRunAsAccountGroup X509 |
|CertyfikatExpirySafetyMargin|TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(43200)|Statyczny|Określ czas w sekundach. Margines bezpieczeństwa wygaśnięcia certyfikatu; Stan raportu kondycji certyfikatu zmienia się z OK na Ostrzeżenie, gdy wygaśnięcie jest bliżej niż to. Wartość domyślna to 30 dni. |
|CertificateHealthReportingInterval (Zgłaszanie raportów)|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(3600 * 8)|Statyczny|Określ czas w sekundach. Określ interwał raportowania kondycji certyfikatu; domyślnie 8 godzin; ustawienie na 0 wyłącza raportowanie kondycji certyfikatu |
|Wydruki KlientaCertThumbprints|ciąg, domyślnie jest ""|Dynamiczny|Odciski palców certyfikatów używanych przez klientów do rozmowy z klastrem; klaster używa tego autoryzowania połączenia przychodzącego. Jest to lista nazw oddzielonych przecinkami. |
|KlientClaimAuthEnabled|bool, domyślnie jest FALSE|Statyczny|Wskazuje, czy uwierzytelnianie oparte na oświadczeniu jest włączone na klientach; ustawienie tego prawdziwego niejawnie ustawia ClientRoleEnabled. |
|Kliki klientów|ciąg, domyślnie jest ""|Dynamiczny|Wszystkie możliwe oświadczenia oczekiwane od klientów do łączenia się z bramą. Jest to lista "OR": \| \| ClaimsEntry \| \| ClaimsEntry ClaimsEntryEntry ... każdy ClaimsEntry jest "I" lista: ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|Tożsamość klienta|ciąg, domyślnie jest ""|Dynamiczny|Tożsamości systemu Windows FabricClient; brama nazewnictwa używa tego do autoryzowania połączeń przychodzących. Jest to lista oddzielona przecinkami; każdy wpis jest nazwą konta domeny lub nazwą grupy. Dla wygody; konto, na które działa plik fabric.exe, jest automatycznie dozwolone; więc są grupy ServiceFabricAllowedUsers i ServiceFabricAdministrators. |
|KlientRoleEnabled|bool, domyślnie jest FALSE|Statyczny|Wskazuje, czy rola klienta jest włączona; gdy jest ustawiona na true; klientom są przypisywane role na podstawie ich tożsamości. Dla V2; włączenie oznacza, że klient nie w AdminClientCommonNames/AdminClientIdentities może wykonywać tylko operacje tylko do odczytu. |
|ClusterCertThumbprints (Odwdzięczanie klastroweCertThumbprints)|ciąg, domyślnie jest ""|Dynamiczny|Odciski palców certyfikatów dozwolonych do przyłączenia się do klastra; listę nazw rozdzielonych przecinkami. |
|Typ klastra z klasterem|ciąg, domyślnie jest "Brak"|Niedozwolone|Wskazuje typ poświadczeń zabezpieczeń, które mają być używane w celu zabezpieczenia klastra. Prawidłowe wartości to "Brak/X509/Windows" |
|Nieidentyfikowalności klastra|ciąg, domyślnie jest ""|Dynamiczny|Tożsamości systemu Windows węzłów klastra; używane do autoryzacji członkostwa w klastrze. Jest to lista oddzielona przecinkami; każdy wpis jest nazwą konta domeny lub nazwą grupy |
|ClusterSpn (KlasterSpn)|ciąg, domyślnie jest ""|Niedozwolone|Nazwa główna usługi klastra; gdy tkanina działa jako użytkownik pojedynczej domeny (konto użytkownika gMSA/domeny). Jest to SPN słuchaczy dzierżawy i słuchaczy w fabric.exe: słuchaczy federacji; wewnętrznych detektorów replikacji; odbiornika usług w czasie wykonywania i odbiornika bramy nazewnictwa. Powinno to pozostać puste, gdy tkanina działa jako konta maszyn; w takim przypadku łączenie bocznego odbiornika obliczeniowego SPN z adresu transportu odbiornika. |
|Żużel w crlchecking|uint, domyślnie jest 0x40000000|Dynamiczny|Domyślna flaga sprawdzania poprawności łańcucha certyfikatów; mogą zostać zastąpione flagą specyficzną dla komponentu; np. Federacja/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Ustawienie na 0 wyłącza sprawdzanie CRL Pełna lista obsługiwanych wartości jest dokumentowana przez dwFlags certGetCificateChain:https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|Okres crldisable|TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(15)|Dynamiczny|Określ czas w sekundach. Jak długo sprawdzanie crl jest wyłączone dla danego certyfikatu po napotkaniu błędu w trybie offline; jeśli błąd programu CRL w trybie offline może zostać zignorowany. |
|CrlOfflineHealthReportTtl (Raport o zdrowiu)|TimeSpan, domyślnie jest wspólny::TimeSpan::FromMinutes(1440)|Dynamiczny|Określ czas w sekundach. |
|DisableFirewallRuleForDomainProfile| bool, domyślnie jest PRAWDA |Statyczny| Wskazuje, czy nie należy włączać reguły zapory dla profilu domeny |
|DisableFirewallRuleForPrivateProfile| bool, domyślnie jest PRAWDA |Statyczny| Wskazuje, czy nie należy włączać reguły zapory dla profilu prywatnego | 
|DisableFirewallRuleForPublicProfile| bool, domyślnie jest PRAWDA | Statyczny|Wskazuje, czy nie należy włączać reguły zapory dla profilu publicznego |
| EnforceLinuxMinTlsVersion (Wymuszanie Wersji Niewola) | bool, domyślnie jest FALSE | Statyczny | Jeśli ustawiona na true; obsługiwane są tylko protokołu TLS w wersji 1.2+.  Jeśli false; obsługuje wcześniejsze wersje TLS. Dotyczy tylko Linuksa |
| EnforcePrevalidationOnSecurityChanges | bool, domyślnie jest FALSE| Dynamiczny | Flaga kontrolowanie zachowania uaktualnienia klastra po wykryciu zmian jego ustawień zabezpieczeń. Jeśli ustawiona jest wartość "true", uaktualnienie klastra będzie próbowało upewnić się, że co najmniej jeden z certyfikatów pasujących do dowolnej reguł prezentacji może przekazać odpowiednią regułę sprawdzania poprawności. Wstępna weryfikacja jest wykonywana przed zastosowaniem nowych ustawień do dowolnego węzła, ale jest uruchamiana tylko w węźle obsługującym podstawową replikę usługi Menedżera klastrów w momencie inicjowania uaktualnienia. Wartość domyślna jest obecnie ustawiona na "false"; począwszy od wersji 7.1, ustawienie zostanie ustawione na "true" dla nowych klastrów sieci szkieletowej usług Azure.|
|TkaninaHostSpn| ciąg, domyślnie jest "" |Statyczny| Nazwa główna usługi FabricHost; gdy tkanina działa jako użytkownik pojedynczej domeny (gMSA/konto użytkownika domeny), a FabricHost działa pod kontem komputera. Jest to SPN ipc odbiornika FabricHost; które domyślnie powinny być puste, ponieważ FabricHost działa pod kontem komputera |
|IgnoreCrlOfflineError|bool, domyślnie jest FALSE|Dynamiczny|Określa, czy zignorować błąd programu CRL w trybie offline, gdy po stronie serwera weryfikuje przychodzące certyfikaty klienta |
|IgnoreSvrCrlOfflineError|bool, domyślnie jest PRAWDA|Dynamiczny|Czy zignorować błąd programu CRL w trybie offline, gdy po stronie klienta weryfikuje przychodzące certyfikaty serwera; domyślnie true. Ataki z odwołanymi certyfikatami serwera wymagają naruszenia systemu DNS; trudniejsze niż w odniesieniu do odwołanych certyfikatów klienta. |
|ServerAuthCredentialType|ciąg, domyślnie jest "Brak"|Statyczny|Wskazuje typ poświadczeń zabezpieczeń, które mają być używane w celu zabezpieczenia komunikacji między FabricClient i klastrem. Prawidłowe wartości to "Brak/X509/Windows" |
|Odbitki serwera|ciąg, domyślnie jest ""|Dynamiczny|Odciski palców certyfikatów serwera używanych przez klaster do rozmów z klientami; klienci używają tego do uwierzytelniania klastra. Jest to lista nazw oddzielonych przecinkami. |
|UstawieniaX509StoreName| ciąg, domyślnie jest "MY"| Dynamiczny|Magazyn certyfikatów X509 używany przez fabrykę do ochrony konfiguracji |
|UseClusterCertForIpcServerTlsBezpieczeństwo|bool, domyślnie jest FALSE|Statyczny|Określa, czy do zabezpieczania jednostki transportu TLS serwera IPC Server ma być używany certyfikat klastra |
|X509Folder|ciąg, domyślnie jest /var/lib/waagent|Statyczny|Folder, w którym znajdują się certyfikaty X509 i klucze prywatne |
|TLS1_2_CipherList| ciąg| Statyczny|Jeśli jest ustawiona na niepusty ciąg; zastępuje obsługiwana listę szyfrowania dla protokołu TLS1.2 i poniżej. Zobacz dokumentację "otwiera szyfry" w celu pobrania obsługiwanej listy szyfrowania i format listy Przykład silnej listy szyfrów dla TLS1.2: "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128-CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Dotyczy tylko Linuksa. |

## <a name="securityadminclientx509names"></a>Zabezpieczenia/AdminClientX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, domyślnie jest Brak|Dynamiczny|Jest to lista pary "Nazwa" i "Wartość". Każda "Nazwa" ma nazwę pospolitą podmiotu lub DnsName certyfikatów X509 autoryzowanych do operacji klienta administracyjnego. Dla danej "Nazwa", "Wartość" jest przecinek oddzielna lista odcisków palców certyfikatu dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów klienta administratora musi znajdować się na liście. |

## <a name="securityclientaccess"></a>Bezpieczeństwo/KlientAccess

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|AktywujNode |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla aktywacji węzła. |
|Przycisk CancelTestCommand |ciąg, domyślnie jest "Admin" |Dynamiczny| Anuluje określony TestCommand - jeśli jest w locie. |
|Kontrola pakietów kodu |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do ponownego uruchamiania pakietów kodu. |
|Tworzenie aplikacji |ciąg, domyślnie jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do tworzenia aplikacji. |
|Tworzenie rozkładu międzyfirmami|ciąg, domyślnie jest "Admin"| Dynamiczny|Tworzy wdrożenie redagowania opisane przez redagowanie plików |
|CreateGatewayResource|ciąg, domyślnie jest "Admin"| Dynamiczny|Tworzenie zasobu bramy |
|Utwórz nazwy |ciąg, domyślnie jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń dla tworzenia identyfikatora URI nazewnictwa. |
|Utwórz sieć|ciąg, domyślnie jest "Admin" |Dynamiczny|Tworzy sieć kontenerów |
|Createservice |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do tworzenia usług. |
|CreateServiceFromTemplate |ciąg, domyślnie jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do tworzenia usługi z szablonu. |
|Utwórzvolume|ciąg, domyślnie jest "Admin"|Dynamiczny|Tworzy wolumin |
|Dezaktywacja: |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do dezaktywacji węzła. |
|Dezaktywacja NiedesBatch |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do dezaktywacji wielu węzłów. |
|Usuń |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracje zabezpieczeń dla operacji usuwania klienta magazynu obrazów. |
|Usuń wniosek |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń usuwania aplikacji. |
|Usuń rozkładu|ciąg, domyślnie jest "Admin"| Dynamiczny|Usuwa wdrożenie redagowania |
|DeleteGatewayŹródło|ciąg, domyślnie jest "Admin"| Dynamiczny|Usuwa zasób bramy |
|Usuńname |ciąg, domyślnie jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń usuwania identyfikatora URI. |
|Usuń sieci|ciąg, domyślnie jest "Admin" |Dynamiczny|Usuwa sieć kontenerów |
|Usługa usuwania |ciąg, domyślnie jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń do usuwania usługi. |
|Usuńvolume|ciąg, domyślnie jest "Admin"|Dynamiczny|Usuwa woluminu.| 
|Właściwości wyliczenia |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Konfiguracja zabezpieczeń wyliczenia właściwości Nazewnictwo. |
|Nazwy wyliczanychSubnames |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń wyliczenia identyfikatora URI. |
|PlikContent (Zatłok plik) |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń transferu plików klienta magazynu obrazów (zewnętrzna do klastra). |
|Filedownload |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla inicjacji pobierania plików klienta magazynu obrazów (zewnętrzna dla klastra). |
|WykończenieInfrastrukturaZadekk |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do wykańczania zadań infrastruktury. |
|Raport GetChaos | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Pobiera stan Chaos w danym przedziale czasu. |
|Konfiguracja GetCluster | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Indukuje GetClusterConfiguration na partycji. |
|GetClusterConfigurationUpgradeStatus | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Indukuje GetClusterConfigurationUpgradeStatus na partycji. |
|Status GetFabricUpgradeStatus |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla stanu uaktualniania klastra sondowania. |
|Getfoldersize |ciąg, domyślnie jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń dla rozmiaru folderu o uzyskiwaniu usługi FileStoreService |
|Status GetNodeDeactivation |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do sprawdzania stanu dezaktywacji. |
|GetNodeTransitionProgress | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń w celu uzyskania postępu w poleceniu przejścia węzła. |
|GetPartitionDataLossProgress | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Pobiera postęp wywołania interfejsu api utraty danych wywołania. |
|GetPartitionQuorumLossProgress | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Pobiera postęp wywołania interfejsu api utraty kworum. |
|GetPartitionRestartProgress | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Pobiera postęp wywołania interfejsu api partycji ponownego uruchomienia. |
|GetSecrets ( GetSecrets )|ciąg, domyślnie jest "Admin"|Dynamiczny|Uzyskaj tajne wartości |
|Opis usługi GetService |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla powiadomień o usługach długich ankiet i odczytywania opisów usług. |
|Lokalizacja GetStaging |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla pobierania lokalizacji tymczasowej klienta magazynu obrazów. |
|Lokalizacja GetStore |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla pobierania lokalizacji magazynu obrazów klienta. |
|GetUpgradeOrchestrationServiceState (Stan obsługi usługi GetUpgradeOrchestration|ciąg, domyślnie jest "Admin"| Dynamiczny|Indukuje GetUpgradeOrchestrationServiceState na partycji |
|GetUpgradesPendingZatwierdzenie |ciąg, domyślnie jest "Admin" |Dynamiczny| Indukuje GetUpgradesPendingApproval na partycji. |
|Status GetUpgrade |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla stanu uaktualniania aplikacji sondowania. |
|Lista wewnętrzna |ciąg, domyślnie jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń dla operacji listy plików klienta magazynu obrazów (wewnętrzna). |
|InvokeContainerApi|ciąg,domyślnie jest "Administrator"|Dynamiczny|Wywoływanie interfejsu API kontenera |
|InvokeInfrastructureCommand |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla poleceń zarządzania zadaniami infrastruktury. |
|InvokeInfrastructureQuery (Rozerwienie infrastruktury) |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Konfiguracja zabezpieczeń do wykonywania zapytań o zadania infrastruktury. |
|List |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Konfiguracja zabezpieczeń dla operacji listy plików klienta magazynu obrazów. |
|MoveNextFabricUpgradeDomain |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do wznawiania uaktualnień klastra za pomocą jawnej domeny uaktualnienia. |
|MoveNextUpgradeDomain |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do wznawiania uaktualnień aplikacji za pomocą jawnej domeny uaktualnienia. |
|Sterowanie MoveReplicaControl |ciąg, domyślnie jest "Admin" | Dynamiczny|Przenieś replikę. |
|NameExists (NazwaExists) |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Konfiguracja zabezpieczeń dla sprawdzania istnienia identyfikatora URI nazewnictwa. |
|Kontrola węzła |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do uruchamiania; zatrzymanie; i ponowne uruchomienie węzłów. |
|WęzełStateRerused |ciąg, domyślnie jest "Admin" |Dynamiczny| Usunięto konfigurację zabezpieczeń dla stanu węzła raportowania. |
|Ping |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla pingów klientów. |
|PredeployPackageToNode |ciąg, domyślnie jest "Admin" |Dynamiczny| Interfejs api przedmieszczenia. |
|Usługa PrefixResolve |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla rozwiązywania prefiksów usługi opartych na skargach. |
|WłaściwośćReadBatch |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla operacji odczytu właściwości nazewnictwa. |
|PropertyWriteBatch (WłaściwośćWriteBatch) |ciąg, domyślnie jest "Admin" |Dynamiczny|Konfiguracje zabezpieczeń dla operacji zapisu właściwości nazewnictwa. |
|Rodzaj aprowizacji |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla inicjowania obsługi administracyjnej typu aplikacji. |
|ProvisionFabric (Fabric) |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla obsługi administracyjnej msi i/lub manifestu klastra. |
|Zapytanie |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla zapytań. |
|Odzyskajpartition |ciąg, domyślnie jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do odzyskiwania partycji. |
|Odzyskiwanieparticji |ciąg, domyślnie jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do odzyskiwania partycji. |
|Odzyskiwanie partycji usług |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do odzyskiwania partycji usługi. |
|Odzyskiwanie partycji systemowych |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do odzyskiwania partycji usługi systemu. |
|Usuń Dezaaktywacja |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do przywracania dezaktywacji w wielu węzłach. |
|ReportFabricUpgradeHealth |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń wznawiania uaktualnień klastra z bieżącym postępem uaktualniania. |
|RaportFault |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla zgłaszania błędów. |
|ReportHealth (Zdrowie) |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do raportowania kondycji. |
|ReportUpgradeHealth |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do wznawiania uaktualnień aplikacji z bieżącym postępem uaktualniania. |
|ResetPartitionLoad |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla resetowania obciążenia jednostki pracy awaryjnej. |
|Właściciel resolvename |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Konfiguracja zabezpieczeń dla rozpoznawania właściciela identyfikatora URI nazewnictwa. |
|RozwiązaniePartition |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" | Dynamiczny|Konfiguracja zabezpieczeń do rozwiązywania usług systemowych. |
|Usługa ResolveService |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń do rozwiązywania problemów z usługami opartymi na skargach. |
|Usługa ResolveSystemService|ciąg, domyślnie\|\|jest "Użytkownik administracyjny"|Dynamiczny| Konfiguracja zabezpieczeń do rozwiązywania usług systemowych |
|Wycofywanie aplikacji Uaktualnienie |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do wycofywania uaktualnień aplikacji. |
|RollbackFabricUpgrade |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do wycofywania uaktualnień klastra. |
|UsługiNotyfikowania |ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla powiadomień o usługach opartych na zdarzeniach. |
|SetUpgradeOrchestrationServiceState (Stan usług y)|ciąg, domyślnie jest "Admin"| Dynamiczny|Indukuje SetUpgradeOrchestrationServiceState na partycji |
|StartApprovedUpgrades |ciąg, domyślnie jest "Admin" |Dynamiczny| Indukuje StartApprovedUpgrades na partycji. |
|StartChaos (StartChaos) |ciąg, domyślnie jest "Admin" |Dynamiczny| Uruchamia Chaos - jeśli nie jest jeszcze uruchomiony. |
|StartClusterConfigurationUpgrade |ciąg, domyślnie jest "Admin" |Dynamiczny| Indukuje StartClusterConfigurationUpgrade na partycji. |
|StartInfrastructureTask |ciąg, domyślnie jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń do uruchamiania zadań infrastruktury. |
|StartNodeTransition (StartNodeTransition) |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń do uruchamiania przejścia węzła. |
|Dane danych startpartition |ciąg, domyślnie jest "Admin" |Dynamiczny| Powoduje utratę danych na partycji. |
|StartPartitionQuorumLoss |ciąg, domyślnie jest "Admin" |Dynamiczny| Wywołuje utratę kworum na partycji. |
|StartPartitionRestart (Początek) |ciąg, domyślnie jest "Admin" |Dynamiczny| Jednocześnie uruchamia niektóre lub wszystkie repliki partycji. |
|StopChaos ( StopChaos ) |ciąg, domyślnie jest "Admin" |Dynamiczny| Zatrzymuje Chaos - jeśli został uruchomiony. |
|PrzełączanieVerboseServicePlacementHealthReporting | ciąg, domyślnie\|\|jest "Użytkownik administracyjny" |Dynamiczny| Konfiguracja zabezpieczeń dla Toggling Verbose ServicePlacement HealthReporting. |
|UnprovisionApplicationType |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla nieobjętego aprowingu typu aplikacji. |
|UnprovisionFabric |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń dla msi i/lub manifestu klastra nieobjętego obsługi administracyjnej. |
|NiewiarygodnaKontrola transportu |ciąg, domyślnie jest "Admin" |Dynamiczny| Zawodne transport do dodawania i usuwania zachowań. |
|UpdateService |ciąg, domyślnie jest "Admin" |Dynamiczny|Konfiguracja zabezpieczeń dla aktualizacji usługi. |
|UaktualnienieApplication |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń uruchamiania lub przerywania uaktualnień aplikacji. |
|UpgradeComposeDeployment|ciąg, domyślnie jest "Admin"| Dynamiczny|Uaktualnia wdrożenie redagowania |
|UpgradeFabric (UpgradeFabric) |ciąg, domyślnie jest "Admin" |Dynamiczny| Konfiguracja zabezpieczeń uruchamiania uaktualnień klastra. |
|Upload |ciąg, domyślnie jest "Admin" | Dynamiczny|Konfiguracja zabezpieczeń dla operacji przekazywania klienta magazynu obrazów. |

## <a name="securityclientcertificateissuerstores"></a>Zabezpieczenia/ClientCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|IssuerStoreKeyValueMap, domyślnie jest Brak |Dynamiczny|X509 magazyny certyfikatów wystawcy dla certyfikatów klientów; Nazwa = clientIssuerCN; Wartość = oddzielona przecinkami lista sklepów |

## <a name="securityclientx509names"></a>Zabezpieczenia/ClientX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, domyślnie jest Brak|Dynamiczny|Jest to lista pary "Nazwa" i "Wartość". Każda "Nazwa" ma nazwę pospolitą podmiotu lub DnsName certyfikatów X509 autoryzowanych do operacji klienta. Dla danej "Nazwa", "Wartość" jest przecinek oddzielna lista odcisków palców certyfikatu dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów klienta musi znajdować się na liście.|

## <a name="securityclustercertificateissuerstores"></a>Zabezpieczenia/ClusterCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|IssuerStoreKeyValueMap, domyślnie jest Brak |Dynamiczny|X509 magazyny certyfikatów wystawcy dla certyfikatów klastra; Nazwa = clusterIssuerCN; Wartość = oddzielona przecinkami lista sklepów |

## <a name="securityclusterx509names"></a>Zabezpieczenia/ClusterX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, domyślnie jest Brak|Dynamiczny|Jest to lista pary "Nazwa" i "Wartość". Każda "Nazwa" ma nazwę pospolitą podmiotu lub DnsName certyfikatów X509 autoryzowanych do operacji klastra. Dla danej "Name","Value" jest przecinek oddzielna lista odcisków palców certyfikatu dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów klastra musi znajdować się na liście.|

## <a name="securityservercertificateissuerstores"></a>Zabezpieczenia/ServerCertificateIssuerStores

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|IssuerStoreKeyValueMap, domyślnie jest Brak |Dynamiczny|X509 magazyny certyfikatów wystawcy dla certyfikatów serwera; Nazwa = serverIssuerCN; Wartość = oddzielona przecinkami lista sklepów |

## <a name="securityserverx509names"></a>Zabezpieczenia/SerwerX509Names

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup|X509NameMap, domyślnie jest Brak|Dynamiczny|Jest to lista pary "Nazwa" i "Wartość". Każda "Nazwa" ma nazwę pospolitą podmiotu lub DnsName certyfikatów X509 autoryzowanych do operacji serwera. Dla danej "Nazwa", "Wartość" jest przecinek oddzielna lista odcisków palców certyfikatu dla przypinania wystawcy, jeśli nie jest pusty, bezpośredni wystawca certyfikatów serwera musi znajdować się na liście.|

## <a name="setup"></a>Konfigurowanie

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Nazwa sieci kontenerowej|ciąg, domyślnie jest ""| Statyczny |Nazwa sieciowa używana podczas konfigurowania sieci kontenerów.|
|ContainerNetworkSetup (Zestaw kontenerów)|bool, domyślnie jest FALSE (Linux) i domyślnie jest TRUE (Windows)| Statyczny |Czy skonfigurować sieć kontenerów.|
|TkaninaDataRoot |Ciąg | Niedozwolone |Katalog główny danych sieci szkieletowej usługi. Domyślnie dla platformy Azure jest d:\svcfab |
|Materiał FabricLogRoot |Ciąg | Niedozwolone |Katalog główny dziennika sieci szkieletowej usług. W tym miejscu są umieszczane dzienniki i ślady SF. |
|NodesToBeRemoved|ciąg, domyślnie jest ""| Dynamiczny |Węzły, które powinny zostać usunięte w ramach uaktualnienia konfiguracji. (Tylko dla wdrożeń autonomicznych)|
|Nazwa konta ServiceRunAsAccount |Ciąg | Niedozwolone |Nazwa konta, pod którym ma być uruchamiana usługa hosta sieci szkieletowej. |
|SkipContainerNetworkResetOnReboot|bool, domyślnie jest FALSE|Notallowed|Czy po ponownym uruchomieniu sieci kontenerów należy pominąć resetowanie sieci kontenerów.|
|SkipFirewallKonfiguracja |Bool, wartość domyślna to false | Niedozwolone |Określa, czy ustawienia zapory muszą być ustawiane przez system, czy nie. Dotyczy to tylko sytuacji, gdy używasz zapory systemu Windows. Jeśli używasz zapór innych firm, musisz otworzyć porty, aby system i aplikacje |

## <a name="tokenvalidationservice"></a>Usługa TokenValidationService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Dostawcy |ciąg, domyślnie jest "DSTS" |Statyczny|Przecinek oddzielona lista dostawców sprawdzania poprawności tokenu, aby włączyć (prawidłowe dostawcy są: DSTS; AAD). Obecnie tylko jeden dostawca może być włączony w dowolnym momencie. |

## <a name="traceetw"></a>Śledź/Etw

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Poziom |Int, wartość domyślna to 4 | Dynamiczny |Poziom etw śledzenia może przyjmować wartości 1, 2, 3, 4. Aby być obsługiwanym, należy zachować poziom śledzenia na poziomie 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval (W przypadku danych: BatchAcknowledgementInterval) | Czas w sekundach, wartość domyślna to 0.015 | Statyczny | Określ czas w sekundach. Określa czas oczekiwania replikatora po otrzymaniu operacji przed wysłaniem potwierdzenia. Inne operacje odebrane w tym okresie będą miały swoje potwierdzenia wysyłane z powrotem w jednej > wiadomości zmniejszając ruch sieciowy, ale potencjalnie zmniejszając przepływność replikatora. |
|Rozmiar maksymalnej wielkości |Uint, wartość domyślna to 16384 | Statyczny |Jest to maksymalna wartość definiuje rozmiar początkowy dla kolejki, która utrzymuje operacje replikacji. Należy pamiętać, że musi to być moc 2. Jeśli w czasie wykonywania kolejka rośnie do tej operacji rozmiaru zostanie ograniczona między replikatorami podstawowymi i pomocniczymi. |
|MaxPrimaryReplicationQueueMemorySize |Uint, wartość domyślna to 0 | Statyczny |Jest to maksymalna wartość kolejki replikacji podstawowej w bajtach. |
|Maksymalna liczba aplikacji MaxPrimaryReplicationQueueSize |Uint, domyślnie jest 8192 | Statyczny |Jest to maksymalna liczba operacji, które mogą istnieć w kolejce replikacji podstawowej. Należy pamiętać, że musi to być moc 2. |
|Rozmiar MaxReplicationMessageSize |Uint, wartość domyślna to 52428800 | Statyczny | Maksymalny rozmiar operacji replikacji. Wartość domyślna to 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, wartość domyślna to 0 | Statyczny |Jest to maksymalna wartość kolejki replikacji pomocniczej w bajtach. |
|MaxSecondaryReplicationQueueSize |Uint, wartość domyślna to 16384 | Statyczny |Jest to maksymalna liczba operacji, które mogą istnieć w kolejce replikacji pomocniczej. Należy pamiętać, że musi to być moc 2. |
|ReplikatorAddress |ciąg, domyślnie jest "localhost:0" | Statyczny | Punkt końcowy w postaci ciągu -'IP:Port' który jest używany przez replikatora sieci szkieletowej systemu Windows do ustanawiania połączeń z innymi replikami w celu wysyłania/odbierania operacji. |

## <a name="transport"></a>Transport
| **Parametr** | **Dozwolone wartości** |**Zasady uaktualniania** |**Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(60)|Statyczny|Określ czas w sekundach. Limit czasu na konfigurację połączenia zarówno po stronie przychodzącej, jak i akceptującej (w tym negocjacji zabezpieczeń w trybie bezpiecznym) |
|FrameHeaderErrorCheckingEnabled|bool, domyślnie jest PRAWDA|Statyczny|Domyślne ustawienie sprawdzania błędów w nagłówku ramki w trybie niezabezpieczonym; zastępuje to ustawienie komponentu. |
|MessageErrorCheckingEnabled|bool, domyślnie jest FALSE|Statyczny|Domyślne ustawienie sprawdzania błędów w nagłówku i treści wiadomości w trybie niezabezpieczonym; zastępuje to ustawienie komponentu. |
|ResolveOption|ciąg, domyślnie jest "nieokreślony"|Statyczny|Określa sposób rozwiązania problemu fqdn.  Prawidłowe wartości to "unspecified/ipv4/ipv6". |
|Limit czasu wysyłania|TimeSpan, domyślnie jest wspólny::TimeSpan::FromSeconds(300)|Dynamiczny|Określ czas w sekundach. Wyślij limit czasu w celu wykrycia zablokowanego połączenia. Raporty o błędach protokołu TCP nie są wiarygodne w niektórych środowiskach. Może to wymagać dostosowania do dostępnej przepustowości sieci\*i rozmiaru danych\/\*wychodzących ( MaxMessageSize SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>Usługa upgradeOrchestrationService

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Automatyczna funkcja aktualizacji | Bool, wartość domyślna jest true |Statyczny| Automatyczne sondowanie i uaktualnianie akcji na podstawie pliku stanu celu. |
|AutoupgradeInstallEnabled|Bool, domyślnie jest FALSE|Statyczny|Automatyczne sondowanie, inicjowanie obsługi administracyjnej i instalowanie akcji uaktualniania kodu na podstawie pliku stanu celu.|
|GoalStateExpirationReminderInDays|int, domyślnie jest to 30|Statyczny|Ustawia liczbę pozostałych dni, po których powinno być wyświetlane przypomnienie o stanie celu.|
|Rozmiar MinReplicaSetSize |Int, wartość domyślna to 0 |Statyczny |MinReplicaSetSize dla UpgradeOrchestrationService.|
|Miejsca docelowe | ciąg, domyślnie jest "" |Statyczny| Usługi PlacementConstraints for UpgradeOrchestrationService. |
|KworumLossWaitDuration | Czas w sekundach, domyślnie jest MaxValue |Statyczny| Określ czas w sekundach. Usługa QuorumLossWaitDuration for UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Czas w sekundach, domyślnie to 60 minut|Statyczny| Określ czas w sekundach. Usługa ReplicaRestartWaitDuration for UpgradeOrchestrationService. |
|StandByReplicaOdwekczenie | Czas w sekundach, domyślnie 60*24*7 minut |Statyczny| Określ czas w sekundach. Usługa StandByReplicaKeepDuration dla upgradeorchestrationService. |
|Rozmiar zestawu TargetReplicaSetSize |Int, wartość domyślna to 0 |Statyczny |TargetReplicaSetSize dla UpgradeOrchestrationService. |
|Wymagana aktualizacjaApproval | Bool, wartość domyślna to false | Statyczny|Ustawienie uaktualnienia kodu wymaga zatwierdzenia przez administratora przed kontynuowaniem. |

## <a name="upgradeservice"></a>Usługa uaktualnienia

| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Baza Zrl | ciąg, domyślnie jest "" |Statyczny|BaseUrl dla UpgradeService. |
|Ład klastra | ciąg, domyślnie jest "" |Statyczny|ClusterId dla upgradeservice. |
|Typ koordynatora | ciąg, domyślnie jest "WUTest"|Niedozwolone|Typ koordynatora usługi uaktualnienia. |
|Rozmiar MinReplicaSetSize | Int, wartość domyślna to 2 |Niedozwolone| Rozmiar MinReplicaSetSize dla usługi uaktualnienia. |
|TylkoBaseUpgrade | Bool, wartość domyślna to false |Dynamiczny|TylkoBaseUpgrade dla UpgradeService. |
|Miejsca docelowe |ciąg, domyślnie jest "" |Niedozwolone|Usługa PlacementConstraints for Upgrade. |
|PollIntervalInSekunds|Timespan, domyślnie jest wspólny::TimeSpan::FromSeconds(60) |Dynamiczny|Określ czas w sekundach. Interwał między upgradeservice sondy dla operacji zarządzania ARM. |
|Rozmiar zestawu TargetReplicaSetSize | Int, wartość domyślna to 3 |Niedozwolone| TargetReplicaSetSize dla upgradeservice. |
|TestCabFolder | ciąg, domyślnie jest "" |Statyczny| TestCabFolder dla UpgradeService. |
|X509findtype | ciąg, domyślnie jest ""|Dynamiczny| X509FindType dla UpgradeService. |
|X509Znajduje się | ciąg, domyślnie jest "" |Dynamiczny| X509FindValue dla UpgradeService. |
|X509SecondaryFindValue | ciąg, domyślnie jest "" |Dynamiczny| X509SecondaryFindValue dla UpgradeService. |
|X509Sklelokacja | ciąg, domyślnie jest "" |Dynamiczny| X509StoreLocation dla UpgradeService. |
|X509Sklearname | ciąg, domyślnie jest "Mój"|Dynamiczny|X509StoreName dla UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities (Usługi obsługi użytkownika)
| **Parametr** | **Dozwolone wartości** | **Zasady uaktualniania** | **Wskazówki lub krótki opis** |
| --- | --- | --- | --- |
|Propertygroup| UserServiceMetricCapacitiesMap, domyślnie jest Brak | Statyczny | Kolekcja limitów zarządzania zasobami usług użytkowników musi być statyczna, ponieważ wpływa na logikę autodetekcji |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [Uaktualnianie konfiguracji klastra platformy Azure](service-fabric-cluster-config-upgrade-azure.md) i [uaktualnianie konfiguracji klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).
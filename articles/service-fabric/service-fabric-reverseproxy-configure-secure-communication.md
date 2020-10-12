---
title: Bezpieczna komunikacja z usługą Azure Service Fabric zwrotny bezpieczny serwer proxy
description: Skonfiguruj zwrotny serwer proxy, aby zapewnić bezpieczną kompleksową komunikację w aplikacji Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256139"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Connect to a secure service with the reverse proxy (Łączenie z bezpieczną usługą przy użyciu zwrotnego serwera proxy)

W tym artykule wyjaśniono, jak ustanowić bezpieczne połączenie między zwrotnym serwerem proxy a usługami, umożliwiając w ten sposób kompleksowe bezpiecznego kanału. Aby dowiedzieć się więcej na temat zwrotnego serwera proxy, zobacz [zwrotny serwer proxy na platformie Azure Service Fabric](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> Łączenie z usługami zabezpieczonymi jest obsługiwane tylko wtedy, gdy serwer proxy odwrotny jest skonfigurowany do nasłuchiwania przy użyciu protokołu HTTPS. W tym artykule założono, że jest to przypadek. Aby skonfigurować zwrotny serwer proxy w programie Service Fabric, zobacz temat [Konfiguracja zwrotnego serwera proxy w usłudze Azure Service Fabric](service-fabric-reverseproxy-setup.md) .

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Ustanowienie bezpiecznego połączenia między zwrotnym serwerem proxy a usługami 

### <a name="reverse-proxy-authenticating-to-services"></a>Zwrotny serwer proxy uwierzytelniania do usług:
Zwrotny serwer proxy identyfikuje siebie w usługach przy użyciu swojego certyfikatu. W przypadku klastrów platformy Azure certyfikat jest określony za pomocą właściwości ***reverseProxyCertificate*** w [sekcji Typ zasobu](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. servicefabric/klastrów**](/azure/templates/microsoft.servicefabric/clusters) szablonu Menedżer zasobów. W przypadku klastrów autonomicznych certyfikat jest określany za pomocą właściwości ***ReverseProxyCertificate*** lub ***ReverseProxyCertificateCommonNames*** w sekcji **Security** ClusterConfig.json. Aby dowiedzieć się więcej, zobacz [Włączanie zwrotnego serwera proxy w klastrach autonomicznych](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Usługi mogą wdrożyć logikę w celu zweryfikowania certyfikatu przedstawionego przez zwrotny serwer proxy. Usługi mogą określić zaakceptowane szczegóły certyfikatu klienta jako ustawienia konfiguracji w pakiecie konfiguracyjnym. Można go odczytać w czasie wykonywania i służy do weryfikowania certyfikatu przedstawionego przez zwrotny serwer proxy. Aby dodać ustawienia konfiguracji, zapoznaj się z tematem [Zarządzanie parametrami aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) . 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Zwrotny serwer proxy weryfikujący tożsamość usługi za pośrednictwem certyfikatu przedstawionego przez usługę:
Zwrotny serwer proxy obsługuje następujące zasady, aby przeprowadzić walidację certyfikatu serwera certyfikatów przedstawionych przez usługi: None, ServiceCommonNameAndIssuer i ServiceCertificateThumbprints.
Aby wybrać zasady dla zwrotnego serwera proxy do użycia, określ **ApplicationCertificateValidationPolicy** w sekcji **elementu applicationgateway/http** w obszarze [fabricSettings](service-fabric-cluster-fabric-settings.md).

W następnej sekcji przedstawiono szczegóły konfiguracji dla każdej z tych opcji.

### <a name="service-certificate-validation-options"></a>Opcje walidacji certyfikatu usługi 

- **Brak**: zwrotny serwer proxy pomija weryfikację certyfikatu usługi proxy i ustanawia bezpieczne połączenie. Jest to zachowanie domyślne.
Określ **ApplicationCertificateValidationPolicy** o wartości **none** w sekcji [**elementu applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: zwrotny serwer proxy weryfikuje certyfikat przedstawiony przez usługę na podstawie nazwy pospolitej certyfikatu i odcisku palca bezpośredniego wystawcy: Określ **ApplicationCertificateValidationPolicy** o wartości **ServiceCommonNameAndIssuer** w sekcji [**elementu applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Aby określić listę wspólnych nazw usług i odcisków palców wystawcy, Dodaj sekcję [**elementu applicationgateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) w obszarze **fabricSettings**, jak pokazano poniżej. W tablicy **parametrów** można dodać wiele par wspólnych nazw certyfikatów i odcisków palców wystawcy. 

   Jeśli nawiązanie połączenia zwrotnego z punktem końcowym jest nawiązywane z certyfikatem, który jest zgodny z dowolnymi z wartości określonych w tym miejscu, zostanie utworzony kanał TLS.
   W przypadku niepowodzenia dopasowania certyfikatu zwrotny serwer proxy nie powiedzie żądania klienta z kodem stanu 502 (zła brama). Wiersz stanu HTTP będzie również zawierać frazę "nieprawidłowy certyfikat SSL". 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: zwrotny serwer proxy sprawdzi certyfikat usługi proxy na podstawie jego odcisku palca. Możesz wybrać tę trasę, gdy usługi są skonfigurowane przy użyciu certyfikatów z podpisem własnym: Określ **ApplicationCertificateValidationPolicy** z wartością **ServiceCertificateThumbprints** w sekcji [**elementu applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Należy również określić odciski palców z wpisem **ServiceCertificateThumbprints** w sekcji **elementu applicationgateway/http** . W polu wartość można określić wiele odcisków palców jako listę rozdzieloną przecinkami, jak pokazano poniżej:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Jeśli odcisk palca certyfikatu serwera znajduje się w tym wpisie konfiguracji, zwrotny serwer proxy pomyślnie nawiązać połączenie TLS. W przeciwnym razie kończy połączenie i kończy się niepowodzeniem żądania klienta z 502 (zła brama). Wiersz stanu HTTP będzie również zawierać frazę "nieprawidłowy certyfikat SSL".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logika wyboru punktu końcowego, gdy usługi ujawniają bezpieczne, a także niezabezpieczone punkty końcowe
Usługa Service Fabric obsługuje Konfigurowanie wielu punktów końcowych dla usługi. Aby uzyskać więcej informacji, zobacz [określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md).

Zwrotny serwer proxy wybiera jeden z punktów końcowych do przesyłania dalej żądania na podstawie parametru zapytania **listenername** w [identyfikatorze URI usługi](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Jeśli nie określono parametru **listenername** , zwrotny serwer proxy może wybrać dowolny punkt końcowy z listy punktów końcowych. W zależności od punktów końcowych skonfigurowanych dla usługi wybrany punkt końcowy może być punktem końcowym HTTP lub HTTPS. Mogą istnieć scenariusze lub wymagania, w których zwrotny serwer proxy ma działać w trybie "tylko zabezpieczenia"; oznacza to, że nie chcesz, aby bezpieczny zwrotny serwer proxy przekazywał żądania do niezabezpieczonych punktów końcowych. Aby ustawić zwrotny serwer proxy w trybie tylko zabezpieczony, określ wpis konfiguracji **SecureOnlyMode** o wartości **true** w sekcji [**elementu applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> W przypadku korzystania z programu **SecureOnlyMode**, jeśli klient określił element **listenername** odpowiadający punktowi końcowemu http (niezabezpieczonemu), zwrotny serwer proxy nie może wykonać żądania o kodzie stanu HTTP 404 (nie można odnaleźć).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Konfigurowanie uwierzytelniania certyfikatu klienta za pomocą zwrotnego serwera proxy
Zakończenie protokołu TLS odbywa się na odwrotnym serwerze proxy i wszystkie dane certyfikatu klienta zostaną utracone. Aby usługi wykonywały uwierzytelnianie certyfikatu klienta, określ ustawienie **ForwardClientCertificate** w sekcji [**elementu applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

1. Gdy **ForwardClientCertificate** ma **wartość false**, zwrotny serwer proxy nie będzie żądać certyfikatu klienta podczas uzgadniania protokołu TLS z klientem.
Jest to zachowanie domyślne.

2. Gdy **ForwardClientCertificate** ma **wartość true**, zwrotny serwer proxy żąda certyfikatu klienta podczas uzgadniania protokołu TLS z klientem.
Następnie przekazuje dane certyfikatu klienta w niestandardowym nagłówku HTTP o nazwie **X-Client-Certificate**. Wartość nagłówka to ciąg formatu PEM szyfrowany algorytmem Base64 certyfikatu klienta. Usługa może zakończyć się powodzeniem/niepowodzeniem żądania przy użyciu odpowiedniego kodu stanu po sprawdzeniu danych certyfikatu.
Jeśli klient nie zaprezentuje certyfikatu, zwrotny serwer proxy przekaże pusty nagłówek i umożliwi usłudze obsługę tego przypadku.

> [!NOTE]
> Zwrotny serwer proxy działa tylko jako usługa przekazywania dalej. Nie zostanie przeprowadzone żadne sprawdzenie poprawności certyfikatu klienta.


## <a name="next-steps"></a>Następne kroki
* Skonfiguruj [i skonfiguruj zwrotny serwer proxy w klastrze](service-fabric-reverseproxy-setup.md).
* Zapoznaj się z tematem [Konfigurowanie zwrotnego serwera proxy w celu nawiązania połączenia z zabezpieczeniami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Zobacz przykład komunikacji HTTP między usługami w [przykładowym projekcie w witrynie GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Zdalne wywołania procedur z Reliable Services komunikacji zdalnej](service-fabric-reliable-services-communication-remoting.md)
* [Internetowy interfejs API, który używa OWIN w Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Zarządzanie certyfikatami klastra](service-fabric-cluster-security-update-certs-azure.md)

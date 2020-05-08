---
title: Apache Kafka proxy REST — Azure HDInsight
description: Dowiedz się, jak wykonywać Apache Kafka operacji przy użyciu serwera proxy REST Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: has-adal-ref
ms.openlocfilehash: affdbfba125b7e9b3f3fe250a56af30e9efe816e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611010"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Korzystanie z klastrów Apache Kafka w usłudze Azure HDInsight przy użyciu serwera proxy REST

Serwer proxy REST Kafka umożliwia współdziałanie z klastrem Kafka za pośrednictwem interfejsu API REST za pośrednictwem protokołu HTTP. Ta akcja oznacza, że klienci Kafka mogą znajdować się poza siecią wirtualną. Klienci mogą tworzyć proste wywołania HTTP do klastra Kafka zamiast polegać na bibliotekach Kafka. W tym artykule przedstawiono sposób tworzenia klastra Kafka z włączonym serwerem proxy REST. Zawiera również przykładowy kod, który pokazuje, jak wykonywać wywołania do serwera proxy REST.

## <a name="rest-api-reference"></a>Dokumentacja interfejsu API REST

Aby uzyskać operacje obsługiwane przez interfejs API REST Kafka, zobacz [Dokumentacja interfejsu API REST usługi HDInsight Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Tło

![Projekt serwera proxy REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Aby zapoznać się z pełną specyfikacją operacji obsługiwanych przez interfejs API, zobacz [Apache KAFKA API REST proxy](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Punkt końcowy serwera proxy REST

Tworzenie klastra Kafka usługi HDInsight z serwerem proxy REST tworzy nowy Publiczny punkt końcowy dla klastra, który można znaleźć we **właściwościach** klastra HDInsight na Azure Portal.

### <a name="security"></a>Zabezpieczenia

Dostęp do serwera proxy REST Kafka jest zarządzany przy użyciu grup zabezpieczeń Azure Active Directory. Podczas tworzenia klastra Kafka Podaj grupę zabezpieczeń usługi Azure AD z dostępem do punktu końcowego REST. Kafka klientów, którzy potrzebują dostępu do serwera proxy REST, powinny być zarejestrowane dla tej grupy przez właściciela grupy. Właściciel grupy może rejestrować się za pośrednictwem portalu lub za pomocą programu PowerShell.

W przypadku żądań punktu końcowego serwera proxy REST aplikacje klienckie powinny uzyskać token OAuth. Token jest używany do weryfikowania przynależności do grupy zabezpieczeń. Znajdź [przykład aplikacji klienckiej](#client-application-sample) , który pokazuje, jak uzyskać token OAuth. Aplikacja kliencka przekazuje token OAuth w żądaniu HTTP do serwera proxy REST.

> [!NOTE]
> Zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md), aby dowiedzieć się więcej na temat grup zabezpieczeń usługi AAD. Aby uzyskać więcej informacji na temat działania tokenów OAuth, zobacz [Autoryzuj dostęp do aplikacji sieci web Azure Active Directory przy użyciu przepływu przyznawania kodu OAuth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. Zarejestrować aplikację w usłudze Azure AD. Aplikacje klienckie zapisywane w celu współdziałania z serwerem proxy REST Kafka będą używać tego identyfikatora aplikacji i klucza tajnego do uwierzytelniania na platformie Azure.

1. Utwórz grupę zabezpieczeń usługi Azure AD. Dodaj aplikację, która została zarejestrowana w usłudze Azure AD, do grupy zabezpieczeń jako **członka** grupy. Ta grupa zabezpieczeń zostanie użyta do kontrolowania, które aplikacje mogą korzystać z serwera proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Sprawdź, czy grupa jest typu **zabezpieczenia**.
    ![Grupy zabezpieczeń](./media/rest-proxy/rest-proxy-group.png)

    Sprawdź, czy aplikacja jest członkiem grupy.
    ![Sprawdź członkostwo](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Tworzenie klastra Kafka z włączonym serwerem proxy REST

1. Podczas przepływu pracy tworzenia klastra Kafka na karcie **zabezpieczenia i sieć** zaznacz opcję **Włącz serwer proxy REST Kafka** .

     ![Włącz serwer proxy REST Kafka i wybierz grupę zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kliknij pozycję **Wybierz grupę zabezpieczeń**. Z listy grup zabezpieczeń wybierz grupę zabezpieczeń, która ma mieć dostęp do serwera proxy REST. Możesz użyć pola wyszukiwania, aby znaleźć odpowiednią grupę zabezpieczeń. Kliknij przycisk **Wybierz** u dołu.

     ![Włącz serwer proxy REST Kafka i wybierz grupę zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Wykonaj pozostałe kroki w celu utworzenia klastra zgodnie z opisem w temacie [Tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Po utworzeniu klastra przejdź do właściwości klastra, aby zarejestrować adres URL serwera proxy REST Kafka.

     ![Wyświetl adres URL serwera proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Przykład aplikacji klienta

Możesz użyć poniższego kodu w języku Python do współpracy z serwerem proxy REST w klastrze Kafka. Aby użyć przykładu kodu, wykonaj następujące kroki:

1. Zapisz przykładowy kod na komputerze, na którym zainstalowano Język Python.
1. Zainstaluj wymagane zależności języka Python, `pip3 install msal`wykonując.
1. Zmodyfikuj sekcję Code, aby **skonfigurować te właściwości** i zaktualizować następujące właściwości środowiska:

    |Właściwość |Opis |
    |---|---|
    |Identyfikator dzierżawy|Dzierżawa platformy Azure, w której znajduje się Twoja subskrypcja.|
    |Identyfikator klienta|Identyfikator aplikacji, która została zarejestrowana w grupie zabezpieczeń.|
    |Klucz tajny klienta|Wpis tajny dla aplikacji, która została zarejestrowana w grupie zabezpieczeń.|
    |Kafkarest_endpoint|Ta wartość jest pobierana z karty **Właściwości** w klastrze przegląd zgodnie z opisem w [sekcji Wdrażanie](#create-a-kafka-cluster-with-rest-proxy-enabled). Powinien być w następującym formacie —`https://<clustername>-kafkarest.azurehdinsight.net`|

1. W wierszu polecenia wykonaj plik Python, wykonując`sudo python3 <filename.py>`

Ten kod wykonuje następujące czynności:

1. Pobiera token OAuth z usługi Azure AD.
1. Pokazuje, jak utworzyć żądanie Kafka serwera proxy REST.

Aby uzyskać więcej informacji na temat uzyskiwania tokenów OAuth w języku Python, zobacz [Python AuthenticationContext Class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). W `topics` tym miejscu mogą pojawić się opóźnienia, które nie zostały utworzone lub usunięte za pomocą serwera proxy REST Kafka. To opóźnienie wynika z faktu odświeżenia pamięci podręcznej.

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Znajdź poniżej inny przykład, aby uzyskać token z platformy Azure dla serwera proxy REST przy użyciu polecenia zwinięcie. **Należy zauważyć, że podczas `scope=https://hib.azurehdinsight.net/.default` pobierania tokenu potrzebujemy określonego elementu.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Następne kroki

* [Dokumenty referencyjne interfejsu API REST usługi Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)

---
title: Apache Kafka proxy REST — Azure HDInsight
description: Dowiedz się, jak wykonywać Apache Kafka operacji przy użyciu serwera proxy REST Kafka w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: a9a007d33226c508e193368b08b189001bf53401
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944074"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Korzystanie z klastrów Apache Kafka w usłudze Azure HDInsight przy użyciu serwera proxy REST

Serwer proxy REST Kafka umożliwia współdziałanie z klastrem Kafka za pośrednictwem interfejsu API REST za pośrednictwem protokołu HTTPS. Ta akcja oznacza, że klienci Kafka mogą znajdować się poza siecią wirtualną. Klienci mogą wykonywać proste i bezpieczne wywołania HTTPS do klastra Kafka zamiast polegać na bibliotekach Kafka. W tym artykule przedstawiono sposób tworzenia klastra Kafka z włączonym serwerem proxy REST. Zawiera również przykładowy kod, który pokazuje, jak wykonywać wywołania do serwera proxy REST.

## <a name="rest-api-reference"></a>Dokumentacja interfejsu API REST

Aby uzyskać operacje obsługiwane przez interfejs API REST Kafka, zobacz [Dokumentacja interfejsu API REST usługi HDInsight Kafka](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Tło

![Projekt serwera proxy REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Aby zapoznać się z pełną specyfikacją operacji obsługiwanych przez interfejs API, zobacz [Apache KAFKA API REST proxy](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Punkt końcowy serwera proxy REST

Tworzenie klastra Kafka usługi HDInsight z serwerem proxy REST tworzy nowy Publiczny punkt końcowy dla klastra, który można znaleźć we **właściwościach** klastra HDInsight na Azure Portal.

### <a name="security"></a>Zabezpieczenia

Dostęp do serwera proxy REST Kafka jest zarządzany przy użyciu grup zabezpieczeń Azure Active Directory. Podczas tworzenia klastra Kafka Podaj grupę zabezpieczeń usługi Azure AD z dostępem do punktu końcowego REST. Kafka klientów, którzy potrzebują dostępu do serwera proxy REST, powinny być zarejestrowane dla tej grupy przez właściciela grupy. Właściciel grupy może rejestrować się za pośrednictwem portalu lub za pomocą programu PowerShell.

W przypadku żądań punktu końcowego serwera proxy REST aplikacje klienckie powinny uzyskać token OAuth. Token jest używany do weryfikowania przynależności do grupy zabezpieczeń. Znajdź [przykład aplikacji klienckiej](#client-application-sample) , który pokazuje, jak uzyskać token OAuth. Aplikacja kliencka przekazuje token OAuth w żądaniu HTTPS do serwera proxy REST.

> [!NOTE]
> Zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md), aby dowiedzieć się więcej na temat grup zabezpieczeń usługi AAD. Aby uzyskać więcej informacji na temat działania tokenów OAuth, zobacz [Autoryzuj dostęp do aplikacji sieci web Azure Active Directory przy użyciu przepływu przyznawania kodu OAuth 2,0](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Serwer proxy REST Kafka z sieciowymi grupami zabezpieczeń
W przypadku przenoszenia własnej sieci wirtualnej i kontrolowania ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń Zezwalaj na ruch **przychodzący** na porcie **9400** oprócz portu 443. Zapewni to dostęp do serwera proxy REST Kafka.

## <a name="prerequisites"></a>Wymagania wstępne

1. Zarejestrować aplikację w usłudze Azure AD. Aplikacje klienckie zapisywane w celu współdziałania z serwerem proxy REST Kafka będą używać tego identyfikatora aplikacji i klucza tajnego do uwierzytelniania na platformie Azure.

1. Utwórz grupę zabezpieczeń usługi Azure AD. Dodaj aplikację, która została zarejestrowana w usłudze Azure AD, do grupy zabezpieczeń jako **członka** grupy. Ta grupa zabezpieczeń zostanie użyta do kontrolowania, które aplikacje mogą korzystać z serwera proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Sprawdź, czy grupa jest typu **zabezpieczenia**.
    ![Grupy zabezpieczeń](./media/rest-proxy/rest-proxy-group.png)

    Sprawdź, czy aplikacja jest członkiem grupy.
    ![Sprawdź członkostwo](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Tworzenie klastra Kafka z włączonym serwerem proxy REST

Poniższe kroki używają Azure Portal. Przykład użycia interfejsu wiersza polecenia platformy Azure można znaleźć w temacie [Create Apache Kafka Cluster proxy REST przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-cli-rest-proxy.md).

1. Podczas przepływu pracy tworzenia klastra Kafka na karcie **zabezpieczenia i sieć** zaznacz opcję **Włącz serwer proxy REST Kafka** .

     ![Zrzut ekranu przedstawia stronę Tworzenie klastra usługi H Insights z wybraną opcją zabezpieczenia i sieć.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kliknij pozycję **Wybierz grupę zabezpieczeń**. Z listy grup zabezpieczeń wybierz grupę zabezpieczeń, która ma mieć dostęp do serwera proxy REST. Możesz użyć pola wyszukiwania, aby znaleźć odpowiednią grupę zabezpieczeń. Kliknij przycisk **Wybierz** u dołu.

     ![Zrzut ekranu przedstawia stronę Tworzenie klastra usługi H Insights z opcją wyboru grupy zabezpieczeń.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Wykonaj pozostałe kroki w celu utworzenia klastra zgodnie z opisem w temacie [Tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu Azure Portal](./apache-kafka-get-started.md).

1. Po utworzeniu klastra przejdź do właściwości klastra, aby zarejestrować adres URL serwera proxy REST Kafka.

     ![Wyświetl adres URL serwera proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Przykład aplikacji klienta

Możesz użyć poniższego kodu w języku Python do współpracy z serwerem proxy REST w klastrze Kafka. Aby użyć przykładu kodu, wykonaj następujące kroki:

1. Zapisz przykładowy kod na komputerze, na którym zainstalowano Język Python.
1. Zainstaluj wymagane zależności języka Python, wykonując `pip3 install msal` .
1. Zmodyfikuj sekcję Code, aby **skonfigurować te właściwości** i zaktualizować następujące właściwości środowiska:

    |Właściwość |Opis |
    |---|---|
    |Identyfikator dzierżawy|Dzierżawa platformy Azure, w której znajduje się Twoja subskrypcja.|
    |Identyfikator klienta|Identyfikator aplikacji, która została zarejestrowana w grupie zabezpieczeń.|
    |Klucz tajny klienta|Wpis tajny dla aplikacji, która została zarejestrowana w grupie zabezpieczeń.|
    |Kafkarest_endpoint|Ta wartość jest pobierana z karty **Właściwości** w klastrze przegląd zgodnie z opisem w [sekcji Wdrażanie](#create-a-kafka-cluster-with-rest-proxy-enabled). Powinien być w następującym formacie — `https://<clustername>-kafkarest.azurehdinsight.net`|

1. W wierszu polecenia wykonaj plik Python, wykonując `sudo python3 <filename.py>`

Ten kod wykonuje następujące czynności:

1. Pobiera token OAuth z usługi Azure AD.
1. Pokazuje, jak utworzyć żądanie Kafka serwera proxy REST.

Aby uzyskać więcej informacji na temat uzyskiwania tokenów OAuth w języku Python, zobacz [Python AuthenticationContext Class](/python/api/adal/adal.authentication_context.authenticationcontext). W tym miejscu mogą pojawić się opóźnienia `topics` , które nie zostały utworzone lub usunięte za pomocą serwera proxy REST Kafka. To opóźnienie wynika z faktu odświeżenia pamięci podręcznej. **Wartość pola wartości** w interfejsie API producenta została ulepszona. Teraz akceptuje obiekty JSON i wszystkie zserializowane formularze.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


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

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Znajdź poniżej inny przykład, aby uzyskać token z platformy Azure dla serwera proxy REST przy użyciu polecenia zwinięcie. **Należy zauważyć, że `scope=https://hib.azurehdinsight.net/.default` podczas pobierania tokenu potrzebujemy określonego elementu.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Następne kroki

* [Dokumenty referencyjne interfejsu API REST usługi Kafka](/rest/api/hdinsight-kafka-rest-proxy/)

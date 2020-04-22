---
title: Apache Kafka REST proxy — Azure HDInsight
description: Dowiedz się, jak wykonywać operacje apache platformy Kafka przy użyciu serwera proxy Odwościenia platformy Kafka w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758995"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interakcja z klastrami platformy Apache w usłudze Azure HDInsight przy użyciu serwera proxy REST

Serwer proxy Kafka REST umożliwia interakcję z klastrem platformy Kafka za pośrednictwem interfejsu API REST za pośrednictwem protokołu HTTP. Ta akcja oznacza, że klienci platformy Kafka mogą znajdować się poza siecią wirtualną. Klienci mogą wykonywać proste wywołania HTTP do klastra platformy Kafka, zamiast polegać na bibliotekach platformy Kafka. W tym artykule pokazano, jak utworzyć klaster platformy Kafka z włączonym serwerem proxy REST. Zawiera również przykładowy kod, który pokazuje, jak nawiązywać połączenia z serwerem proxy REST.

## <a name="rest-api-reference"></a>Dokumentacja interfejsu API REST

Aby uzyskać informacje na tematy związane z operacjami obsługiwanymi przez interfejs API interfejsu KAFKA REST, zobacz [odwołanie do interfejsu API serwera proxy interfejsu USPozycjów SYSTEMU HDInsight.](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

## <a name="background"></a>Tło

![Projekt serwera proxy Kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Aby uzyskać pełną specyfikację operacji obsługiwanych przez interfejs API, zobacz [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Punkt końcowy serwera proxy REST

Utworzenie klastra platformy Windows usługi HDInsight za pomocą serwera proxy REST tworzy nowy publiczny punkt końcowy dla klastra, który można znaleźć w usłudze WŁAŚCIWOŚCI **klastra** USŁUGI HDInsight w witrynie Azure portal.

### <a name="security"></a>Zabezpieczenia

Dostęp do serwera proxy Usługi Kafka REST jest zarządzany za pomocą grup zabezpieczeń usługi Azure Active Directory. Podczas tworzenia klastra platformy Kafka, podaj grupie zabezpieczeń usługi Azure AD dostęp do punktu końcowego REST. Klienci platformy Kafka, którzy potrzebują dostępu do serwera proxy REST, powinni być zarejestrowani w tej grupie przez właściciela grupy. Właściciel grupy może zarejestrować się za pośrednictwem portalu lub za pośrednictwem programu PowerShell.

W przypadku żądań punktu końcowego serwera proxy REST aplikacje klienckie powinny uzyskać token OAuth. Token służy do weryfikacji członkostwa w grupie zabezpieczeń. Znajdź [przykład aplikacji klienta](#client-application-sample) poniżej, który pokazuje, jak uzyskać token OAuth. Aplikacja kliencka przekazuje token OAuth w żądaniu HTTP do serwera proxy REST.

> [!NOTE]  
> Aby dowiedzieć się więcej o grupach zabezpieczeń usługi AAD, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup usługi Azure Active Directory.](../../active-directory/fundamentals/active-directory-manage-groups.md) Aby uzyskać więcej informacji na temat działania tokenów OAuth, zobacz [Autoryzowanie dostępu do aplikacji sieci Web usługi Azure Active Directory przy użyciu przepływu dotacji kodu OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. Zarejestrować aplikację w usłudze Azure AD. Aplikacje klienckie, które piszesz do interakcji z serwerem proxy Rest platformy Kafka użyje identyfikatora tej aplikacji i klucz tajny do uwierzytelniania na platformie Azure.

1. Tworzenie grupy zabezpieczeń usługi Azure AD. Dodaj aplikację zarejestrowaną w usłudze Azure AD do grupy zabezpieczeń jako **członek** grupy. Ta grupa zabezpieczeń będzie używana do kontrolowania, które aplikacje mogą wchodzić w interakcje z serwerem proxy REST. Aby uzyskać więcej informacji na temat tworzenia grup usługi Azure AD, zobacz [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Sprawdź poprawność grupa jest typu **Security**.
    ![Grupy zabezpieczeń](./media/rest-proxy/rest-proxy-group.png)

    Sprawdź poprawność, że aplikacja jest członkiem grupy.
    ![Sprawdź członkostwo](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Tworzenie klastra platformy Kafka z włączonym serwerem proxy REST

1. Podczas tworzenia przepływu pracy klastra platformy Kafka na karcie **Zabezpieczenia + sieć** sprawdź opcję Włącz serwer proxy **Odcół kafka.**

     ![Włączanie serwera proxy Usługi Kafka REST i wybieranie grupy zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Kliknij **pozycję Wybierz grupę zabezpieczeń**. Z listy grup zabezpieczeń wybierz grupę zabezpieczeń, do której chcesz mieć dostęp do serwera proxy REST. Za pomocą pola wyszukiwania można znaleźć odpowiednią grupę zabezpieczeń. Kliknij przycisk **Wybierz** u dołu.

     ![Włączanie serwera proxy Usługi Kafka REST i wybieranie grupy zabezpieczeń](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Wykonaj pozostałe kroki, aby utworzyć klaster zgodnie z opisem w [obszarze Tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu portalu Azure.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. Po utworzeniu klastra przejdź do właściwości klastra, aby zarejestrować adres URL serwera proxy Usługi Kafka REST.

     ![wyświetl adres URL serwera proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Przykład aplikacji klienckiej

Poniższy kod języka Python służy do interakcji z serwerem proxy REST w klastrze platformy Kafka. Aby użyć przykładu kodu, wykonaj następujące kroki:

1. Zapisz przykładowy kod na komputerze z zainstalowanym pythonem.
1. Zainstaluj wymagane zależności pythona, `pip3 install msal`wykonując .
1. Zmodyfikuj sekcję kodu **Skonfiguruj te właściwości** i zaktualizuj następujące właściwości dla swojego środowiska:

    |Właściwość |Opis |
    |---|---|
    |Identyfikator dzierżawy|Dzierżawa platformy Azure, w której znajduje się twoja subskrypcja.|
    |Identyfikator klienta|Identyfikator aplikacji zarejestrowanej w grupie zabezpieczeń.|
    |Klucz tajny klienta|Klucz tajny aplikacji zarejestrowanej w grupie zabezpieczeń.|
    |Kafkarest_endpoint|Pobierz tę wartość z karty **Właściwości** w przeglądzie klastra, zgodnie z opisem w [sekcji wdrażania](#create-a-kafka-cluster-with-rest-proxy-enabled). Powinien być w następującym formacie –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Z wiersza polecenia wykonaj plik pythona, wykonując`sudo python3 <filename.py>`

Ten kod wykonuje następującą akcję:

1. Pobiera token OAuth z usługi Azure AD.
1. Pokazuje, jak złożyć żądanie do serwera proxy Usługi Kafka REST.

Aby uzyskać więcej informacji na temat uzyskiwania tokenów OAuth w pythonie, zobacz [Python AuthenticationContext klasy](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Może pojawić się `topics` opóźnienie, podczas gdy te nie są tworzone lub usuwane za pośrednictwem serwera proxy ODW kafka są tam odzwierciedlane. To opóźnienie jest spowodowane odświeżania pamięci podręcznej.

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

Poniżej znajduje się inny przykład, jak pobrać token z platformy Azure dla serwera proxy REST przy użyciu polecenia curl. **Należy zauważyć, `scope=https://hib.azurehdinsight.net/.default` że musimy określony podczas uzyskiwania tokenu.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Następne kroki

* [Dokumenty referencyjne interfejsu API serwera proxy systemu Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)

---
title: Ustawienia nadrzędne w usłudze Azure Signal Service
description: Zapoznaj się z wprowadzeniem ustawień nadrzędnych i protokołów komunikatów nadrzędnych.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724063"
---
# <a name="upstream-settings"></a>Ustawienia nadrzędne

Funkcja nadrzędnego jest funkcją w wersji zapoznawczej, która umożliwia usłudze Azure sygnalizującej wysyłanie komunikatów i zdarzeń połączeń do zestawu punktów końcowych w trybie bezserwerowym. Można użyć nadrzędnego, aby wywołać metodę Hub od klientów w trybie bezserwerowym i umożliwić punktom końcowym otrzymywanie powiadomień, gdy połączenia klienckie są połączone lub rozłączone.

> [!NOTE]
> Tylko tryb bezserwerowy może konfigurować ustawienia nadrzędne.

## <a name="details-of-upstream-settings"></a>Szczegóły ustawień nadrzędnych

Ustawienia nadrzędne składają się z listy elementów zależnych od kolejności. Każdy element składa się z:

* Szablon adresu URL, który określa, gdzie będą wysyłane wiadomości.
* Zestaw reguł.
* Konfiguracje uwierzytelniania. 

Gdy wystąpi określone zdarzenie, reguły elementu są sprawdzane jeden według kolejności. Komunikaty będą wysyłane do adresu URL pierwszego zgodnego elementu.

### <a name="url-template-settings"></a>Ustawienia szablonu adresu URL

Możesz Sparametryzuj adres URL, aby obsługiwać różne wzorce. Istnieją trzy wstępnie zdefiniowane parametry:

|Wstępnie zdefiniowany parametr|Opis|
|---------|---------|
|Centralny| Centrum jest koncepcją usługi Azure sygnalizującej. Koncentrator jest jednostką izolacji. Zakres użytkowników i dostarczania komunikatów jest ograniczony do centrum.|
|kategorii| Kategoria może być jedną z następujących wartości: <ul><li>**połączenia**: zdarzenia okresu istnienia połączenia. Jest on uruchamiany, gdy połączenie z klientem jest połączone lub rozłączone. Obejmuje to połączone i odłączone zdarzenia.</li><li>**komunikaty**: wywoływane, gdy klienci wywołują metodę Hub. Zawiera wszystkie inne zdarzenia, z wyjątkiem tych w kategorii **połączenia** .</li></ul>|
|wydarzen| W przypadku kategorii **wiadomości** zdarzenie jest obiektem docelowym w [komunikacie wywołania](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) wysyłanym przez klientów. W przypadku kategorii **połączenia** używane są tylko *połączone* i *rozłączone* .|

Te wstępnie zdefiniowane parametry mogą być używane we wzorcu adresu URL. Parametry zostaną zastąpione określoną wartością podczas oceniania nadrzędnego adresu URL. Na przykład: 
```
http://host.com/{hub}/api/{category}/{event}
```
Po nawiązaniu połączenia z klientem w centrum "Rozmowa" zostanie wysłany komunikat do tego adresu URL:
```
http://host.com/chat/api/connections/connected
```
Gdy klient w centrum "Rozmowa" wywoła metodę Hub `broadcast` , zostanie wysłany komunikat do tego adresu URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>Key Vault odwołanie do wpisu tajnego w ustawieniach szablonu adresu URL

Adres URL elementu nadrzędnego nie jest szyfrowany w spoczynku. Jeśli masz jakieś informacje poufne, sugerowane jest użycie Key Vault do ich zapisania, gdy kontrola dostępu ma lepsze ubezpieczenie. W zasadzie można włączyć zarządzaną tożsamość usługi Azure Signal Service, a następnie udzielić uprawnienia do odczytu w wystąpieniu Key Vault i użyć odwołania Key Vault zamiast zwykłego tekstu w wzorcu nadrzędnego adresu URL.

1. Dodawanie tożsamości przypisanej do systemu lub tożsamości przypisanej do użytkownika. Zobacz [jak dodać tożsamość zarządzaną w witrynie Azure Portal](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. Przyznaj uprawnienia do odczytu dla tożsamości zarządzanej w zasadach dostępu w Key Vault. Zobacz [przypisywanie zasad dostępu Key Vault przy użyciu Azure Portal](../key-vault/general/assign-access-policy-portal.md)

3. Zastąp poufny tekst składnią `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` w wzorcu nadrzędnego adresu URL.

> [!NOTE]
> Zawartość wpisu tajnego jest odczytywana tylko wtedy, gdy zmienisz ustawienia nadrzędnego lub zmienisz tożsamość zarządzaną. Upewnij się, że masz uprawnienia do odczytu tożsamości zarządzanej, przed użyciem Key Vault tajnego odwołania.

### <a name="rule-settings"></a>Ustawienia reguły

Reguły dla *reguł centrum*, *reguł kategorii* i *reguł zdarzeń* można ustawić osobno. Reguła dopasowywania obsługuje trzy formaty. Wykonaj na przykład reguły zdarzeń:
- Użyj gwiazdki (*), aby dopasować wszystkie zdarzenia.
- Użyj przecinka (,), aby dołączyć wiele zdarzeń. Na przykład `connected, disconnected` dopasowuje zdarzenia połączone i odłączone.
- Użyj pełnej nazwy zdarzenia, aby dopasować zdarzenie. Na przykład `connected` dopasowuje zdarzenie połączone.

> [!NOTE]
> Jeśli używasz Azure Functions i [wyzwalacza sygnalizującego](../azure-functions/functions-bindings-signalr-service-trigger.md), wyzwalacz sygnalizujący udostępni pojedynczy punkt końcowy w następującym formacie: `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>` .
> Można po prostu skonfigurować **Ustawienia szablonu adresu URL** dla tego adresu URL i zachować domyślne **Ustawienia reguły** . Zobacz [integracja usługi sygnalizującej](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration) , aby uzyskać szczegółowe informacje na temat sposobu znajdowania `<Function_App_URL>` i `<API_KEY>` .

### <a name="authentication-settings"></a>Ustawienia uwierzytelniania

Uwierzytelnianie dla każdego elementu ustawienia nadrzędnego można skonfigurować osobno. W przypadku konfigurowania uwierzytelniania token jest ustawiany w `Authentication` nagłówku komunikatu nadrzędnego. Obecnie usługa sygnałów platformy Azure obsługuje następujące typy uwierzytelniania:
- `None`
- `ManagedIdentity`

Po wybraniu `ManagedIdentity` tej opcji należy najpierw włączyć zarządzaną tożsamość w usłudze Azure Signal Service i opcjonalnie określić zasób. Szczegóły można znaleźć w temacie [zarządzane tożsamości dla usługi Azure Signal Service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Utwórz ustawienia nadrzędne za pośrednictwem Azure Portal

1. Przejdź do usługi Azure Signal Service.
2. Wybierz pozycję **Ustawienia** i Przełącz **tryb usługi** na **bezserwerowy**. Zostaną wyświetlone ustawienia nadrzędne:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Ustawienia nadrzędne":::

3. Dodaj adresy URL w ramach **wzorca nadrzędnego adresu URL**. Następnie ustawienia, takie jak **reguły centrum** , będą zawierać wartość domyślną.
4. Aby ustawić ustawienia dla **reguł centrum**, **reguły zdarzeń**, **reguły kategorii** i **uwierzytelnianie nadrzędne**, wybierz wartość **reguły centrum**. Zostanie wyświetlona strona, która umożliwia edytowanie ustawień:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Szczegóły ustawienia nadrzędnego":::

5. Aby ustawić **uwierzytelnianie nadrzędne**, upewnij się, że została najpierw włączona tożsamość zarządzana. Następnie wybierz pozycję **Użyj tożsamości zarządzanej**. Zgodnie z potrzebami można wybrać opcje w obszarze **Identyfikator zasobu uwierzytelniania**. Szczegóły można znaleźć w temacie [zarządzane tożsamości dla usługi Azure Signal Service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Tworzenie ustawień nadrzędnych za pomocą szablonu Menedżer zasobów

Aby utworzyć ustawienia nadrzędne przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md), należy ustawić `upstream` Właściwość we `properties` właściwości. Poniższy fragment kodu przedstawia sposób ustawiania `upstream` właściwości na potrzeby tworzenia i aktualizowania ustawień nadrzędnych.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>Protokoły bezserwerowe

Usługa sygnałów platformy Azure wysyła komunikaty do punktów końcowych, które są zgodne z następującymi protokołami. Możesz użyć [powiązania wyzwalacza usługi sygnalizującego](../azure-functions/functions-bindings-signalr-service-trigger.md) z aplikacja funkcji, który obsługuje te protokoły.

### <a name="method"></a>Metoda

POST

### <a name="request-header"></a>Nagłówek żądania

|Nazwa |Opis|
|---------|---------|
|X-ASRS-Connection-ID |Identyfikator połączenia dla połączenia klienta.|
|X-ASRS-Hub |Centrum, do którego należy połączenie z klientem.|
|X-ASRS-Kategoria |Kategoria, do której należy wiadomość.|
|X-ASRS-Event |Zdarzenie, do którego należy wiadomość.|
|X-ASRS-Signature |Kod uwierzytelniania wiadomości oparty na skrócie (HMAC) używany do walidacji. Aby uzyskać szczegółowe informacje, zobacz temat [Signature](#signature) .|
|X-ASRS-User-Claims |Grupa oświadczeń połączenia z klientem.|
|X-ASRS-User-ID |Tożsamość użytkownika klienta wysyłającego wiadomość.|
|X-ASRS-Client-Query |Zapytanie o żądanie, gdy klienci łączą się z usługą.|
|Authentication |Opcjonalny token podczas korzystania z programu `ManagedIdentity` . |

### <a name="request-body"></a>Treść żądania

#### <a name="connected"></a>Połączone

Content-Type: Application/JSON

#### <a name="disconnected"></a>Odłączony

Typ zawartości: `application/json`

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|Błąd |ciąg |Komunikat o błędzie zamkniętego połączenia. Puste, gdy połączenia są zamykane bez błędów.|

#### <a name="invocation-message"></a>Komunikat wywołania

Typ zawartości: `application/json` lub `application/x-msgpack`

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|InvocationId |ciąg | Opcjonalny ciąg, który reprezentuje komunikat wywołania. Znajdź szczegóły w [wywołaniach](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Cel |ciąg | Taka sama jak zdarzenie i taka sama jak wartość docelowa w [komunikacie wywołania](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argumenty |Tablica obiektów |Tablica zawierająca argumenty, które mają zostać zastosowane do metody, do której odwołuje się `Target` . |

### <a name="signature"></a>Podpis

Usługa będzie obliczać kod SHA256 dla `X-ASRS-Connection-Id` wartości przy użyciu podstawowego klucza dostępu i pomocniczego klucza dostępu jako `HMAC` klucza. Usługa ustawi ją w `X-ASRS-Signature` nagłówku podczas wykonywania żądań HTTP do nadrzędnego:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzane tożsamości dla usługi Azure Signal Service](howto-use-managed-identity.md)
- [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](signalr-concept-serverless-development-config.md)
- [Obsługa komunikatów z usługi sygnalizującego (powiązanie wyzwalacza)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [Przykład powiązania wyzwalacza usługi sygnalizującego](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
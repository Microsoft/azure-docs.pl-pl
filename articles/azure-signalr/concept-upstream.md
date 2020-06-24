---
title: Ustawienia nadrzędne w usłudze Azure Signal Service
description: Wprowadzenie ustawień i protokołu nadrzędnych komunikatów
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 7434e8796ddcd89968a0ffa0328a823d635f51c9
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988667"
---
# <a name="upstream-settings"></a>Ustawienia nadrzędne

Nadrzędny to funkcja, która umożliwia usłudze sygnalizującej wysyłanie komunikatów i zdarzeń połączeń do zestawu punktów końcowych w trybie bezserwerowym. Nadrzędny może służyć do wywoływania metody Hub od klientów w trybie bezserwerowym i umożliwiania punktów końcowych, gdy połączenia klienckie są połączone lub rozłączone.

> [!NOTE]
> Tylko tryb bezserwerowy może konfigurować ustawienia nadrzędne.

## <a name="upstream-settings-details"></a>Szczegóły ustawień nadrzędnych

Ustawienia nadrzędne składają się z listy zależnych od kolejności elementów. Każdy element składa się z `URL Template` , który określa, gdzie wiadomości wysyła do, zestaw `Rules` i `Authentication` konfiguracje. Po wystąpieniu określonego zdarzenia element `Rules` zostanie sprawdzony jeden według jednego w kolejności, a komunikaty będą wysyłane do nadrzędnego adresu URL elementu.

### <a name="url-template-settings"></a>Ustawienia szablonu adresu URL

Adres URL można sparametryzowane, aby obsługiwał różne wzorce. Istnieją trzy wstępnie zdefiniowane parametry:

|Wstępnie zdefiniowany parametr|Opis|
|---------|---------|
|Centralny| Centrum jest pojęciem sygnalizującym. Centrum jest jednostką izolacji, zakres dostarczania użytkowników i komunikatów jest ograniczony do centrum|
|kategorii| Kategoria może być jedną z następujących wartości: <ul><li>**połączenia**: zdarzenia okresu istnienia połączenia. Uruchamiany, gdy połączenie z klientem jest połączone lub rozłączone. Łącznie ze zdarzeniami *połączonymi* i *odłączonymi*</li><li>**komunikaty**: wywoływane, gdy klienci wywołują metodę Hub. Uwzględnianie wszystkich innych zdarzeń poza tymi w kategorii *połączeń*</li></ul>|
|wydarzen| W przypadku kategorii *messages* , Event to *obiekt docelowy* w [komunikacie wywołania](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) wysyłanym przez klientów. W kategorii *połączenia* jest używana tylko *połączona* i *rozłączona* .|

Te wstępnie zdefiniowane parametry mogą być używane w `URL Pattern` parametrach i zostaną zastąpione określoną wartością podczas oceniania nadrzędnego adresu URL. Na przykład 
```
http://host.com/{hub}/api/{category}/{event}
```
Po nawiązaniu połączenia z klientem w centrum "Rozmowa" zostanie wysłany komunikat do adresu URL:
```
http://host.com/chat/api/connections/connected
```
Gdy klient w centrum `chat` wywoła metodę Hub `broadcast` , zostanie wysłany komunikat do adresu URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rules-settings"></a>Ustawienia reguł

Reguły dla reguł *centrów*, *reguł kategorii* i zasad dotyczących *zdarzeń* można ustawić osobno. Reguła dopasowywania obsługuje trzy formaty. Zapoznaj się z *regułami zdarzeń* w postaci przykładu:
- Użyj gwiazdki (*), aby dopasować wszystkie zdarzenia.
- Przecinek użytkownika (,), aby dołączyć wiele zdarzeń. Na przykład `connected, disconnected` dopasowuje zdarzenia *połączone* i *odłączone*.
- Użyj pełnej nazwy zdarzenia, aby dopasować zdarzenie. Na przykład `connected` dopasowuje zdarzenie *połączone* .

### <a name="authentication-settings"></a>Ustawienia uwierzytelniania

*Uwierzytelnianie* dla każdego elementu nadrzędnego ustawienia można skonfigurować osobno. W przypadku skonfigurowania *uwierzytelniania*token zostanie ustawiony w nagłówku *uwierzytelniania* komunikatu nadrzędnego. Bieżąca usługa sygnalizująca obsługuje następujący typ uwierzytelniania:
- Brak
- ManagedIdentity

W przypadku wybrania opcji *ManagedIdentity*należy ponownie włączyć zarządzaną tożsamość w usłudze sygnalizującego i opcjonalnie określić *zasób*. Szczegółowe informacje znajdują się w temacie [jak używać zarządzanych tożsamości usługi Azure Signal Service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings"></a>Utwórz ustawienia nadrzędne

### <a name="create-upstream-settings-via-azure-portal"></a>Utwórz ustawienia nadrzędne za pośrednictwem Azure Portal

1. Przejdź do usługi Azure Signal Service.
2. Kliknij pozycję *Ustawienia* Bland i Przełącz *tryb usługi* na *serwer*. *Ustawienia nadrzędne* będą wyświetlane w następujący sposób:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Ustawienia nadrzędne":::

3. Wypełnij adresy URL *wzorcem nadrzędnego adresu URL*, a następnie ustawienia, takie jak *reguły centrum* , będą zawierać wartość domyślną.
4. Aby ustawić ustawienia, takie jak *reguły centrum*, *reguły zdarzeń*, *reguły kategorii* i *uwierzytelnianie nadrzędne*, kliknij wartość *reguły centrum*. Zostanie wyświetlona strona umożliwiająca edytowanie ustawień, jak pokazano poniżej:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Ustawienia nadrzędne":::

5. Aby ustawić *uwierzytelnianie nadrzędne*, upewnij się, że masz najpierw włączoną tożsamość zarządzaną, a następnie wybierz pozycję *Użyj tożsamości zarządzanej* w ramach *uwierzytelniania nadrzędnego*. Zgodnie z potrzebami można wybrać opcje w obszarze *Identyfikator zasobu uwierzytelniania*. Aby uzyskać szczegółowe informacje [, zobacz Jak włączyć zarządzaną tożsamość](howto-use-managed-identity.md) .

### <a name="create-upstream-settings-via-arm-template"></a>Tworzenie ustawień nadrzędnych za pomocą szablonu ARM

Aby utworzyć ustawienia nadrzędne przy użyciu [szablonu Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview), należy ustawić `upstream` Właściwość we `properties` właściwości. Poniższe fragmenty kodu pokazują, jak ustawić `upstream` Właściwość na potrzeby tworzenia i aktualizowania ustawień nadrzędnych.

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

## <a name="signalr-serverless-protocol"></a>Protokół sygnałów bezserwerowych

Usługa sygnalizująca wysyła komunikaty do punktów końcowych, które są zgodne z następującymi protokołami.

### <a name="method"></a>Metoda

POST

### <a name="request-header"></a>Nagłówek żądania

|Nazwa |Opis|
|---------|---------|
|X-ASRS-Connection-ID |Identyfikator połączenia dla połączenia klienta|
|X-ASRS-Hub |Centrum, do którego należy połączenie z klientem|
|X-ASRS-Kategoria |Kategoria, do której należy wiadomość|
|X-ASRS-Event |Zdarzenie, do którego należy wiadomość|
|X-ASRS-Signature |HMAC używany do walidacji. Aby uzyskać szczegółowe informacje, zobacz temat [Signature](#signature) .|
|X-ASRS-User-Claims |Grupa oświadczeń połączenia klienta|
|X-ASRS-User-ID |Tożsamość użytkownika klienta wysyłającego komunikat|
|X-ASRS-Client-Query |Zapytanie o żądanie, gdy klienci łączą się z usługą|
|Uwierzytelnianie |Opcjonalny token w przypadku korzystania z *ManagedIdentity* |

### <a name="request-body"></a>Treść żądania

#### <a name="connected"></a>Połączone

Content-Type: Application/JSON

#### <a name="disconnected"></a>Odłączony

Typ zawartości:`application/json`

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|Błąd |ciąg |Komunikat o błędzie został zamknięty. Puste, gdy połączenia są zamknięte bez błędów|

#### <a name="invocation-message"></a>Komunikat wywołania

Typ zawartości: `application/json` lub`application/x-msgpack`

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|InvocationId |ciąg | Opcjonalny ciąg reprezentuje komunikat wywołania. Znajdź szczegóły w [wywołaniach](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Środowisko docelowe |ciąg | Taka sama jak *zdarzenie* i taka sama jak wartość *docelowa* w [komunikacie wywołania](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argumenty |Tablica obiektów |Tablica zawierająca argumenty do zastosowania do metody, do której odwołuje się element docelowy. |

### <a name="signature"></a>Podpis

Usługa obliczy kod SHA256 dla `X-ASRS-Connection-Id` wartości przy użyciu podstawowego klucza dostępu i klucza dostępu pomocniczego jako `HMAC` klucza i ustawi go w `X-ASRS-Signature` nagłówku podczas wykonywania żądań HTTP do nadrzędnego:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Następne kroki

- [Jak używać tożsamości zarządzanych dla usługi Azure Signal Service](howto-use-managed-identity.md)
- [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](signalr-concept-serverless-development-config.md)

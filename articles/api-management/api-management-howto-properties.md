---
title: Jak używać nazwanych wartości w zasadach API Management Azure
description: Dowiedz się, jak używać nazwanych wartości w zasadach API Management Azure. Nazwane wartości mogą zawierać ciągi literałów, wyrażenia zasad i wpisy tajne przechowywane w Azure Key Vault.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: a970eb23c171522907b6066454d9ca15d85f0835
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812292"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Używanie nazwanych wartości w zasadach API Management Azure

[API Management to](api-management-howto-policies.md) zaawansowana funkcja systemu, która umożliwia wydawcy zmianę zachowania interfejsu API za pośrednictwem konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Instrukcje zasad mogą być konstruowane przy użyciu wartości tekstowych literału, wyrażeń zasad i nazwanych wartości.

*Nazwane wartości* to globalna kolekcja par nazwa/wartość w API Management wystąpienia. Nie ma żadnego limitu liczby elementów w kolekcji. Nazwane wartości mogą służyć do zarządzania stałymi wartościami ciągów i wpisami tajnymi we wszystkich konfiguracjach i zasadach interfejsu API. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Nazwane wartości w Azure Portal":::

## <a name="value-types"></a>Typy wartości

|Typ  |Opis  |
|---------|---------|
|Zwykły     |  Ciąg literału lub wyrażenie zasad     |
|Wpis tajny     |   Ciąg literału lub wyrażenie zasad szyfrowane za pomocą API Management      |
|[Magazyn kluczy](#key-vault-secrets)     |  Identyfikator klucza tajnego przechowywanego w magazynie kluczy platformy Azure.      |

Zwykłe wartości lub wpisy tajne mogą zawierać [wyrażenia zasad](./api-management-policy-expressions.md). Na przykład wyrażenie zwraca `@(DateTime.Now.ToString())` ciąg zawierający bieżącą datę i godzina.

Aby uzyskać szczegółowe informacje o nazwanych atrybutach wartości, zobacz API Management [api REST](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate).

## <a name="key-vault-secrets"></a>Wpisy tajne magazynu kluczy

Wartości wpisów tajnych mogą być przechowywane jako zaszyfrowane ciągi w API Management (niestandardowe wpisy tajne) lub przez odwoływanie się do wpisów tajnych [w Azure Key Vault](../key-vault/general/overview.md). 

Używanie wpisów tajnych magazynu kluczy jest zalecane, ponieważ pomaga zwiększyć API Management zabezpieczeń:

* Wpisy tajne przechowywane w magazynach kluczy mogą być ponownie używane w usługach
* Zasady [szczegółowego dostępu można](../key-vault/general/security-features.md#privileged-access) stosować do wpisów tajnych
* Wpisy tajne zaktualizowane w magazynie kluczy są automatycznie obracane w API Management. Po aktualizacji w magazynie kluczy nazwana wartość w API Management zostanie zaktualizowana w ciągu 4 godzin. Możesz również ręcznie odświeżyć klucz tajny przy użyciu Azure Portal lub za pośrednictwem interfejsu API REST zarządzania.

### <a name="prerequisites-for-key-vault-integration"></a>Wymagania wstępne dotyczące integracji z magazynem kluczy

1. Aby uzyskać instrukcje dotyczące tworzenia magazynu kluczy, zobacz [Szybki start: tworzenie magazynu kluczy przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).
1. Włącz tożsamość zarządzaną przypisaną przez system lub przypisaną przez użytkownika [w](api-management-howto-use-managed-service-identity.md) wystąpieniu API Management zarządzanego.
1. Przypisz [zasady dostępu magazynu kluczy do](../key-vault/general/assign-access-policy-portal.md) tożsamości zarządzanej z uprawnieniami do uzyskiwania i listy wpisów tajnych z magazynu. Aby dodać zasady:
    1. W portalu przejdź do magazynu kluczy.
    1. Wybierz **pozycję Ustawienia > zasad dostępu > + Dodaj zasady dostępu.**
    1. Wybierz **pozycję Uprawnienia do tajnych,** a następnie wybierz pozycję **Pobierz** **i wyekstoruj.**
    1. Na **stronie Wybierz podmiot** zabezpieczeń wybierz nazwę zasobu tożsamości zarządzanej. Jeśli używasz tożsamości przypisanej przez system, podmiot zabezpieczeń jest nazwą wystąpienia API Management.
1. Utwórz lub zaimportuj klucz tajny do magazynu kluczy. Zobacz [Szybki start: ustawianie i pobieranie informacji tajnych z Azure Key Vault pomocą Azure Portal](../key-vault/secrets/quick-create-portal.md).

Aby użyć klucza tajnego magazynu kluczy, [dodaj lub edytuj nazwaną](#add-or-edit-a-named-value)wartość i określ typ **magazynu kluczy**. Wybierz klucz tajny z magazynu kluczy.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Dodawanie lub edytowanie nazwanej wartości

### <a name="add-a-key-vault-secret"></a>Dodawanie klucza tajnego magazynu kluczy

Zobacz [Wymagania wstępne dotyczące integracji z magazynem kluczy](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> W przypadku używania klucza tajnego magazynu kluczy API Management należy uważać, aby nie usuwać klucza tajnego, magazynu kluczy lub tożsamości zarządzanej używanej do uzyskiwania dostępu do magazynu kluczy.

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. W **obszarze Interfejsy API** wybierz pozycję **Nazwane wartości**+  >  **Dodaj**.
1. Wprowadź identyfikator **nazwy,** a następnie wprowadź nazwę **wyświetlaną** używaną do odwołania się do właściwości w zasadach.
1. W **typie wartość** wybierz pozycję **Magazyn kluczy.**
1. Wprowadź identyfikator klucza tajnego magazynu kluczy (bez wersji) lub wybierz pozycję **Wybierz,** aby wybrać wpis tajny z magazynu kluczy.
    > [!IMPORTANT]
    > Jeśli samodzielnie wprowadzasz identyfikator klucza tajnego magazynu kluczy, upewnij się, że nie zawiera on informacji o wersji. W przeciwnym razie klucz tajny nie zostanie automatycznie obrócony API Management aktualizacji w magazynie kluczy.
1. W **tożsamości klienta** wybierz tożsamość zarządzaną przypisaną przez system lub istniejącą przypisaną przez użytkownika. Dowiedz się, [jak dodawać lub modyfikować tożsamości zarządzane w usłudze API Management usługi](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Tożsamość musi mieć uprawnienia do uzyskania wpisów tajnych i ich listy z magazynu kluczy. Jeśli nie skonfigurowano jeszcze dostępu do magazynu kluczy, API Management monit, aby automatycznie skonfigurować tożsamość z niezbędnymi uprawnieniami.
1. Dodaj co najmniej jeden opcjonalny tag, aby ułatwić organizowanie nazwanych wartości, a następnie zapisz **.**
1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Dodawanie wartości klucza tajnego magazynu kluczy":::

### <a name="add-a-plain-or-secret-value"></a>Dodawanie wartości zwykłego lub tajnego

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego API Management wystąpienia.
1. W **obszarze Interfejsy API** wybierz pozycję **Nazwane wartości**+  >  **Dodaj**.
1. Wprowadź identyfikator **nazwy,** a następnie wprowadź nazwę **wyświetlaną** używaną do odwołania do właściwości w zasadach.
1. W **typie wartości** wybierz opcję **Zwykły** lub **Tajny.**
1. W **wartości** wprowadź ciąg lub wyrażenie zasad.
1. Dodaj co najmniej jeden opcjonalny tag, aby ułatwić organizowanie nazwanych wartości, a następnie wybierz **pozycję Zapisz**.
1. Wybierz przycisk **Utwórz**.

Po utworzeniu nazwanej wartości możesz ją edytować, wybierając nazwę. Jeśli zmienisz nazwę wyświetlaną, wszystkie zasady odwołujące się do tej nazwanej wartości zostaną automatycznie zaktualizowane w celu użycia nowej nazwy wyświetlanej.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Aby dodać nazwaną wartość, użyj [polecenia az apim nv create:](/cli/azure/apim/nv#az_apim_nv_create)

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Po utworzeniu nazwanej wartości można ją zaktualizować za pomocą [polecenia az apim nv update.](/cli/azure/apim/nv#az_apim_nv_update) Aby wyświetlić wszystkie nazwane wartości, uruchom [polecenie az apim nv list:](/cli/azure/apim/nv#az_apim_nv_list)

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Aby wyświetlić szczegóły nazwanej wartości utworzonej w tym przykładzie, uruchom [polecenie az apim nv show:](/cli/azure/apim/nv#az_apim_nv_show)

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Ten przykład jest wartością tajnego. Poprzednie polecenie nie zwraca wartości . Aby wyświetlić wartość, uruchom [polecenie az apim nv show-secret:](/cli/azure/apim/nv#az_apim_nv_show_secret)

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Aby usunąć nazwaną wartość, użyj [polecenia az apim nv delete:](/cli/azure/apim/nv#az_apim_nv_delete)

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Używanie nazwanej wartości

W przykładach w tej sekcji są podane nazwane wartości przedstawione w poniższej tabeli.

| Nazwa               | Wartość                      | Wpis tajny | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Fałsz  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Prawda   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Fałsz  | 

Aby użyć nazwanej wartości w zasadach, umieść jej nazwę wyświetlaną wewnątrz podwójnej pary nawiasów klamrowych, takich jak , jak `{{ContosoHeader}}` pokazano w poniższym przykładzie:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

W tym przykładzie jest używana jako nazwa nagłówka w zasadach i jest używana jako `ContosoHeader` `set-header` wartość tego `ContosoHeaderValue` nagłówka. Gdy te zasady są oceniane podczas żądania lub odpowiedzi do bramy API Management i są `{{ContosoHeader}}` `{{ContosoHeaderValue}}` zastępowane odpowiednimi wartościami.

Nazwane wartości mogą być używane jako pełne wartości atrybutów lub elementów, jak pokazano w poprzednim przykładzie, ale można je również wstawić do lub połączyć z częścią wyrażenia tekstowego literału, jak pokazano w poniższym przykładzie: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Nazwane wartości mogą również zawierać wyrażenia zasad. W poniższym przykładzie używane `ExpressionProperty` jest wyrażenie .

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Podczas oceniania tych zasad wartość `{{ExpressionProperty}}` jest zastępowana wartością `@(DateTime.Now.ToString())` . Ponieważ wartość jest wyrażeniem zasad, wyrażenie jest oceniane, a zasady kontynuują jego wykonywanie.

Możesz to przetestować w portalu Azure Portal lub [portalu](api-management-howto-developer-portal.md) dla deweloperów, wywołując operację, która ma zasady o nazwanych wartościach w zakresie. W poniższym przykładzie wywoływana jest operacja z dwiema poprzednimi przykładami zasad `set-header` o nazwanych wartościach. Zwróć uwagę, że odpowiedź zawiera dwa nagłówki niestandardowe, które zostały skonfigurowane przy użyciu zasad o nazwanych wartościach.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Odpowiedź interfejsu API testu":::

Jeśli przyjrzysz się śladowi interfejsu [API](api-management-howto-api-inspector.md) ruchu wychodzącego dla wywołania zawierającego dwie poprzednie przykładowe zasady z nazwanych wartościami, zobaczysz dwie zasady z wstawionym nazwanych wartościami, a także ocenę wyrażenia zasad dla nazwanej wartości zawierającej `set-header` wyrażenie zasad.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Śledzenie inspektora interfejsu API":::

> [!CAUTION]
> Jeśli zasada odwołuje się do klucza tajnego w Azure Key Vault, wartość z magazynu kluczy będzie widoczna dla użytkowników, którzy mają dostęp do subskrypcji z włączoną obsługą śledzenia [żądań interfejsu API.](api-management-howto-api-inspector.md)

Nazwane wartości mogą zawierać wyrażenia zasad, ale nie mogą zawierać innych nazwanych wartości. Jeśli tekst zawierający odwołanie do nazwanej wartości jest używany dla wartości, takiej jak , to odwołanie `Text: {{MyProperty}}` nie zostanie rozpoznane i zastąpione.

## <a name="delete-a-named-value"></a>Usuwanie nazwanej wartości

Aby usunąć nazwaną wartość, wybierz nazwę, a następnie wybierz pozycję **Usuń** z menu kontekstowego (**...**).

> [!IMPORTANT]
> Jeśli nazwana wartość jest przywołyowana przez dowolne zasady API Management, nie można jej usunąć, dopóki nie usuniesz nazwanej wartości ze wszystkich zasad, które z nich korzystają.

## <a name="next-steps"></a>Następne kroki

-   Dowiedz się więcej o pracy z zasadami
    -   [Zasady w API Management](api-management-howto-policies.md)
    -   [Dokumentacja zasad](./api-management-policies.md)
    -   [Wyrażenia zasad](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png


---
title: Włącz zarządzaną tożsamość dla zdarzeń routingu (wersja zapoznawcza) — interfejs wiersza polecenia
titleSuffix: Azure Digital Twins
description: Zapoznaj się z tematem Włączanie tożsamości przypisanej do systemu na potrzeby usługi Azure Digital bliźniaczych reprezentacji i używania jej do przesyłania dalej zdarzeń przy użyciu interfejsu wiersza polecenia platformy Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202964"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Włącz zarządzaną tożsamość do routingu zdarzeń usługi Azure Digital bliźniaczych reprezentacji (wersja zapoznawcza): interfejs wiersza polecenia platformy Azure

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

W tym artykule opisano sposób włączania [tożsamości przypisanej do systemu dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (obecnie w wersji zapoznawczej) i korzystania z tożsamości podczas przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   miejsc docelowych i [kontener usługi Azure Storage](../storage/blobs/storage-blobs-introduction.md).

Ten artykuł przeprowadzi Cię przez proces przy użyciu [**interfejsu wiersza polecenia platformy Azure**](/cli/azure/what-is-azure-cli).

Poniżej przedstawiono kroki omówione w tym artykule: 

1. Utwórz wystąpienie usługi Azure Digital bliźniaczych reprezentacji z tożsamością przypisaną do systemu lub Włącz tożsamość przypisaną przez system w istniejącym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. 
1. Dodaj odpowiednią rolę lub role do tożsamości. Na przykład Przypisz rolę **nadawcy danych usługi Azure Event Hub** do tożsamości, jeśli punkt końcowy ma wartość centrum zdarzeń lub **Azure Service Bus rolę nadawcy danych** , jeśli punkt końcowy jest Service Bus.
1. Utwórz punkt końcowy w usłudze Azure Digital bliźniaczych reprezentacji, który może korzystać z tożsamości przypisanych do systemu na potrzeby uwierzytelniania.

## <a name="enable-system-managed-identities-for-an-instance"></a>Włącz tożsamość zarządzaną przez system dla wystąpienia 

Po włączeniu tożsamości przypisanej do systemu w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji platforma Azure automatycznie tworzy dla niej tożsamość w [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Tej tożsamości można następnie użyć do uwierzytelniania w usłudze Azure Digital bliźniaczych reprezentacji Endpoints w celu przekazywania zdarzeń.

Tożsamość zarządzaną przez system można włączyć dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji **jako część początkowej konfiguracji wystąpienia** lub **włączyć ją później w wystąpieniu, które już istnieje**.

Każda z tych metod tworzenia będzie mieć te same opcje konfiguracji i ten sam wynik końcowy dla danego wystąpienia. W tej sekcji opisano sposób wykonywania obu tych czynności.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Dodawanie tożsamości zarządzanej przez system podczas tworzenia wystąpienia

W tej sekcji dowiesz się, jak włączyć tożsamość zarządzaną przez system w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, która jest aktualnie tworzona. 

W tym celu należy dodać `--assign-identity` parametr do `az dt create` polecenia, które jest używane do tworzenia wystąpienia. (Aby uzyskać więcej informacji na temat tego polecenia, zobacz jego [dokumentację referencyjną](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) lub [Ogólne instrukcje dotyczące konfigurowania wystąpienia usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)).

Aby utworzyć wystąpienie z tożsamością zarządzaną systemu, należy dodać  `--assign-identity` parametr podobny do tego:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Dodawanie tożsamości zarządzanej przez system do istniejącego wystąpienia

W tej sekcji dodasz tożsamość zarządzaną przez system do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które już istnieje.

Jest to również wykonywane z `az dt create` poleceniem i `--assign-identity` parametrem. Zamiast podawania nowej nazwy wystąpienia do utworzenia, można podać nazwę wystąpienia, które już istnieje, aby zaktualizować wartość `--assign-identity` dla tego wystąpienia.

Polecenie umożliwiające **włączenie** tożsamości zarządzanej jest takie samo jak polecenie, aby utworzyć wystąpienie z tożsamością zarządzaną przez system. Wszystkie zmiany są wartościami parametru Nazwa wystąpienia:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Aby **wyłączyć** tożsamość zarządzaną w wystąpieniu, w którym jest obecnie włączona, użyj następującego podobnego polecenia, aby ustawić wartość `--assign-identity` `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Przypisywanie ról platformy Azure do tożsamości 

Po utworzeniu tożsamości przypisanej do systemu dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy przypisać odpowiednie role do uwierzytelniania za pomocą różnych typów [punktów końcowych](concepts-route-events.md) w celu przekazywania zdarzeń do obsługiwanych miejsc docelowych. Ta sekcja zawiera opis opcji ról i sposobu ich przypisywania do tożsamości przypisanej do systemu.

>[!NOTE]
> Jest to ważny krok — bez niego, tożsamość nie będzie mogła uzyskiwać dostępu do punktów końcowych, a zdarzenia nie zostaną dostarczone.

### <a name="supported-destinations-and-azure-roles"></a>Obsługiwane miejsca docelowe i role platformy Azure 

Poniżej znajdują się minimalne role, których tożsamość musi uzyskać dostęp do punktu końcowego, w zależności od typu miejsca docelowego. Role z wyższymi uprawnieniami (na przykład role właściciela danych) również będą działały.

| Element docelowy | Rola na platformie Azure |
| --- | --- |
| Azure Event Hubs | Nadawca danych Event Hubs platformy Azure |
| Usługa Azure Service Bus | Nadawca danych Azure Service Bus |
| Kontener usługi Azure Storage | Współautor danych obiektu blob usługi Storage |

Aby uzyskać więcej informacji na temat punktów końcowych, tras i typów miejsc docelowych obsługiwanych w przypadku routingu w usłudze Azure Digital bliźniaczych reprezentacji, zobacz [*pojęcia: trasy zdarzeń*](concepts-route-events.md).

### <a name="assign-the-role"></a>Przypisywanie roli

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Można dodać `--scopes` parametr `az dt create` do polecenia, aby przypisać tożsamość do co najmniej jednego zakresu z określoną rolą. Można go użyć podczas pierwszego tworzenia wystąpienia lub później przez przekazanie nazwy wystąpienia, które już istnieje.

Oto przykład, który tworzy wystąpienie z tożsamością zarządzaną przez system i przypisuje tożsamości rolę niestandardową o nazwie `MyCustomRole` w centrum zdarzeń.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Więcej przykładów przypisań ról za pomocą tego polecenia można znaleźć w [dokumentacji **AZ DT Create** Reference](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create).

Alternatywnie można również utworzyć role i zarządzać nimi za pomocą polecenia [**AZ role przypisania**](/cli/azure/role/assignment) grupy. Może służyć do obsługi dodatkowych scenariuszy, w których nie chcesz grupować przypisywania ról za pomocą polecenia CREATE.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Tworzenie punktu końcowego z uwierzytelnianiem na podstawie tożsamości

Po skonfigurowaniu tożsamości zarządzanej przez system dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji i przypisaniu go do odpowiednich ról można utworzyć usługi Azure Digital bliźniaczych reprezentacji [Endpoints](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) , które mogą korzystać z tożsamości na potrzeby uwierzytelniania. Ta opcja jest dostępna tylko dla punktów końcowych centrum zdarzeń i typu Service Bus (nie jest to obsługiwane w przypadku Event Grid).

>[!NOTE]
> Nie można edytować punktu końcowego, który został już utworzony za pomocą tożsamości opartej na kluczach, aby zmienić uwierzytelnianie oparte na tożsamości. Podczas pierwszego tworzenia punktu końcowego należy wybrać typ uwierzytelniania.

W tym celu należy dodać `--auth-type` parametr do `az dt endpoint create` polecenia, które służy do tworzenia punktu końcowego. (Aby uzyskać więcej informacji na temat tego polecenia, zobacz jego [dokumentację referencyjną](/cli/azure/ext/azure-iot/dt/endpoint/create) lub [Ogólne instrukcje dotyczące konfigurowania punktu końcowego usługi Azure Digital bliźniaczych reprezentacji](how-to-manage-routes-apis-cli.md#create-the-endpoint)).

Aby utworzyć punkt końcowy, który używa uwierzytelniania opartego na tożsamościach, określ `IdentityBased` Typ uwierzytelniania z  `--auth-type` parametrem. Poniższy przykład ilustruje ten element dla punktu końcowego Event Hubs.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Zagadnienia dotyczące wyłączania tożsamości zarządzanych przez system

Ze względu na to, że tożsamość jest zarządzana niezależnie od punktów końcowych, które go używają, należy wziąć pod uwagę wpływ wszelkich zmian tożsamości lub ról na punkty końcowe w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Jeśli tożsamość jest wyłączona lub zostanie z niej usunięta niezbędna rola punktu końcowego, punkt końcowy może stać się niedostępny i przepływ zdarzeń zostanie zakłócony.

Aby nadal korzystać z punktu końcowego, który został skonfigurowany przy użyciu tożsamości zarządzanej, która została już wyłączona, należy usunąć punkt końcowy i [utworzyć go ponownie](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) z innym typem uwierzytelniania. Wznowienie dostarczania w punkcie końcowym do punktu końcowego może potrwać do godziny.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tożsamościach zarządzanych w usłudze Azure AD: 
* [*Tożsamości zarządzane dla zasobów platformy Azure*](../active-directory/managed-identities-azure-resources/overview.md)
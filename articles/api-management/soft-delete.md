---
title: Azure API Management Soft-Delete (wersja zapoznawcza) | Microsoft Docs
description: Funkcja usuwania nietrwałego umożliwia odzyskanie usuniętych wystąpień API Management.
ms.service: api-management
ms.topic: conceptual
author: vladvino
ms.author: apimpm
ms.date: 11/27/2020
ms.openlocfilehash: fca98414a87f3b8a4f3c0969a28ee95c7ed47dc3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501576"
---
# <a name="api-management-soft-delete-preview"></a>API Management usuwanie nietrwałe (wersja zapoznawcza)

Za pomocą narzędzia API Management Soft-Delete (wersja zapoznawcza) można odzyskiwać i przywracać ostatnio usunięte wystąpienia API Management (APIM).

> [!IMPORTANT]
> Tylko API Management wystąpienia usunięte przy użyciu `2020-01-01-preview` i nowsze wersje interfejsu API będą usunięte nietrwale i odzyskania przy użyciu kroków opisanych w tym artykule. Wystąpienia APIM usunięte przy użyciu poprzednich wersji interfejsu API będą nadal usunięte. W Azure PowerShell i interfejs wiersza polecenia platformy Azure obecnie nie jest używana `2020-06-01-preview` wersja i spowoduje to również trwałe usunięcie.

## <a name="supporting-interfaces"></a>Interfejsy pomocnicze

Funkcja usuwania nietrwałego jest dostępna za pomocą [interfejsu API REST](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/restore).

> [!TIP]
> Zapoznaj się z dokumentacją [interfejsu API REST platformy Azure](/rest/api/azure/) , aby uzyskać porady i narzędzia do wywoływania interfejsów API REST platformy Azure.

| Operacja | Opis | API Management przestrzeń nazw | Minimalna wersja interfejsu API |
|--|--|--|--|
| [Utwórz lub zaktualizuj](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) | Tworzy lub aktualizuje usługę API Management.  | Usługa API Management | Dowolne |
| [Utwórz lub zaktualizuj](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) `restore` Właściwość z właściwością ustawioną na **wartość true** | Cofa usunięcie usługi API Management, jeśli została wcześniej usunięta. Jeśli `restore` jest określona i ustawiona na `true` wszystkie inne właściwości zostanie zignorowana.  | Usługa API Management |  2020-06-01 — wersja zapoznawcza |
| [Usuń](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/delete) | Usuwa istniejącą usługę API Management. | Usługa API Management | 2020-01-01 — wersja zapoznawcza|
| [Pobierz według nazwy](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) | Pobierz nietrwałe usługi zarządzania interfejsami API według nazwy. | Usunięte usługi | 2020-06-01 — wersja zapoznawcza |
| [Lista według subskrypcji](/rest/api/apimanagement/2020-06-01-preview/deletedservices/listbysubscription) | Wyświetla listę wszystkich usuniętych nietrwałych usług dostępnych do cofnięcia usunięcia dla danej subskrypcji. | Usunięte usługi | 2020-06-01 — wersja zapoznawcza
| [Usuwa](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) | Przeczyszcza API Management usługi (usuwa ją bez opcji usuwania). | Usunięte usługi | 2020-06-01 — wersja zapoznawcza

## <a name="soft-delete-behavior"></a>Zachowanie nietrwałego usuwania

Możesz użyć dowolnej wersji interfejsu API, aby utworzyć wystąpienie API Management, jednak do `2020-01-01-preview` nietrwałego usunięcia wystąpienia usługi APIM należy użyć lub nowsze wersje.

Po usunięciu wystąpienia API Management usługa będzie istnieć w stanie usunięte, co uniemożliwi dostęp do żadnych operacji APIM. W tym stanie wystąpienie APIM może zostać wyświetlone, odzyskane lub usunięte (trwale usunięte).

W tym samym czasie platforma Azure zaplanuje usunięcie danych źródłowych odpowiadających wystąpieniu APIM do wykonania po upływie wstępnie określonego (48-godzinnego) interwału przechowywania. Rekord DNS odpowiadający wystąpieniu również jest zachowywany na czas trwania interwału przechowywania. Nie można ponownie użyć nazwy wystąpienia API Management, które zostało usunięte nietrwałe, dopóki nie upłynie okres przechowywania.

Jeśli wystąpienie APIM nie zostanie odzyskane w ciągu 48 godzin, zostanie trwale usunięte (nieodwracalne). Możesz również wybrać [przeczyszczanie](#purge-a-soft-deleted-apim-instance) (trwałe usuwanie) wystąpienia APIM, forgoing nietrwałego okresu przechowywania.

## <a name="list-deleted-apim-instances"></a>Wyświetlanie listy usuniętych wystąpień APIM

Można sprawdzić, czy jest dostępne nietrwałe wystąpienie [APIM, które](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) ma zostać przywrócone (cofanie usunięcia) za pomocą operacji "usunięte usługi" lub [listy przez operacje subskrypcji](/deletedservices/listbysubscription) .

### <a name="get-a-soft-deleted-instance-by-name"></a>Pobierz nietrwałe wystąpienie o nazwie

Użyj API Management operacji [Get według nazwy](/rest/api/apimanagement/2020-06-01-preview/deletedservices/getbyname) , podstawiania `{subscriptionId}` , `{location}` , i `{serviceName}` z subskrypcją platformy Azure, lokalizacji zasobów i nazwy wystąpienia API Management:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Jeśli jest dostępny do cofnięcia usunięcia, platforma Azure zwróci rekord wystąpienia APIM, `deletionDate` `scheduledPurgeDate` na przykład:

```json
{
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ApiManagement/locations/southcentralus/deletedservices/apimtest",
    "name": "apimtest",
    "type": "Microsoft.ApiManagement/deletedservices",
    "location": "South Central US",
    "properties": {
        "serviceId": "/subscriptions/########-####-####-####-############/resourceGroups/apimtestgroup/providers/Microsoft.ApiManagement/service/apimtest",
        "scheduledPurgeDate": "2020-11-26T19:40:26.3596893Z",
        "deletionDate": "2020-11-24T19:40:50.1013572Z"
    }
}
```

### <a name="list-all-soft-deleted-instances-for-a-given-subscription"></a>Wyświetl listę wszystkich wystąpień nietrwałych usuniętych dla danej subskrypcji

Użyj operacji API Management [listy według subskrypcji](/deletedservices/listbysubscription) , ZASTĘPUJĄC ją `{subscriptionId}` identyfikatorem subskrypcji:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/deletedservices?api-version=2020-06-01-preview
```

Spowoduje to zwrócenie listy wszystkich nieusuniętych i nietrwałych usług dostępnych do cofnięcia usunięcia w ramach danej subskrypcji, pokazując `deletionDate` i `scheduledPurgeDate` dla każdego z nich.

## <a name="recover-a-deleted-apim-instance"></a>Odzyskaj usunięte wystąpienie APIM

Użyj API Management [tworzenia lub aktualizowania](/rest/api/apimanagement/2020-06-01-preview/apimanagementservice/createorupdate) operacji, podstawiania `{subscriptionId}` , `{resourceGroup}` , i `{apimServiceName}` z subskrypcją platformy Azure, nazwą grupy zasobów i nazwą API Management:

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ApiManagement/service/{apimServiceName}?api-version=2020-06-01-preview
```

. . . i ustaw `restore` Właściwość na `true` w treści żądania. (Jeśli ta flaga jest określona i ma *wartość true*, wszystkie inne właściwości zostaną zignorowane). Na przykład:

```json
{
  "properties": {
    "publisherEmail": "help@contoso.com",
    "publisherName": "Contoso",
    "restore": true
  },
  "sku": {
    "name": "Developer",
    "capacity": 1
  },
  "location": "South Central US"
}
```

## <a name="purge-a-soft-deleted-apim-instance"></a>Przeczyść nietrwałe wystąpienie APIM

Użyj operacji [przeczyszczania](/rest/api/apimanagement/2020-06-01-preview/deletedservices/purge) API Management, podstawiania, `{subscriptionId}` `{location}` , i `{serviceName}` z subskrypcją platformy Azure, lokalizacji zasobów i nazwy API Management:

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ApiManagement/locations/{location}/deletedservices/{serviceName}?api-version=2020-06-01-preview
```

Spowoduje to trwałe usunięcie wystąpienia API Management z platformy Azure.

## <a name="next-steps"></a>Następne kroki

Informacje na temat długoterminowych API Management opcji tworzenia kopii zapasowych i odzyskiwania:

- [Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia i przywracania kopii zapasowych w usłudze Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)
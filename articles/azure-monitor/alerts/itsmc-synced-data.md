---
title: Dane zsynchronizowane z produktu narzędzia ITSM do obszaru roboczego LA
description: Ten artykuł zawiera omówienie danych synchronizowanych z produktu narzędzia ITSM do usługi LA Workspace.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: fd570950190ceabac413aca2d68368e5e722a3da
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614400"
---
# <a name="data-synced-from-your-itsm-product"></a>Dane zsynchronizowane z produktu narzędzia ITSM

Zdarzenia i żądania zmiany są synchronizowane z poziomu narzędzia Narzędzia ITSM z obszarem roboczym Log Analytics w oparciu o konfigurację połączenia (przy użyciu pola "Synchronizuj dane"):
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Zsynchronizowane dane

W tej sekcji przedstawiono kilka przykładów danych zebranych przez ITSMC.

Pola w **ServiceDesk_CL** różnią się w zależności od typu elementu pracy, który można zaimportować do log Analytics. Poniżej znajduje się lista pól dla dwóch typów elementów roboczych:

**Element roboczy:** **zdarzenia**  
ServiceDeskWorkItemType_s = "zdarzenie"

**Pola**

- ServiceDeskConnectionName
- Identyfikator działu obsługi
- Stan
- Pilność
- Wpływ
- Priorytet
- Eskalacja
- Created By
- Rozwiązane przez
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Kategoria
- Tytuł
- Opis
- Data utworzenia
- Data zamknięcia
- Data rozwiązania
- Data ostatniej modyfikacji
- Computer (Komputer)

**Element roboczy:** **żądania zmiany**

ServiceDeskWorkItemType_s = "ChangeRequest"

**Pola**
- ServiceDeskConnectionName
- Identyfikator działu obsługi
- Created By
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Tytuł
- Typ
- Kategoria
- Stan
- Eskalacja
- Stan konfliktu
- Pilność
- Priorytet
- Ryzyko
- Wpływ
- Przypisano do
- Data utworzenia
- Data zamknięcia
- Data ostatniej modyfikacji
- Żądana Data
- Planowana data rozpoczęcia
- Planowana data zakończenia
- Data rozpoczęcia pracy
- Data zakończenia pracy
- Opis
- Computer (Komputer)

## <a name="servicenow-example"></a>Przykład usługi ServiceNow 
### <a name="output-data-for-a-servicenow-incident"></a>Dane wyjściowe dla zdarzenia usługi ServiceNow

| Pole Log Analytics | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| IncidentState_s | Stan |
| Urgency_s |Pilność |
| Impact_s |Wpływ|
| Priority_s | Priorytet |
| CreatedBy_s | Otwarte przez |
| ResolvedBy_s | Rozwiązane przez|
| ClosedBy_s  | Zamknięte przez |
| Source_s| Typ kontaktu |
| AssignedTo_s | Przypisano do  |
| Category_s | Kategoria |
| Title_s|  Krótki opis |
| Description_s|  Uwagi |
| CreatedDate_t|  Otworzyć |
| ClosedDate_t| zamknięte|
| ResolvedDate_t|Resolved|
| Computer (Komputer)  | Pozycja konfiguracji |

### <a name="output-data-for-a-servicenow-change-request"></a>Dane wyjściowe żądania zmiany usługi ServiceNow

| Log Analytics | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| CreatedBy_s | Żądane przez |
| ClosedBy_s | Zamknięte przez |
| AssignedTo_s | Przypisano do  |
| Title_s|  Krótki opis |
| Type_s|  Typ |
| Category_s|  Kategoria |
| CRState_s|  Stan|
| Urgency_s|  Pilność |
| Priority_s| Priorytet|
| Risk_s| Ryzyko|
| Impact_s| Wpływ|
| RequestedDate_t  | Żądane w dniu |
| ClosedDate_t | Data zamknięcia |
| PlannedStartDate_t  | Planowana data rozpoczęcia |
| PlannedEndDate_t  | Planowana data zakończenia |
| WorkStartDate_t  | Rzeczywista data rozpoczęcia |
| WorkEndDate_t | Rzeczywista data zakończenia|
| Description_s | Opis |
| Computer (Komputer)  | Element konfiguracji |

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)

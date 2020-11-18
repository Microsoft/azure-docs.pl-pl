---
title: Rozwiązywanie problemów z replikacją między regionami dla Azure NetApp Files | Microsoft Docs
description: Opisuje komunikaty o błędach i rozwiązania, które mogą pomóc w rozwiązywaniu problemów z replikacją między regionami w Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: 6fbb9b054433905d41d0171ab08b4647618be466
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94745687"
---
# <a name="troubleshoot-cross-region-replication"></a>Rozwiązywanie problemów z replikacją między regionami

W tym artykule opisano komunikaty o błędach i rozwiązania, które mogą pomóc w rozwiązywaniu problemów z replikacją między regionami Azure NetApp Files. 

## <a name="errors-creating-replication"></a>Błędy podczas tworzenia replikacji  

|     Komunikat o błędzie    |     Rozwiązanie    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     Nie można utworzyć replikacji z woluminem źródłowym, który znajduje się już w relacji replikacji danych.    |
|     `Peered region   '{0}' is not accepted`    |     Podjęto próbę utworzenia replikacji między regionami nienależącymi do komunikacji równorzędnej.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     Sprawdź, czy identyfikator zasobu zdalnego jest IDENTYFIKATORem zasobu woluminu.    |

## <a name="errors-authorizing-volume"></a>Błędy autoryzacji woluminu  

|     Komunikat o błędzie    |     Rozwiązanie    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     `RemoteResourceID`Brak lub nieprawidłowy w interfejsie użytkownika lub żądanie interfejsu API (Usuń komunikat o błędzie).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     `RemoteResourceID`Brak lub nieprawidłowy w interfejsie użytkownika lub w żądaniu interfejsu API.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     Sprawdź   `RemoteResourceID` , czy użytkownik jest prawidłowy, czy istnieje.    |
|     `Remote volume   '{0}' is not configured for replication`    |     Wolumin docelowy nie jest woluminem ochrony danych.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     Wolumin ochrony danych nie ma tego woluminu źródłowego w IDENTYFIKATORze zasobu zdalnego (wprowadzono nieprawidłowy identyfikator źródła).    |
|     `The   destination volume replication creation failed (message: {0})`    |     Ten błąd wskazuje na błąd serwera. Skontaktuj się z pomocą techniczną.    |

## <a name="errors-deleting-replication"></a>Błędy podczas usuwania replikacji

|     Komunikat o błędzie    |     Rozwiązanie    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     Sprawdź, czy replikacja została przerwana, czy nie została zainicjowana i bezczynna (niepowodzenie inicjalizacji).    |
|     `Cannot delete   source replication`    |     Usuwanie replikacji ze strony źródłowej jest niedozwolone. Upewnij się, że usuwasz replikację ze strony docelowej.    |
| `Volume with replication cannot be deleted`  |  Usuń replikację przed usunięciem woluminu. Zobacz [Usuwanie replikacji](cross-region-replication-delete.md). Ta operacja wymaga przerwania komunikacji równorzędnej przed usunięciem replikacji woluminu. 

## <a name="errors-resyncing-volume"></a>Błędy podczas ponownej synchronizacji woluminu

|     Komunikat o błędzie    |     Rozwiązanie    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Sprawdź, czy replikacja woluminu jest w stanie "uszkodzona".    |

## <a name="errors-deleting-snapshot"></a>Błędy usuwania migawki 

|     Komunikat o błędzie    |     Rozwiązanie    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Sprawdź, czy replikacja woluminu została przerwana, jeśli chcesz usunąć tę migawkę.    |
|     `Cannot delete   volume replication generated snapshot`    |     Usuwanie migawek linii bazowej replikacji jest niedozwolone.    |

## <a name="next-steps"></a>Następne kroki  

* [Replikacja między regionami](cross-region-replication-introduction.md)
* [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md)
* [Tworzenie komunikacji równorzędnej replikacji](cross-region-replication-create-peering.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Zarządzanie odzyskiwaniem po awarii](cross-region-replication-manage-disaster-recovery.md)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)

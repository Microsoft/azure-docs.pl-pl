---
title: Często zadawane pytania dotyczące Azure Cosmos DB funkcji przywracania do punktu w czasie.
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących funkcji przywracania do momentu Azure Cosmos DB, która została osiągnięta przy użyciu trybu ciągłej kopii zapasowej.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393228"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Często zadawane pytania dotyczące funkcji przywracania do momentu Azure Cosmos DB (wersja zapoznawcza)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera listę często zadawanych pytań dotyczących funkcji przywracania do punktu w czasie Azure Cosmos DB (wersja zapoznawcza), która jest realizowana przy użyciu trybu ciągłej kopii zapasowej.

## <a name="how-much-time-does-it-takes-to-restore"></a>Ile czasu trwa przywracanie?
Czas trwania przywracania zależy od rozmiaru danych.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Czy mogę przesłać czas przywracania w czasie lokalnym?
Przywracanie może nie nastąpić w zależności od tego, czy w danym momencie istniały kluczowe zasoby, takie jak bazy danych lub kontenery. Możesz sprawdzić, wprowadzając czas i sprawdzając wybraną bazę danych lub kontener w danym momencie. Jeśli nie istnieją żadne zasoby, które nie istnieją do przywrócenia, proces przywracania nie działa.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Jak mogę śledzić, czy konto jest przywracane?
Po przesłaniu polecenia Restore i poczekaj na tę samą stronę po zakończeniu operacji na pasku stanu zostanie wyświetlony komunikat o błędzie przywrócony pomyślnie. Możesz również wyszukać przywrócone konto i [śledzić stan przywracanego konta](continuous-backup-restore-portal.md#track-restore-status). Podczas przywracania trwa *Tworzenie* stanu konta. po zakończeniu operacji przywracania stan konta zmieni się na *online*.

Podobnie w przypadku programu PowerShell i interfejsu wiersza polecenia można śledzić postęp operacji przywracania, wykonując `az cosmosdb show` polecenie w następujący sposób:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

ProvisioningState ukazuje *się pomyślnie* , gdy konto jest w trybie online.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Jak mogę sprawdzić, czy konto zostało przywrócone z innego konta?
Uruchom `az cosmosdb show` polecenie w danych wyjściowych, aby zobaczyć, że wartość `createMode` właściwości. Jeśli wartość jest ustawiona na **przywracanie**. oznacza to, że konto zostało przywrócone z innego konta. `restoreParameters`Właściwość zawiera dalsze szczegóły, takie jak `restoreSource` , które mają identyfikator konta źródłowego. Ostatni identyfikator GUID w `restoreSource` parametrze jest identyfikator InstanceId konta źródłowego.

Na przykład w następujących danych wyjściowych identyfikator wystąpienia konta źródłowego to *7b4bb-f6a0-430E-ade1-638d781830cc*

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Co się stanie w przypadku przywrócenia udostępnionej bazy danych przepływności lub kontenera w ramach udostępnionej bazy danych przepływności?
Zostanie przywrócona cała udostępniona baza danych przepływności. Nie można wybrać podzestawu kontenerów w udostępnionej bazie danych przepływności na potrzeby przywracania.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Co to jest użycie obiektu InstanceID w definicji konta?
W dowolnym momencie, Azure Cosmos DB `accountName` Właściwość konta jest globalnie unikatowa, gdy jest aktywny. Jednak po usunięciu konta można utworzyć inne konto o tej samej nazwie, w związku z czym "AccountName" nie jest już wystarczające do zidentyfikowania wystąpienia konta. 

Identyfikator lub `instanceId` jest właściwością wystąpienia konta i jest używany do odróżnienia na wielu kontach (na żywo i usunięte), jeśli mają taką samą nazwę w przypadku przywracania. Identyfikator wystąpienia można uzyskać, uruchamiając `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` polecenia lub. Wartość atrybutu Name oznacza "InstanceID".

## <a name="next-steps"></a>Następne kroki

* Co to jest tryb [ciągłej kopii zapasowej](continuous-backup-restore-introduction.md) ?
* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.
* [Model zasobów trybu ciągłej kopii zapasowej](continuous-backup-restore-resource-model.md)
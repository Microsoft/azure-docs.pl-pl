---
title: Dzienniki inspekcji zaświadczania platformy Azure
description: Zawiera opis pełnych dzienników inspekcji zbieranych na potrzeby zaświadczania platformy Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95759029"
---
# <a name="audit-logs-for-azure-attestation"></a>Dzienniki inspekcji dla zaświadczania platformy Azure

Dzienniki inspekcji są bezpieczne, niezmienne, sygnatury czasowe zdarzeń dyskretnych, które wystąpiły w czasie. Te dzienniki przechwytują ważne zdarzenia, które mogą mieć wpływ na funkcjonalność wystąpienia zaświadczania.

Zaświadczanie platformy Azure zarządza wystąpieniami zaświadczania i skojarzonymi z nimi zasadami. Akcje związane z zarządzaniem wystąpieniem i zmianami zasad są poddawane inspekcji i rejestrowane.

Ten artykuł zawiera informacje o rejestrowanych zdarzeniach, zebranych informacjach i lokalizacji tych dzienników.

## <a name="about-audit-logs"></a>Informacje o dziennikach inspekcji

Zaświadczanie platformy Azure używa kodu do tworzenia dzienników inspekcji zdarzeń, które mają wpływ na sposób zaświadczania. Zwykle jest to zagotowanie w zależności od tego, jak i kiedy zmiany zasad są wprowadzane do wystąpienia zaświadczania, a także niektóre akcje administracyjne.

### <a name="auditable-events"></a>Zdarzenia podlegające inspekcji
Oto niektóre z dzienników inspekcji, które zbieramy:

|     Zdarzenie/interfejs API                              |     Opis zdarzenia                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Utwórz wystąpienie                        |     Tworzy nowe wystąpienie usługi zaświadczania. |
|     Zniszcz wystąpienie                       |     Niszczy wystąpienie usługi zaświadczania. |
|     Dodaj certyfikat zasad                 |     Dodanie certyfikatu do bieżącego zestawu certyfikatów zarządzania zasadami. |
|     Usuń certyfikat zasad              |     Usuń certyfikat z bieżącego zestawu certyfikatów zarządzania zasadami. |
|     Ustaw bieżące zasady                     |     Ustawia zasady zaświadczania dla danego typu TEE. |
|     Resetuj zasady zaświadczania               |     Resetuje zasady zaświadczania dla danego typu TEE. |
|     Przygotowanie do aktualizacji zasad               |     Przygotuj się, aby zaktualizować zasady zaświadczania dla danego typu TEE. |
|     Odwodne dzierżawy po awarii       |     Ponowne zamknięcia wszystkich dzierżawców zaświadczania w tym wystąpieniu usługi zaświadczania. Może to być wykonywane tylko przez administratorów usługi zaświadczania. |

### <a name="collected--information"></a>Zebrane informacje
W przypadku każdego z tych zdarzeń zaświadczanie platformy Azure zbiera następujące informacje:

- Nazwa operacji
- Powodzenie operacji
- Obiekt wywołujący operacji, który może być jednym z następujących elementów:
    - Nazwa UPN usługi Azure AD
    - Identyfikator obiektu
    - Certyfikat
    - Identyfikator dzierżawy usługi Azure AD
- Wartość docelowa operacji, która może być dowolną z następujących:
    - Środowisko
    - Region usługi
    - Rola usługi
    - Wystąpienie roli usługi
    - Identyfikator zasobu
    - Region zasobów

### <a name="sample-audit-log"></a>Przykładowy dziennik inspekcji

Dzienniki inspekcji są udostępniane w formacie JSON. Oto przykład tego, jak może wyglądać dziennik inspekcji.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Dzienniki inspekcji dostępu

Te dzienniki są przechowywane na platformie Azure i nie można uzyskać do nich bezpośredniego dostępu. Jeśli chcesz uzyskać dostęp do tych dzienników, należy uzyskać bilet pomocy technicznej. Aby uzyskać więcej informacji, zobacz [Contact pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/). 

Po zgłoszeniu biletu pomocy technicznej firma Microsoft pobierze i udostępni dostęp do tych dzienników.

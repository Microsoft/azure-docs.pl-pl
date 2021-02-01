---
title: Zarządzanie zasadami rejestrowania — Azure
description: W tym temacie wyjaśniono, jak zarządzać zasadami rejestrowania.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224993"
---
# <a name="manage-recording-policy"></a>Zarządzanie zasadami nagrywania

Korzystając z usługi Analiza filmów wideo na żywo, możesz IoT Edge na potrzeby [ciągłego nagrywania wideo](continuous-video-recording-concept.md), dzięki czemu możesz nagrać wideo w chmurze przez tygodnie lub miesiące. Możesz zarządzać długością (w dniach) tego archiwum chmury, korzystając z narzędzi do [zarządzania cyklem życia](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) wbudowanych w usługę Azure Storage.  

Twoje konto usługi multimediów jest połączone z kontem magazynu platformy Azure, a po zarejestrowaniu wideo w chmurze zawartość jest zapisywana w obszarze [zasobów](../latest/assets-concept.md)usługi Media. Każdy element zawartości jest mapowany na kontener na koncie magazynu. Zarządzanie cyklem życia umożliwia definiowanie [zasad](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) dla konta magazynu, w którym można określić [regułę](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) taką jak następujące.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

Powyższa reguła:

* Dotyczy wszystkich blokowych obiektów BLOB na koncie magazynu.
* Określa, że po upływie okresu ważności obiektów BLOB przez 30 dni są one przenoszone z [warstwy dostępu gorąca do chłodna](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).
* Po osiągnięciu wieku obiektów BLOB powyżej 90 dni zostaną one usunięte.

W przypadku używania usługi Live Video Analytics do rejestrowania w elemencie zawartości należy określić `segmentLength` Właściwość informującą moduł, aby zagregł minimalny czas trwania wideo (w sekundach) przed zapisaniem w chmurze. Element zawartości będzie zawierać serię segmentów, z których każdy ma sygnaturę czasową utworzenia `segmentLength` nowszą od poprzedniej. Gdy zasady zarządzania cyklem życia są uruchamiane w programie, usuwa segmenty starsze niż określony próg. Jednak nadal będzie można uzyskiwać dostęp do pozostałych segmentów za pośrednictwem interfejsów API usługi Media Service i odtwarzać je. Aby uzyskać więcej informacji, zobacz [odtwarzanie zwrotne](playback-recordings-how-to.md). 

## <a name="limitations"></a>Ograniczenia

Poniżej przedstawiono niektóre znane ograniczenia dotyczące zarządzania cyklem życia:

* W ramach zasad można mieć co najwyżej 100 reguł, a Każda reguła może zawierać maksymalnie 10 kontenerów. W związku z tym jeśli trzeba mieć różne zasady rejestrowania (na przykład archiwum 3-dniowe dla aparatu z ilością miejsca parkingowego, 30 dni dla aparatu w Docku ładowania i 180 dni dla aparatu za licznik wyewidencjonowania), za pomocą jednego konta usługi Media Service można dostosować reguły dla maksymalnie 1000 kamer.
* Aktualizacje zasad zarządzania cyklem życia nie są natychmiastowe. Aby uzyskać więcej informacji, zobacz [sekcję często zadawanych pytań](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) .
* Jeśli zdecydujesz się zastosować zasady, gdy obiekty blob zostaną przeniesione do warstwy chłodna, może to wpłynąć na odtwarzanie tej części archiwum. Mogą pojawić się dodatkowe opóźnienia lub sporadyczne błędy. Media Services nie obsługuje odtwarzania zawartości w warstwie archiwum.

## <a name="next-steps"></a>Następne kroki

[Odtwarzanie nagrań](playback-recordings-how-to.md)

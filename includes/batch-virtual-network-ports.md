---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 02/16/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: baf146bdd89d45c5d7e1ed359822a35d383b7b6c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561932"
---
### <a name="general-requirements"></a>Wymagania ogólne

* Sieć wirtualna musi znajdować się w tej samej subskrypcji i w tym samym regionie co konto usługi Batch użyte do utworzenia puli.

* Podsieć określona dla puli musi mieć wystarczającą liczbę nieprzypisanych adresów IP do obsługi maszyn wirtualnych przeznaczony dla puli, czyli sumę właściwości puli `targetDedicatedNodes` i `targetLowPriorityNodes`. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, pula częściowo przydzieli węzły obliczeniowe, a następnie wystąpi błąd dotyczący zmiany rozmiaru.

* Punkt końcowy usługi Azure Storage musi być rozpoznawany przez dowolne niestandardowe serwery DNS, które obsługują sieć wirtualną. W szczególności rozpoznawalne powinny być adresy URL formularzy `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` i `<account>.blob.core.windows.net`.

* Można utworzyć wiele pul w tej samej sieci wirtualnej lub w tej samej podsieci (o ile ma wystarczającą przestrzeń adresową). Jedna pula nie może istnieć między wieloma sieci wirtualnychami lub podsieciami.

Dodatkowe wymagania dotyczące sieci wirtualnej różnią się w zależności od tego, czy pula usługi Batch znajduje się w konfiguracji maszyny wirtualnej, czy w konfiguracji usługi Cloud Services. W przypadku nowych wdrożeń puli w sieci wirtualnej zalecana jest konfiguracja maszyny wirtualnej.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

**Obsługiwane sieci wirtualne** — tylko sieci wirtualne oparte na usłudze Azure Resource Manager

**Identyfikator podsieci** — w przypadku określenia podsieci za pomocą interfejsów API usługi Batch użyj *identyfikatora zasobu* podsieci. Identyfikator podsieci ma postać:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**Uprawnienia** — sprawdź, czy zasady zabezpieczeń lub blokady dla subskrypcji lub grupy zasobów sieci wirtualnej ograniczają uprawnienia użytkownika do zarządzania siecią wirtualną.

**Dodatkowe zasoby sieciowe** — Partia zadań automatycznie tworzy dodatkowe zasoby sieciowe w grupie zasobów zawierającej sieć wirtualną.

> [!IMPORTANT]
> W przypadku 100 każdego węzła dedykowanego lub o niskim priorytecie usługa Batch tworzy: jedną siećową grupę zabezpieczeń (sieciowej grupy zabezpieczeń), jeden publiczny adres IP i jeden moduł równoważenia obciążenia. Te zasoby są ograniczone przez [limity zasobów](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. W przypadku dużych pul może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z tych zasobów.

#### <a name="network-security-groups-batch-default"></a>Sieciowe grupy zabezpieczeń: domyślna Sekwencja wsadowa

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, aby można było zaplanować zadania w węzłach obliczeniowych, a komunikacja wychodząca do komunikacji z usługą Azure Storage lub innymi zasobami, zgodnie z potrzebami obciążeń. W przypadku pul w konfiguracji maszyny wirtualnej wsadowe dodaje sieciowych grup zabezpieczeń na poziomie interfejsów sieciowych (nic) dołączonego do węzłów obliczeniowych. Te sieciowych grup zabezpieczeń są konfigurowane przy użyciu następujących reguł dodatkowych:

* Ruch przychodzący TCP na portach 29876 i 29877 z adresów IP usługi Batch, które odpowiadają `BatchNodeManagement` tagowi usługi.
* Ruch przychodzący protokołu TCP na port 22 (węzły z systemem Linux) lub port 3389 (węzły z systemem Windows), aby zezwolić na dostęp zdalny. W przypadku niektórych typów zadań z wieloma wystąpieniami w systemie Linux (na przykład MPI) należy również zezwolić na ruch z portu SSH 22 dla adresów IP w podsieci zawierającej węzły obliczeniowe usługi Batch. Może to być zablokowane dla reguł sieciowej grupy zabezpieczeń na poziomie podsieci (patrz poniżej).
* Ruch wychodzący na dowolny port do sieci wirtualnej. Może to być zmienione dla reguł sieciowej grupy zabezpieczeń na poziomie podsieci (patrz poniżej).
* Ruch wychodzący na dowolnym porcie do Internetu. Może to być zmienione dla reguł sieciowej grupy zabezpieczeń na poziomie podsieci (patrz poniżej).

> [!IMPORTANT]
> Należy zachować ostrożność w przypadku modyfikacji lub dodania reguł ruchu przychodzącego lub wychodzącego w sieciowych grup zabezpieczeń skonfigurowanym przez partię. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciowej grupy zabezpieczeń, usługa Batch ustawi stan węzłów obliczeniowych na **niezdatny do użytku**. Ponadto nie należy stosować blokad zasobów do żadnych zasobów utworzonych w usłudze Batch, ponieważ może to uniemożliwić Oczyszczanie zasobów w wyniku akcji inicjowanych przez użytkownika, takich jak usuwanie puli.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Sieciowe grupy zabezpieczeń: Określanie reguł poziomu podsieci

Nie trzeba określać sieciowych grup zabezpieczeń na poziomie podsieci sieci wirtualnej, ponieważ usługa Batch konfiguruje własny sieciowych grup zabezpieczeń (Zobacz powyżej). Jeśli istnieje sieciowej grupy zabezpieczeń skojarzona z podsiecią, w której wdrożone są węzły obliczeniowe wsadowe, lub jeśli chcesz zastosować niestandardowe reguły sieciowej grupy zabezpieczeń w celu zastąpienia ustawień domyślnych, należy skonfigurować ten sieciowej grupy zabezpieczeń przy użyciu co najmniej reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego przedstawionych w poniższych tabelach.

Skonfiguruj ruch przychodzący na porcie 3389 (Windows) lub 22 (Linux) tylko wtedy, gdy musisz zezwolić na dostęp zdalny do węzłów obliczeniowych ze źródeł zewnętrznych. Może być konieczne włączenie reguł portów 22 w systemie Linux, jeśli wymagana jest obsługa zadań z wieloma wystąpieniami z pewnymi środowiskami uruchomieniowymi MPI. Zezwalanie na ruch na tych portach nie jest absolutnie wymagane do użycia w węzłach obliczeniowych puli.

> [!WARNING]
> Adresy IP usługi Batch mogą ulec zmianie z upływem czasu. W związku z tym zdecydowanie zalecamy użycie `BatchNodeManagement` znacznika usługi (lub wariantu regionalnej) dla reguł sieciowej grupy zabezpieczeń wskazanych w poniższych tabelach. Unikaj wypełniania reguł sieciowej grupy zabezpieczeń przy użyciu określonych adresów IP usługi Batch.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Tag usługi źródłowej | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- | --- |
| Nie dotyczy | `BatchNodeManagement`[tag usługi](../articles/virtual-network/network-security-groups-overview.md#service-tags) (jeśli jest używany wariant regionalny w tym samym regionie, w którym znajduje się konto usługi Batch) | * | Dowolne | 29876-29877 | TCP | Zezwalaj |
| Adresy IP źródeł użytkowników umożliwiające zdalne uzyskiwanie dostępu do węzłów obliczeniowych i/lub podsieć węzłów obliczeniowych dla zadań z wielowystąpieniami systemu Linux, jeśli jest to wymagane. | Nie dotyczy | * | Dowolne | 3389 (Windows), 22 (Linux) | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Docelowy tag usługi | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- | --- |
| Dowolne | * | [Tag usługi](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `Storage` (Jeśli używany jest odmiana regionalna, w tym samym regionie, w którym znajduje się konto usługi Batch) | 443 | TCP | Zezwalaj |
| Dowolne | * | [Tag usługi](../articles/virtual-network/network-security-groups-overview.md#service-tags) | `BatchNodeManagement` (Jeśli używany jest odmiana regionalna, w tym samym regionie, w którym znajduje się konto usługi Batch) | 443 | TCP | Zezwalaj |

Do `BatchNodeManagement` nawiązania połączenia z usługą Batch z węzłów obliczeniowych, takich jak zadania Menedżera zadań, jest wymagany ruch wychodzący.

### <a name="pools-in-the-cloud-services-configuration"></a>Pule w konfiguracji usługi Cloud Services

> [!WARNING]
> Pule konfiguracji usługi w chmurze są przestarzałe. Zamiast tego użyj pul konfiguracji maszyny wirtualnej.

**Obsługiwane sieci wirtualne** — tylko klasyczne sieci wirtualne

**Identyfikator podsieci** — w przypadku określenia podsieci za pomocą interfejsów API usługi Batch użyj *identyfikatora zasobu* podsieci. Identyfikator podsieci ma postać:

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**Uprawnienia** — jednostka `Microsoft Azure Batch` usługi musi mieć `Classic Virtual Machine Contributor` rolę platformy Azure dla określonej sieci wirtualnej.

#### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch, co umożliwia planowanie zadań w węzłach obliczeniowych, i na komunikację wychodzącą na potrzeby komunikacji z usługą Azure Storage lub innymi zasobami.

Nie trzeba określać sieciowej grupy zabezpieczeń, ponieważ usługa Batch konfiguruje komunikację przychodzącą tylko z adresów IP usługi Batch do węzłów puli. Jednak jeśli określona sieć wirtualna ma skojarzone sieciowe grupy zabezpieczeń i/lub zaporę, skonfiguruj reguły zabezpieczeń ruchu przychodzącego i wychodzącego tak, jak pokazano w poniższych tabelach. Jeśli komunikacja z węzłami obliczeniowymi w określonej podsieci zostanie odrzucona przez sieciowej grupy zabezpieczeń, usługa Batch ustawia stan węzłów obliczeniowych na **niezdatny do użytku**.

Skonfiguruj ruch przychodzący na porcie 3389 dla systemu Windows, jeśli chcesz zezwolić na dostęp RDP do węzłów puli. Nie jest to wymagane, aby węzły puli były użyteczne.

**Reguły zabezpieczeń dla ruchu przychodzącego**

| Źródłowe adresy IP | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- |
Dowolne <br /><br />Mimo że w praktyce wymaga to zezwolenia na cały ruch, usługa Batch stosuje regułę listy ACL filtrującą wszystkie adresy IP nienależące do usługi Batch na poziomie każdego węzła. | * | Dowolne | 10100, 20100, 30100 | TCP | Zezwalaj |
| Opcjonalne, aby umożliwić dostęp RDP do węzłów obliczeniowych. | * | Dowolne | 3389 | TCP | Zezwalaj |

**Reguły zabezpieczeń dla ruchu wychodzącego**

| Element źródłowy | Porty źródłowe | Element docelowy | Porty docelowe | Protokół | Akcja |
| --- | --- | --- | --- | --- | --- |
| Dowolne | * | Dowolne | 443  | Dowolne | Zezwalaj |

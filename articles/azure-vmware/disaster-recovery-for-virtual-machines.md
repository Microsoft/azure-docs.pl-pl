---
title: Ukończ odzyskiwanie po awarii maszyn wirtualnych
description: W tym artykule pokazano, jak ukończyć odzyskiwanie po awarii maszyn wirtualnych przy użyciu rozwiązania Azure VMware
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 688d91bc181e1479f5090a10af4b3b262d7ddb7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779615"
---
# <a name="complete-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Ukończ odzyskiwanie po awarii maszyn wirtualnych przy użyciu rozwiązania Azure VMware

Ten artykuł zawiera proces kończenia odzyskiwania po awarii maszyn wirtualnych z rozwiązaniem VMware HCX i korzystania z chmury prywatnej rozwiązania VMware platformy Azure jako lokacji odzyskiwania lub docelowej.

Program VMware HCX udostępnia różne operacje zapewniające ścisłą kontrolę i stopień szczegółowości zasad replikacji. Dostępne są następujące operacje:

- **Wstecz** — po wystąpieniu awarii. Odwróć pomaga utworzyć lokację w lokacji źródłowej i lokacji a, w której znajduje się chroniona maszyna wirtualna.

- **Wstrzymaj** — Wstrzymaj bieżące zasady replikacji skojarzone z WYBRANą maszyną wirtualną.

- **Wznów** — Wznów bieżące zasady replikacji skojarzone z WYBRANą maszyną wirtualną.

- **Usuń** — usuwa bieżące zasady replikacji skojarzone z WYBRANą maszyną wirtualną.

- **Synchronizuj teraz** — poza powiązaniem ŹRÓDŁOWEJ maszyny wirtualnej synchronizacji z CHRONIONą maszyną wirtualną.

Ten przewodnik obejmuje następujące scenariusze replikacji:

- Ochrona maszyny wirtualnej lub grupy maszyn wirtualnych.

- Ukończ test odzyskiwania maszyny wirtualnej lub grupy maszyn wirtualnych.

- Odzyskaj maszynę wirtualną lub grupę maszyn wirtualnych.

- Odwracanie ochrony maszyny wirtualnej lub grupy maszyn wirtualnych.

## <a name="protect-vms"></a>Ochrona maszyn wirtualnych

1. Zaloguj się do **klienta vSphere** w lokacji źródłowej i uzyskaj dostęp do **wtyczki HCX**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="Opcja HCX w vSphere" border="true":::

1. Wprowadź obszar **odzyskiwania po awarii** i wybierz pozycję **Chroń maszyny wirtualne**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Wybierz pozycję Chroń maszyny wirtualne" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Wybierz źródło i lokacje zdalne. Zdalna witryna w tym przypadku powinna być chmurą prywatną rozwiązania Azure VMware.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="okno ochrony maszyn wirtualnych" border="true":::

1. W razie konieczności wybierz **domyślne opcje replikacji** :

   - **Włącz kompresję:** Zalecane w przypadku scenariuszy o niskiej przepływności.

   - **Włącz Quiescence:** Wstrzymuje maszynę wirtualną, aby upewnić się, że spójna kopia jest synchronizowana z lokacją zdalną.

   - **Magazyn docelowy:** Zdalny magazyn danych dla chronionych maszyn wirtualnych oraz w prywatnej chmurze rozwiązania VMware platformy Azure, który powinien być magazynem danych sieci vSAN.

   - **Kontener obliczeniowy:** Zdalny klaster vSphere lub Pula zasobów.

   - **Folder docelowy:** Zdalny folder docelowy, który jest opcjonalny, a jeśli folder nie jest zaznaczony, maszyny wirtualne są umieszczane bezpośrednio w wybranym klastrze.

   - **Cel punktu odzyskiwania:** Interwał synchronizacji między źródłową maszyną wirtualną a chronioną maszyną wirtualną. Może ona znajdować się w dowolnym miejscu od 5 minut do 24 godzin.

   - **Interwał migawek:** Interwał między migawkami.

   - **Liczba migawek:** Łączna liczba migawek w skonfigurowanym interwale migawek.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="opcje ochrony maszyn wirtualnych" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Wybierz co najmniej jedną maszynę wirtualną z listy i skonfiguruj opcje replikacji zgodnie z wymaganiami.

   Domyślnie maszyny wirtualne dziedziczą globalne zasady ustawień skonfigurowane w domyślnych opcjach replikacji. Dla każdego interfejsu sieciowego na wybranej maszynie wirtualnej Skonfiguruj zdalną **grupę portów sieci** i wybierz pozycję **Zakończ** , aby rozpocząć proces ochrony.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Opcje interfejsu sieciowego" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Monitoruj proces dla każdej z wybranych maszyn wirtualnych w tym samym obszarze odzyskiwania po awarii.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="monitorowanie postępu ochrony" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Po włączeniu ochrony maszyny wirtualnej można wyświetlić różne migawki na karcie **migawki** .

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="Lista migawek" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Żółty trójkąt oznacza, że migawki i maszyny wirtualne nie zostały przetestowane w testowej operacji odzyskiwania.

   Istnieją kluczowe różnice między maszyną wirtualną, która jest wyłączona, a jednym włączonym. Obraz przedstawia proces synchronizacji dla maszyny wirtualnej z obsługą. Uruchamia proces synchronizacji do momentu zakończenia pierwszej migawki, która jest pełną kopią maszyny wirtualnej, a następnie kończy kolejne z nich w skonfigurowanym interwale. Synchronizuje kopię dla wyłączonej maszyny wirtualnej, a następnie maszyna wirtualna jest wyświetlana jako nieaktywna, a operacja ochrony jest pokazywana jako ukończona.  Gdy maszyna wirtualna jest włączona, rozpocznie proces synchronizacji do zdalnej lokacji.

## <a name="complete-a-test-recover-of-vms"></a>Ukończ odzyskiwanie testów maszyn wirtualnych

1. Zaloguj się do **VSphere Client** w zdalnej witrynie, która jest chmurą prywatną rozwiązania Azure VMware. 
1. W ramach **wtyczki HCX** w obszarze odzyskiwania po awarii wybierz wielokropek pionowy na dowolnej maszynie wirtualnej, aby wyświetlić menu operacje, a następnie wybierz pozycję **Testuj odzyskiwanie maszyny wirtualnej**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Wybierz test odzyskiwania maszyny wirtualnej" border="true":::

1. Wybierz opcje dla testu i migawkę, która ma być używana do testowania różnych stanów maszyny wirtualnej.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Wybierz migawkę i wybierz test" border="true":::

1. Po wybraniu **testu** rozpocznie się operacja odzyskiwania.

1. Po zakończeniu możesz sprawdzić nową maszynę wirtualną w rozwiązaniu Azure VMware Private Cloud chmurowego vCenter.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="Sprawdź operację odzyskiwania" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Po zakończeniu testowania na maszynie wirtualnej lub w aplikacji uruchomionej na niej wykonaj czyszczenie, aby usunąć wystąpienie testowe.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Wyczyść wystąpienie testu" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Odzyskiwanie maszyn wirtualnych

1. Zaloguj się do **VSphere Client** w zdalnej witrynie, która jest chmurą prywatną rozwiązania Azure VMware, i uzyskaj dostęp do **wtyczki HCX**.

   W przypadku scenariusza odzyskiwania Grupa maszyn wirtualnych użyta w tym przykładzie.

1. Z listy wybierz maszynę wirtualną do odzyskania, otwórz menu **Akcje** , a następnie wybierz polecenie **Odzyskaj maszyny wirtualne**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="odzyskiwanie maszyn wirtualnych" border="true":::

1. Skonfiguruj opcje odzyskiwania dla każdego wystąpienia i wybierz polecenie **Odzyskaj** , aby rozpocząć operację odzyskiwania.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="potwierdzenie odzyskiwania maszyn wirtualnych" border="true":::

1. Po zakończeniu operacji odzyskiwania nowe maszyny wirtualne będą widoczne w spisie vCenter Server zdalnego.

## <a name="complete-a-reverse-replication-on-vms"></a>Ukończ replikację odwrotną na maszynach wirtualnych

1. Zaloguj się do **klienta vSphere** w chmurze prywatnej rozwiązania platformy Azure VMware i uzyskaj dostęp do **wtyczki HCX**.
   
   >[!NOTE]
   > Przed rozpoczęciem replikacji odwrotnej upewnij się, że oryginalne maszyny wirtualne w lokacji źródłowej są wyłączone. Operacja kończy się niepowodzeniem, jeśli maszyny wirtualne nie są wyłączone.

1. Z listy wybierz maszyny wirtualne do replikacji z powrotem do lokacji źródłowej, otwórz menu **Akcje** i wybierz polecenie **Odwróć**. 
1. Wybierz pozycję **Odwróć** , aby rozpocząć replikację.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Wybierz akcję wsteczną w obszarze Ochrona operacji" border="true":::

1. Monitoruj sekcję szczegóły każdej maszyny wirtualnej.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="Przejrzyj wyniki akcji Odwróć" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automatyzacja planu odzyskiwania po awarii

Program VMware HCX obecnie nie ma wbudowanego mechanizmu do tworzenia i automatyzowania planu odzyskiwania po awarii. Jednak program VMware HCX udostępnia zestaw interfejsów API REST, w tym interfejsów API dla operacji odzyskiwania po awarii. Do specyfikacji interfejsu API można uzyskać dostęp w programie VMware HCX Manager w adresie URL.

Te interfejsy API obejmują następujące operacje odzyskiwania po awarii.

- Ochrona

- Odzyskiwanie

- Odzyskiwanie testowe

- Zaplanowane odzyskiwanie

- Reverse

- Zapytanie

- Czyszczenie testów

- Wstrzymanie

- Wznów

- Usuń ochronę

- Ponowne konfigurowanie

Poniżej przedstawiono przykład ładunku operacji odzyskiwania w formacie JSON.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Za pomocą tych interfejsów API można utworzyć niestandardowy mechanizm do automatyzowania tworzenia i wykonywania planu odzyskiwania po awarii.

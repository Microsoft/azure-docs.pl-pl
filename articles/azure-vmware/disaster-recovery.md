---
title: Wykonaj odzyskiwanie po awarii maszyn wirtualnych
description: W tym artykule pokazano, jak wykonać odzyskiwanie po awarii maszyn wirtualnych przy użyciu funkcji automatycznej synchronizacji
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 5fdfc252486d94fcc22ebba9705fa5e259539921
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148161"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Kończenie odzyskiwania po awarii maszyn wirtualnych przy użyciu rozwiązania Azure VMware

Ten artykuł zawiera proces przeprowadzania odzyskiwania po awarii maszyn wirtualnych przy użyciu rozwiązania VMware hybryd Cloud Extension (HCX) i korzystania z chmury prywatnej rozwiązania VMware platformy Azure jako lokacji odzyskiwania lub docelowej.

Program VMware HCX udostępnia różne operacje zapewniające ścisłą kontrolę i stopień szczegółowości zasad replikacji. Dostępne są następujące operacje:

- Wstecz — po wystąpieniu awarii. Odwróć pomaga utworzyć lokację w lokacji źródłowej i lokacji A, w której znajduje się chroniona maszyna wirtualna.

- Wstrzymaj — Wstrzymaj bieżące zasady replikacji skojarzone z wybraną maszyną wirtualną.

- Wznów — wstrzymuje bieżące zasady replikacji skojarzone z wybraną maszyną wirtualną.

- Usuń — usuwa bieżące zasady replikacji skojarzone z wybraną maszyną wirtualną.

- Synchronizuj teraz — poza powiązaną źródłową maszynę wirtualną synchronizacji z chronioną maszyną wirtualną.

W tym przewodniku omówiono następujące scenariusze replikacji:

- Ochrona maszyny wirtualnej lub grupy maszyn wirtualnych.

- Ukończ test odzyskiwania maszyny wirtualnej lub grupy maszyn wirtualnych.

- Odzyskaj maszynę wirtualną lub grupę maszyn wirtualnych.

- Odwracanie ochrony maszyny wirtualnej lub grupy maszyn wirtualnych.

## <a name="protect-virtual-machines"></a>Ochrona maszyn wirtualnych

Zaloguj się do **klienta vSphere** w lokacji źródłowej i uzyskaj dostęp do **wtyczki HCX**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Opcja HCX w vSphere" border="true":::

Wprowadź obszar **odzyskiwania po awarii** i kliknij pozycję **Chroń maszyny wirtualne**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="Wybierz pozycję Chroń maszyny wirtualne" border="true":::

W otwartym oknie Wybierz źródło i lokacje zdalne, a w tym przypadku lokację zdalną powinna być chmurą prywatną.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="okno ochrony maszyn wirtualnych" border="true":::

W razie konieczności wybierz domyślne opcje replikacji:

- **Włącz kompresję:** Zalecane w przypadku scenariuszy o niskiej przepływności.

- **Włącz Quiescence:** Wstrzymuje maszynę wirtualną, aby upewnić się, że spójna kopia jest synchronizowana z lokacją zdalną.

- **Magazyn docelowy:** Wybierz zdalny magazyn danych dla chronionych maszyn wirtualnych. W chmurze prywatnej automatycznej synchronizacji ten wybór powinien być magazynem danych sieci vSAN.

- **Kontener obliczeniowy:** Zdalny klaster vSphere lub Pula zasobów.

- **Folder docelowy:** Zdalny folder docelowy, to ustawienie jest opcjonalne i jeśli nie wybrano żadnego folderu, maszyny wirtualne będą można wykonać bezpośrednio w wybranym klastrze.

- **Cel punktu odzyskiwania:** Ta wartość to interwał synchronizacji między źródłową maszyną wirtualną a chronioną maszyną wirtualną i może znajdować się w dowolnym miejscu od 5 minut do 24 godzin.

- **Interwał migawek:** Interwał między migawkami.

- **Liczba migawek:** Łączna liczba migawek w skonfigurowanym interwale migawek.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="opcje ochrony maszyn wirtualnych" border="true":::

Wybierz co najmniej jedną maszynę wirtualną z listy i skonfiguruj w razie konieczności maszynę wirtualną z opcjami replikacji.

Domyślnie maszyny wirtualne będą dziedziczyć globalne zasady ustawień skonfigurowane w domyślnych opcjach replikacji. Dla każdego interfejsu sieciowego na wybranej maszynie wirtualnej Skonfiguruj zdalną **grupę portów sieci** i wybierz pozycję **Zakończ** , aby rozpocząć proces ochrony.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="Opcje interfejsu sieciowego" border="true":::

Jak widać na poniższej ilustracji, można monitorować proces dla każdej z wybranych maszyn wirtualnych w tym samym obszarze odzyskiwania po awarii.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="monitorowanie postępu ochrony" border="true":::

Po włączeniu ochrony maszyny wirtualnej różne migawki można zobaczyć na karcie **migawki** .

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="Lista migawek" border="true":::

Żółty trójkąt oznacza, że migawki i wirtualne nie zostały przetestowane w operacji odzyskiwania testowego.

Istnieją kluczowe różnice między maszyną wirtualną, która jest wyłączona, a tą, która jest włączona.
Poprzedni zrzut ekranu przedstawia proces synchronizacji dla maszyny wirtualnej, która jest obsługiwana. Uruchomiono proces synchronizacji do momentu zakończenia pierwszej migawki, która jest pełną kopią maszyny wirtualnej, a następnie kończy kolejne z nich w skonfigurowanym interwale.

W przypadku maszyny wirtualnej z wyłączeniem program zsynchronizuje kopię, a następnie zostanie wyświetlona, gdy operacja ochrony zostanie pokazana jako ukończona.

Po włączeniu maszyny wirtualnej rozpocznie się synchronizowanie procesu z lokacją zdalną.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Ukończ odzyskiwanie testów maszyn wirtualnych

Zaloguj się do **VSphere Client** w zdalnej witrynie, która jest chmurą prywatną. W ramach **wtyczki HCX**w obszarze odzyskiwania po awarii wybierz wielokropek pionowy na dowolnej maszynie wirtualnej, aby wyświetlić menu operacje. Wybierz pozycję **Testuj odzyskiwanie maszyny wirtualnej**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Wybierz test odzyskiwania maszyny wirtualnej" border="true":::

W nowym oknie wybierz Opcje testu. Wybierz migawkę, która ma być używana do testowania różnych stanów maszyny wirtualnej.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="Wybierz migawkę i kliknij pozycję Testuj." border="true":::

Po kliknięciu **testu**zostanie rozpoczęta operacja odzyskiwania.

Po zakończeniu testowego odzyskiwania można sprawdzić nową maszynę wirtualną w programie vCenter w chmurze prywatnej chmury.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="Sprawdź operację odzyskiwania" border="true":::

Na koniec po zakończeniu testowania na maszynie wirtualnej lub dowolna uruchomiona na niej aplikacja wykonuje czyszczenie, aby usunąć wystąpienie testu.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="Wyczyść wystąpienie testu" border="true":::

## <a name="recover-virtual-machines"></a>Odzyskiwanie maszyn wirtualnych

Zaloguj się do **VSphere Client** w zdalnej witrynie, która jest chmurą prywatną do automatycznej synchronizacji, i uzyskaj dostęp do **wtyczki HCX**.

W przypadku scenariusza odzyskiwania Grupa maszyn wirtualnych użyta w tym przykładzie.

Z listy wybierz maszynę wirtualną do odzyskania, otwórz menu **Akcje** , a następnie wybierz polecenie **Odzyskaj maszyny wirtualne**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="odzyskiwanie maszyn wirtualnych" border="true":::

Skonfiguruj opcje odzyskiwania dla każdego wystąpienia i kliknij polecenie **Odzyskaj** , aby rozpocząć operację odzyskiwania.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="potwierdzenie odzyskiwania maszyn wirtualnych" border="true":::

Po zakończeniu operacji odzyskiwania nowe maszyny wirtualne będą widoczne w spisie vCenter Server zdalnego.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Wykonaj replikację odwrotną na maszynach wirtualnych

Zaloguj się do **klienta usługi vSphere** w chmurze prywatnej automatycznej synchronizacji i uzyskaj dostęp do **wtyczki HCX**.
Przed rozpoczęciem replikacji odwrotnej wymagane jest, aby oryginalne maszyny wirtualne w lokacji źródłowej były wyłączone. Operacja kończy się niepowodzeniem, jeśli maszyny wirtualne nie są wyłączone.

Wybierz Maszyny wirtualne, które mają być replikowane z powrotem do lokacji źródłowej z listy, otwórz menu **Akcje** i wybierz polecenie **Odwróć**. W oknie podręcznym kliknij pozycję **Odwróć** , aby rozpocząć replikację.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Wybierz akcję wsteczną w obszarze Ochrona operacji" border="true":::

Replikację można monitorować w sekcji Szczegóły każdej maszyny wirtualnej.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="Przejrzyj wyniki akcji Odwróć" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automatyzacja planu odzyskiwania po awarii

Program VMware HCX obecnie nie ma wbudowanego mechanizmu do tworzenia i automatyzowania planu odzyskiwania po awarii. Ta funkcja nie istnieje w HCX. Udostępnia on jednak zestaw interfejsów API REST, w tym interfejsy API dla operacji odzyskiwania po awarii.

Specyfikacja interfejsu API może mieć dostęp w programie HCX Manager w adresie URL.

Te interfejsy API obejmują następujące operacje odzyskiwania po awarii.

- Ochrona

- Recover

- Odzyskiwanie testowe

- Zaplanowane odzyskiwanie

- Reverse

- Zapytanie

- Czyszczenie testów

- Wstrzymanie

- Wznawianie

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

Korzystając z tych interfejsów API, klient może utworzyć niestandardowy mechanizm do automatyzowania tworzenia i wykonywania planu odzyskiwania po awarii.

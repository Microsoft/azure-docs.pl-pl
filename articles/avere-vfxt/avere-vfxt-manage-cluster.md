---
title: Zarządzanie klastrem avere vFXT — Azure
description: Jak zarządzać klastrem avere — Dodawanie lub usuwanie węzłów, ponowne uruchamianie, zatrzymywanie lub niszczenie klastra vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85505498"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Zarządzanie klastrem Avere vFXT

W pewnym momencie cyklu życia usługi avere vFXT dla klastra platformy Azure może być konieczne dodanie węzłów klastra lub uruchomienie lub ponowne uruchomienie klastra. Po zakończeniu projektu należy wiedzieć, jak zatrzymać klaster i usunąć go trwale.

W tym artykule wyjaśniono, jak dodać lub usunąć węzły klastra i inne podstawowe operacje klastra. Aby zmienić ustawienia klastra lub monitorować jego działanie, użyj [Panelu sterowania avere](avere-vfxt-cluster-gui.md).

W zależności od zadania zarządzania może być konieczne użycie jednego z trzech różnych narzędzi: avere Panel sterowania, skrypt zarządzania klastrami vfxt.py wiersza polecenia i Azure Portal.

Ta tabela zawiera omówienie narzędzi, które mogą być używane dla każdego zadania.

| Akcja | Panel sterowania avere | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Dodawanie węzłów klastra | nie | tak | nie |
| Usuń węzły klastra | tak | nie | nie |
| Zatrzymaj węzeł klastra | tak (można także ponownie uruchomić usługi lub ponowny rozruch) | nie | wyłączenie maszyny wirtualnej węzła z portalu jest interpretowane jako awaria węzła |
| Uruchom zatrzymany węzeł | nie | nie | tak |
| Niszczenie jednego węzła klastra | nie | nie | tak |
| Ponowne uruchamianie klastra |  |  |  |
| Bezpieczne wyłączanie lub zatrzymywanie klastra | tak | tak | nie |
| Zniszcz klaster  | nie | tak | tak, ale integralność danych nie jest gwarantowana |

Szczegółowe instrukcje dotyczące każdego narzędzia znajdują się poniżej.

## <a name="about-stopped-instances-in-azure"></a>Informacje o zatrzymanych wystąpieniach na platformie Azure

Po wyłączeniu lub zatrzymaniu dowolnej maszyny wirtualnej platformy Azure usługa przestaje naliczać opłaty za obliczenia, ale nadal musi być obciążana za magazyn. Jeśli wyłączysz węzeł vFXT lub cały klaster vFXT i nie planujesz go uruchomić ponownie, użyj Azure Portal, aby usunąć powiązane maszyny wirtualne.

W Azure Portal *zatrzymany* węzeł (który można uruchomić ponownie) pokazuje stan **zatrzymany** w Azure Portal. *Usunięty* węzeł pokazuje stan **zatrzymane (cofnięty przydział)** i nie jest już używany w przypadku obliczeń ani opłat za magazyn.

Przed usunięciem maszyny wirtualnej upewnij się, że wszystkie zmienione dane zostały wpisane z pamięci podręcznej do magazynu zaplecza przy użyciu panelu sterowania avere lub opcji vfxt.py, aby zatrzymać lub wyłączyć klaster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Zarządzanie klastrem za pomocą panelu sterowania avere

Panel sterowania avere może służyć do wykonywania następujących zadań:

* Zatrzymywanie lub ponowne uruchamianie poszczególnych węzłów
* Usuwanie węzła z klastra
* Zatrzymywanie lub ponowne uruchamianie całego klastra

Panel sterowania avere ustala priorytety integralności danych, dlatego podejmuje próbę zapisania wszelkich zmienionych danych w magazynie zaplecza przed prawdopodobną operacją. Zapewnia to bezpieczniejsze rozwiązanie niż Azure Portal.

Dostęp do panelu sterowania avere z przeglądarki sieci Web. Postępuj zgodnie z instrukcjami w temacie [dostęp do klastra vFXT,](avere-vfxt-cluster-gui.md) Jeśli potrzebujesz pomocy.

### <a name="manage-nodes-with-avere-control-panel"></a>Zarządzanie węzłami przy użyciu panelu sterowania avere

Strona ustawienia **węzłów FXT** ma kontrolki do zarządzania poszczególnymi węzłami.

Aby zamknąć, ponownie uruchomić lub usunąć węzeł, Znajdź węzeł na liście na stronie **węzły FXT** i kliknij odpowiedni przycisk w kolumnie **Akcje** .

> [!NOTE]
> Adresy IP mogą być przenoszone między węzłami klastra, gdy zmienia się liczba aktywnych węzłów.

Aby uzyskać więcej informacji, przeczytaj temat [Cluster > FXT nodes](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) w przewodniku ustawień klastra avere.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Zatrzymywanie lub ponowne uruchamianie klastra za pomocą panelu sterowania avere

Strona ustawienia **konserwacji systemu** zawiera polecenia służące do ponownego uruchamiania usług klastra, ponownego uruchamiania klastra lub bezpiecznego włączania klastrów. Aby uzyskać szczegółowe informacje, przeczytaj temat [administracja > Konserwacja systemu](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (w podręczniku ustawień klastra avere).

Po rozpoczęciu zamykania klastra komunikaty o stanie są ogłaszane na karcie **pulpit nawigacyjny** . Po kilku chwilach komunikaty zostaną zatrzymane i ostatecznie sesja panelu sterowania avere przestanie odpowiadać, co oznacza, że klaster został zamknięty.

## <a name="manage-the-cluster-with-vfxtpy"></a>Zarządzanie klastrem za pomocą vfxt.py

vfxt.py to narzędzie wiersza polecenia do tworzenia klastrów i zarządzania nimi.

vfxt.py jest wstępnie zainstalowany na maszynie wirtualnej kontrolera klastra. Jeśli chcesz zainstalować ją w innym systemie, zapoznaj się z dokumentacją w witrynie <https://github.com/Azure/AvereSDK> .

Skrypt vfxt.py może służyć do wykonywania następujących zadań zarządzania klastrami:

* Dodawanie nowych węzłów do klastra
* Zatrzymywanie lub uruchamianie klastra  
* Niszczenie klastra

Podobnie jak w panelu sterowania avere, operacje vfxt.py próbują upewnić się, że zmienione dane są trwale przechowywane w magazynie zaplecza przed zamknięciem lub zniszczeniem klastra lub węzła. Zapewnia to bezpieczniejsze rozwiązanie niż Azure Portal.

Pełny Przewodnik użytkowania vfxt.py jest dostępny w witrynie GitHub: [Zarządzanie klastrem w chmurze za pomocą usługi vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Dodawanie węzłów klastra za pomocą vfxt.py

Przykładowy skrypt poleceń służący do dodawania węzłów klastra znajduje się na kontrolerze klastra. Znajdź ``./add-nodes`` kontroler i otwórz go w edytorze, aby dostosować go przy użyciu informacji o klastrze.

Aby można było użyć tego polecenia, klaster musi być uruchomiony.

Podaj następujące wartości:

* Nazwa grupy zasobów dla klastra, a także dla zasobów sieciowych i magazynu, jeśli nie znajdują się w tej samej grupie zasobów co klaster
* Lokalizacja klastra
* Sieć klastrów i podsieć
* Rola dostępu do węzła klastra (Użyj wbudowanego [operatora avere](../role-based-access-control/built-in-roles.md#avere-operator)roli)
* Adres IP i hasło administracyjne zarządzania klastrami
* Liczba węzłów do dodania (1, 2 lub 3)
* Wartości typu wystąpienia węzła i rozmiaru pamięci podręcznej

Jeśli nie używasz prototypu, należy utworzyć polecenie podobne do następujących, w tym wszystkie informacje opisane powyżej.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Aby uzyskać więcej informacji, przeczytaj temat [Dodawanie węzłów do klastra](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) w przewodniku użytkowania vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Zatrzymaj klaster z vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Uruchamianie zatrzymanego klastra z vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Ponieważ klaster jest zatrzymany, należy przekazać identyfikatory wystąpień, aby określić węzły klastra. Aby dowiedzieć się więcej, zobacz temat [Określanie klastra do zmodyfikowania](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) w przewodniku użytkowania vfxt.py.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Niszczenie klastra za pomocą vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Opcja ``--quick-destroy`` może być użyta, jeśli nie chcesz zapisywać zmienionych danych z pamięci podręcznej klastra.

Więcej informacji można znaleźć w [podręczniku użytkowania vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) .

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Zarządzanie maszynami wirtualnymi klastra z poziomu Azure Portal

Azure Portal może służyć do niszczenia pojedynczych maszyn wirtualnych klastra, ale integralność danych nie jest gwarantowana, Jeśli klaster nie zostanie najpierw zamknięty.

Azure Portal można użyć do tych zadań zarządzania klastrami:

* Uruchom zatrzymany węzeł vFXT
* Zatrzymaj pojedynczy węzeł vFXT (klaster interpretuje go jako awarię węzła)
* Zniszcz klaster vFXT, *Jeśli* nie ma potrzeby upewnienia się, że zmienione dane w pamięci podręcznej klastra są zapisywane w podstawowym pliku
* Trwale Usuń węzły vFXT i inne zasoby klastra po ich bezpiecznym zamknięciu

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Uruchom ponownie wystąpienia usługi vFXT z Azure Portal

Jeśli musisz ponownie uruchomić zatrzymany węzeł, musisz użyć Azure Portal. Wybierz pozycję **maszyny wirtualne** w menu po lewej stronie, a następnie kliknij nazwę maszyny wirtualnej na liście, aby otworzyć jej stronę przeglądu.

Kliknij przycisk **Start** w górnej części strony przegląd, aby ponownie UAKTYWNIć maszynę wirtualną.

![Azure Portal ekranu z opcją uruchomienia zatrzymanej maszyny wirtualnej](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Usuwanie węzłów klastra

Jeśli chcesz usunąć jeden węzeł z klastra vFXT, ale zachować resztę klastra, należy najpierw [usunąć węzeł z klastra](#manage-nodes-with-avere-control-panel) za pomocą panelu sterowania avere.

> [!CAUTION]
> Jeśli usuniesz węzeł bez wcześniejszego usunięcia go z klastra vFXT, dane mogą zostać utracone.

Aby trwale zniszczyć co najmniej jedno wystąpienie używane jako węzeł vFXT, użyj Azure Portal.
Wybierz pozycję **maszyny wirtualne** w menu po lewej stronie, a następnie kliknij nazwę maszyny wirtualnej na liście, aby otworzyć jej stronę przeglądu.

Kliknij przycisk **Usuń** w górnej części strony przegląd, aby trwale zniszczyć maszynę wirtualną.

Tej metody można użyć do trwałego usunięcia węzłów klastra po ich ponownym zamknięciu.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Zniszcz klaster z Azure Portal

> [!NOTE]
> Jeśli chcesz, aby wszystkie pozostałe zmiany klienta w pamięci podręcznej były zapisywane w magazynie zaplecza, użyj opcji vfxt.py `--destroy` lub panelu sterowania avere, aby zamknąć klaster w sposób czysty przed usunięciem wystąpień węzła w Azure Portal.

Wystąpienia węzłów można zniszczyć trwale, usuwając je w Azure Portal. Można je usunąć pojedynczo, zgodnie z powyższym opisem, lub użyć strony **Virtual Machines** , aby znaleźć wszystkie maszyny wirtualne klastra, zaznacz je z polami wyboru i kliknij przycisk **Usuń** , aby usunąć je wszystkie w jednej akcji.

![Lista maszyn wirtualnych w portalu filtrowana według terminu "klaster" z trzema z czterech zaznaczonych i wyróżnionych](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Usuń dodatkowe zasoby klastra z Azure Portal

Jeśli utworzono dodatkowe zasoby przeznaczone dla klastra vFXT, można je usunąć w ramach rozrywania klastra. Nie niszczy elementów zawierających potrzebne dane lub elementów, które są współużytkowane z innymi projektami.

Oprócz usuwania węzłów klastra należy rozważyć usunięcie tych składników:

* Maszyna wirtualna kontrolera klastra
* Dyski danych skojarzone z węzłami klastra
* Interfejsy sieciowe i publiczne adresy IP skojarzone ze składnikami klastra
* Sieci wirtualne
* Kontenery magazynu i konta magazynu (**tylko** wtedy, gdy nie zawierają ważnych danych)
* Zestaw dostępności

![Lista Azure Portal "wszystkie zasoby" pokazująca zasoby utworzone dla klastra testowego](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Usuwanie grupy zasobów klastra z Azure Portal

Jeśli grupa zasobów została utworzona w celu przechowania klastra, można zniszczyć wszystkie powiązane zasoby klastra, niszczyjąc grupę zasobów.

> [!Caution]
> Należy zniszczyć grupę zasobów tylko wtedy, gdy masz pewność, że żadne wartości nie znajdują się w grupie. Na przykład upewnij się, że wszystkie potrzebne dane zostały przeniesione z dowolnego kontenera magazynu w grupie zasobów.  

Aby usunąć grupę zasobów, kliknij pozycję **grupy zasobów** w menu po lewej stronie portalu, a następnie Przefiltruj listę grup zasobów, aby znaleźć tę utworzoną dla klastra vFXT. Wybierz grupę zasobów i kliknij trzy kropki po prawej stronie panelu. Wybierz pozycję **Usuń grupę zasobów**. W portalu zostanie wyświetlony komunikat z informacją o potwierdzeniu usunięcia, które jest nieodwracalne.

![Grupa zasobów zawierająca akcję "Usuń grupę zasobów"](media/avere-vfxt-delete-resource-group.png)

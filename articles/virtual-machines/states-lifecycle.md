---
title: Cykl życia i Stany maszyny wirtualnej na platformie Azure
description: Omówienie cyklu życia maszyny wirtualnej na platformie Azure, w tym opisy różnych stanów, w których maszyna wirtualna może znajdować się w dowolnym momencie.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 8c9641e722cf3892450bbfbea54f169ac6dc764b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963299"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Cykl życia i Stany maszyn wirtualnych

Usługa Azure Virtual Machines (VM) umożliwia przechodzenie między różnymi stanami, które można podzielić na możliwości *aprowizacji* *i Stany* . Celem tego artykułu jest opisywanie tych stanów i wyraźne wyróżnienie, gdy klienci są rozliczani za użycie wystąpień. 

## <a name="power-states"></a>Stany zasilania

Stan zasilacza reprezentuje ostatni znany stan maszyny wirtualnej.

![Diagram stanu baterii maszyny wirtualnej](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Poniższa tabela zawiera opis każdego stanu wystąpienia i wskazuje, czy jest rozliczana za użycie wystąpienia, czy nie.

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="":::

   **Opis**

   :::column-end:::
   :::column span="":::

   **Użycie wystąpienia rozliczane**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Uruchamianie**

   :::column-end:::
   :::column span="":::

   Maszyna wirtualna jest uruchamiana.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Bez opłat**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Uruchomienie**

   :::column-end:::
   :::column span="":::

   Normalny stan roboczy dla maszyny wirtualnej

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Rozliczane**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Zatrzymywanie**

   :::column-end:::
   :::column span="":::

   Jest to stan przejściowy. Po zakończeniu będzie wyświetlana jako **zatrzymana**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Rozliczane**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Zatrzymano**

   :::column-end:::
   :::column span="":::

   Maszyna wirtualna została zamknięta z poziomu systemu operacyjnego gościa lub przy użyciu interfejsów API wyłączenie.

   Sprzęt jest nadal przydzielony do maszyny wirtualnej i pozostaje na hoście.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Rozliczane***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Cofanie przydziału**

   :::column-end:::
   :::column span="":::

   Stan przejściowy. Po zakończeniu maszyna wirtualna będzie wyświetlana jako **cofnięta alokacja**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Bez opłat***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Cofnięto przydział**

   :::column-end:::
   :::column span="":::

   Maszyna wirtualna została pomyślnie zatrzymana i usunięta z hosta.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Bez opłat**

   :::column-end:::
:::row-end:::


&#42; niektórych zasobów platformy Azure, takich jak dyski i sieci, są naliczane opłaty. W licencjach na oprogramowanie w wystąpieniu nie są naliczane opłaty.

## <a name="provisioning-states"></a>Stany aprowizacji

Stan aprowizacji to stan zainicjowanej przez użytkownika operacji kontroli płaszczyzny na maszynie wirtualnej. Te Stany są oddzielone od stanu zasilacza maszyny wirtualnej.

- **Utwórz** — Tworzenie maszyny wirtualnej.

- **Update** — aktualizuje model istniejącej maszyny wirtualnej. Niektóre zmiany niezwiązane z modelem maszyny wirtualnej, takie jak uruchomienie/ponowne uruchomienie, również podlegają aktualizacji.

- **Usuń** — usunięcie maszyny wirtualnej.

- **Cofnięcie alokacji** — to miejsce, w którym maszyna wirtualna jest zatrzymana i usuwana z hosta. Cofanie przydziału maszyny wirtualnej jest uznawane za aktualizację, więc spowoduje wyświetlenie Stanów aprowizacji związanych z aktualizacją.



Poniżej znajdują się Stany operacji przejściowych po zaakceptowaniu przez platformę akcji zainicjowanej przez użytkownika:

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="2":::

   **Opis**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Tworzenie**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Aktualizowanie**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Usuwanie**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Stany aprowizacji systemu operacyjnego**
   
   :::column-end:::
   :::column span="2":::

   **Opis**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Jeśli maszyna wirtualna jest tworzona przy użyciu obrazu systemu operacyjnego, a nie obrazu specjalistycznego, można zaobserwować następujące podstany:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   Maszyna wirtualna jest uruchomiona, a instalacja systemu operacyjnego gościa jest w toku.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Stan krótkotrwały. Maszyna wirtualna szybko przechodzi do **sukcesu** , chyba że trzeba zainstalować rozszerzenia. Instalowanie rozszerzeń może zająć trochę czasu.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Uwaga**: Inicjowanie obsługi systemu operacyjnego może przejść do **niepowodzenia** , jeśli wystąpi błąd systemu operacyjnego lub system operacyjny nie zostanie zainstalowany w czasie. Klienci będą rozliczani za wdrożoną maszynę wirtualną w infrastrukturze.

   :::column-end:::

:::row-end:::

Po zakończeniu operacji maszyna wirtualna przejdzie do jednego z następujących stanów:

- **Powodzenie** — akcje zainicjowane przez użytkownika zostały ukończone.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Niepowodzenie** — reprezentuje operację, która nie powiodła się. Zapoznaj się z kodami błędów, aby uzyskać więcej informacji i możliwych rozwiązań.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Widok wystąpienia maszyny wirtualnej

Interfejs API widoku wystąpienia zawiera informacje o stanie uruchomienia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz dokumentację interfejsu API [widoku Virtual Machines-instance](/rest/api/compute/virtualmachines/instanceview) .

Eksplorator zasobów platformy Azure udostępnia prosty interfejs użytkownika do wyświetlania stanu działania maszyny wirtualnej: [Eksplorator zasobów](https://resources.azure.com/).

Stany aprowizacji są widoczne w obszarze właściwości maszyny wirtualnej i widok wystąpienia. Stany mocy są dostępne w widoku wystąpienia maszyny wirtualnej.

Aby pobrać stan mocy wszystkich maszyn wirtualnych w ramach subskrypcji, użyj opcji [Virtual Machines wszystkie interfejsy API](/rest/api/compute/virtualmachines/listall) z parametrem **statusOnly** na *wartość true*.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o monitorowaniu maszyny wirtualnej, zobacz [monitorowanie maszyn wirtualnych na platformie Azure](../azure-monitor/insights/monitor-vm-azure.md).
---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183170"
---
Usługa Azure Virtual Machines (VM) umożliwia przechodzenie między różnymi stanami, które można podzielić na możliwości *aprowizacji* *i Stany* . Celem tego artykułu jest opisywanie tych stanów i wyraźne wyróżnienie, gdy klienci są rozliczani za użycie wystąpień. 

## <a name="power-states"></a>Stany zasilania

Stan zasilacza reprezentuje ostatni znany stan maszyny wirtualnej.

![Diagram stanu baterii maszyny wirtualnej](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Poniższa tabela zawiera opis każdego stanu wystąpienia i wskazuje, czy jest rozliczana za użycie wystąpienia, czy nie.

<table>
<tr>
<th>
Stan
</th>
<th>
Opis
</th>
<th>
Rozliczenia użycia wystąpienia
</th>
</tr>
<tr>
<td>
<p><b>Uruchamianie</b></p>
</td>
<td>
<p>Maszyna wirtualna jest uruchamiana.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Bez opłat</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Uruchomiono</b></p>
</td>
<td>
<p>Normalny stan roboczy dla maszyny wirtualnej</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Rozliczane</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zatrzymywanie</b></p>
</td>
<td>
<p>Jest to stan przejściowy. Po zakończeniu będzie wyświetlana jako **zatrzymana**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Rozliczane</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zatrzymano</b></p>
</td>
<td>
<p>Maszyna wirtualna została zamknięta z poziomu systemu operacyjnego gościa lub przy użyciu interfejsów API wyłączenie.</p>
<p>Sprzęt jest nadal przydzielony do maszyny wirtualnej i pozostaje na hoście. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Rozliczanie&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Cofanie przydziału</b></p>
</td>
<td>
<p>Stan przejściowy. Po zakończeniu maszyna wirtualna będzie wyświetlana jako **cofnięta alokacja**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Rozliczane&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Cofnięto przydział</b></p>
</td>
<td>
<p>Maszyna wirtualna została pomyślnie zatrzymana i usunięta z hosta. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Bez opłat</b></p>
</td>
</tr>
</tbody>
</table>


&#42;niektórych zasobów platformy Azure, takich jak dyski i sieci, są naliczane opłaty. W licencjach na oprogramowanie w wystąpieniu nie są naliczane opłaty.

## <a name="provisioning-states"></a>Stany aprowizacji

Stan aprowizacji to stan zainicjowanej przez użytkownika operacji kontroli płaszczyzny na maszynie wirtualnej. Te Stany są oddzielone od stanu zasilacza maszyny wirtualnej.

- **Utwórz** — Tworzenie maszyny wirtualnej.

- **Update** — aktualizuje model istniejącej maszyny wirtualnej. Niektóre zmiany niezwiązane z modelem maszyny wirtualnej, takie jak uruchomienie/ponowne uruchomienie, również podlegają aktualizacji.

- **Usuń** — usunięcie maszyny wirtualnej.

- **Cofnięcie alokacji** — to miejsce, w którym maszyna wirtualna jest zatrzymana i usuwana z hosta. Cofanie przydziału maszyny wirtualnej jest uznawane za aktualizację, więc spowoduje wyświetlenie Stanów aprowizacji związanych z aktualizacją.



Poniżej znajdują się Stany operacji przejściowych po zaakceptowaniu przez platformę akcji zainicjowanej przez użytkownika:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Stany</b></p>
</td>
<td width="366">
<p>Opis</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Tworzenie</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Aktualizowanie</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Usunąć</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Stany aprowizacji systemu operacyjnego</b></p>
</td>
<td width="366">
<p>Jeśli maszyna wirtualna jest tworzona przy użyciu obrazu systemu operacyjnego, a nie obrazu specjalistycznego, można zaobserwować następujące podstany:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; maszyna wirtualna jest uruchomiona, a instalacja systemu operacyjnego gościa jest w toku. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; stan krótkotrwały. Maszyna wirtualna szybko przechodzi do **sukcesu** , chyba że trzeba zainstalować rozszerzenia. Instalowanie rozszerzeń może zająć trochę czasu. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Uwaga</b>: Inicjowanie obsługi systemu operacyjnego może przejść do **niepowodzenia** , jeśli wystąpi błąd systemu operacyjnego lub system operacyjny nie zostanie zainstalowany w czasie. Klienci będą rozliczani za wdrożoną maszynę wirtualną w infrastrukturze.</p>
</td>
</tr>
</table>


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

Interfejs API widoku wystąpienia zawiera informacje o stanie uruchomienia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz dokumentację interfejsu API [widoku Virtual Machines-instance](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) .

Eksplorator zasobów platformy Azure udostępnia prosty interfejs użytkownika do wyświetlania stanu działania maszyny wirtualnej: [Eksplorator zasobów](https://resources.azure.com/).

Stany aprowizacji są widoczne w obszarze właściwości maszyny wirtualnej i widok wystąpienia. Stany mocy są dostępne w widoku wystąpienia maszyny wirtualnej. 


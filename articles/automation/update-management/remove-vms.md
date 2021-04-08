---
title: Usuń maszyny wirtualne z Azure Automation Update Management
description: W tym artykule opisano sposób usuwania maszyn zarządzanych przy użyciu Update Management.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97913196"
---
# <a name="remove-vms-from-update-management"></a>Usuwanie maszyn wirtualnych z rozwiązania Update Management

Po zakończeniu zarządzania aktualizacjami na maszynach wirtualnych w środowisku można zatrzymać zarządzanie maszynami wirtualnymi za pomocą funkcji [Update Management](overview.md) . Aby przestać zarządzać nimi, Edytuj zapisane zapytanie wyszukiwania `MicrosoftDefaultComputerGroup` w obszarze roboczym log Analytics, który jest połączony z kontem usługi Automation.

## <a name="sign-into-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Aby usunąć maszyny wirtualne

1. W Azure Portal Uruchom **Cloud Shell** od górnego nawigowania Azure Portal. Jeśli nie znasz Azure Cloud Shell, zobacz [omówienie Azure Cloud Shell](../../cloud-shell/overview.md).

2. Użyj następującego polecenia, aby zidentyfikować identyfikator UUID komputera, który chcesz usunąć z zarządzania.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. W Azure Portal przejdź do **obszaru log Analytics obszary robocze**. Z listy wybierz swój obszar roboczy.

4. W obszarze roboczym Log Analytics wybierz pozycję **Ustawienia zaawansowane** , a następnie wybierz pozycję **grupy komputerów** z menu po lewej stronie.

5. Z **grup komputerów** w okienku po prawej stronie wybierz pozycję **zapisane grupy**.

6. W tabeli, aby uzyskać aktualizacje zapisanego zapytania wyszukiwania **: MicrosoftDefaultComputerGroup**, kliknij ikonę **Wyświetl członków** , aby uruchomić i wyświetlić jego członków.

7. W edytorze zapytań przejrzyj zapytanie i Znajdź identyfikator UUID dla maszyny wirtualnej. Usuń identyfikator UUID dla maszyny wirtualnej i powtórz kroki dla innych maszyn wirtualnych, które chcesz usunąć.

8. Zapisz zapisane wyszukiwanie po zakończeniu edycji, wybierając pozycję **Zapisz** na górnym pasku. Po wyświetleniu monitu podaj następujące elementy:

    * **Nazwa**: MicrosoftDefaultComputerGroup
    * **Zapisz jako**: funkcja
    * **Alias**: Updates__MicrosoftDefaultComputerGroup
    * **Kategoria**: aktualizacje

>[!NOTE]
>Po wyrejestrowaniu maszyn nadal są one wyświetlane, ponieważ zgłaszamy wszystkie maszyny ocenione w ciągu ostatnich 24 godzin. Po usunięciu komputera należy odczekać 24 godziny, zanim nie będą już wyświetlane.

## <a name="next-steps"></a>Następne kroki

Aby ponownie włączyć zarządzanie maszyną wirtualną, zobacz [włączanie Update Management przez przeglądanie Azure Portal](enable-from-portal.md) lub [Włączanie Update Management z poziomu maszyny wirtualnej platformy Azure](enable-from-vm.md).
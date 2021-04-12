---
title: Uruchamianie połączenia z maszyną wirtualną — Azure
description: Jak skonfigurować funkcję Uruchom maszynę wirtualną w programie Connect.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d9ce9811cd660c9a9b1fcb6f9e24cadd65d3c2bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445605"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Uruchom maszynę wirtualną w programie Connect (wersja zapoznawcza)

> [!IMPORTANT]
> Funkcja Uruchom maszynę wirtualną w programie Connect jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkcja Uruchom maszynę wirtualną (VM) w programie Connect (wersja zapoznawcza) umożliwia oszczędzanie kosztów, umożliwiając cofanie alokacji maszyn wirtualnych, gdy nie są one używane. Gdy musisz ponownie użyć maszyny wirtualnej, wszystkie czynności, które należy wykonać, umożliwiają ponowne włączenie maszyn wirtualnych.

>[!NOTE]
>Pulpit wirtualny systemu Windows (klasyczny) nie obsługuje tej funkcji.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Funkcję Uruchom maszynę wirtualną można włączyć tylko dla puli hostów osobistych. Aby dowiedzieć się więcej na temat osobistych pul hostów, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md#host-pools).

Następujący klienci usług pulpitu zdalnego obsługują funkcję Uruchom maszynę wirtualną w programie Connect:

- [Klient sieci Web](connect-web.md)
- [Klient systemu Windows (wersja 1,2748 lub nowsza)](connect-windows-7-10.md)

Możesz sprawdzić powiadomienia o aktualizacjach i obsłudze klienta na [forum społeczności Tech](https://aka.ms/wvdtc).

>[!IMPORTANT]
>Funkcja Uruchom maszynę wirtualną w programie Connect obecnie obsługuje tylko program PowerShell i interfejs API REST, a nie Azure Portal. Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizowanie puli hostów](/rest/api/desktopvirtualization/hostpools/createorupdate).

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Utwórz rolę niestandardową dla uruchomienia maszyny wirtualnej w programie Connect

Aby można było skonfigurować funkcję Uruchom maszynę wirtualną w programie Connect, należy przypisać maszynę wirtualną do roli kontroli dostępu opartej na rolach. Ta rola umożliwi pulpitowi wirtualnemu systemu Windows zarządzanie maszynami wirtualnymi w ramach subskrypcji. Za pomocą tej roli można również włączyć maszyny wirtualne, sprawdzić ich stan i zgłosić informacje diagnostyczne. Jeśli chcesz dowiedzieć się więcej na temat działania poszczególnych ról, zapoznaj się z tematem [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md).

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby użyć Azure Portal do przypisania roli niestandardowej do uruchamiania maszyny wirtualnej w programie Connect:

1. Otwórz Azure Portal i przejdź do pozycji **subskrypcje**.

2. Przejdź do obszaru **Kontrola dostępu (IAM)** i wybierz pozycję **Dodaj rolę niestandardową**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający menu rozwijane z przycisku Dodaj w kontroli dostępu (IAM). "Dodaj rolę niestandardową" jest wyróżniony kolorem czerwonym.](media/add-custom-role.png)

3. Następnie nadaj nazwę roli niestandardowej i Dodaj opis. Zalecamy nazwę "Uruchom maszynę wirtualną w programie Connect".

4. Na karcie **uprawnienia** Dodaj następujące uprawnienia do subskrypcji, do której przypiszesz rolę: 
 
   - Microsoft. COMPUTE/virtualMachines/Start/akcja
   - Microsoft. COMPUTE/virtualMachines/Read

5. Gdy skończysz, wybierz **przycisk OK**.

Po tym celu należy przypisać rolę, aby udzielić dostępu do pulpitu wirtualnego systemu Windows.

Aby przypisać rolę niestandardową:

1. Na **karcie Kontrola dostępu (IAM)** wybierz pozycję **Dodaj przypisania ról**.

2. Wybierz właśnie utworzoną rolę.

3. Na pasku wyszukiwania wprowadź i wybierz pozycję **pulpit wirtualny systemu Windows**.

      >[!NOTE]
      >Jeśli wdrożono pulpit wirtualny systemu Windows (klasyczny), mogą zostać wyświetlone dwie aplikacje. Przypisz rolę do obu aplikacji, które widzisz.
      >
      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający kartę kontrola dostępu (IAM). Na pasku wyszukiwania zarówno pulpit wirtualny systemu Windows, jak i pulpit wirtualny systemu Windows (klasyczny) są wyróżnione kolorem czerwonym.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Tworzenie roli niestandardowej z szablonem pliku JSON

Jeśli używasz pliku JSON do utworzenia roli niestandardowej, Poniższy przykład pokazuje podstawowy szablon, którego można użyć. Upewnij się, że wartość identyfikatora subskrypcji jest zastępowana IDENTYFIKATORem subskrypcji, do której ma zostać przypisana rola.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>Skonfiguruj funkcję Uruchom maszynę wirtualną w programie Connect

Teraz, po przypisaniu roli, można skonfigurować funkcję Uruchom maszynę wirtualną za pomocą funkcji Connect.

### <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania 

Opcja Uruchom maszynę wirtualną w programie Connect jest ustawieniem puli hostów. Jeśli chcesz, aby ta funkcja była używana tylko przez wybraną grupę użytkowników, upewnij się, że tylko wymagana rola jest przypisana do użytkowników, których chcesz dodać.

>[!IMPORTANT]
> Tę funkcję można skonfigurować tylko w istniejących pulach hosta. Ta funkcja jest niedostępna podczas tworzenia nowej puli hostów.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby skonfigurować to ustawienie przy użyciu programu PowerShell, należy upewnić się, że masz nazwy grup zasobów i pul hostów, które chcesz skonfigurować. Należy również zainstalować [moduł Azure PowerShell (w wersji 2.1.0 lub nowszej)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

Aby skonfigurować uruchamianie maszyny wirtualnej przy użyciu programu PowerShell:

1. Otwórz okno polecenia programu PowerShell.

2. Uruchom następujące polecenie cmdlet, aby włączyć Uruchamianie maszyny wirtualnej w programie Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Uruchom następujące polecenie cmdlet, aby wyłączyć uruchamianie maszyny wirtualnej w programie Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Środowisko użytkownika

W typowych sesjach czas potrzebny użytkownikowi na nawiązanie połączenia z cofniętą alokacją maszyny wirtualnej rośnie, ponieważ maszyna wirtualna wymaga czasu ponownego włączenia, podobnie jak włączenie komputera fizycznego. Klient Pulpit zdalny ma wskaźnik umożliwiający użytkownikowi określenie, że komputer jest włączony podczas nawiązywania połączenia.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli funkcja jest uruchamiana w ramach jakichkolwiek problemów, zalecamy użycie [funkcji diagnostyki](diagnostics-log-analytics.md) pulpitu wirtualnego systemu Windows w celu wyszukania problemów. Jeśli zostanie wyświetlony komunikat o błędzie, pamiętaj, aby zwrócić uwagę na treść wiadomości, a następnie skopiuj nazwę błędu w miejscu dla odwołania.

Możesz również użyć [Azure monitor dla pulpitu wirtualnego systemu Windows](azure-monitor.md) , aby uzyskać sugestie dotyczące sposobu rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Jeśli wystąpią problemy z zapytaniem, że nie można rozwiązać problemu z dokumentacją lub funkcją diagnostyki, zapoznaj [się z tematem Rozpoczynanie pracy z maszyną wirtualną przy nawiązywaniu połączenia](start-virtual-machine-connect-faq.md)
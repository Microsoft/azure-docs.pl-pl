---
title: Uruchamianie połączenia z maszyną wirtualną — Azure
description: Jak skonfigurować funkcję uruchamiania maszyny wirtualnej w programie Connect.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 161a4d578509a7752f9438ce8f05d599bdb54e93
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832288"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Uruchamianie maszyny wirtualnej przy oknie połączenia (wersja zapoznawcza)

> [!IMPORTANT]
> Funkcja uruchamiania maszyny wirtualnej w oknie connect jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkcja Uruchamiania maszyny wirtualnej w oknie Łączenie (wersja zapoznawcza) umożliwia oszczędzanie kosztów, umożliwiając użytkownikom końcowych włączanie maszyn wirtualnych tylko wtedy, gdy ich potrzebują. Następnie możesz wyłączyć maszyny wirtualne, gdy nie są potrzebne.

>[!NOTE]
>Windows Virtual Desktop (wersja klasyczna) nie obsługuje tej funkcji.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Funkcję Uruchamiania maszyny wirtualnej można włączyć tylko dla osobistych pul hostów w funkcji Połącz. Aby dowiedzieć się więcej na temat pul hostów osobistych, [zobacz Windows Virtual Desktop środowiska](environment-setup.md#host-pools).

Następujący klienci usług pulpitu zdalnego obsługują funkcję Uruchamiania maszyny wirtualnej w programie Connect:

- [Klient internetowy](connect-web.md)
- [Klient systemu Windows (wersja 1.2748 lub nowsza)](connect-windows-7-10.md)

Aby uzyskać informacje o aktualizacjach i pomocy technicznej dla klientów, możesz sprawdzić na [forum Społeczności technicznej](https://aka.ms/wvdtc).

Chmura Azure Government obecnie nie obsługuje uruchamiania maszyny wirtualnej w chmurze connect.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Tworzenie roli niestandardowej dla uruchamiania maszyny wirtualnej w programie Connect

Aby można było skonfigurować funkcję Uruchom maszynę wirtualną w programie Connect, musisz przypisać maszynie wirtualnej niestandardową rolę RBAC (kontrola dostępu oparta na rolach). Ta rola umożliwia Windows Virtual Desktop maszynami wirtualnych w ramach subskrypcji. Za pomocą tej roli można również włączać maszyny wirtualne, sprawdzać ich stan i raportować informacje diagnostyczne. Jeśli chcesz dowiedzieć się więcej o poszczególnych rolach, zobacz Role [niestandardowe platformy Azure.](../role-based-access-control/custom-roles.md)

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby przy użyciu Azure Portal przypisać rolę niestandardową dla uruchamiania maszyny wirtualnej przy użyciu opcji Połącz:

1. Otwórz okno Azure Portal przejdź do **subskrypcji**.

2. Przejdź do **opcji Kontrola dostępu (IAM)** i wybierz **pozycję Dodaj rolę niestandardową.**

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający menu rozwijane z przycisku Dodaj w kontrolce dostępu (IAM). Przycisk "Dodaj rolę niestandardową" został wyróżniony na czerwono.](media/add-custom-role.png)

3. Następnie nadaj roli niestandardowej nazwę i dodaj opis. Zalecamy nadaj jej nazwę "Uruchom maszynę wirtualną po nawiązać połączenie".

4. Na **karcie** Uprawnienia dodaj następujące uprawnienia do subskrypcji, do których przypisujesz rolę: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. Po zakończeniu wybierz przycisk **OK.**

Następnie należy przypisać rolę, aby udzielić dostępu do Windows Virtual Desktop.

Aby przypisać rolę niestandardową:

1. Na karcie **Kontrola dostępu (IAM)** wybierz pozycję **Dodaj przypisania ról.**

2. Wybierz właśnie utworzoną rolę.

3. Na pasku wyszukiwania wprowadź i wybierz **pozycję Windows Virtual Desktop**.

      >[!NOTE]
      >Jeśli wdrożono aplikację w wersji Windows Virtual Desktop klasycznej, mogą być Windows Virtual Desktop aplikacje. Przypisz rolę do obu wyświetlonych aplikacji.
      >
      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający kartę Kontrola dostępu (IAM). Na pasku wyszukiwania obie Windows Virtual Desktop i Windows Virtual Desktop (klasyczne) są wyróżnione na czerwono.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Tworzenie roli niestandardowej przy użyciu szablonu pliku JSON

Jeśli używasz pliku JSON do utworzenia roli niestandardowej, w poniższym przykładzie pokazano podstawowy szablon, który możesz użyć. Pamiętaj, aby zastąpić wartość identyfikatora subskrypcji identyfikatorem subskrypcji, do której chcesz przypisać rolę.

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

## <a name="configure-the-start-vm-on-connect-feature"></a>Konfigurowanie funkcji Uruchamiania maszyny wirtualnej przy nawiązywania połączenia

Teraz, po przypisaniu subskrypcji roli, nas czas na skonfigurowanie funkcji Uruchamiania maszyny wirtualnej w skrypcie Połącz.

### <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania 

Ustawienie Uruchom maszynę wirtualną przy nawiązywaniu połączenia to ustawienie puli hostów. Jeśli chcesz, aby ta funkcja była dostępna tylko dla wybranej grupy użytkowników, upewnij się, że do użytkowników, których chcesz dodać, przypisano tylko wymaganą rolę.

>[!IMPORTANT]
> Tę funkcję można skonfigurować tylko w istniejących pulach hostów. Ta funkcja nie jest dostępna podczas tworzenia nowej puli hostów.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby użyć tej Azure Portal, aby skonfigurować uruchamianie maszyny wirtualnej przy użyciu przycisku Połącz:

1. Otwórz przeglądarkę i przejdź do [Azure Portal](https://portal.azure.com).

2. W Azure Portal przejdź do **Windows Virtual Desktop**.

3. Wybierz **pozycję Pule** hostów, a następnie znajdź pulę hostów zawierającą osobiste komputery stacjonarne, do których przypisano rolę.

   >[!NOTE]
   > Pula hostów skonfigurowana w programie musi mieć osobiste komputery stacjonarne z bezpośrednimi przypisaniami ról. Jeśli pulpity w puli hostów nie są prawidłowo skonfigurowane, proces konfiguracji nie będzie działać.

4. W puli hostów wybierz pozycję **Właściwości.** W **obszarze Uruchom maszynę wirtualną przy** połączeniu wybierz pozycję **Tak,** a następnie **wybierz pozycję Zapisz,** aby natychmiast zastosować ustawienie.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający okno Właściwości. Opcja Uruchom maszynę wirtualną przy połączeniu jest wyróżniona kolorem czerwonym.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby skonfigurować to ustawienie przy użyciu programu PowerShell, upewnij się, że masz nazwy grupy zasobów i pul hostów, które chcesz skonfigurować. Musisz również zainstalować moduł Azure PowerShell (w wersji [2.1.0 lub nowszej).](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)

Aby skonfigurować uruchamianie maszyny wirtualnej przy użyciu programu PowerShell:

1. Otwórz okno polecenia programu PowerShell.

2. Uruchom następujące polecenie cmdlet, aby włączyć uruchamianie maszyny wirtualnej w programie Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Uruchom następujące polecenie cmdlet, aby wyłączyć uruchamianie maszyny wirtualnej podczas nawiązywania połączenia:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Środowisko użytkownika

W typowych sesjach czas łączenia się użytkownika z maszyną wirtualną, dla których cofnie się alokacja, wydłuża się, ponieważ maszyna wirtualna potrzebuje czasu na jej włączenie, podobnie jak włączenie komputera fizycznego. Klient Pulpit zdalny ma wskaźnik, który informuje użytkownika o tym, że komputer jest włączony podczas nawiązywania połączenia.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli w tej funkcji występują jakiekolwiek problemy, zalecamy sprawdzenie, czy nie Windows Virtual Desktop [diagnostyki.](diagnostics-log-analytics.md) Jeśli zostanie wyświetlony komunikat o błędzie, pamiętaj, aby zwrócić szczególną uwagę na jego zawartość i skopiować nazwę błędu w inne miejsce.

Możesz również użyć funkcji [Azure Monitor Windows Virtual Desktop,](azure-monitor.md) aby uzyskać sugestie dotyczące sposobu rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Jeśli wystąpią problemy, których nie można rozwiązać w dokumentacji rozwiązywania problemów lub funkcji diagnostyki, zapoznaj się z często zadawanymi pytaniami na temat uruchamiania maszyny wirtualnej na [stronie Łączenie.](start-virtual-machine-connect-faq.md)

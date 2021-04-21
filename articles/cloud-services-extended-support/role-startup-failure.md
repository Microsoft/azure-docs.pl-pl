---
title: Niepowodzenie uruchamiania wystąpienia roli w Azure Cloud Services (rozszerzona obsługa)
description: Rozwiązywanie problemów z niepowodzeniem uruchamiania wystąpienia roli w Azure Cloud Services (rozszerzona obsługa).
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f4892fe50c1832628181a11a5166c8cb705f79aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748967"
---
# <a name="troubleshoot-azure-cloud-services-extended-support-roles-that-fail-to-start"></a>Rozwiązywanie Azure Cloud Services (rozszerzonej pomocy technicznej), których nie można uruchomić

Oto niektóre typowe problemy i rozwiązania związane z Azure Cloud Services (rozszerzona pomoc techniczna), których nie można uruchomić.

## <a name="cloud-service-operation-fails-with-roleinstancestartuptimeouterror"></a>Operacja usługi w chmurze kończy się niepowodzeniem z operacją RoleInstanceStartupTimeoutError

Co najmniej jedno wystąpienie roli w programie Azure Cloud Services (rozszerzona pomoc techniczna) może działać wolno lub mogą one być wytłaniane i wystąpienie roli kończy się niepowodzeniem. Zostanie wyświetlony błąd aplikacji `RoleInstanceStartupTimeoutError` roli.

Aplikacja roli zawiera dwie części, które mogą spowodować ponowne uruchomienie *roli:* zadania uruchamiania i kod *roli (implementacja roliEntryPoint).* 

Jeśli rola zostanie zatrzymana, agent platformy jako usługi (PaaS) ponownie uruchomi rolę.

Bieżący stan i szczegóły wystąpienia roli można uzyskać, aby zdiagnozować błędy przy użyciu programu PowerShell lub Azure Portal:

* **PowerShell:** użyj polecenia cmdlet [Get-AzCloudServiceRoleInstanceView,](/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) aby uzyskać informacje o stanie środowiska uruchomieniowego wystąpienia roli:

    ```powershell
    Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
     
    Statuses           PlatformFaultDomain PlatformUpdateDomain
    --------           ------------------- --------------------
    {RoleStateStarting} 0                   0
    ```

* **Azure Portal:** w portalu przejdź do wystąpienia usługi w chmurze. Aby wyświetlić szczegóły stanu, **wybierz pozycję Role i wystąpienia,** a następnie wybierz wystąpienie roli.

  :::image type="content" source="media/role-startup-failure-portal.png" alt-text="Zrzut ekranu przedstawiający błąd uruchamiania roli w Azure Portal.":::

## <a name="missing-dlls-or-dependencies"></a>Brakujące biblioteki DLL lub zależności

Nieodpowiadające role i role, które są przełączane między stanami, mogą być spowodowane brakującymi bibliotekami DLL lub zestawami.

Poniżej podano niektóre objawy braku bibliotek DLL lub zestawów:

* Wystąpienie roli przechodzi przez stany **Inicjowanie,** **Zajęte** i **Zatrzymywanie**.
* Wystąpienie roli zostało przeniesione do stanu **Gotowe,** ale po dojściu do aplikacji internetowej strona nie jest widoczna.


W witrynie internetowej wdrożonej w roli sieci Web bez biblioteki DLL może być wyświetlany ten błąd środowiska uruchomieniowego serwera:

  :::image type="content" source="media/role-startup-failure-runtime-error.png" alt-text="Zrzut ekranu przedstawiający błąd środowiska uruchomieniowego po niepowodzeniu uruchamiania roli.":::

### <a name="resolve-missing-dlls-and-assemblies"></a>Rozwiązywanie problemów z brakującymi bibliotekami DLL i zestawami

Aby usunąć błędy związane z brakującymi bibliotekami DLL i zestawami:

1. W Visual Studio otwórz rozwiązanie.
2. W Eksplorator rozwiązań otwórz folder *Odwołania.*
3. Wybierz zestaw, który został zidentyfikowany w błędzie.
4. W **właściwościach** ustaw **właściwość Kopiuj lokalnie** na wartość **True.**
5. Ponowne wdychaj usługę w chmurze.

Po sprawdzeniu, czy błędy nie są już wyświetlane, ponownie wdeń usługę. Podczas konfiguracji wdrożenia nie zaznaczaj pola wyboru Włącz role **intellitrace dla programu .NET 4.**

## <a name="diagnose-role-instance-errors"></a>Diagnozowanie błędów wystąpienia roli

Wybierz jedną z poniższych opcji, aby zdiagnozować problemy z wystąpieniami ról.

### <a name="turn-off-custom-errors"></a>Wyłączanie błędów niestandardowych

Aby wyświetlić pełne informacje o błędzie, w *web.config* roli sieci Web ustaw niestandardowy tryb błędu na wartość , a następnie ponownie `off` wdeń usługę:

1. W Visual Studio otwórz rozwiązanie.
2. W Eksplorator rozwiązań otwórz plik *web.config.*
3. W `system.web` sekcji dodaj następujący kod:

   ```xml
   <customErrors mode="Off" />
   ```

4. Zapisz plik.
5. Ponownie spakuj i wdaj ponownie usługę.

Po ponownej lodowania usługi zostanie wyświetlony komunikat o błędzie z nazwą brakujących zestawów lub bibliotek DLL.

### <a name="use-remote-desktop"></a>Korzystanie z pulpitu zdalnego

Użyj Pulpit zdalny, aby uzyskać dostęp do roli i wyświetlić pełne informacje o błędzie:

1. [Dodaj rozszerzenie Pulpit zdalny dla Azure Cloud Services (rozszerzona obsługa).](enable-rdp.md)
2. W Azure Portal, gdy wystąpienie usługi w chmurze ma stan Gotowe, użyj Pulpit zdalny, aby zalogować się do usługi w chmurze.  Aby uzyskać więcej informacji, zobacz [Nawiązywanie połączenia z wystąpieniami ról przy użyciu Pulpit zdalny](enable-rdp.md#connect-to-role-instances-with-remote-desktop-enabled).
3. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń użytych do skonfigurowania Pulpit zdalny.
4. Otwórz okno wiersza polecenia.
5. W wierszu polecenia wprowadź **polecenie ipconfig**. Zanotuj zwróconą wartość adresu IPv4.
6. Otwórz program Microsoft Internet Explorer.
7. Na pasku adresu wprowadź adres IPv4, po którym następuje ukośnik i nazwa domyślnego pliku aplikacji internetowej. Na przykład `http://<IPv4 address>/default.aspx`.

Jeśli teraz do witryny internetowej zostanie wyświetlony komunikat o błędzie, który zawiera więcej informacji. Oto przykład:

:::image type="content" source="media/role-startup-failure-error-message.png" alt-text="Zrzut ekranu przedstawiający przykładowy komunikat o błędzie.":::
  
### <a name="use-the-compute-emulator"></a>Korzystanie z emulatora obliczeń

Emulator obliczeń platformy Azure umożliwia diagnozowanie i rozwiązywanie problemów z brakującymi zależnościami *iweb.config* błędów. W przypadku używania tej metody do diagnozowania problemów, aby uzyskać najlepsze wyniki, należy użyć komputera lub maszyny wirtualnej, która ma czystą instalację systemu Windows.

Aby zdiagnozować problemy przy użyciu emulatora obliczeń platformy Azure:

1. Zainstaluj zestaw [Azure SDK.](https://azure.microsoft.com/downloads/)
2. Na komputerze dewelopera skompilować projekt usługi w chmurze.
3. W Eksplorator plików w projekcie usługi w chmurze przejdź do *folderu bin\debug.*
4. Skopiuj folder *csx* i plik *cscfg* na komputer, za pomocą których debugujesz problemy.
5. Na maszynie czystej otwórz okno wiersza polecenia zestawu Azure SDK.
6. W wierszu polecenia wprowadź **csrun.exe /devstore:start.**
7. Następnie wprowadź wartość **run csrun \<path to .csx folder\> \<path to .cscfg file\> /launchBrowser.**
8. Podczas uruchamiania roli program Internet Explorer szczegółowe informacje o błędzie.

Jeśli wymagana jest większa diagnostyka, można użyć standardowych narzędzi do rozwiązywania problemów z systemem Windows.

### <a name="use-intellitrace"></a>Korzystanie z funkcji IntelliTrace

W przypadku ról procesów roboczych i ról sieci Web, .NET Framework 4, można użyć [funkcji IntelliTrace.](/visualstudio/debugger/intellitrace) Funkcja IntelliTrace jest dostępna w Visual Studio Enterprise.

Aby wdrożyć usługę w chmurze przy włączonej funkcji IntelliTrace:

1. Upewnij się, że zainstalowano zestaw Azure SDK 1.3 lub nowszy.
2. W Visual Studio wdrożyć rozwiązanie. Po skonfigurowaniu wdrożenia zaznacz pole wyboru **Włącz funkcję IntelliTrace dla ról .NET 4.**
3. Po otwarciu wystąpienia roli otwórz Eksplorator serwera.
4. Rozwiń **węzeł Azure\Cloud Services** węzeł.
5. Rozwiń wdrożenie, aby wyświetlić listę wystąpień roli. Kliknij prawym przyciskiem myszy wystąpienie roli.
6. Wybierz **pozycję Wyświetl dzienniki IntelliTrace.**
7. W **podsumowaniu IntelliTrace** przejdź do opcji  **Dane wyjątku**.
8. Rozwiń **dane** wyjątku i poszukaj `System.IO.FileNotFoundException` błędu:

   :::image type="content" source="media/role-startup-failure-exception-data.png" alt-text="Zrzut ekranu przedstawiający dane wyjątku dla niepowodzenia uruchamiania roli." lightbox="media/role-startup-failure-exception-data.png":::

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z rolą usługi w chmurze przy użyciu danych diagnostycznych komputera PaaS platformy Azure.](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)

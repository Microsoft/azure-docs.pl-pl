---
title: Niepowodzenie uruchamiania wystąpienia roli dla usługi Azure Cloud Services (obsługa rozszerzona)
description: Rozwiązywanie problemów z niepowodzeniem uruchamiania wystąpienia roli dla usługi Azure Cloud Services (obsługa rozszerzona)
ms.topic: article
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachadw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: ced7675350c0e0deadf77837cf0f13ba54fffab9
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382359"
---
# <a name="troubleshoot-azure-cloud-service-extended-support-roles-that-fail-to-start"></a>Rozwiązywanie problemów z rolami usługi w chmurze platformy Azure (obsługa rozszerzona), których nie można uruchomić
Poniżej przedstawiono niektóre typowe problemy i rozwiązania dotyczące ról Cloud Services (obsługa rozszerzona), których uruchomienie nie powiodło się.

## <a name="cloud-service-operation-failed-with-roleinstancestartuptimeouterror"></a>Operacja usługi w chmurze nie powiodła się z RoleInstanceStartupTimeoutError
Co najmniej jedno wystąpienie roli usługi może nie zostać uruchomione w określonym czasie. Te wystąpienia roli mogą potrwać czas uruchamiania lub mogą być odtwarzane, a wystąpienie roli może zakończyć się niepowodzeniem z RoleInstanceStartupTimeoutError jest to błąd aplikacji roli. Aplikacja roli zawiera dwie główne części: "zadania uruchamiania" i "kod roli (implementacja RoleEntryPoint)", co może spowodować odtworzenie roli. W przypadku awarii roli Agent PaaS będzie zawsze go ponownie uruchamiać. 

Aby uzyskać bieżący stan i szczegóły wystąpień roli w przypadku błędów, użyj:

* PowerShell: Użyj polecenia cmdlet [Get-AzCloudServiceRoleInstanceView](https://docs.microsoft.com/powershell/module/az.cloudservice/get-azcloudserviceroleinstanceview) , aby pobrać informacje o stanie czasu wykonywania wystąpienia roli w usłudze w chmurze.
```powershell
Get-AzCloudServiceRoleInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "WebRole1_IN_0"
 
Statuses           PlatformFaultDomain PlatformUpdateDomain
--------           ------------------- --------------------
{RoleStateStarting} 0                   0
```

* Azure Portal: Przejdź do usługi w chmurze i wybierz kartę role i wystąpienia. Kliknij wystąpienie roli, aby wyświetlić jego szczegóły stanu 
 
   :::image type="content" source="media/role-startup-failure-1.png" alt-text="Obraz przedstawiający niepowodzenie uruchamiania roli w portalu.":::
   
Poniżej przedstawiono niektóre typowe problemy i rozwiązania dotyczące ról usługi Azure Cloud Services (obsługa rozszerzona), których nie można uruchomić lub które cykluje między Stanami inicjowania, zajętości i zatrzymywania.

## <a name="missing-dlls-or-dependencies"></a>Brak bibliotek DLL lub zależności
Nieodpowiadające role i role, które są cykliczne między inicjalizacją, zajętą i zatrzymywaniem, mogą być spowodowane brakującymi bibliotekami DLL lub zestawami.
Objawy brakujących bibliotek DLL lub zestawów mogą być następujące:

* Wystąpienie roli jest cykliczne przez **Inicjowanie**, **zajęte** i **Zatrzymywanie** Stanów.
* Wystąpienie roli zostało przeniesione do **gotowe** , ale jeśli przejdziesz do aplikacji sieci Web, Strona nie zostanie wyświetlona.

Istnieje kilka zalecanych metod badania tych problemów.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnozuj brakujące problemy z biblioteką DLL w roli sieci Web
Po przejściu do witryny internetowej wdrożonej w roli sieci Web, a w przeglądarce zostanie wyświetlony komunikat o błędzie serwera podobny do poniższego, może to oznaczać, że brakuje biblioteki DLL.

:::image type="content" source="media/role-startup-failure-2.png" alt-text="Obraz przedstawia błąd środowiska uruchomieniowego podczas uruchamiania roli":::

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnozuj problemy, wyłączając błędy niestandardowe
Więcej informacji o błędach można wyświetlić przez skonfigurowanie web.config dla roli sieci Web w celu ustawienia niestandardowego trybu błędu na off i ponowne wdrożenie usługi.
Aby wyświetlić bardziej szczegółowe błędy bez używania Pulpit zdalny:
1.  Otwórz rozwiązanie w Microsoft Visual Studio.
2.  W Eksplorator rozwiązań Znajdź plik web.config i otwórz go.
3.  W pliku web.config zlokalizuj sekcję system. Web i Dodaj następujący wiersz:
 ```xml
<customErrors mode="Off" />
```
4.  Zapisz plik.
5.  Ponownie spakować i ponownie Wdróż usługę.
Po ponownym wdrożeniu usługi zostanie wyświetlony komunikat o błędzie z nazwą brakującego zestawu lub biblioteki DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnozuj problemy, wyświetlając błąd zdalnie
Możesz użyć Pulpit zdalny, aby uzyskać dostęp do roli i wyświetlić więcej pełnych informacji o błędzie. Aby wyświetlić błędy przy użyciu Pulpit zdalny, wykonaj następujące kroki:
1.  Włączenie rozszerzenia pulpitu zdalnego dla usługi w chmurze (obsługa rozszerzona). Aby uzyskać więcej informacji, zobacz [Apply pulpit zdalny Extension to Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal](enable-rdp.md)
2.  Na Azure Portal, gdy wystąpienie wskazuje stan gotowe, zdalny do wystąpienia. Aby uzyskać więcej informacji na temat korzystania z pulpitu zdalnego z Cloud Services (obsługa rozszerzona), zobacz [łączenie z wystąpieniami ról przy użyciu pulpit zdalny](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-rdp#connect-to-role-instances-with-remote-desktop-enabled)
3.  Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń określonych podczas konfiguracji Pulpit zdalny.
4.  Otwórz okno polecenia.
5.  Wpisz polecenie IPconfig.
6.  Zanotuj wartość adres IPv4.
7.  Otwórz program Internet Explorer.
8.  Wpisz adres i nazwę aplikacji sieci Web. Na przykład http:// <IPV4 Address> /default.aspx.
Przechodzenie do witryny sieci Web spowoduje teraz zwrócenie bardziej jawnych komunikatów o błędach:
* Błąd serwera w aplikacji "/".
* Opis: Wystąpił nieobsługiwany wyjątek podczas wykonywania bieżącego żądania sieci Web. Przejrzyj ślad stosu, aby uzyskać więcej informacji o błędzie i o tym, skąd pochodzi w kodzie.
* Szczegóły wyjątku: System. IO. FIleNotFoundException: nie można załadować pliku lub zestawu "Microsoft. WindowsAzure. StorageClient, Version = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35" lub jednej z jego zależności. W systemie nie można odnaleźć określonego pliku.
Na przykład:

  :::image type="content" source="media/role-startup-failure-3.png" alt-text="Obraz przedstawia wyjątek podczas uruchamiania roli":::
  
## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnozowanie problemów przy użyciu emulatora obliczeń
Emulatora obliczeń platformy Azure można użyć do diagnozowania i rozwiązywania problemów z brakującymi zależnościami oraz web.config błędów.
Aby uzyskać najlepsze wyniki przy użyciu tej metody diagnostyki, należy użyć komputera lub maszyny wirtualnej, która ma czystą instalację systemu Windows. 
1.  Instalowanie [zestawu Azure SDK](https://azure.microsoft.com/downloads/) 
2.  Na komputerze deweloperskim Skompiluj projekt usługi w chmurze.
3.  W Eksploratorze Windows przejdź do folderu bin\Debug w projekcie usługi w chmurze.
4.  Skopiuj folder. CSX i plik. cscfg na komputer, który jest używany do debugowania problemów.
5.  Na czystym komputerze otwórz okno wiersza polecenia zestawu Azure SDK i wpisz csrun.exe/devstore: Start.
6.  W wierszu polecenia wpisz Run csrun <Path to CSX folder> <ścieżka do pliku cscfg>/launchBrowser.
7.  Po uruchomieniu roli zostaną wyświetlone szczegółowe informacje o błędzie w programie Internet Explorer. Możesz również użyć standardowych narzędzi do rozwiązywania problemów systemu Windows, aby dodatkowo zdiagnozować problem.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnozowanie problemów przy użyciu IntelliTrace
W przypadku ról procesów roboczych i sieci Web, które używają .NET Framework 4, możesz użyć [IntelliTrace](https://docs.microsoft.com/visualstudio/debugger/intellitrace), który jest dostępny w Microsoft Visual Studio Enterprise.
Wykonaj następujące kroki, aby wdrożyć usługę z włączonym IntelliTrace:
1.  Upewnij się, że zainstalowano zestaw Azure SDK 1,3 lub nowszy.
2.  Wdróż rozwiązanie przy użyciu programu Visual Studio. Podczas wdrażania zaznacz pole wyboru Włącz role IntelliTrace for .NET 4.
3.  Po uruchomieniu wystąpienia Otwórz Eksplorator serwera.
4.  Rozwiń węzeł usługi Azure\Cloud Services i Znajdź wdrożenie.
5.  Rozwiń wdrożenie, dopóki nie zobaczysz wystąpień roli. Kliknij prawym przyciskiem myszy jedno z wystąpień.
6.  Wybierz pozycję Wyświetl dzienniki IntelliTrace. Zostanie otwarte podsumowanie IntelliTrace.
7.  Znajdź sekcję wyjątki podsumowania. Jeśli istnieją wyjątki, sekcja będzie zawierać etykiety danych wyjątków.
8.  Rozwiń dane wyjątku i Wyszukaj błędy System. IO. FileNotFoundException podobne do następujących:

    :::image type="content" source="media/role-startup-failure-4.png" alt-text="Obraz przedstawia dane wyjątku podczas uruchamiania roli" lightbox="media/role-startup-failure-4.png":::

## <a name="address-missing-dlls-and-assemblies"></a>Brak bibliotek DLL i zestawów
Aby rozwiązać brakujące błędy bibliotek DLL i zestawów, wykonaj następujące kroki:
1.  Otwórz rozwiązanie w programie Visual Studio.
2.  W Eksplorator rozwiązań otwórz folder References.
3.  Kliknij zestaw zidentyfikowany w błędzie.
4.  W okienku właściwości Znajdź opcję Kopiuj lokalną właściwość i ustaw wartość na true.
5.  Wdróż ponownie usługę w chmurze.
Po sprawdzeniu, czy wszystkie błędy zostały poprawione, można wdrożyć usługę bez sprawdzania, czy pole wyboru Włącz role IntelliTrace for .NET 4.

## <a name="next-steps"></a>Następne kroki 
- Aby dowiedzieć się, jak rozwiązywać problemy z rolą usługi w chmurze, korzystając z danych diagnostycznych dotyczących komputerów z usługą Azure PaaS, zobacz [Seria blogów Piotr Williamson](https://docs.microsoft.com/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

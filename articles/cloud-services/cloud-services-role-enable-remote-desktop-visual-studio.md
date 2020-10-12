---
title: Korzystanie z programu Visual Studio, Włączanie Pulpit zdalny dla roli (Cloud Services platformy Azure)
description: Jak skonfigurować aplikację usługi w chmurze platformy Azure w celu zezwalania na połączenia pulpitu zdalnego
services: cloud-services
author: ghogen
manager: jillfra
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: f4622e44c795182ee68c617f335c9e1651d3adcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80294383"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Włączanie Podłączanie pulpitu zdalnego roli na platformie Azure Cloud Services przy użyciu programu Visual Studio

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Program Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu roli działającej na platformie Azure. Połączenia Pulpit zdalny można użyć do rozwiązywania problemów i diagnozowania problemów z aplikacją, gdy jest ona uruchomiona.

Kreator publikowania, który oferuje program Visual Studio dla usług w chmurze, zawiera opcję włączania Pulpit zdalny podczas procesu publikowania przy użyciu podania poświadczeń. Użycie tej opcji jest odpowiednie w przypadku korzystania z programu Visual Studio 2017 w wersji 15,4 lub starszej.

Jednak w przypadku programu Visual Studio 2017 w wersji 15,5 lub nowszej zaleca się uniknięcie włączania Pulpit zdalny za pomocą Kreatora publikacji, chyba że pracujesz tylko jako jeden Deweloper. W przypadku każdej sytuacji, w której projekt może być otwarty przez innych deweloperów, zamiast tego należy włączyć Pulpit zdalny za Azure Portal pomocą programu PowerShell lub potoku wydania w przepływie pracy ciągłego wdrażania. To zalecenie jest spowodowane zmianą sposobu, w jaki program Visual Studio komunikuje się z Pulpit zdalny na maszynie wirtualnej usługi w chmurze, zgodnie z opisem w tym artykule.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Konfigurowanie Pulpit zdalny za poorednictwem programu Visual Studio 2017 w wersji 15,4 i starszej

W przypadku korzystania z programu Visual Studio 2017 w wersji 15,4 lub starszej można użyć opcji **włącz pulpit zdalny dla wszystkich ról** w Kreatorze publikacji. Można nadal używać Kreatora programu Visual Studio 2017 w wersji 15,5 lub nowszej, ale nie należy używać opcji Pulpit zdalny.

1. W programie Visual Studio Uruchom Kreatora publikacji, klikając prawym przyciskiem myszy projekt usługi w chmurze w Eksplorator rozwiązań i wybierając pozycję **Publikuj**.

2. Zaloguj się do subskrypcji platformy Azure w razie konieczności i wybierz pozycję **dalej**.

3. Na stronie **Ustawienia** wybierz pozycję **Włącz pulpit zdalny dla wszystkich ról**, a następnie wybierz łącze **Ustawienia...** , aby otworzyć okno dialogowe **Konfiguracja pulpit zdalny** .

4. W dolnej części okna dialogowego wybierz pozycję **więcej opcji**. To polecenie wyświetla listę rozwijaną, w której tworzysz lub wybierasz certyfikat, aby można było szyfrować informacje o poświadczeniach podczas nawiązywania połączenia za pośrednictwem pulpitu zdalnego.

   > [!Note]
   > Certyfikaty, które są potrzebne dla połączenia pulpitu zdalnego, różnią się od certyfikatów używanych dla innych operacji platformy Azure. Certyfikat dostępu zdalnego musi mieć klucz prywatny.

5. Wybierz certyfikat z listy lub wybierz pozycję ** &lt; Utwórz... &gt; **. W przypadku tworzenia nowego certyfikatu podaj przyjazną nazwę dla nowego certyfikatu po wyświetleniu monitu, a następnie wybierz **przycisk OK**. Nowy certyfikat pojawi się w polu listy rozwijanej.

6. Podaj nazwę użytkownika i hasło. Nie można użyć istniejącego konta. Nie używaj "administratora" jako nazwy użytkownika dla nowego konta.

7. Wybierz datę wygaśnięcia konta i, po upływie którego Pulpit zdalny połączenia będą blokowane.

8. Po podaniu wszystkich wymaganych informacji wybierz **przycisk OK**. Program Visual Studio dodaje Pulpit zdalny ustawienia do projektu `.cscfg` i `.csdef` plików, łącznie z hasłem zaszyfrowanym przy użyciu wybranego certyfikatu.

9. Wykonaj wszystkie pozostałe kroki przy użyciu przycisku **dalej** , a następnie wybierz pozycję **Publikuj** , gdy wszystko jest gotowe do opublikowania usługi w chmurze. Jeśli nie jesteś gotowy do opublikowania, wybierz pozycję **Anuluj** i odpowiedź **tak** po wyświetleniu monitu o zapisanie zmian. Usługę w chmurze można opublikować później przy użyciu tych ustawień.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>Skonfiguruj Pulpit zdalny w przypadku korzystania z programu Visual Studio 2017 w wersji 15,5 lub nowszej

W programie Visual Studio 2017 w wersji 15,5 lub nowszej można nadal używać Kreatora publikacji z projektem usługi w chmurze. Możesz również użyć opcji **włącz pulpit zdalny dla wszystkich ról** , jeśli pracujesz tylko jako jeden Deweloper.

W przypadku pracy w ramach zespołu należy zamiast tego włączyć pulpit zdalny w usłudze w chmurze platformy Azure przy użyciu [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) lub [programu PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

To zalecenie wynika ze zmian w sposobie, w jaki program Visual Studio 2017 w wersji 15,5 lub nowszej komunikuje się z maszyną wirtualną usługi w chmurze. Podczas włączania Pulpit zdalny za pomocą Kreatora publikacji wcześniejsze wersje programu Visual Studio komunikują się z maszyną wirtualną za pomocą tego, co jest nazywane "wtyczką RDP". Program Visual Studio 2017 w wersji 15,5 i nowszej zamiast tego komunikuje się z użyciem "rozszerzenia RDP", które jest bardziej bezpieczne i bardziej elastyczne. Ta zmiana jest również wyrównana z faktem, że metody Azure Portal i PowerShell do włączenia Pulpit zdalny również używają rozszerzenia RDP.

Gdy program Visual Studio komunikuje się z rozszerzeniem RDP, przesyła hasło w postaci zwykłego tekstu za pośrednictwem protokołu TLS. Jednak pliki konfiguracji projektu przechowują tylko zaszyfrowane hasło, które można odszyfrować w postaci zwykłego tekstu tylko przy użyciu certyfikatu lokalnego, który został pierwotnie użyty do jego zaszyfrowania.

Jeśli projekt usługi w chmurze zostanie wdrożony z tego samego komputera deweloperskiego za każdym razem, ten certyfikat lokalny jest dostępny. W takim przypadku można nadal korzystać z opcji **włącz pulpit zdalny dla wszystkich ról** w Kreatorze publikacji.

Jeśli jednak deweloperzy chcą wdrożyć projekt usługi w chmurze z różnych komputerów, te inne komputery nie będą mieć niezbędnego certyfikatu do odszyfrowania hasła. W związku z tym zostanie wyświetlony następujący komunikat o błędzie:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Hasło można zmienić przy każdym wdrożeniu usługi w chmurze, ale ta akcja będzie niewygodna dla wszystkich użytkowników, którzy muszą korzystać z Pulpit zdalny.

Jeśli projekt jest udostępniany zespołowi, najlepszym rozwiązaniem jest wyczyszczenie opcji w Kreatorze publikacji i zamiana Pulpit zdalny bezpośrednio za pośrednictwem [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md) lub przy użyciu [programu PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Wdrażanie z serwera kompilacji z programem Visual Studio 2017 w wersji 15,5 lub nowszej

Projekt usługi w chmurze można wdrożyć na serwerze kompilacji (na przykład z Azure DevOps Services), na którym jest zainstalowany program Visual Studio 2017 w wersji 15,5 lub nowszej w agencie kompilacji. W tym rozmieszczeniu wdrożenie odbywa się z tego samego komputera, na którym jest dostępny certyfikat szyfrowania.

Aby użyć rozszerzenia RDP z Azure DevOps Services, należy uwzględnić następujące szczegóły w potoku kompilacji:

1. Dołącz `/p:ForceRDPExtensionOverPlugin=true` do argumentów programu MSBuild, aby upewnić się, że wdrożenie współpracuje z rozszerzeniem RDP, a nie z wtyczką RDP. Na przykład:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. Po wykonaniu kroków kompilacji Dodaj krok **wdrożenie usługi w chmurze Azure** i ustaw jego właściwości.

1. Po kroku wdrożenia Dodaj krok **programu Azure PowerShell** , ustaw jego właściwość **Nazwa wyświetlana** na "Azure Deployment: Enable RDP" (lub inną odpowiednią nazwę), a następnie wybierz odpowiednią subskrypcję platformy Azure.

1. Ustaw **Typ skryptu** na "inline" i wklej poniższy kod do pola **skryptu wbudowanego** . (Możesz również utworzyć `.ps1` plik w projekcie za pomocą tego skryptu, ustawić **Typ skryptu** na "ścieżka do pliku skryptu" i ustawić **ścieżkę skryptu** , aby wskazywała plik).

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Nawiązywanie połączenia z rolą platformy Azure przy użyciu Pulpit zdalny

Po opublikowaniu usługi w chmurze na platformie Azure i włączeniu Pulpit zdalny można użyć programu Visual Studio Eksplorator serwera do zalogowania się do maszyny wirtualnej usługi w chmurze:

1. W Eksplorator serwera rozwiń węzeł **Azure** , a następnie rozwiń węzeł usługi w chmurze i jedną z jej ról, aby wyświetlić listę wystąpień.

2. Kliknij prawym przyciskiem myszy węzeł wystąpienia i wybierz polecenie **Połącz przy użyciu pulpit zdalny**.

3. Wprowadź nazwę użytkownika i hasło, które zostały wcześniej utworzone. Użytkownik jest zalogowany do sesji zdalnej.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Jak skonfigurować Cloud Services](cloud-services-how-to-configure-portal.md)

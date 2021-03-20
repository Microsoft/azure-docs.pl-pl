---
title: Tworzenie graficznego elementu Runbook w Azure Automation
description: W tym artykule przedstawiono sposób tworzenia, testowania i publikowania prostego graficznego elementu Runbook w Azure Automation.
keywords: runbook, szablon runbook, automatyzacja runbook, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 1b6c02778b0ee790d81c713283e653058c29c153
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92899800"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Samouczek: Tworzenie graficznego elementu Runbook

Ten samouczek przeprowadzi Cię przez proces tworzenia [graficznego elementu Runbook](../automation-runbook-types.md#graphical-runbooks) w usłudze Azure Automation. Możesz tworzyć i edytować graficzne i graficzne elementy Runbook przepływu pracy programu PowerShell przy użyciu edytora graficznego w Azure Portal. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie prostego graficznego elementu Runbook
> * Testowanie i publikowanie elementu Runbook
> * Uruchamianie i śledzenie stanu zadania elementu Runbook
> * Zaktualizuj element Runbook, aby uruchomić maszynę wirtualną platformy Azure z użyciem parametrów elementu Runbook i linków warunkowych

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Konto usługi Automation](../index.yml) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna ona być produkcyjną maszyną wirtualną.
* W razie potrzeby [zaimportuj moduły platformy Azure](../shared-resources/modules.md) lub [Aktualizuj moduły](../automation-update-azure-modules.md) na podstawie używanych poleceń cmdlet.

## <a name="step-1---create-runbook"></a>Krok 1. Tworzenie elementu runbook

Zacznij od utworzenia prostego elementu Runbook, który wyświetla tekst `Hello World` .

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z tych elementów zawartości to moduły automatycznie dołączone do nowego konta usługi Automation. Należy również mieć zasób poświadczeń skojarzony z subskrypcją.

2. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.

3. Utwórz nowy element Runbook, wybierając pozycję **Utwórz element Runbook**.

4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Graphical**.

5. W tym przypadku utworzysz [graficzny element Runbook](../automation-graphical-authoring-intro.md). Wybierz pozycję **graficzny** dla **typu elementu Runbook**.

    ![Nowy element Runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor graficzny.

## <a name="step-2---add-activities"></a>Krok 2. Dodawanie działań

Kontrolka Biblioteka po lewej stronie edytora umożliwia wybranie działań do dodania do elementu Runbook. Zamierzasz dodać `Write-Output` polecenie cmdlet do tekstu wyjściowego z elementu Runbook.

1. W kontrolce Biblioteka kliknij pole wyszukiwania i wpisz `write-output` . Wyniki wyszukiwania są wyświetlane na poniższej ilustracji.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Przewiń listę w dół. Kliknij prawym przyciskiem myszy pozycję **Write-Output** i wybierz pozycję **Dodaj do kanwy**. Alternatywnie możesz kliknąć przycisk wielokropka (...) obok nazwy polecenia cmdlet, a następnie wybrać pozycję **Dodaj do kanwy**.

3. Kliknij pozycję **Write-Output** na kanwie. Ta akcja powoduje otwarcie strony kontrola konfiguracji, która umożliwia skonfigurowanie działania.

4. Nazwa pola **etykieta** jest domyślnie ustawiona na nazwę polecenia cmdlet, ale można ją zmienić na bardziej przyjazną. Zmień ją na `Write Hello World to output`.

5. Kliknij pozycję **Parametry**, aby podać wartości parametrów polecenia cmdlet.

   Niektóre polecenia cmdlet mają wiele zestawów parametrów i należy wybrać, który z nich ma być używany. W tym przypadku `Write-Output` ma tylko jeden zestaw parametrów.

6. Wybierz `InputObject` parametr. Jest to parametr używany do określania tekstu do wysłania do strumienia wyjściowego.

7. Menu rozwijane **Źródło danych** zawiera źródła, których można użyć do wypełniania wartości parametru. W tym menu wybierz pozycję **wyrażenie programu PowerShell**.

   Możesz użyć danych wyjściowych z takich źródeł jako innego działania, zasobu usługi Automation lub wyrażenia programu PowerShell. W tym przypadku dane wyjściowe są właśnie `Hello World` . Możesz użyć wyrażenia programu PowerShell i wprowadzić ciąg.

8. W polu **wyrażenie** wpisz, `"Hello World"` a następnie dwukrotnie kliknij przycisk **OK** , aby powrócić do kanwy.

9. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem elementu Runbook w celu udostępnienia go w środowisku produkcyjnym należy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook uruchamia jego wersję roboczą i umożliwia interakcyjne wyświetlanie danych wyjściowych.

1. Wybierz **okienko testowania** , aby otworzyć okienko testowania.

2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.

3. Należy pamiętać, że [zadanie elementu Runbook](../automation-runbook-execution.md) jest tworzone i jego stan jest wyświetlany w okienku.

   Stan zadania zaczyna się od `Queued` , co oznacza, że zadanie oczekuje na dostępność elementu Runbook Worker w chmurze. Stan zmieni się na `Starting` po potwierdzeniu pracownika przez proces roboczy. Na koniec stan zmieni się, `Running` gdy element Runbook faktycznie zacznie działać.

4. Po zakończeniu zadania elementu Runbook w okienku test zostaną wyświetlone dane wyjściowe. W tym przypadku zobaczysz `Hello World` .

    ![Hello world danych wyjściowych elementu Runbook](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony element Runbook jest nadal w trybie wersji roboczej. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Wybierz pozycję **Publikuj** , aby opublikować element Runbook, a następnie przycisk **tak** po wyświetleniu monitu.

2. Przewiń w lewo, aby wyświetlić element Runbook na stronie elementy Runbook, i pamiętaj, że wartość **Stan tworzenia** jest ustawiona na **Publikowanie**.

3. Przewiń z powrotem w prawo, aby wyświetlić stronę **MyFirstRunbook-Graphic**.

   Opcje w górnej części umożliwiają rozpoczęcie elementu Runbook, zaplanowanie przyszłego czasu rozpoczęcia lub utworzenie [elementu webhook](../automation-webhooks.md) , aby można było uruchomić element Runbook za pośrednictwem wywołania http.

4. Wybierz przycisk **Start** , a następnie pozycję **tak** po wyświetleniu monitu o uruchomienie elementu Runbook.

5. Zostanie otwarte okienko zadań dla zadania elementu Runbook, które zostało utworzone. Sprawdź, czy pole **stan zadania** zawiera wartość **ukończono**.

6. Kliknij pozycję **dane wyjściowe** , aby otworzyć stronę wyjściową, w której można wyświetlić `Hello World` wyświetlane dane.

7. Zamknij stronę wyjściową.

8. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być widoczny tylko `Hello World` w strumieniu danych wyjściowych.

    Należy pamiętać, że w okienku strumienie mogą być wyświetlane inne strumienie zadania elementu Runbook, takie jak pełne strumienie i błędy, jeśli element Runbook zapisze w nich.

9. Zamknij okienko strumieni i okienko zadania, aby wrócić do strony MyFirstRunbook-Graphical.

10. Aby wyświetlić wszystkie zadania dla elementu Runbook, wybierz pozycję **zadania** w obszarze **zasoby**. Na stronie zadania są wyświetlane wszystkie zadania utworzone przez element Runbook. Powinno zostać wyświetlone tylko jedno zadanie, ponieważ zostało uruchomione tylko raz.

11. Kliknij nazwę zadania, aby otworzyć okienko zadania, które zostało wyświetlone po uruchomieniu elementu Runbook. To okienko służy do wyświetlania szczegółów wszystkich zadań utworzonych dla elementu Runbook.

## <a name="step-5---create-variable-assets"></a>Krok 5. Tworzenie zmiennych elementów zawartości

Element Runbook został przetestowany i opublikowany, ale dotąd nie jest to przydatne do zarządzania zasobami platformy Azure. Przed skonfigurowaniem elementu Runbook do uwierzytelniania należy utworzyć zmienną do przechowywania identyfikatora subskrypcji, skonfigurować działanie do uwierzytelniania, a następnie odwołać się do zmiennej. Dołączenie odwołania do kontekstu subskrypcji pozwala łatwo współpracować z wieloma subskrypcjami.

1. Skopiuj identyfikator subskrypcji z opcji **subskrypcje** w okienku nawigacji.

2. Na stronie konta usługi Automation wybierz **zmienne** w obszarze **zasoby udostępnione**.

3. Wybierz pozycję **Dodaj zmienną**.

4. Na stronie Nowa zmienna wprowadź następujące ustawienia w udostępnionych polach.

    * **Nazwa** — wprowadź `AzureSubscriptionId` .
    * **Wartość** — wprowadź swój identyfikator subskrypcji.
    * **Typ** — Zachowaj wybraną ciąg.
    * **Szyfrowanie** — Użyj wartości domyślnej.

5. Kliknij pozycję **Utwórz**, aby utworzyć zmienną.

## <a name="step-6---add-authentication"></a>Krok 6. Dodawanie uwierzytelniania

Teraz, gdy masz zmienną do przechowywania identyfikatora subskrypcji, możesz skonfigurować element Runbook do uwierzytelniania przy użyciu poświadczeń Uruchom jako dla subskrypcji. W tym celu Dodaj jako element zawartości połączenie Uruchom jako platformy Azure. Należy również dodać polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) i polecenie cmdlet [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) do kanwy.

>[!NOTE]
>Dla elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Należy zauważyć, że te aliasy nie są dostępne dla graficznych elementów Runbook. Graficzny element Runbook może korzystać tylko z `Connect-AzAccount` siebie.

1. Przejdź do elementu Runbook i wybierz pozycję **Edytuj** na stronie MyFirstRunbook-Graphical.

2. Ten wpis nie jest potrzebny `Write Hello World to output` . Po prostu kliknij wielokropek i wybierz pozycję **Usuń**.

3. W kontrolce Biblioteka rozwiń węzeł **zasoby**, a następnie pozycję **połączenia**. Dodaj `AzureRunAsConnection` do kanwy, wybierając pozycję **Dodaj do kanwy**.

4. Zmień nazwę `AzureRunAsConnection` na `Get Run As Connection` .

5. W kontrolce Biblioteka wpisz `Connect-AzAccount` w polu wyszukiwania.

6. Dodaj `Connect-AzAccount` do kanwy.

7. Umieść wskaźnik myszy `Get Run As Connection` , dopóki okrąg nie pojawi się w dolnej części kształtu. Kliknij okrąg i przeciągnij strzałkę do, `Connect-AzAccount` Aby utworzyć łącze. Element Runbook zaczyna się od `Get Run As Connection` , a następnie uruchamia `Connect-AzAccount` .

    ![Tworzenie połączenia między działaniami](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Na kanwie wybierz pozycję `Connect-AzAccount` . W okienku kontrola konfiguracji wpisz **Logowanie do platformy Azure** w polu **etykieta** .

9. Kliknij pozycję **Parametry**, a zostanie wyświetlona strona Konfiguracja parametru działania.

10. `Connect-AzAccount`Polecenie cmdlet ma wiele zestawów parametrów i należy wybrać jeden przed podaniem wartości parametrów. Kliknij pozycję **zestaw parametrów** , a następnie wybierz pozycję **ServicePrincipalCertificateWithSubscriptionId**.

11. Parametry dla tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametru działania. Kliknij pozycję **APPLICATIONID**.

    ![Dodawanie parametrów konta platformy Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Na stronie wartość parametru wprowadź następujące ustawienia, a następnie kliknij przycisk **OK**.

   * **Źródło danych** — wybierz pozycję **dane wyjściowe działania**.
   * Lista źródeł danych — wybierz pozycję **Pobierz połączenie automatyzacji**.
   * **Ścieżka pola** --wpisz `ApplicationId` . Określasz nazwę właściwości dla ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami.

13. Kliknij pozycję **CERTIFICATETHUMBPRINT** i na stronie wartość parametru wprowadź następujące ustawienia, a następnie kliknij przycisk **OK**.

    * **Źródło danych** — wybierz pozycję **dane wyjściowe działania**.
    * Lista źródeł danych — wybierz pozycję **Pobierz połączenie automatyzacji**.
    * **Ścieżka pola** --wpisz `CertificateThumbprint` .

14. Kliknij pozycję **serviceprincipal** i na stronie wartość parametru wybierz pozycję **ConstantValue** dla pola **Źródło danych** . Kliknij opcję **prawda**, a następnie kliknij przycisk **OK**.

15. Kliknij pozycję **TENANTID** i wprowadź następujące ustawienia na stronie wartości parametru. Po zakończeniu kliknij przycisk **OK** dwa razy.

    * **Źródło danych** — wybierz pozycję **dane wyjściowe działania**.
    * Lista źródeł danych — wybierz pozycję **Pobierz połączenie automatyzacji**.
    * **Ścieżka pola** --wpisz `TenantId` .

16. W kontrolce Biblioteka wpisz `Set-AzContext` w polu wyszukiwania.

17. Dodaj `Set-AzContext` do kanwy.

18. Wybierz pozycję `Set-AzContext` na kanwie. W okienku sterowania konfiguracją wprowadź `Specify Subscription Id` w polu **etykieta** .

19. Kliknij przycisk **Parametry** i zostanie wyświetlona strona Konfiguracja parametru działania.

20. `Set-AzContext`Polecenie cmdlet ma wiele zestawów parametrów i należy wybrać jeden przed podaniem wartości parametrów. Kliknij pozycję **zestaw parametrów** , a następnie wybierz pozycję Identyfikator **subskrypcji**.

21. Parametry dla tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametru działania. Kliknij pozycję Identyfikator **subskrypcji**.

22. Na stronie wartość parametru wybierz opcję **zasób zmiennej** dla pola **Źródło danych** i wybierz pozycję **AzureSubscriptionId** z listy źródło. Po zakończeniu kliknij przycisk **OK** dwa razy.

23. Umieść wskaźnik myszy `Login to Azure` , dopóki okrąg nie pojawi się w dolnej części kształtu. Kliknij okrąg i przeciągnij strzałkę do `Specify Subscription Id` . Element Runbook powinien wyglądać podobnie do poniższego.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Zrzut ekranu elementu Runbook po przeciągnięciu strzałki do elementu &quot;Określ Identyfikator subskrypcji&quot;.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Krok 7. Dodawanie działania w celu uruchomienia maszyny wirtualnej

Teraz musisz dodać działanie, `Start-AzVM` Aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure, a teraz zakodowana jej nazwę w poleceniu cmdlet [Start-AzVM](/powershell/module/az.compute/start-azvm) .

1. W kontrolce Biblioteka wpisz `Start-Az` w polu wyszukiwania.

2. Dodaj `Start-AzVM` element do kanwy, a następnie kliknij i przeciągnij go poniżej `Specify Subscription Id` .

3. Umieść wskaźnik myszy `Specify Subscription Id` , dopóki okrąg nie pojawi się w dolnej części kształtu. Kliknij okrąg i przeciągnij strzałkę do `Start-AzVM` .

4. Wybierz pozycję `Start-AzVM`. Kliknij opcję **Parametry** , a następnie pozycję **zestaw parametrów** , aby wyświetlić zestawy dla działania.

5. Wybierz pozycję **ResourceGroupNameParameterSetName** dla zestawu parametrów. Pola **ResourceGroupName** i **name** mają wykrzykniki obok nich, aby wskazać, że są one wymaganymi parametrami. Należy zauważyć, że oba pola oczekują wartości ciągu.

6. Wybierz pozycję **Nazwa**. Wybierz **wyrażenie programu PowerShell** dla pola **Źródło danych** . W przypadku maszyny wirtualnej, która jest używana do uruchamiania tego elementu Runbook, wpisz nazwę maszyny ujętą w podwójne cudzysłowy. Kliknij przycisk **OK**.

7. Wybierz pozycję **ResourceGroupName**. Użyj **wyrażenia programu PowerShell** wartości dla pola **Źródło danych** i wpisz nazwę grupy zasobów ujętej w podwójne cudzysłowy. Kliknij przycisk **OK**.

8. Kliknij pozycję **okienko testowania** , aby umożliwić przetestowanie elementu Runbook.

9. Kliknij przycisk **Rozpocznij** , aby rozpocząć test. Po zakończeniu upewnij się, że maszyna wirtualna została uruchomiona. Element Runbook powinien wyglądać podobnie do poniższego.

    ![Dane wyjściowe elementu Runbook Start-AzVM](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Krok 8. Dodawanie dodatkowych parametrów wejściowych

Element Runbook aktualnie uruchamia maszynę wirtualną w grupie zasobów określonej dla tego `Start-AzVM` polecenia cmdlet. Element Runbook będzie bardziej użyteczny, jeśli podczas uruchamiania elementu Runbook określisz nazwę i grupę zasobów. Dodajmy do elementu Runbook parametry wejściowe w celu zapewnienia tej funkcjonalności.

1. Otwórz edytor graficzny, klikając pozycję **Edytuj** na stronie MyFirstRunbook-Graphical.

2. Wybierz pozycję **dane wejściowe i wyjściowe** , a następnie **Dodaj dane wejściowe** , aby otworzyć okienko parametru wejściowego elementu Runbook.

3. Wprowadź następujące ustawienia w podanych polach, a następnie kliknij przycisk **OK**.
   * **Nazwa** — Określ `VMName` .
   * **Wpisz** --Zachowaj ustawienie ciągu.
   * **Obowiązkowe** — Zmień wartość na **tak**.

4. Utwórz drugi obowiązkowy parametr wejściowy o nazwie `ResourceGroupName` , a następnie kliknij przycisk **OK** , aby zamknąć okienko dane wejściowe i wyjściowe.

    ![Parametry wejściowe elementu Runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Wybierz `Start-AzVM` działanie, a następnie kliknij pozycję **Parametry**.

6. Zmień pole **źródła danych** **na** **dane wejściowe elementu Runbook**. Następnie wybierz pozycję **VMName**.

7. Zmień pole **źródła danych** dla **ResourceGroupName** na **dane wejściowe elementu Runbook** , a następnie wybierz pozycję **ResourceGroupName**.

    ![Parametry Start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.

9. Zamknij okienko testowania.

10. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.

11. Zatrzymaj maszynę wirtualną, która została wcześniej uruchomiona.

12. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości dla `VMName` i `ResourceGroupName` dla maszyny wirtualnej, która ma zostać uruchomiona.

13. Po zakończeniu działania elementu Runbook upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="step-9---create-a-conditional-link"></a>Krok 9. Tworzenie połączenia warunkowego

Teraz można zmodyfikować element Runbook, aby próbował uruchomić maszynę wirtualną, jeśli nie została jeszcze uruchomiona. W tym celu dodaj polecenie cmdlet [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) , które pobiera stan wystąpienia maszyny wirtualnej. Następnie można dodać moduł kodu przepływu pracy programu PowerShell o nazwie `Get Status` z fragmentem kodu programu PowerShell, aby określić, czy stan maszyny wirtualnej jest uruchomiony lub zatrzymany. Łącze warunkowe z `Get Status` modułu działa tylko `Start-AzVM` wtedy, gdy bieżący stan uruchomienia jest zatrzymany. Po zakończeniu tej procedury element Runbook używa `Write-Output` polecenia cmdlet do wyprowadzania komunikatu, aby poinformować użytkownika o tym, czy maszyna wirtualna została pomyślnie uruchomiona.

1. Otwórz **MyFirstRunbook-graficznej** w edytorze graficznym.

2. Usuń łącze między `Specify Subscription Id` i, `Start-AzVM` klikając je, a następnie naciskając klawisz **delete**.

3. W kontrolce Biblioteka wpisz `Get-Az` w polu wyszukiwania.

4. Dodaj `Get-AzVM` do kanwy.

5. Wybierz `Get-AzVM` , a następnie kliknij pozycję **zestaw parametrów** , aby wyświetlić zestawy dla tego polecenia cmdlet.

6. Wybierz zestaw parametrów **GetVirtualMachineInResourceGroupNameParamSet**. Pola **ResourceGroupName** i **name** mają wykrzykniki obok nich, wskazując, że określają wymagane parametry. Należy zauważyć, że oba pola oczekują wartości ciągu.

7. W obszarze **Źródło danych** dla pozycji **Nazwa** wybierz pozycję **dane wejściowe elementu Runbook**, a następnie **VMName**. Kliknij przycisk **OK**.

8. W obszarze **Źródło danych** dla **ResourceGroupName** wybierz pozycję **dane wejściowe elementu Runbook**, a następnie **ResourceGroupName**. Kliknij przycisk **OK**.

9. W obszarze **Źródło danych** dla pozycji **stan** wybierz opcję **stała wartość**, a następnie **wartość true**. Kliknij przycisk **OK**.

10. Utwórz łącze z `Specify Subscription Id` do `Get-AzVM` .

11. W kontrolce Biblioteka rozwiń pozycję **Kontrola elementu Runbook** i Dodaj **kod** do kanwy.  

12. Utwórz łącze z `Get-AzVM` do `Code` .  

13. Kliknij `Code` i w okienku Konfiguracja Zmień etykietę, aby **uzyskać stan**.

14. Wybierz `Code` i zostanie wyświetlona strona Edytor kodu.  

15. Wklej poniższy fragment kodu do strony edytora.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Utwórz łącze z `Get Status` do `Start-AzVM` .

    ![Element Runbook z modułem kodu](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Wybierz łącze i w okienku Konfiguracja Zmień **warunek Zastosuj** na **wartość tak**. Należy pamiętać, że łącze stanie się linią kreskowaną, co oznacza, że działanie docelowe jest uruchamiane tylko wtedy, gdy warunek zostanie spełniony.  

18. Dla **wyrażenia warunku** wpisz `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` teraz działa tylko wtedy, gdy maszyna wirtualna jest zatrzymana.

19. W kontrolce Biblioteka rozwiń pozycję **Polecenia cmdlet**, a następnie pozycję **Microsoft.PowerShell.Utility**.

20. `Write-Output`Dwa razy Dodaj do kanwy.

21. W przypadku pierwszej `Write-Output` kontrolki kliknij pozycję **Parametry** i zmień wartość **etykieta** na **powiadamianie o uruchomieniu maszyny wirtualnej**.

22. Dla elementu **inputobject** Zmień **Źródło danych** na **wyrażenie programu PowerShell** i wpisz wyrażenie `$VMName successfully started.` .

23. Na drugiej `Write-Output` kontrolce kliknij pozycję **Parametry** , a następnie zmień wartość **etykieta** na **powiadamianie o nieudanym uruchomieniu maszyny wirtualnej**.

24. Dla elementu **inputobject** Zmień **Źródło danych** na **wyrażenie programu PowerShell** i wpisz wyrażenie `$VMName could not start` .

25. Utwórz linki z `Start-AzVM` do `Notify VM Started` i `Notify VM Start Failed` .

26. Wybierz łącze do `Notify VM Started` i Zmień **warunek zastosowania** na true.

27. Dla **wyrażenia warunku** wpisz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Ta `Write-Output` kontrolka jest teraz uruchamiana tylko wtedy, gdy maszyna wirtualna zostanie uruchomiona pomyślnie.

28. Wybierz łącze do `Notify VM Start Failed` i Zmień **warunek zastosowania** na true.

29. Dla pola **wyrażenie warunku** wpisz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Ta `Write-Output` kontrolka jest teraz uruchamiana tylko wtedy, gdy maszyna wirtualna nie została pomyślnie uruchomiona. Element Runbook powinien wyglądać jak na poniższej ilustracji.

    ![Element Runbook z pozycją Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Zapisz element Runbook, a następnie otwórz okienko testowania.

31. Uruchom element Runbook z zatrzymaną maszyną wirtualną i uruchom maszynę.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu graficznym, zobacz [Tworzenie graficznego elementu Runbook w Azure Automation](../automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz [Tworzenie elementu Runbook programu PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz temat [Tworzenie elementu Runbook przepływu pracy programu PowerShell](automation-tutorial-runbook-textual.md).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).

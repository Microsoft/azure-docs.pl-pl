---
title: Tworzenie graficznego element runbook w Azure Automation
description: W tym artykule nauczysz się tworzyć, testować i publikować prosty graficzny element Runbook w Azure Automation.
keywords: runbook, szablon runbook, automatyzacja runbook, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 668f661d2c8f349f4dac3f850800d83890d2f4d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831442"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Samouczek: tworzenie graficznego element runbook

Ten samouczek przeprowadzi Cię przez proces tworzenia [graficznego elementu Runbook](../automation-runbook-types.md#graphical-runbooks) w usłudze Azure Automation. Graficzne i graficzne elementy Runbook przepływu pracy programu PowerShell można tworzyć i edytować przy użyciu edytora graficznego w Azure Portal. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie prostego graficznego element runbook
> * Testowanie i publikowanie runbook
> * Uruchamianie i śledzenie stanu zadania runbook
> * Aktualizowanie element runbook w celu uruchomienia maszyny wirtualnej platformy Azure przy użyciu parametrów i linków warunkowych

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Konto usługi Automation](../index.yml) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna to być produkcyjna maszyna wirtualna.
* W razie potrzeby [zaimportuj moduły platformy Azure](../shared-resources/modules.md) lub [zaktualizuj moduły](../automation-update-azure-modules.md) w oparciu o polecenia cmdlet, których używasz.

## <a name="step-1---create-runbook"></a>Krok 1. Tworzenie elementu runbook

Rozpocznij od utworzenia prostego podręcznika runbook, który wyprowadza tekst `Hello World` .

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość tych zasobów to moduły automatycznie uwzględniane na nowym koncie usługi Automation. Z subskrypcją powinien być również skojarzony zasób Poświadczenie.

2. Wybierz **pozycję Runbook** w obszarze **Automatyzacja procesów,** aby otworzyć listę elementów Runbook.

3. Utwórz nowy podręcznik Runbook, wybierając **pozycję Utwórz.**

4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Graphical**.

5. W tym przypadku utworzysz graficzny element [runbook](../automation-graphical-authoring-intro.md). Wybierz **opcję Graficzne** dla typu **runbook**.

    ![Nowy element Runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor graficzny.

## <a name="step-2---add-activities"></a>Krok 2. Dodawanie działań

Kontrolka Biblioteka po lewej stronie edytora umożliwia wybranie działań do dodania do elementu Runbook. Dodasz polecenie `Write-Output` cmdlet służące do wyprowadzania tekstu z runbook.

1. W kontrolce Biblioteka kliknij pole wyszukiwania i wpisz `write-output` . Wyniki wyszukiwania przedstawiono na poniższej ilustracji.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Przewiń listę w dół. Kliknij prawym przyciskiem myszy **pozycję Write-Output i** wybierz **polecenie Dodaj do kanwy.** Alternatywnie możesz kliknąć wielokropek (...) obok nazwy polecenia cmdlet, a następnie wybrać pozycję **Dodaj do kanwy.**

3. Kliknij pozycję **Write-Output** na kanwie. Ta akcja powoduje otwarcie strony Kontrolka konfiguracji, na której można skonfigurować działanie.

4. Wartość **domyślna** pola Etykieta to nazwa polecenia cmdlet, ale można ją zmienić na bardziej przyjazną. Zmień ją na `Write Hello World to output`.

5. Kliknij pozycję **Parametry**, aby podać wartości parametrów polecenia cmdlet.

   Niektóre polecenia cmdlet mają wiele zestawów parametrów i należy wybrać, którego z nich użyć. W tym przypadku `Write-Output` parametr ma tylko jeden zestaw parametrów.

6. Wybierz `InputObject` parametr . Jest to parametr, który służy do określania tekstu do wysłania do strumienia wyjściowego.

7. Menu **rozwijane Źródło** danych zawiera źródła, których można użyć do wypełnienia wartości parametru. W tym menu wybierz pozycję **Wyrażenie programu PowerShell.**

   Możesz użyć danych wyjściowych z takich źródeł, jak inne działanie, zasób usługi Automation lub wyrażenie programu PowerShell. W tym przypadku dane wyjściowe to po prostu `Hello World` . Możesz użyć wyrażenia programu PowerShell i wprowadzić ciąg.

8. W polu **Wyrażenie** wpisz , `"Hello World"` a następnie kliknij dwukrotnie przycisk **OK,** aby wrócić do kanwy.

9. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem podręcznika Runbook w celu jego udostępnić w środowisku produkcyjnym należy go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementów Runbook uruchamia jego wersję roboczą i umożliwia interaktywne wyświetlanie jego danych wyjściowych.

1. Wybierz **pozycję Okienko testowania,** aby otworzyć okienko Test.

2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.

3. Pamiętaj, że [zadanie runbook](../automation-runbook-execution.md) jest tworzone, a jego stan jest wyświetlany w okienku.

   Stan zadania rozpoczyna się od , co oznacza, że zadanie oczekuje na dostęp do procesu roboczego `Queued` runbook w chmurze. Stan zmienia się na `Starting` , gdy proces roboczy oświadczenia zadania. Na koniec stan staje się wtedy, gdy faktycznie zacznie `Running` działać ten runbook.

4. Po zakończeniu zadania runbook w okienku Test zostaną wyświetlone jego dane wyjściowe. W tym przypadku zobaczysz `Hello World` .

    ![Hello world danych wyjściowych runbook](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony przez Ciebie runbook jest nadal w trybie wersji roboczej. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Wybierz **pozycję Publikuj,** aby opublikować ten runbook, a **następnie wybierz pozycję Tak** po wyświetleniu monitu.

2. Przewiń w lewo, aby wyświetlić ten runbook na stronie Runbook, i zwróć uwagę, że wartość **Stan** tworzenia jest ustawiona na **Opublikowano.**

3. Przewiń w prawo, aby wyświetlić stronę **myFirstRunbook-Graphical**.

   Opcje w górnej części pozwalają uruchomić element Runbook teraz, zaplanować przyszły czas rozpoczęcia lub utworzyć element [webhook,](../automation-webhooks.md) aby element Runbook można było uruchomić za pośrednictwem wywołania HTTP.

4. Wybierz **pozycję Uruchom,** a następnie wybierz pozycję **Tak** po wyświetleniu monitu o uruchomienie runbook.

5. Zostanie otwarte okienko Zadania dla utworzonego zadania runbook. Sprawdź, czy **pole Stan zadania** ma stan **Ukończono.**

6. Kliknij **pozycję Dane** wyjściowe, aby otworzyć stronę Dane wyjściowe. `Hello World`

7. Zamknij stronę Dane wyjściowe.

8. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinny być one dostępne `Hello World` tylko w strumieniu wyjściowym.

    Należy pamiętać, że w okienku Strumienie mogą być wyświetlane inne strumienie zadania runbook, takie jak pełne i błędne strumienie, jeśli są w nich zapisywane.

9. Zamknij okienko Strumienie i okienko Zadania, aby wrócić do MyFirstRunbook-Graphical strony.

10. Aby wyświetlić wszystkie zadania dla tego runbook, wybierz pozycję **Zadania w** obszarze **Zasoby.** Strona Zadania zawiera listę wszystkich zadań utworzonych przez twój runbook. Na liście powinno być wyświetlane tylko jedno zadanie, ponieważ zadanie jest uruchamiane tylko raz.

11. Kliknij nazwę zadania, aby otworzyć to samo okienko zadania, które było przeglądane podczas uruchamiania tego runbook. Użyj tego okienka, aby wyświetlić szczegóły dowolnego zadania utworzonego dla tego runbook.

## <a name="step-5---create-variable-assets"></a>Krok 5. Tworzenie zmiennych elementów zawartości

Udało Ci się przetestować i opublikować swój runbook, ale do tej pory nie robi on nic przydatnego do zarządzania zasobami platformy Azure. Przed skonfigurowaniem element Runbook do uwierzytelniania należy utworzyć zmienną do przechowywania identyfikatora subskrypcji, skonfigurować działanie do uwierzytelniania, a następnie odwołać się do zmiennej. Do uwzględnienia odwołania do kontekstu subskrypcji można łatwo pracować z wieloma subskrypcjami.

1. Skopiuj identyfikator subskrypcji z **opcji Subskrypcje** w okienku nawigacji.

2. Na stronie Konta usługi Automation wybierz pozycję **Zmienne w** obszarze **Udostępnione zasoby.**

3. Wybierz **pozycję Dodaj zmienną**.

4. Na stronie Nowa zmienna w podanych polach należy wprowadzić następujące ustawienia.

    * **Nazwa** — wprowadź `AzureSubscriptionId` .
    * **Wartość** — wprowadź identyfikator subskrypcji.
    * **Wpisz** - zachowaj wybrany ciąg.
    * **Szyfrowanie** — użyj wartości domyślnej.

5. Kliknij pozycję **Utwórz**, aby utworzyć zmienną.

## <a name="step-6---add-authentication"></a>Krok 6. Dodawanie uwierzytelniania

Teraz, gdy masz zmienną do przechowywania identyfikatora subskrypcji, możesz skonfigurować go do uwierzytelniania przy użyciu poświadczeń Uruchom jako dla subskrypcji. W tym celu dodaj połączenie Uruchom jako platformy Azure jako element zawartości. Należy również dodać do kanwy polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) i polecenie cmdlet [Set-AzContext.](/powershell/module/az.accounts/Set-AzContext)

>[!NOTE]
>W przypadku podręczników Runbook programu PowerShell `Add-AzAccount` `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Należy pamiętać, że te aliasy nie są dostępne dla graficznych elementów Runbook. Graficzny element Runbook może używać tylko `Connect-AzAccount` samego siebie.

1. Przejdź do swojego runbooka i wybierz **pozycję Edytuj** na MyFirstRunbook-Graphical aplikacji.

2. Wpis nie jest `Write Hello World to output` już potrzebny. Po prostu kliknij wielokropek i wybierz pozycję **Usuń.**

3. W kontrolce Biblioteka rozwiń pozycję **ZASOBY**, a następnie **pozycję Połączenia.** Dodaj `AzureRunAsConnection` do kanwy, wybierając pozycję **Dodaj do kanwy.**

4. Zmień `AzureRunAsConnection` nazwę na `Get Run As Connection` .

5. W kontrolce Biblioteka wpisz `Connect-AzAccount` w polu wyszukiwania.

6. Dodaj `Connect-AzAccount` do kanwy.

7. Umieść kursor `Get Run As Connection` nad kursorem, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do pozycji , `Connect-AzAccount` aby utworzyć link. Ten podręcznik Runbook rozpoczyna się od , `Get Run As Connection` a następnie uruchamia program `Connect-AzAccount` .

    ![Tworzenie połączenia między działaniami](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Na kanwie wybierz pozycję `Connect-AzAccount` . W okienku kontrolki Konfiguracja wpisz **Identyfikator logowania** do platformy Azure w polu **Etykieta.**

9. Kliknij **pozycję Parametry**. Zostanie wyświetlona strona Konfiguracja parametru działania.

10. Polecenie cmdlet ma wiele zestawów parametrów i należy wybrać jeden przed `Connect-AzAccount` podaniem wartości parametrów. Kliknij **pozycję Zestaw parametrów,** a następnie wybierz **pozycję ServicePrincipalCertificateWithSubscriptionId.**

11. Parametry tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametru działania. Kliknij pozycję **APPLICATIONID**.

    ![Dodawanie parametrów konta platformy Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Na stronie Wartość parametru wybierz następujące ustawienia, a następnie kliknij przycisk **OK.**

   * **Źródło danych** — wybierz pozycję **Dane wyjściowe działania**.
   * Lista źródeł danych — wybierz pozycję **Pobierz połączenie usługi Automation.**
   * **Ścieżka pola** — wpisz `ApplicationId` . Określasz nazwę właściwości ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami.

13. Kliknij **pozycję CERTIFICATETHUMBPRINT** i na stronie Wartość parametru wybierz następujące ustawienia, a następnie kliknij przycisk **OK.**

    * **Źródło danych** — wybierz pozycję **Dane wyjściowe działania**.
    * Lista źródeł danych — wybierz pozycję **Pobierz połączenie usługi Automation.**
    * **Ścieżka pola** — wpisz `CertificateThumbprint` .

14. Kliknij **pozycję SERVICEPRINCIPAL,** a następnie na stronie Wartość parametru wybierz **wartość ConstantValue** dla **pola Źródło** danych. Kliknij opcję **Prawda,** a następnie kliknij przycisk **OK.**

15. Kliknij **pozycję TENANTID** i na stronie Wartość parametru wybierz następujące ustawienia. Po zakończeniu kliknij dwukrotnie przycisk **OK.**

    * **Źródło danych** — wybierz pozycję **Dane wyjściowe działania**.
    * Lista źródeł danych — wybierz pozycję **Pobierz połączenie usługi Automation.**
    * **Ścieżka pola** — wpisz `TenantId` .

16. W kontrolce Biblioteka wpisz `Set-AzContext` w polu wyszukiwania.

17. Dodaj `Set-AzContext` do kanwy.

18. Wybierz `Set-AzContext` pozycję na kanwie. W okienku Kontrolka konfiguracji wprowadź `Specify Subscription Id` wartość w **polu Etykieta.**

19. Kliknij **pozycję Parametry.** Zostanie wyświetlona strona Konfiguracja parametrów działania.

20. Polecenie cmdlet ma wiele zestawów parametrów i należy je wybrać `Set-AzContext` przed podaniem wartości parametrów. Kliknij **pozycję Zestaw parametrów,** a następnie wybierz **pozycję SubscriptionId.**

21. Parametry tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametru działania. Kliknij **pozycję SubscriptionID**.

22. Na stronie Wartość parametru wybierz pozycję **Zasób** zmiennej dla **pola Źródło** danych, a następnie wybierz pozycję **AzureSubscriptionId** z listy źródeł. Po zakończeniu kliknij dwukrotnie przycisk **OK.**

23. Umieść kursor `Login to Azure` nad kursorem, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do pozycji `Specify Subscription Id` . Na tym etapie twój podręcznik Runbook powinien wyglądać podobnie do poniższego.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Zrzut ekranu przedstawiający runbook po przeciągnięciu strzałki do pozycji &quot;Określ identyfikator subskrypcji&quot;.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Krok 7. Dodawanie działania w celu uruchomienia maszyny wirtualnej

Teraz musisz dodać `Start-AzVM` działanie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure i na razie kodować jej nazwę do polecenia cmdlet [Start-AzVM.](/powershell/module/az.compute/start-azvm)

1. W kontrolce Biblioteka wpisz `Start-Az` w polu wyszukiwania.

2. Dodaj `Start-AzVM` element do kanwy, a następnie kliknij i przeciągnij go poniżej `Specify Subscription Id` .

3. Umieść kursor `Specify Subscription Id` nad kursorem, aż w dolnej części kształtu pojawi się okrąg. Kliknij okrąg i przeciągnij strzałkę do pozycji `Start-AzVM` .

4. Wybierz pozycję `Start-AzVM`. Kliknij **pozycję Parametry,** a następnie pozycję **Zestaw parametrów,** aby wyświetlić zestawy dla działania.

5. Wybierz **pozycję ResourceGroupNameParameterSetName** jako zestaw parametrów. Pola **ResourceGroupName i** **Name mają** wykrzyknik obok nich, aby wskazać, że są wymagane parametry. Zwróć uwagę, że oba pola oczekują wartości ciągu.

6. Wybierz pozycję **Nazwa**. Wybierz **pozycję Wyrażenie programu PowerShell** dla pola **Źródło** danych. W przypadku maszyny wirtualnej, która jest używana do uruchamiania tego runbook, wpisz nazwę maszyny otoczoną podwójnymi cudzysłowami. Kliknij przycisk **OK**.

7. Wybierz pozycję **ResourceGroupName**. Użyj wartości **Wyrażenie programu PowerShell** dla pola **Źródło** danych i wpisz nazwę grupy zasobów otoczonej podwójnymi cudzysłowami. Kliknij przycisk **OK**.

8. Kliknij **pozycję Okienko testowania,** aby przetestować ten runbook.

9. Kliknij **przycisk Start,** aby rozpocząć test. Po zakończeniu upewnij się, że maszyna wirtualna została uruchomiona. Na tym etapie twój runbook powinien wyglądać podobnie do poniższego.

    ![Dane wyjściowe Start-AzVM Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Krok 8. Dodawanie dodatkowych parametrów wejściowych

Twój runbook aktualnie uruchamia maszynę wirtualną w grupie zasobów określonej dla `Start-AzVM` polecenia cmdlet . Ten typ runbook będzie bardziej przydatny, jeśli określisz zarówno nazwę, jak i grupę zasobów podczas uruchamiania. Dodajmy parametry wejściowe do runbook, aby zapewnić tę funkcjonalność.

1. Otwórz edytor graficzny, klikając **pozycję Edytuj** na MyFirstRunbook-Graphical strony.

2. Wybierz **pozycję Input and output (Dane** wejściowe i wyjściowe), a następnie pozycję Add input **(Dodaj dane wejściowe),** aby otworzyć okienko parametru wejściowego runbook.

3. W podanych polach należy wprowadzić następujące ustawienia, a następnie kliknąć przycisk **OK.**
   * **Nazwa** — określ `VMName` wartość .
   * **Typ** — zachowaj ustawienie ciągu.
   * **Obowiązkowe** — zmień wartość na **Tak.**

4. Utwórz drugi obowiązkowy parametr wejściowy o nazwie `ResourceGroupName` , a następnie kliknij przycisk **OK,** aby zamknąć okienko Dane wejściowe i wyjściowe.

    ![Parametry wejściowe elementu Runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Wybierz `Start-AzVM` działanie, a następnie kliknij pozycję **Parametry.**

6. Zmień pole **Źródło danych** w polu **Nazwa na** Dane **wejściowe runbook.** Następnie wybierz pozycję **VMName**.

7. Zmień pole **Źródło danych dla** **parametru ResourceGroupName na** wejście **runbook,** a następnie wybierz **pozycję ResourceGroupName.**

    ![Start-AzVM parametrów](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.

9. Zamknij okienko testowania.

10. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.

11. Zatrzymaj maszynę wirtualną, która została wcześniej uruchomiona.

12. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości dla i `VMName` `ResourceGroupName` dla maszyny wirtualnej, która ma być uruchamiana.

13. Po zakończeniu pracy z runbook upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="step-9---create-a-conditional-link"></a>Krok 9. Tworzenie połączenia warunkowego

Teraz możesz zmodyfikować ten runbook tak, aby podejmował próbę uruchomienia maszyny wirtualnej tylko wtedy, gdy nie została jeszcze uruchomiona. W tym celu dodaj polecenie cmdlet [Get-AzVM,](/powershell/module/Az.Compute/Get-AzVM) które pobiera stan na poziomie wystąpienia maszyny wirtualnej. Następnie możesz dodać moduł kodu przepływu pracy programu PowerShell o nazwie z fragmentem kodu programu PowerShell, aby określić, czy stan maszyny wirtualnej `Get Status` jest uruchomiony, czy zatrzymany. Link warunkowy z modułu jest uruchamiany tylko wtedy, gdy bieżący stan działania `Get Status` jest `Start-AzVM` zatrzymany. Na końcu tej procedury twój runbook używa polecenia cmdlet do wyprowadzania komunikatu z informacjami o pomyślnym `Write-Output` uruchomieniu maszyny wirtualnej.

1. Otwórz **element MyFirstRunbook-Graphical** w edytorze graficznym.

2. Usuń połączenie między i `Specify Subscription Id` `Start-AzVM` , klikając je, a następnie naciskając klawisz **Delete**.

3. W kontrolce Biblioteka wpisz `Get-Az` w polu wyszukiwania.

4. Dodaj `Get-AzVM` do kanwy.

5. Wybierz pozycję , a następnie `Get-AzVM` kliknij **pozycję Zestaw parametrów,** aby wyświetlić zestawy dla polecenia cmdlet.

6. Wybierz zestaw parametrów **GetVirtualMachineInResourceGroupNameParamSet**. Pola **ResourceGroupName** **i Name** są obok nich wykrzyknikami wskazującymi, że określają wymagane parametry. Zwróć uwagę, że oba pola oczekują wartości ciągu.

7. W **obszarze Źródło danych** w obszarze **Nazwa** wybierz pozycję **Wejście runbook,** a następnie **pozycję VMName.** Kliknij przycisk **OK**.

8. W **obszarze Źródło danych** dla **parametru ResourceGroupName** wybierz pozycję **Wejście runbook,** a następnie **pozycję ResourceGroupName.** Kliknij przycisk **OK**.

9. W **obszarze Źródło danych** w obszarze **Stan** wybierz pozycję **Wartość stała**, a następnie wartość **True.** Kliknij przycisk **OK**.

10. Utwórz link z `Specify Subscription Id` do `Get-AzVM` .

11. W kontrolce Biblioteka rozwiń pozycję **Kontrolka runbook** i dodaj **kod** do kanwy.  

12. Utwórz link z `Get-AzVM` do `Code` .  

13. Kliknij `Code` pozycję i w okienku Konfiguracja zmień etykietę na Pobierz **stan**.

14. Wybierz `Code` pozycję i zostanie wyświetlona strona Edytor kodu.  

15. Wklej poniższy fragment kodu na stronie edytora.

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

16. Utwórz link z `Get Status` do `Start-AzVM` .

    ![Element Runbook z modułem kodu](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Wybierz link i w okienku Konfiguracja zmień pozycję **Zastosuj warunek** na **Tak.** Zwróć uwagę, że link staje się linią przerywaną, co oznacza, że działanie docelowe jest uruchamiane tylko wtedy, gdy warunek jest rozpoznany jako true.  

18. W **przypadku wyrażenia Warunek** wpisz `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` Teraz działa tylko wtedy, gdy maszyna wirtualna jest zatrzymana.

19. W kontrolce Biblioteka rozwiń pozycję **Polecenia cmdlet**, a następnie pozycję **Microsoft.PowerShell.Utility**.

20. Dodaj `Write-Output` dwukrotnie do kanwy.

21. W przypadku pierwszej kontrolki kliknij pozycję Parametry i zmień wartość `Write-Output` **etykiety** na **Powiadamianie o uruchomionej maszynie wirtualnej.** 

22. W **przypadku obiektu InputObject** **zmień** źródło danych na wyrażenie **programu PowerShell** i wpisz wyrażenie `$VMName successfully started.` .

23. W drugiej kontrolce kliknij pozycję Parametry i zmień wartość `Write-Output` **etykiety** na **Powiadamianie o nieudanym uruchomieniu maszyny wirtualnej.** 

24. W **przypadku obiektu InputObject** **zmień** źródło danych na wyrażenie **programu PowerShell** i wpisz wyrażenie `$VMName could not start` .

25. Utwórz linki z `Start-AzVM` do `Notify VM Started` i `Notify VM Start Failed` .

26. Wybierz link do i `Notify VM Started` zmień wartość opcji Zastosuj **warunek** na true.

27. W przypadku **wyrażenia Warunek** wpisz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Ta `Write-Output` kontrolka jest teraz uruchamiana tylko wtedy, gdy maszyna wirtualna zostanie uruchomiona pomyślnie.

28. Wybierz link do i `Notify VM Start Failed` zmień wartość opcji Zastosuj **warunek** na true.

29. W polu **Wyrażenie warunku** wpisz `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Ta `Write-Output` kontrolka jest teraz uruchamiana tylko wtedy, gdy maszyna wirtualna nie została pomyślnie uruchomiona. Twój plik Runbook powinien wyglądać tak, jak na poniższej ilustracji.

    ![Element Runbook z pozycją Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Zapisz element Runbook, a następnie otwórz okienko testowania.

31. Uruchom runbook z zatrzymaną maszyną wirtualną, a maszyna powinna zostać uruchomione.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia graficznego, zobacz [Author a graphical runbook in Azure Automation](../automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z podręcznikami Runbook programu PowerShell, zobacz Create a PowerShell runbook (Tworzenie [runbook programu PowerShell).](automation-tutorial-runbook-textual-powershell.md)
* Aby rozpocząć pracę z podręcznikami Runbook przepływu pracy programu PowerShell, zobacz [Tworzenie runbook przepływu pracy programu PowerShell.](automation-tutorial-runbook-textual.md)
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).

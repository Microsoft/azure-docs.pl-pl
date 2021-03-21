---
title: Tworzenie elementu Runbook języka Python 3 (wersja zapoznawcza) w Azure Automation
description: W tym artykule przedstawiono sposób tworzenia, testowania i publikowania prostego elementu Runbook języka Python 3 (wersja zapoznawcza).
services: automation
ms.subservice: process-automation
ms.date: 02/16/2021
ms.topic: tutorial
ms.openlocfilehash: c19f7e177d51a3de75e7d7ae2b83442e23efd243
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546146"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Samouczek: Tworzenie elementu Runbook języka Python 3 (wersja zapoznawcza)

Ten samouczek przeprowadzi Cię przez proces tworzenia [elementu Runbook języka Python 3](../automation-runbook-types.md#python-runbooks) (wersja zapoznawcza) w Azure Automation. Kompilacja elementów Runbook języka Python w języku Python 2 i 3. Kod elementu Runbook można edytować bezpośrednio przy użyciu edytora tekstu w Azure Portal.

> [!div class="checklist"]
> * Tworzenie prostego elementu Runbook języka Python
> * Testowanie i publikowanie elementu Runbook
> * Uruchamianie i śledzenie stanu zadania elementu Runbook
> * Aktualizowanie elementu Runbook w celu uruchomienia maszyny wirtualnej platformy Azure z parametrami elementu Runbook

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka są potrzebne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Konto usługi Automation](../automation-security-overview.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.

- Maszyna wirtualna platformy Azure. Podczas pracy z tym samouczkiem uruchomisz i zatrzymasz tę maszynę, więc nie powinna to być produkcyjna maszyna wirtualna.

- Możesz użyć hybrydowego procesu roboczego elementu Runbook dla systemu Linux lub Windows, aby uruchomić elementy Runbook języka Python 3.Postępuj zgodnie z instrukcjami dotyczącymi instalowania [](../automation-linux-hrw-install.md)    [](../automation-windows-hrw-install.md)   hybrydowego procesu roboczego elementu Runbook dla systemu Linux lub Windows.Nie ma żadnego konkretnego wymagania wstępnego dla hybrydowego procesu roboczego systemu Linux. Aby jednak używać hybrydowego procesu roboczego systemu Windows dla elementów Runbook języka Python 3, należy skonfigurować komputer obsługujący proces roboczy elementu Runbook na podstawie tego, czy jest obsługiwany tylko język Python 3, czy też musi być współużytkowane środowisko Python 2 i 3.

   * Jeśli jest zainstalowany język Python 2 lub Python *3,* należy dołączyć ścieżkę do folderu, w którym python.exe znajduje się w zmiennej środowiskowej **Path** . Na przykład jeśli python.exe znajduje się w ścieżce instalacji `C:\Python` , należy dołączyć tę ścieżkę do zmiennej środowiskowej **Path** .

   * Jeśli masz zainstalowany język Python 2 i Python 3 i chcesz uruchomić oba typy elementów Runbook, należy skonfigurować następujące zmienne środowiskowe:

     * Python 2 — Tworzenie nowej zmiennej środowiskowej o nazwie `PYTHON_2_PATH` i Określanie folderu instalacji. Jeśli na przykład folder instalacyjny to, należy `C:\Python27` dodać tę ścieżkę do zmiennej.

     * Python 3 — Tworzenie nowej zmiennej środowiskowej o nazwie `PYTHON_3_PATH` i Określanie folderu instalacji. Jeśli na przykład folder instalacyjny to, należy `C:\Python3` dodać tę ścieżkę do zmiennej.

## <a name="create-a-new-runbook"></a>Utwórz nowy element Runbook

Zacznij od utworzenia prostego elementu Runbook, który będzie wyprowadzał tekst *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).

2. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.

3. Wybierz pozycję **Dodaj element Runbook** , aby utworzyć nowy element Runbook.

4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Python**.

5. W obszarze **Typ elementu Runbook** wybierz pozycję **Python 3** .

6. Wybierz pozycję **Utwórz** , aby utworzyć element Runbook i otworzyć Edytor tekstu.

## <a name="add-code-to-the-runbook"></a>Dodawanie kodu do elementu Runbook

Teraz można dodać proste polecenie do drukowania tekstu `Hello World` .

```python
print("Hello World!")
```

Wybierz pozycję **Zapisz** , aby zapisać element Runbook.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Podczas testowania elementu Runbook uruchamiasz jego wersję roboczą i interaktywnie Wyświetlaj dane wyjściowe.

1. Wybierz **okienko testowania** , aby otworzyć okienko **testowania** .

2. Wybierz pozycję **Rozpocznij** , aby rozpocząć test. Powinna to być jedyna włączona opcja.

3. Zostanie utworzone [zadanie elementu Runbook](../automation-runbook-execution.md) i pojawi się jego stan.
   Zadanie jest uruchamiane jako **kolejkowane**, co oznacza, że oczekuje na udostępnienie procesu roboczego elementu Runbook w chmurze. Jest on zmieniany na **Uruchamianie** , gdy proces roboczy pozyskuje zadanie, a następnie **uruchamia** się, gdy element Runbook faktycznie zacznie działać.

4. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W takim przypadku powinna zostać wyświetlona `Hello World` .

5. Zamknij okienko **testowania** , aby powrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i Uruchamianie elementu Runbook

Utworzony element Runbook nadal działa w trybie roboczym. Należy ją opublikować przed uruchomieniem jej w środowisku produkcyjnym. Po opublikowaniu elementu Runbook Zastąp istniejącą opublikowaną wersję wersją roboczą. W takim przypadku nie masz jeszcze opublikowanej wersji, ponieważ element Runbook został właśnie utworzony.

1. Wybierz pozycję **Publikuj** , aby opublikować element Runbook, a następnie przycisk **tak** po wyświetleniu monitu.

2. Jeśli przewiniesz w lewo, aby wyświetlić element Runbook na stronie **elementy Runbook** , zobaczysz **Stan tworzenia** **opublikowanych**.

3. Przewiń z powrotem w prawo, aby wyświetlić okienko dla **MyFirstRunbook-python3**.

   Opcje w górnej części umożliwiają uruchomienie elementu Runbook, wyświetlenie elementu Runbook lub zaplanowanie jego rozpoczęcia w przyszłości.

4. Wybierz przycisk **Start** , a następnie wybierz przycisk **OK** po otwarciu okienka **uruchamiania elementu Runbook** .

5. Zostanie otwarte okienko **zadań** dla utworzonego zadania elementu Runbook. Można zamknąć to okienko, ale pozostawić je otwarte, aby można było obserwować postęp zadania.

6. Stan zadania jest pokazywany w **podsumowaniu zadania** i jest zgodny z Stanami wyświetlonymi podczas testowania elementu Runbook.

7. Po **zakończeniu** wyświetlania stanu elementu Runbook wybierz pozycję **dane wyjściowe**. Zostanie otwarte okienko **danych wyjściowych** , w którym można zobaczyć `Hello World` .

8. Zamknij okienko **danych wyjściowych** .

9. Wybierz pozycję **wszystkie dzienniki** , aby otworzyć okienko **strumieni** dla zadania elementu Runbook. Powinien być widoczny tylko `Hello World` w strumieniu danych wyjściowych. Jednak w tym okienku można wyświetlić inne strumienie zadania elementu Runbook, takie jak **pełne** informacje i **błąd**, jeśli element Runbook zapisze w nich.

10. Zamknij okienko **strumieni** i okienko **zadania** , aby wrócić do okienka **MyFirstRunbook-python3** .

11. Wybierz pozycję **zadania** , aby otworzyć stronę **zadania** dla tego elementu Runbook. Ta strona zawiera listę wszystkich zadań utworzonych przez ten element Runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.

12. Możesz wybrać to zadanie, aby otworzyć okienko **zadania** , które zostało wyświetlone po uruchomieniu elementu Runbook. To okienko pozwala wrócić w czasie i wyświetlić szczegóły każdego zadania, które zostało utworzone dla określonego elementu Runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure.
W tym celu skrypt musi uwierzytelniać się przy użyciu poświadczeń z konta usługi Automation.

> [!NOTE]
> Konto usługi Automation musi zostać utworzone za pomocą funkcji głównej usługi, aby można było używać certyfikatu Uruchom jako.
> Jeśli konto usługi Automation nie zostało utworzone za pomocą nazwy głównej, można uwierzytelnić się zgodnie z opisem w temacie [uwierzytelnianie za pomocą bibliotek zarządzania platformy Azure dla języka Python](/azure/python/python-sdk-azure-authenticate).

1. Otwórz edytor tekstowy, wybierając pozycję **Edytuj** w okienku **MyFirstRunbook-python3** .

2. Dodaj następujący kod w celu uwierzytelnienia na platformie Azure:

    ```python
    from OpenSSL import crypto 
    import binascii 
    from msrestazure import azure_active_directory 
    import adal 

    # Get the Azure Automation RunAs service principal certificate 
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate") 
    pks12_cert = crypto.load_pkcs12(cert) 
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey()) 
    
    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"] 
    thumbprint = runas_connection["CertificateThumbprint"] 
    tenant_id = runas_connection["TenantId"] 
    
    # Authenticate with service principal certificate 
    resource ="https://management.core.windows.net/" 
    authority_url = ("https://login.microsoftonline.com/"+tenant_id) 
    context = adal.AuthenticationContext(authority_url) 
    return azure_active_directory.AdalAuthentication( 
      lambda: context.acquire_token_with_client_certificate( 
          resource, 
          application_id, 
          pem_pkey, 
          thumbprint) 
    ) 
    ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Dodaj kod, aby utworzyć klienta obliczeniowego języka Python i uruchomić maszynę wirtualną

Aby współpracować z maszynami wirtualnymi platformy Azure, Utwórz wystąpienie [klienta obliczeniowego platformy Azure dla języka Python](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Użyj klienta obliczeniowego, aby uruchomić maszynę wirtualną. Dodaj następujący kod do elementu Runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Gdzie `MyResourceGroup` to nazwa grupy zasobów zawierającej maszynę wirtualną, a `TestVM` to nazwa maszyny wirtualnej, która ma zostać uruchomiona.

Przetestuj i uruchom ponownie element Runbook, aby zobaczyć, że uruchamia maszynę wirtualną.

## <a name="use-input-parameters"></a>Użyj parametrów wejściowych

Element Runbook obecnie używa trwale zakodowanych wartości nazw grupy zasobów i maszyny wirtualnej. Teraz Dodajmy kod, który pobiera te wartości z parametrów wejściowych.

`sys.argv`Zmienna służy do pobierania wartości parametrów. Dodaj następujący kod do elementu Runbook bezpośrednio po innych `import` instrukcjach:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Spowoduje to zaimportowanie `sys` modułu i utworzenie dwóch zmiennych w celu przechowywania nazw grup zasobów i maszyn wirtualnych. Zauważ, że element listy argumentów, `sys.argv[0]` , jest nazwą skryptu i nie jest wejściowy przez użytkownika.

Teraz można zmodyfikować ostatnie dwa wiersze elementu Runbook, aby używały wartości parametrów wejściowych zamiast używać zakodowanych wartości:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Po uruchomieniu elementu Runbook w języku Python z okienka **test** lub jako opublikowanego elementu Runbook możesz wprowadzić wartości parametrów na stronie **Uruchom element Runbook** w obszarze **Parametry**.

Po rozpoczęciu wprowadzania wartości w pierwszym polu zostanie wyświetlona druga i tak dalej, aby można było wprowadzić tyle wartości parametrów, ile jest to konieczne.

Wartości są dostępne dla skryptu w `sys.argv` tablicy tak jak w kodzie, który właśnie został dodany.

Wprowadź nazwę grupy zasobów jako wartość pierwszego parametru i nazwę maszyny wirtualnej, która ma być uruchamiana jako wartość drugiego parametru.

![Wprowadź wartości parametrów](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Wybierz **przycisk OK** , aby uruchomić element Runbook. Element Runbook działa i uruchamia określoną maszynę wirtualną.

## <a name="error-handling-in-python"></a>Obsługa błędów w języku Python

Można również użyć poniższych konwencji do pobrania różnych strumieni z elementów Runbook języka Python, w tym ostrzeżeń, błędów i debugowania strumieni.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

W poniższym przykładzie przedstawiono Konwencję używaną w `try...except` bloku.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o typach elementów Runbook, ich zaletach i ograniczeniach, zobacz [Azure Automation typów elementów Runbook](../automation-runbook-types.md).

- Aby dowiedzieć się więcej na temat tworzenia aplikacji dla platformy Azure przy użyciu języka Python, zobacz [Azure for Python Developers](/azure/python/).

- Aby wyświetlić Przykładowe elementy Runbook języka Python 3, zobacz [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).

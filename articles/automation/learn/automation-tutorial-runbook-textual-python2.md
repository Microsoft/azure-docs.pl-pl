---
title: Tworzenie śmiętu języka Python w usłudze Azure Automation
description: Samouczek pokazujący, jak utworzyć, przetestować i opublikować prosty podręcznik pythona.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 116f2544b23ed5f9bc0fabbb945cb5cb2b51af96
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726284"
---
# <a name="tutorial-create-a-python-runbook"></a>Samouczek: Tworzenie podręcznika Pythona

W tym samouczku przeprowadzi Cię tworzenie [elementu runbook języka Python](../automation-runbook-types.md#python-runbooks) w usłudze Azure Automation. Python runbooks kompilować w Pythonie 2. Można bezpośrednio edytować kod życiówki za pomocą edytora tekstu w witrynie Azure portal.

> [!div class="checklist"]
> * Tworzenie prostego podręcznika pythonowego
> * Testowanie i publikowanie podręcznika runbook
> * Uruchamianie i śledzenie stanu zadania runbook
> * Aktualizowanie uruchomieniu w celu uruchomienia maszyny wirtualnej platformy Azure z parametrami zestawu runbook

> [!NOTE]
> Używanie elementu webhook do uruchamiania elementu runbook języka Python nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Konto usługi Automation](../automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
- Maszyna wirtualna platformy Azure. Będziesz uruchamiać i zatrzymywać tę maszynę, dlatego należy użyć maszyny innej niż produkcyjna.

## <a name="create-a-new-runbook"></a>Tworzenie nowego śmiętu

Zacznij od utworzenia prostego podstawowego podstawowego podstawowego tekstu *Hello World*.

1. W witrynie Azure Portal otwórz konto usługi Automation.

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z nich to moduły, które są automatycznie uwzględniane na nowym koncie usługi Automation. Powinien istnieć również element zawartości Poświadczenie wymieniony w części dotyczącej [wymagań wstępnych](#prerequisites).

2. Wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów,** aby otworzyć listę śmięty.

3. Wybierz **pozycję Dodaj projekt runbook,** aby utworzyć nowy projekt runbook.

4. Nadaj projektowi uszytemu nazwę **MyFirstRunbook-Python**.

5. Wybierz **Python 2** dla **typu runbook**.

6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor tekstów.

## <a name="add-code-to-the-runbook"></a>Dodawanie kodu do podręcznika runbooka

Teraz dodajesz proste polecenie, `Hello World`aby wydrukować tekst .

```python
print("Hello World!")
```

Kliknij **przycisk Zapisz,** aby zapisać projekt runbook.

## <a name="test-the-runbook"></a>Testowanie elementu runbook

Przed opublikowaniem elementu runbook w celu udostępnienia go w środowisku produkcyjnym warto go przetestować, aby upewnić się, że działa prawidłowo. Testowanie elementu Runbook polega na uruchomieniu jego wersji roboczej i interaktywnym przejrzeniu danych wyjściowych.

1. Kliknij pozycję **Okienko testowania**, aby otworzyć okienko testowania.

2. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.

3. Zostanie utworzone [zadanie elementu Runbook](../automation-runbook-execution.md) i pojawi się jego stan.
   Stan zadania rozpoczyna się jako w kolejce, co oznacza, że oczekuje na pracownika przewodniczącego w chmurze, aby być dostępne. Przenosi się do Uruchamianie, gdy pracownik żąda zadania, a następnie uruchomiony, gdy runbook faktycznie rozpoczyna się uruchomiony.

4. Po zakończeniu zadania elementu Runbook zostaną wyświetlone jego dane wyjściowe. W takim przypadku powinieneś zobaczyć `Hello World`.

5. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="publish-and-start-the-runbook"></a>Publikowanie i uruchamianie podręcznika runbook

Nowo utworzony element runbook nadal działa w trybie roboczym. Należy go opublikować, zanim będzie można uruchomić go w produkcji.
Podczas publikowania likrządu należy zastąpić istniejącą opublikowaną wersję wersją roboczą.
W takim przypadku nie masz jeszcze opublikowanej wersji, ponieważ właśnie utworzono projekt runbook.

1. Kliknij przycisk **Opublikuj**, aby opublikować element Runbook, a następnie kliknij opcję **Tak** po wyświetleniu monitu.

2. Jeśli przewiniesz w lewo, aby wyświetlić element runbook na stronie **Runbooks,** powinien zostać wyświetlony **stan tworzenia** **opublikowanych**.

3. Przewiń z powrotem w prawo, aby wyświetlić okienko **myfirstRunbook-Python**.

   Opcje w górnej części pozwalają uruchomić runbook, wyświetlić runbook lub zaplanować jego uruchomienie w pewnym momencie w przyszłości.

4. Kliknij **przycisk Start,** a następnie kliknij przycisk **OK** po otwarciu bloku Rozpocznij program Runbook.

5. Okienko Zadania jest otwierane dla utworzonego zadania ego. Możesz zamknąć to okienko, ale pozostawmy je otwarte, aby można było obserwować postęp zadania.

6. Stan zadania jest wyświetlany w **podsumowaniu zadania** i odpowiada stanom, które zostały wyświetlone podczas testowania śmiwoja.

7. Gdy stan liksu eksletu zostanie wyświetlony, kliknij pozycję **Wyjście**. Zostanie otwarte okienko Dane wyjściowe, w którym można zobaczyć `Hello World`.

8. Zamknij okienko danych wyjściowych.

9. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być `Hello World` widoczny tylko w strumieniu danych wyjściowych. Jednak to okienko może wyświetlać inne strumienie dla zadania przewodniczącego, takie jak Pełne i Błąd, jeśli system runbook zapisuje do nich.

10. Zamknij okienko Strumienie i okienko zadania, aby powrócić do okienka MyFirstRunbook-Python.

11. Kliknij **pozycję Zadania,** aby otworzyć stronę Zadania dla tego liksu. Ta strona zawiera listę wszystkich zadań utworzonych przez ten projekt runbook. Ponieważ uruchomiono zadanie tylko raz, powinno być widoczne tylko jedno zadanie.

12. Możesz kliknąć to zadanie, aby otworzyć okienko Zadania wyświetlone wcześniej po uruchomieniu elementu runbook. To okienko umożliwia cofnąć się w czasie i wyświetlić szczegóły dowolnego zadania, które zostało utworzone dla określonego egonia.

## <a name="add-authentication-to-manage-azure-resources"></a>Dodawanie uwierzytelniania w celu zarządzania zasobami platformy Azure

Element runbook został przetestowany i opublikowany, ale jak do tej pory nie wykonuje on żadnych użytecznych czynności. Powinien zarządzać zasobami platformy Azure.
Aby to zrobić, skrypt musi uwierzytelnić przy użyciu poświadczeń z konta automatyzacji. Aby ci pomóc, możesz użyć [pakietu narzędzi usługi Azure Automation,](https://github.com/azureautomation/azure_automation_utility) aby ułatwić uwierzytelnianie zasobów platformy Azure i interakcję z nimi.

> [!NOTE]
> Konto automatyzacji musi zostać utworzone przy za pomocą funkcji głównej usługi, aby certyfikat Uruchom jako był.
> Jeśli twoje konto automatyzacji nie zostało utworzone z jednostką usługi, można uwierzytelnić się zgodnie z opisem w [uwierzytelniij za pomocą bibliotek zarządzania platformy Azure dla języka Python.](/azure/python/python-sdk-azure-authenticate)

1. Otwórz edytor tekstu, klikając przycisk **Edytuj** w okienku MyFirstRunbook-Python.

2. Dodaj następujący kod do uwierzytelniania na platformie Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
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

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Dodaj kod, aby utworzyć klienta obliczeniowego języka Python i uruchomić maszynę wirtualną

Aby pracować z maszynami wirtualnymi platformy Azure, utwórz [wystąpienie klienta obliczeń platformy Azure dla języka Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Użyj klienta obliczeniowego, aby uruchomić maszynę wirtualną. Dodaj następujący kod do elementów runbook:

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

Gdzie `MyResourceGroup` jest nazwa grupy zasobów, która zawiera `TestVM` maszynę wirtualną i jest nazwą maszyny Wirtualnej, którą chcesz uruchomić.

Przetestuj i uruchom runbook ponownie, aby zobaczyć, że uruchamia maszynę wirtualną.

## <a name="use-input-parameters"></a>Użyj parametrów wejściowych

Księga runbooka używa obecnie zakodowanych wartości dla nazw grupy zasobów i maszyny Wirtualnej. Teraz dodajmy kod, który pobiera te wartości z parametrów wejściowych.

Zmienna `sys.argv` służy do uzyskania wartości parametrów. Dodaj następujący kod do elementów runbook natychmiast po innych `import` instrukcji:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Spowoduje to `sys` zaimportowanie modułu i utworzenie dwóch zmiennych do przechowywania nazw grupy zasobów i maszyn wirtualnych. Należy zauważyć, że element `sys.argv[0]`listy argumentów, , jest nazwą skryptu i nie jest wprowadzany przez użytkownika.

Teraz możesz zmodyfikować dwa ostatnie wiersze żytnika, aby używać wartości parametrów wejściowych zamiast wartości zakodowanych na twardo:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Po uruchomieniu ślikmy pythonowej (w okienku testowym lub jako opublikowany projekt runbook) można wprowadzić wartości parametrów na stronie Rozpocznij runbook w obszarze **Parametry**.

Po rozpoczęciu wprowadzania wartości w pierwszym polu pojawia się druga itd., dzięki czemu można wprowadzić dowolną liczbę wartości parametrów.

Wartości są dostępne dla skryptu w `sys.argv` tablicy, jak w kodzie, który właśnie dodano.

Wprowadź nazwę grupy zasobów jako wartość pierwszego parametru i nazwę maszyny Wirtualnej, aby rozpocząć jako wartość drugiego parametru.

![Wprowadzanie wartości parametrów](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Kliknij **przycisk OK,** aby uruchomić program runbook. Projekt runbook uruchamia i uruchamia określoną maszynę wirtualną.

## <a name="error-handling-in-python"></a>Obsługa błędów w pythonie

Można również użyć następujących konwencji do pobierania różnych strumieni z elementów runbook języka Python, w tym OSTRZEŻENIE, BŁĄD i debug strumieni.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Poniższy przykład pokazuje tę `try...except` konwencję używaną w bloku.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Usługa Azure Automation `sys.stderr`nie obsługuje .

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z podręcznikami runbook programu PowerShell, zobacz [Tworzenie śmięty programu PowerShell](automation-tutorial-runbook-textual-powershell.md).
- Aby rozpocząć pracę z elementami runbooki graficzne, zobacz [Tworzenie graficznego eksmisji](automation-tutorial-runbook-graphical.md).
- Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Tworzenie uruchomieniu kędu programu PowerShell](automation-tutorial-runbook-textual.md).
- Aby dowiedzieć się więcej o typach elementów runbook, ich zaletach i ograniczeniach, zobacz [Typy elementów runbook usługi Azure Automation](../automation-runbook-types.md).
- Aby dowiedzieć się więcej o tworzeniu platformy Azure za pomocą języka Python, zobacz [Azure for Python developers](/azure/python/).
- Aby wyświetlić przykładowe elementy runbook języka Python 2, zobacz [usługi Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).

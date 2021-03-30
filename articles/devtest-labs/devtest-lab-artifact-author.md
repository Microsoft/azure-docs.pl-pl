---
title: Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs | Microsoft Docs
description: Dowiedz się, jak tworzyć artefakty do użycia z Azure DevTest Labs do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi administracyjnej maszyny wirtualnej.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270686"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Tworzenie niestandardowych artefaktów dla maszyny wirtualnej DevTest Labs

Obejrzyj poniższy film wideo, aby zapoznać się z omówieniem kroków opisanych w tym artykule:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Omówienie
Możesz użyć *artefaktów* do wdrożenia i skonfigurowania aplikacji po zainicjowaniu obsługi maszyny wirtualnej. Artefakt składa się z pliku definicji artefaktu i innych plików skryptów, które są przechowywane w folderze w repozytorium git. Pliki definicji artefaktów składają się z notacji JSON i wyrażeń, których można użyć do określenia, co chcesz zainstalować na maszynie wirtualnej. Na przykład można zdefiniować nazwę artefaktu, polecenie do uruchomienia i parametry, które są dostępne po uruchomieniu polecenia. Można odwołać się do innych plików skryptu w pliku definicji artefaktu według nazwy.

## <a name="artifact-definition-file-format"></a>Format pliku definicji artefaktu
Poniższy przykład przedstawia sekcje, które składają się na podstawową strukturę pliku definicji:

```json
  {
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "",
    "description": "",
    "iconUri": "",
    "targetOsType": "",
    "parameters": {
      "<parameterName>": {
        "type": "",
        "displayName": "",
        "description": ""
      }
    },
    "runCommand": {
      "commandToExecute": ""
    }
  }
```

| Nazwa elementu | Wymagane? | Opis |
| --- | --- | --- |
| $schema |Nie |Lokalizacja pliku schematu JSON. Plik schematu JSON może pomóc w przetestowaniu ważności pliku definicji. |
| tytuł |Tak |Nazwa artefaktu wyświetlanego w laboratorium. |
| description (opis) |Tak |Opis artefaktu wyświetlanego w środowisku laboratoryjnym. |
| iconUri |Nie |Identyfikator URI ikony wyświetlanej w laboratorium. |
| targetOsType |Tak |System operacyjny maszyny wirtualnej, na której jest zainstalowany artefakt. Obsługiwane opcje to Windows i Linux. |
| parameters |Nie |Wartości, które są dostarczane, gdy polecenie instalacji artefaktu jest uruchamiane na komputerze. Ułatwia to dostosowanie artefaktu. |
| Akcji |Tak |Polecenie instalacji artefaktu, które jest wykonywane na maszynie wirtualnej. |

### <a name="artifact-parameters"></a>Parametry artefaktu
W sekcji parametry pliku definicji Określ wartości, które użytkownik może wprowadzać podczas instalacji artefaktu. Można odwołać się do tych wartości w poleceniu instalacji artefaktu.

Aby zdefiniować parametry, należy użyć następującej struktury:

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| Nazwa elementu | Wymagane? | Opis |
| --- | --- | --- |
| typ |Tak |Typ wartości parametru. Zapoznaj się z poniższą listą typów dozwolonych. |
| displayName |Tak |Nazwa parametru, który jest wyświetlany użytkownikowi w laboratorium. |
| description (opis) |Tak |Opis parametru, który jest wyświetlany w laboratorium. |

Dozwolone typy to:

* ciąg (dowolny prawidłowy ciąg JSON)
* int (dowolna prawidłowa liczba całkowita JSON)
* bool (dowolna prawidłowa wartość logiczna JSON)
* Array (dowolna prawidłowa tablica JSON)

## <a name="secrets-as-secure-strings"></a>Wpisy tajne jako ciągi bezpieczne
Zadeklaruj klucze tajne jako bezpieczne ciągi. Poniżej przedstawiono składnię deklarującą parametr Secure String w `parameters` sekcji **artifactfile.jsw** pliku:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

W przypadku polecenia install artefaktu Uruchom skrypt programu PowerShell, który pobiera bezpieczny ciąg utworzony przy użyciu polecenia ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Aby uzyskać kompletny przykład artifactfile.jsna i artifact.ps1 (skrypt programu PowerShell), zobacz [ten przykład w witrynie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Innym ważnym punktem notatki nie jest rejestrowanie wpisów tajnych w konsoli programu, ponieważ dane wyjściowe są przechwytywane do debugowania przez użytkownika. 

## <a name="artifact-expressions-and-functions"></a>Wyrażenia artefaktów i funkcje
Możesz użyć wyrażeń i funkcji, aby skonstruować polecenie instalacji artefaktu.
Wyrażenia są ujęte w nawiasy kwadratowe ([i]) i są oceniane po zainstalowaniu artefaktu. Wyrażenia mogą znajdować się w dowolnym miejscu w wartości ciągu JSON. Wyrażenia zawsze zwracają kolejną wartość JSON. Jeśli konieczne jest użycie ciągu literału rozpoczynającego się od nawiasu ([), należy użyć dwóch nawiasów ([[).
Zazwyczaj do konstruowania wartości służą wyrażenia z funkcjami. Podobnie jak w języku JavaScript, wywołania funkcji są sformatowane jako **FunctionName (arg1, arg2, Arg3)**.

Na poniższej liście przedstawiono typowe funkcje:

* **Parametry (ParameterName)**: zwraca wartość parametru, która jest dostarczana, gdy jest wykonywane polecenie artefaktu.
* **concat (arg1, arg2, arg3,.....)**: łączy wiele wartości ciągu. Ta funkcja może przyjmować wiele argumentów.

Poniższy przykład pokazuje, jak używać wyrażeń i funkcji do konstruowania wartości:

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>Tworzenie artefaktu niestandardowego

1. Zainstaluj Edytor JSON. Do pracy z plikami definicji artefaktów potrzebny jest Edytor JSON. Zalecamy używanie [Visual Studio Code](https://code.visualstudio.com/), który jest dostępny dla systemów Windows, Linux i OS X.
2. Pobierz przykładową artifactfile.jsw pliku definicji. Zapoznaj się z artefaktami utworzonymi przez zespół DevTest Labs w naszym [repozytorium GitHub](https://github.com/Azure/azure-devtestlab). Utworzyliśmy rozbudowaną bibliotekę artefaktów, która może pomóc w tworzeniu własnych artefaktów. Pobierz plik definicji artefaktu i wprowadź w nim zmiany w celu utworzenia własnych artefaktów.
3. Korzystanie z funkcji IntelliSense. Użyj funkcji IntelliSense, aby zobaczyć prawidłowe elementy, których można użyć do skonstruowania pliku definicji artefaktu. Widoczne są również różne opcje dla wartości elementu. Na przykład podczas edytowania elementu **TargetOsType** technologia IntelliSense wyświetla dwie opcje dla systemu Windows lub Linux.
4. Zapisz artefakt w [publicznym repozytorium git dla DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) lub [własnego repozytorium git](devtest-lab-add-artifact-repo.md). W repozytorium publicznym można przeglądać artefakty udostępniane przez inne osoby, które mogą być używane bezpośrednio lub dostosować je do własnych potrzeb.
   
   1. Utwórz oddzielny katalog dla każdego artefaktu. Nazwa katalogu powinna być taka sama jak nazwa artefaktu.
   2. Zapisz plik definicji artefaktu (artifactfile.json) w utworzonym katalogu.
   3. Zapisz skrypty, do których odwołuje się polecenie instalacji artefaktu.
      
      Oto przykład, jak może wyglądać folder artefaktu:
      
      ![Przykład folderu artefaktów](./media/devtest-lab-artifact-author/git-repo.png)
5. Jeśli używasz własnego repozytorium do przechowywania artefaktów, Dodaj repozytorium do laboratorium, wykonując instrukcje zawarte w artykule: [Dodawanie repozytorium git dla artefaktów i szablonów](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Pokrewne artykuły:
* [Jak zdiagnozować błędy artefaktów w DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Przyłączanie maszyny wirtualnej do istniejącej domeny Active Directory przy użyciu szablonu Menedżer zasobów w DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać repozytorium artefaktów git do laboratorium](devtest-lab-add-artifact-repo.md).

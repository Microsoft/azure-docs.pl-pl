---
title: Korzystanie z graficznego zestawu SDK dla grafików usługi Azure Automation
description: W tym artykule opisano sposób korzystania z zestawu SDK grafiki azure automation.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 21f6ff8078d5a1db88b2fde33c9063a56b3ee43a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682905"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Korzystanie z graficznego zestawu SDK dla grafików usługi Azure Automation

[Graficzne programy runbook](automation-graphical-authoring-intro.md) ułatwiają zarządzanie złożonością podstawowego kodu przepływu pracy programu Windows PowerShell lub programu PowerShell. Zestaw SDK do tworzenia grafiki microsoft azure automation umożliwia deweloperom tworzenie i edytowanie autorskich wiązek do użytku z usługą Azure Automation. W tym artykule opisano podstawowe kroki tworzenia graficznego podstawowego podstawowego kodu.

## <a name="prerequisites"></a>Wymagania wstępne

Zaimportuj `Microsoft.Azure.Automation.GraphicalRunbook.Model` pakiet do projektu.

## <a name="create-a-runbook-object-instance"></a>Tworzenie wystąpienia obiektu egoisty

Odwołanie `Orchestrator.GraphRunbook.Model` się do zestawu i `Orchestrator.GraphRunbook.Model.GraphRunbook` utworzyć wystąpienie klasy:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Dodawanie parametrów umnienia

Tworzenie wystąpienia `Orchestrator.GraphRunbook.Model.Parameter` obiektów i dodawanie ich do łańczy ekwidu:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Dodawanie działań i łączy

Tworzenie wystąpienia działań odpowiednich typów i dodawanie ich do elementów runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Działania są implementowane przez następujące `Orchestrator.GraphRunbook.Model` klasy w obszarze nazw.

|Klasa  |Działanie  |
|---------|---------|
|Działania poleceń     | Wywołuje polecenie programu PowerShell (polecenie cmdlet, funkcja itp.).        |
|InvokeRunbookAktywność     | Wywołuje inny wbudowany podręcznik.        |
|Działalność skrzyżowań     | Czeka na zakończenie wszystkich przychodzących gałęzi.        |
|Działania w języku roboczym     | Wykonuje blok kodu przepływu pracy programu PowerShell lub programu PowerShell (w zależności od typu elementów runbook) w kontekście elementów runbook. Jest to potężne narzędzie, ale nie należy go nadużywania: interfejs użytkownika pokaże ten blok skryptu jako tekst; aparat wykonywania będzie traktować dostarczony blok jako czarne pole i nie będzie podejmować żadnych prób analizowania jego zawartości, z wyjątkiem podstawowego sprawdzania składni. Jeśli wystarczy wywołać pojedyncze polecenie programu PowerShell, preferuj CommandActivity.        |

> [!NOTE]
> Nie wywodź własnych działań z podanych klas. Usługa Azure Automation nie może używać elementów runbook z niestandardowymi typami działań.

Należy podać `CommandActivity` `InvokeRunbookActivity` i parametry jako deskryptory wartości, a nie wartości bezpośrednie. Deskryptory wartości określają sposób tworzenia rzeczywistych wartości parametrów. Obecnie dostępne są następujące deskryptory wartości:


|Deskryptora  |Definicja  |
|---------|---------|
|Deskryptor ConstantValue     | Odnosi się do wartości stałej zakodowane.        |
|Deskryptor języka RunbookParameterValue     | Odnosi się do parametru runbook według nazwy.        |
|Deskryptor aplikacji ActivityOutputValueDescriptor     | Odnosi się do danych wyjściowych działania nadrzędnego, umożliwiając jedno działanie do "subskrybowania" danych utworzonych przez inne działanie.        |
|AutomationVariableValueDeptor     | Odnosi się do zasobu zmiennej automatyzacji według nazwy.         |
|AutomationCredentialValueDeptor     | Odnosi się do zasobu certyfikatu automatyzacji według nazwy.        |
|Deskryptor AutomationConnectionValueDescriptor     | Odnosi się do zasobu połączenia automatyzacji według nazwy.        |
|Deskryptor programu PowerShellExpressionValueDescriptor     | Określa wyrażenie programu PowerShell w postaci swobodnej, które zostanie ocenione tuż przed wywołaniem działania.  <br/>Jest to potężne narzędzie, ale nie należy go nadużywania: interfejs użytkownika pokaże to wyrażenie jako tekst; aparat wykonywania będzie traktować dostarczony blok jako czarne pole i nie będzie podejmować żadnych prób analizowania jego zawartości, z wyjątkiem podstawowego sprawdzania składni. Jeśli to możliwe, preferuj bardziej szczegółowe deskryptory wartości.      |

> [!NOTE]
> Nie wyprowadzaj własnych deskryptorów wartości z podanych klas. Usługa Azure Automation nie może używać elementów runbook z typami deskryptora wartości niestandardowych.

Tworzenie wystąpienia łączy łączących działania i dodawanie ich do egonatu:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Zapisywanie emuńnika w pliku

Służy `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` do serializacji śmigania do ciągu:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Ciąg ten można zapisać w pliku z rozszerzeniem **.graphrunbook.** Odpowiedni projekt runbook można zaimportować do usługi Azure Automation.
Format serializowany może ulec zmianie `Orchestrator.GraphRunbook.Model.dll`w przyszłych wersjach programu . Obiecujemy zgodność wsteczną: każdy element runbook serializowany ze starszą wersją `Orchestrator.GraphRunbook.Model.dll` może być deserializowany przez dowolną nowszą wersję. Zgodność z przekazywaniem dalej nie jest gwarantowana: system runbook serializowany z nowszą wersją może nie być deserializowalny przez starsze wersje.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o elementach eks-graficznych w usłudze Azure Automation, zobacz [Wprowadzenie do tworzenia symboli graficznych](automation-graphical-authoring-intro.md).
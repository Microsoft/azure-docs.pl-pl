---
title: Przegląd szablonów
description: W tym artykule opisano korzyści przy użyciu szablonów usługi Azure Resource Manager do wdrażania zasobów.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086338"
---
# <a name="what-are-arm-templates"></a>Co to są szablony usługi ARM?

Wraz z przejściem do chmury wiele zespołów przyjęło metody programowania agile. Te zespoły szybko się iterują. Muszą wielokrotnie wdrażać swoje rozwiązania w chmurze i wiedzieć, że ich infrastruktura jest w stanie niezawodnym. Ponieważ infrastruktura stała się częścią procesu iteracyjnego, podział między operacjami a rozwojem zniknął. Zespoły muszą zarządzać infrastrukturą i kodem aplikacji za pośrednictwem ujednoliconego procesu.

Aby sprostać tym wyzwaniom, można zautomatyzować wdrożenia i używać praktyki infrastruktury jako kodu. W kodzie można zdefiniować infrastrukturę, która musi zostać wdrożona. Kod infrastruktury staje się częścią projektu. Podobnie jak kod aplikacji, kod infrastruktury można przechowywać w repozytorium źródłowym i wersjonować go. Każdy z zespołu można uruchomić kod i wdrożyć podobne środowiska.

Aby zaimplementować infrastrukturę jako kod dla rozwiązań platformy Azure, użyj szablonów usługi Azure Resource Manager (ARM). Szablon jest plikiem notacji obiektu JavaScript (JSON), który definiuje infrastrukturę i konfigurację projektu. Szablon używa składni deklaratywnej, która pozwala podać, co zamierzasz wdrożyć bez konieczności zapisywania sekwencji poleceń programowania, aby go utworzyć. W szablonie należy określić zasoby do wdrożenia i właściwości dla tych zasobów.

## <a name="why-choose-arm-templates"></a>Dlaczego warto wybrać szablony ARM?

Jeśli próbujesz zdecydować między używaniem szablonów ARM a jedną z innych infrastruktury jako usług kodu, rozważ następujące zalety korzystania z szablonów:

* **Składnia deklaratywna:** szablony ARM umożliwiają tworzenie i wdrażanie całej infrastruktury platformy Azure deklaratywnie. Na przykład można wdrożyć nie tylko maszyny wirtualne, ale także infrastrukturę sieciową, systemy pamięci masowej i inne zasoby, które mogą być potrzebne.

* **Powtarzalne wyniki:** wielokrotnie wdrażaj infrastrukturę w całym cyklu życia rozwoju i mieć pewność, że zasoby są wdrażane w spójny sposób. Szablony są idempotentne, co oznacza, że można wdrożyć ten sam szablon wiele razy i uzyskać te same typy zasobów w tym samym stanie. Można opracować jeden szablon, który reprezentuje żądany stan, zamiast tworzenia wielu oddzielnych szablonów do reprezentowania aktualizacji.

* **Aranżacja**: Nie musisz się martwić o złożoność operacji zamawiania. Menedżer zasobów organizuje wdrażanie współzależnych zasobów, dzięki czemu są one tworzone w odpowiedniej kolejności. Jeśli to możliwe, Menedżer zasobów wdraża zasoby równolegle, dzięki czemu wdrożenia kończą się szybciej niż wdrożenia szeregowe. Szablon można wdrożyć za pomocą jednego polecenia, a nie za pomocą wielu poleceń imperatywów.

   ![Porównanie wdrażania szablonów](./media/overview/template-processing.png)

* **Pliki modułowe**: Szablony można podzielić na mniejsze składniki wielokrotnego pożytezania i połączyć je ze sobą w czasie wdrażania. Można również zagnieżdżać jeden szablon wewnątrz innych szablonów.

* **Utwórz dowolny zasób platformy Azure:** możesz natychmiast używać nowych usług i funkcji platformy Azure w szablonach. Jak tylko dostawca zasobów wprowadza nowe zasoby, można wdrożyć te zasoby za pomocą szablonów. Nie trzeba czekać na narzędzia lub moduły, które mają być aktualizowane przed użyciem nowych usług.

* **Rozszerzalność:** Za pomocą [skryptów wdrażania](deployment-script-template.md)można dodać skrypty programu PowerShell lub Bash do szablonów. Skrypty wdrażania rozszerzają możliwość konfigurowania zasobów podczas wdrażania. Skrypt może być dołączony do szablonu lub przechowywany w źródle zewnętrznym i odwołuje się do szablonu. Skrypty wdrażania umożliwiają ukończenie konfiguracji środowiska typu end-to-end w jednym szablonie ARM.

* **Testowanie:** Możesz upewnić się, że szablon jest zgodny z zalecanymi wytycznymi, testując go za pomocą zestawu narzędzi szablonu ARM (arm-ttk). Ten zestaw testowy to skrypt programu PowerShell, który można pobrać z [GitHub.](https://github.com/Azure/arm-ttk) Zestaw narzędzi ułatwia rozwijanie wiedzy specjalistycznej przy użyciu języka szablonów.

* **Podgląd zmian:** Przed wdrożeniem szablonu można użyć [operacji co jeśli,](template-deploy-what-if.md) aby uzyskać podgląd zmian. W przypadku co jeśli zostanie wyświetlone zasoby, które zostaną utworzone, zaktualizowane lub usunięte, a także wszystkie właściwości zasobów, które ulegną zmianie. Operacja co jeśli sprawdza bieżący stan środowiska i eliminuje konieczność zarządzania stanem.

* **Wbudowana weryfikacja:** Szablon jest wdrażany tylko po przejściu sprawdzania poprawności. Menedżer zasobów sprawdza szablon przed rozpoczęciem wdrażania, aby upewnić się, że wdrożenie zakończy się pomyślnie. Wdrożenie jest mniej prawdopodobne, aby zatrzymać w stanie półukończonym.

* **Śledzone wdrożenia:** W witrynie Azure portal można przejrzeć historię wdrażania i uzyskać informacje o wdrożeniu szablonu. Można zobaczyć szablon, który został wdrożony, wartości parametrów przekazywane w i wszystkie wartości wyjściowe. Inna infrastruktura jako usługi kodu nie są śledzone za pośrednictwem portalu.

   ![Historia wdrażania](./media/overview/deployment-history.png)

* **Zasady jako kod:** [Zasady platformy Azure](../../governance/policy/overview.md) to zasada jako framework kodu do automatyzacji nadzoru. Jeśli używasz zasad platformy Azure, korygowanie zasad odbywa się na niezgodnych zasobów po wdrożeniu za pomocą szablonów.

* **Plany wdrażania:** Można skorzystać z [planów dostarczonych](../../governance/blueprints/overview.md) przez firmę Microsoft w celu spełnienia standardów regulacyjnych i zgodności. Te plany obejmują wstępnie utworzone szablony dla różnych architektur.

* **Integracja ciągłej integracji/dysku CD:** Można zintegrować szablony z narzędziami ciągłej integracji i ciągłego wdrażania (CI/CD), które mogą zautomatyzować potoki wydań w celu szybkiego i niezawodnego aktualizowania aplikacji i infrastruktury. Korzystając z zadania szablonu Azure DevOps i Resource Manager, można użyć usługi Azure Pipelines do ciągłego tworzenia i wdrażania projektów szablonów ARM. Aby dowiedzieć się więcej, zobacz [projekt programu VS z potokami](add-template-to-azure-pipelines.md) i [samouczek: ciągła integracja szablonów usługi Azure Resource Manager z potokami platformy Azure.](./deployment-tutorial-pipeline.md)

* **Eksportowany kod:** Szablon istniejącej grupy zasobów można uzyskać, eksportując bieżący stan grupy zasobów lub wyświetlając szablon używany dla określonego wdrożenia. Przeglądając [wyeksportowany szablon](export-template-portal.md), można poznać jego składnię.

* **Narzędzia do tworzenia:** Można tworzyć szablony za pomocą [programu Visual Studio Code](use-vs-code-to-create-template.md) i rozszerzenia narzędzia szablonu. Otrzymujesz intellisense, wyróżnianie składni, pomoc w wierszu i wiele innych funkcji językowych. Oprócz kodu programu Visual Studio można również użyć [programu Visual Studio.](create-visual-studio-deployment-project.md)

## <a name="template-file"></a>Plik szablonu

W szablonie można pisać [wyrażenia szablonu,](template-expressions.md) które rozszerzają możliwości JSON. Wyrażenia te korzystają z [funkcji oferowanych](template-functions.md) przez Menedżera zasobów.

Szablon zawiera następujące sekcje:

* [Parametry](template-parameters.md) — podaj wartości podczas wdrażania, które umożliwiają ten sam szablon do użycia w różnych środowiskach.

* [Zmienne](template-variables.md) — zdefiniuj wartości, które są ponownie używane w szablonach. Mogą być konstruowane na podstawie wartości parametrów.

* [Funkcje zdefiniowane przez użytkownika](template-user-defined-functions.md) — tworzenie niestandardowych funkcji, które upraszczają szablon.

* [Zasoby](template-syntax.md#resources) — określ zasoby do wdrożenia.

* [Dane wyjściowe](template-outputs.md) — zwraca wartości z wdrożonych zasobów.

## <a name="template-deployment-process"></a>Proces wdrażania szablonu

Podczas wdrażania szablonu Menedżer zasobów konwertuje szablon na operacje interfejsu API REST. Na przykład, gdy usługa Resource Manager odbiera szablon o następującej definicji zasobu:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Konwertuje definicję do następującej operacji interfejsu API REST, która zostaje wysłana do dostawcy zasobów Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Projekt szablonu

Sposób definiowania szablonów i grup zasobów zależy wyłącznie od użytkownika, podobnie jak sposób zarządzania rozwiązaniem. Można na przykład wdrożyć aplikację trójwarstwową za pomocą jednego szablonu do pojedynczej grupy zasobów.

![szablon trójwarstwowy](./media/overview/3-tier-template.png)

Nie trzeba jednak definiować całej infrastruktury w jednym szablonie. Często dobrym rozwiązaniem jest podział wymagań dotyczących wdrożenia na szablony przeznaczone do określonego celu. Te szablony mogą bez problemu być używane wielokrotnie w różnych rozwiązaniach. Aby wdrożyć dane rozwiązanie, należy utworzyć szablon wzorcowy połączony ze wszystkimi wymaganymi szablonami. Na poniższej ilustracji przedstawiono sposób wdrażania rozwiązania trójwarstwowego za pomocą szablonu nadrzędnego, który zawiera trzy szablony zagnieżdżone.

![zagnieżdżony szablon warstwowy](./media/overview/nested-tiers-template.png)

Jeśli przewidujesz, że warstwy będą miały osobne cykle, możesz wdrożyć trzy warstwy do osobnych grup zasobów. Należy pamiętać, że zasoby mogą nadal być powiązane z zasobami w innych grupach zasobów.

![szablon warstwowy](./media/overview/tier-templates.png)

Informacje dotyczące szablonów zagnieżdżonych można znaleźć w temacie [Using linked templates with Azure Resource Manager](linked-templates.md) (Używanie szablonów połączonych w usłudze Azure Resource Manager).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać samouczek krok po kroku, który poprowadzi Cię przez proces tworzenia szablonu, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](template-tutorial-create-first-template.md).
* Aby uzyskać informacje o właściwościach w plikach [szablonów, zobacz Opis struktury i składni szablonów ARM](template-syntax.md).
* Aby dowiedzieć się więcej o eksportowaniu szablonów, zobacz [Szybki start: Tworzenie i wdrażanie szablonów ARM przy użyciu witryny Azure Portal](quickstart-create-templates-use-the-portal.md).

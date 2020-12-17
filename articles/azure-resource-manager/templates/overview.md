---
title: Przegląd szablonów
description: Opisuje zalety korzystania z szablonów Azure Resource Manager (szablony ARM) na potrzeby wdrażania zasobów.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: c4995791b784351219458c546442d082e2396315
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657315"
---
# <a name="what-are-arm-templates"></a>Co to są szablony usługi ARM?

Dzięki przeniesieniu do chmury wiele zespołów przyjmuje metody programowania Agile. Te zespoły szybko iterą. Muszą one wielokrotnie wdrażać swoje rozwiązania w chmurze i wiedzieć, że ich infrastruktura jest w niezawodnym stanie. Ponieważ infrastruktura stała się częścią procesu iteracji, podział między operacjami i programowaniem zniknął. Zespoły muszą zarządzać infrastrukturą i kodem aplikacji za pomocą ujednoliconego procesu.

Aby sprostać tym wyzwaniom, można zautomatyzować wdrożenia i korzystać z zalet infrastruktury jako kodu. W kodzie można zdefiniować infrastrukturę, która musi zostać wdrożona. Kod infrastruktury jest częścią projektu. Podobnie jak w przypadku kodu aplikacji, kod infrastruktury jest przechowywany w repozytorium źródłowym i w wersji. Każdy z nich w zespole może uruchomić kod i wdrożyć podobne środowiska.

Aby zaimplementować infrastrukturę jako kod dla rozwiązań platformy Azure, użyj szablonów Azure Resource Manager (szablony ARM). Szablon jest plikiem JavaScript Object Notation (JSON), który definiuje infrastrukturę i konfigurację projektu. Szablon używa składni deklaratywnej, która pozwala określić, co zamierzasz wdrożyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. W szablonie należy określić zasoby do wdrożenia oraz właściwości tych zasobów.

## <a name="why-choose-arm-templates"></a>Dlaczego warto wybrać Szablony ARM?

Jeśli próbujesz zdecydować się na korzystanie z szablonów usługi ARM i jednej z innych infrastruktury jako usług kodu, weź pod uwagę następujące korzyści związane z korzystaniem z szablonów:

* **Składnia deklaracyjne**: szablony ARM umożliwiają deklaratywne tworzenie i wdrażanie całej infrastruktury platformy Azure. Można na przykład wdrożyć nie tylko maszyny wirtualne, ale również infrastrukturę sieci, systemy magazynu i inne zasoby, które mogą być potrzebne.

* **Powtarzalne wyniki**: wielokrotnie wdrażać infrastrukturę w całym cyklu rozwoju i mieć pewność, że zasoby są wdrażane w spójny sposób. Szablony są idempotentne, co oznacza, że można wdrożyć ten sam szablon wiele razy i uzyskać te same typy zasobów w tym samym stanie. Możesz opracować jeden szablon, który reprezentuje żądany stan, a nie opracowując wielu oddzielnych szablonów do reprezentowania aktualizacji.

* **Aranżacja**: nie trzeba martwić się o złożone operacje porządkowania. Menedżer zasobów organizuje wdrażanie zasobów zależnych, aby zostały utworzone w odpowiedniej kolejności. Gdy jest to możliwe, Menedżer zasobów wdraża zasoby równolegle, dzięki czemu wdrożenia kończą się szybciej niż wdrożenia seryjne. Szablon jest wdrażany za pomocą jednego polecenia, a nie przez wiele niekoniecznych poleceń.

   ![Porównanie Template deployment](./media/overview/template-processing.png)

* **Pliki modularne**: można rozbić szablony na mniejsze składniki, wielokrotnego użytku i łączyć je ze sobą w czasie wdrażania. Możesz również zagnieżdżać jeden szablon w innych szablonach.

* **Utwórz dowolny zasób platformy Azure**: możesz od razu korzystać z nowych usług i funkcji platformy Azure w szablonach. Gdy tylko dostawca zasobów wprowadza nowe zasoby, można wdrożyć te zasoby za pomocą szablonów. Nie musisz czekać na aktualizację narzędzi lub modułów przed rozpoczęciem korzystania z nowych usług.

* **Rozszerzalność**: ze [skryptami wdrażania](deployment-script-template.md)można dodać do szablonów skrypty programu PowerShell lub bash. Skrypty wdrażania zwiększają możliwości konfigurowania zasobów podczas wdrażania. Skrypt może być uwzględniony w szablonie lub przechowywany w zewnętrznym źródle i przywoływany w szablonie. Skrypty wdrażania umożliwiają ukończenie konfiguracji całego środowiska w ramach jednego szablonu ARM.

* **Testowanie**: można upewnić się, że szablon jest zgodny z zalecanymi wskazówkami, testując go za pomocą zestawu narzędzi szablonów ARM (ARM-TTK). Ten zestaw testów jest skryptem programu PowerShell, który można pobrać z witryny [GitHub](https://github.com/Azure/arm-ttk). Zestaw narzędzi ułatwia opracowywanie wiedzy przy użyciu języka szablonów.

* **Podgląd zmian**: możesz użyć operacji działania [warunkowego](template-deploy-what-if.md) , aby uzyskać podgląd zmian przed wdrożeniem szablonu. Dzięki czemu możesz zobaczyć, które zasoby zostaną utworzone, zaktualizowane lub usunięte, oraz wszystkie właściwości zasobów, które zostaną zmienione. Operacja działania warunkowego sprawdza bieżący stan środowiska i eliminuje konieczność zarządzania stanem.

* **Wbudowana weryfikacja**: szablon jest wdrażany dopiero po przekazaniu walidacji. Menedżer zasobów sprawdza szablon przed rozpoczęciem wdrażania, aby upewnić się, że wdrożenie zakończy się pomyślnie. Wdrożenie jest mniej prawdopodobnie zatrzymane w stanie Half-gotowym.

* **Śledzone wdrożenia**: w Azure Portal można przejrzeć historię wdrożenia i uzyskać informacje o wdrożeniu szablonu. Zobaczysz szablon, który został wdrożony, wartości parametrów przekazywane i wszystkie wartości wyjściowe. Inne infrastruktury jako usługi kodu nie są śledzone za pomocą portalu.

   ![Historia wdrożenia](./media/overview/deployment-history.png)

* **Zasady jako kod**: [Azure Policy](../../governance/policy/overview.md) jest zasadą jako strukturę kodu do automatyzowania zarządzania. Jeśli korzystasz z zasad platformy Azure, korygowanie zasad odbywa się w przypadku niezgodnych zasobów w przypadku ich wdrożenia za pomocą szablonów.

* **Plany wdrożenia**: możesz skorzystać z [planów](../../governance/blueprints/overview.md) zapewnianych przez firmę Microsoft, aby spełnić standardy zgodności z przepisami. Te plany obejmują wstępnie skompilowane szablony dla różnych architektur.

* **Integracja** ciągłej integracji i ciągłego wdrażania: możesz zintegrować szablony z narzędziami do ciągłego i nieciągłego wdrożenia (Ci/CD), które mogą zautomatyzować potoki wydań w celu uzyskania szybkich i niezawodnych aktualizacji aplikacji i infrastruktury. Za pomocą zadania DevOps platformy Azure i szablonu Menedżer zasobów można używać Azure Pipelines do ciągłego kompilowania i wdrażania projektów szablonów ARM. Aby dowiedzieć się więcej, zobacz [projekt programu vs z potokami](add-template-to-azure-pipelines.md) i [Samouczekem: Ciągła integracja szablonów Azure Resource Manager z Azure Pipelines](./deployment-tutorial-pipeline.md).

* **Kod możliwy do eksportu**: można uzyskać szablon dla istniejącej grupy zasobów, eksportując bieżący stan grupy zasobów lub wyświetlając szablon używany do określonego wdrożenia. Przeglądając [wyeksportowany szablon](export-template-portal.md), można poznać jego składnię.

* **Narzędzia autorskie**: szablony można tworzyć za pomocą [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) i rozszerzenia narzędzia szablonu. Uzyskasz funkcję IntelliSense, wyróżnianie składni, pomoc w wierszu i wiele innych funkcji języka. Oprócz Visual Studio Code można również użyć [programu Visual Studio](create-visual-studio-deployment-project.md).

## <a name="template-file"></a>Plik szablonu

W ramach szablonu można napisać [wyrażenia szablonu](template-expressions.md) , które zwiększają możliwości JSON. Te wyrażenia wykorzystują [funkcje](template-functions.md) udostępniane przez Menedżer zasobów.

Szablon zawiera następujące sekcje:

* [Parametry](template-parameters.md) — podaj wartości podczas wdrażania, które umożliwiają użycie tego samego szablonu w różnych środowiskach.

* [Zmienne](template-variables.md) — Zdefiniuj wartości, które są ponownie używane w szablonach. Mogą być zbudowane z wartości parametrów.

* [Funkcje zdefiniowane przez użytkownika](template-user-defined-functions.md) — tworzenie dostosowanych funkcji, które upraszczają szablon.

* [Zasoby](template-syntax.md#resources) — Określ zasoby do wdrożenia.

* Dane [wyjściowe](template-outputs.md) — zwraca wartości ze wdrożonych zasobów.

## <a name="template-deployment-process"></a>Proces Template deployment

Podczas wdrażania szablonu Menedżer zasobów konwertuje szablon na operacje interfejsu API REST. Na przykład, gdy usługa Resource Manager odbiera szablon o następującej definicji zasobu:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

Konwertuje definicję do następującej operacji interfejsu API REST, która zostaje wysłana do dostawcy zasobów Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

Zwróć uwagę, że **apiVersion** ustawiony w szablonie dla zasobu jest używany jako wersja interfejsu API dla operacji Rest. Można wielokrotnie wdrożyć szablon i mieć pewność, że będzie on nadal działał. Korzystając z tej samej wersji interfejsu API, nie trzeba martwić się o istotne zmiany, które mogą zostać wprowadzone w nowszych wersjach.

## <a name="template-design"></a>Projekt szablonu

Sposób definiowania szablonów i grup zasobów zależy wyłącznie od użytkownika, podobnie jak sposób zarządzania rozwiązaniem. Można na przykład wdrożyć aplikację trójwarstwową za pomocą jednego szablonu do pojedynczej grupy zasobów.

![szablon trójwarstwowy](./media/overview/3-tier-template.png)

Nie trzeba jednak definiować całej infrastruktury w jednym szablonie. Często dobrym rozwiązaniem jest podział wymagań dotyczących wdrożenia na szablony przeznaczone do określonego celu. Te szablony mogą bez problemu być używane wielokrotnie w różnych rozwiązaniach. Aby wdrożyć konkretne rozwiązanie, należy utworzyć główny szablon, który łączy wszystkie wymagane szablony. Na poniższej ilustracji przedstawiono sposób wdrażania rozwiązania trójwarstwowego za pomocą szablonu nadrzędnego, który zawiera trzy szablony zagnieżdżone.

![zagnieżdżony szablon warstwowy](./media/overview/nested-tiers-template.png)

Jeśli przewidujesz, że warstwy będą miały osobne cykle, możesz wdrożyć trzy warstwy do osobnych grup zasobów. Należy pamiętać, że zasoby mogą nadal być powiązane z zasobami w innych grupach zasobów.

![szablon warstwowy](./media/overview/tier-templates.png)

Informacje dotyczące szablonów zagnieżdżonych można znaleźć w temacie [Using linked templates with Azure Resource Manager](linked-templates.md) (Używanie szablonów połączonych w usłudze Azure Resource Manager).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu usługi ARM](template-tutorial-create-first-template.md).
* Aby dowiedzieć się więcej na temat szablonów ARM za pomocą zestawu z przewodnikiem na Microsoft Learn, zobacz [wdrażanie zasobów i zarządzanie nimi na platformie Azure przy użyciu szablonów ARM](/learn/paths/deploy-manage-resource-manager-templates/).
* Informacje o właściwościach w plikach szablonów można znaleźć w temacie [Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby dowiedzieć się więcej na temat eksportowania szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów ARM przy użyciu Azure Portal](quickstart-create-templates-use-the-portal.md).
* Odpowiedzi na często zadawane pytania można znaleźć w sekcji [często zadawanych pytań dotyczących szablonów ARM](frequently-asked-questions.md).

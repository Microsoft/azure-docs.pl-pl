---
title: Nieprawidłowe błędy szablonu
description: Opisuje sposób rozwiązywania nieprawidłowych błędów szablonów podczas wdrażania szablonów Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 05/22/2020
ms.openlocfilehash: ba19d3c4e72a765e2aaff7393915b77a80daf2ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185797"
---
# <a name="resolve-errors-for-invalid-template"></a>Usuwanie błędów związanych z nieprawidłowym szablonem

W tym artykule opisano sposób rozwiązywania nieprawidłowych błędów szablonów.

## <a name="symptom"></a>Objaw

Podczas wdrażania szablonu pojawia się błąd wskazujący:

```
Code=InvalidTemplate
Message=<varies>
```

Komunikat o błędzie zależy od typu błędu.

## <a name="cause"></a>Przyczyna

Ten błąd może wynikać z kilku różnych typów błędów. Zwykle obejmują one składnię lub błąd strukturalny w szablonie.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Rozwiązanie 1 — błąd składniowy

Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że sprawdzanie poprawności szablonu nie powiodło się, może wystąpić błąd składniowy w szablonie.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ten błąd jest łatwy do przeprowadzenia, ponieważ wyrażenia szablonu mogą być Intricate. Na przykład następujące przypisanie nazwy dla konta magazynu ma jeden zestaw nawiasów, trzy funkcje, trzy zestawy nawiasów, jeden zestaw apostrofów i jedną właściwość:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Jeśli nie podano pasującej składni, szablon tworzy wartość różną od zamierzonej.

Po otrzymaniu tego typu błędu należy uważnie przejrzeć składnię wyrażenia. Rozważ użycie edytora JSON, takiego jak [Visual Studio](create-visual-studio-deployment-project.md) lub [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), który może ostrzec o błędach składni.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Rozwiązanie 2 — nieprawidłowe długości segmentów

Jeśli nazwa zasobu nie ma poprawnego formatu, występuje inny nieprawidłowy błąd szablonu.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Zasób poziomu głównego musi mieć jeden segment less w nazwie niż w typie zasobu. Każdy segment jest zróżnicowany przez ukośnik. W poniższym przykładzie typ ma dwa segmenty, a nazwa ma jeden segment, więc jest **prawidłową nazwą**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Ale następny przykład nie jest **prawidłową nazwą** , ponieważ ma taką samą liczbę segmentów jak typ.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

W przypadku zasobów podrzędnych typ i nazwa mają tę samą liczbę segmentów. Ta liczba segmentów ma sens, ponieważ pełna nazwa i typ elementu podrzędnego zawierają nazwę i typ elementu nadrzędnego. W związku z tym pełna nazwa nadal ma jeden segment less niż pełny typ.

```json
"resources": [
  {
    "type": "Microsoft.KeyVault/vaults",
    "name": "contosokeyvault",
    ...
    "resources": [
      {
        "type": "secrets",
        "name": "appPassword",
        ...
      }
    ]
  }
]
```

Uzyskanie segmentów bezpośrednio może być trudne w przypadku Menedżer zasobów typów, które są stosowane w ramach dostawców zasobów. Na przykład zastosowanie blokady zasobu do witryny sieci Web wymaga typu z czterema segmentami. W związku z tym nazwa ma trzy segmenty:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-is-not-valid"></a>Rozwiązanie 3 — parametr jest nieprawidłowy

Jeśli podano wartość parametru, która nie jest jedną z dozwolonych wartości, zostanie wyświetlony komunikat podobny do następującego błędu:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Podwójne sprawdzanie dozwolonych wartości w szablonie i udostępnianie ich podczas wdrażania. Aby uzyskać więcej informacji na temat dozwolonych wartości parametrów, zobacz [sekcję parametry w szablonach Azure Resource Manager](template-syntax.md#parameters).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Rozwiązanie 4 — zbyt wiele docelowych grup zasobów

Ten błąd może pojawić się we wcześniejszych wdrożeniach, ponieważ ograniczono do pięciu docelowych grup zasobów w jednym wdrożeniu. W maju 2020 limit został zwiększony do 800 grup zasobów. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](./deploy-to-resource-group.md).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Rozwiązanie 5 — Wykryto zależność cykliczną

Ten błąd występuje, gdy zasoby są zależne od siebie w sposób uniemożliwiający uruchomienie wdrożenia. Połączenie wzajemnych zależności powoduje, że co najmniej jeden zasób czeka na inne zasoby, które również oczekują. Na przykład Resource1 zależy od resource3, resource2 zależy od Resource1, a resource3 zależy od resource2. Zazwyczaj można rozwiązać ten problem, usuwając zbędne zależności.

Aby rozwiązać zależność cykliczną:

1. W szablonie Znajdź zasób zidentyfikowany w zależności cyklicznej.
2. W przypadku tego zasobu Sprawdź Właściwość **dependsOn** i wszelkie zastosowania funkcji **Reference** , aby zobaczyć, które zasoby są od niego zależne.
3. Sprawdź te zasoby, aby zobaczyć, które zasoby są od nich zależne. Obserwuj zależności do momentu zauważenia zasobu, który zależy od oryginalnego zasobu.
5. W przypadku zasobów związanych z zależnością cykliczną należy uważnie sprawdzić wszystkie zastosowania właściwości **dependsOn** , aby zidentyfikować wszelkie zależności, które nie są potrzebne. Usuń te zależności. Jeśli nie masz pewności, że jest wymagana zależność, spróbuj ją usunąć.
6. Wdróż ponownie szablon.

Usunięcie wartości z właściwości **dependsOn** może spowodować błędy podczas wdrażania szablonu. Jeśli wystąpi błąd, Dodaj zależność z powrotem do szablonu.

Jeśli takie podejście nie rozwiąże zależności cyklicznej, rozważ przeniesienie części logiki wdrażania do zasobów podrzędnych (takich jak rozszerzenia lub ustawienia konfiguracji). Skonfiguruj te zasoby podrzędne do wdrożenia po zasobach występujących w zależności cyklicznej. Załóżmy na przykład, że wdrażasz dwie maszyny wirtualne, ale musisz ustawić właściwości dla każdej z nich, która odwołuje się do drugiego. Można je wdrożyć w następującej kolejności:

1. vm1
2. VM2
3. Rozszerzenie na VM1 zależy od VM1 i VM2. Rozszerzenie ustawia wartości w VM1, które pobiera z VM2.
4. Rozszerzenie na VM2 zależy od VM1 i VM2. Rozszerzenie ustawia wartości w VM2, które pobiera z VM1.

Takie samo podejście działa w przypadku aplikacji App Service. Rozważ przeniesienie wartości konfiguracyjnych do zasobu podrzędnego zasobu aplikacji. Dwie aplikacje sieci Web można wdrażać w następującej kolejności:

1. webapp1
2. webapp2
3. Konfiguracja programu webapp1 zależy od webapp1 i webapp2. Zawiera ustawienia aplikacji z wartościami z webapp2.
4. Konfiguracja programu webapp2 zależy od webapp1 i webapp2. Zawiera ustawienia aplikacji z wartościami z webapp1.
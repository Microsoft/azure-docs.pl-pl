---
title: Struktura i składnia szablonu
description: Opisuje strukturę i właściwości szablonów usługi Azure Resource Manager przy użyciu deklaratywnej składni JSON.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 60d800eb5251fb3454ba60a67bd109261c6ff9d4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687880"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Opis struktury i składni szablonów ARM

W tym artykule opisano strukturę szablonu usługi Azure Resource Manager (ARM). Przedstawia różne sekcje szablonu i właściwości, które są dostępne w tych sekcjach.

Ten artykuł jest przeznaczony dla użytkowników, którzy mają pewne znajomości szablonów ARM. Zawiera szczegółowe informacje na temat struktury szablonu. Aby uzyskać samouczek krok po kroku, który poprowadzi Cię przez proces tworzenia szablonu, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu usługi Azure Resource Manager](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Template format (Format szablonu)

W najprostszej strukturze szablon zawiera następujące elementy:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nazwa elementu | Wymagany | Opis |
|:--- |:--- |:--- |
| $schema |Tak |Lokalizacja pliku schematu JSON, który opisuje wersję języka szablonu. Numer wersji, którego używasz, zależy od zakresu wdrożenia i edytora JSON.<br><br>Jeśli używasz [programu VS Code z rozszerzeniem narzędzi usługi Azure Resource Manager,](use-vs-code-to-create-template.md)użyj najnowszej wersji dla wdrożeń grup zasobów:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Inne edytory (w tym visual studio) może nie być w stanie przetworzyć tego schematu. Dla tych redaktorów, użyj:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>W przypadku wdrożeń subskrypcji należy użyć:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>W przypadku wdrożeń grup zarządzania należy użyć:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>W przypadku wdrożeń dzierżawy należy użyć:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Tak |Wersja szablonu (na przykład 1.0.0.0). Można podać dowolną wartość dla tego elementu. Ta wartość służy do dokumentowania istotnych zmian w szablonie. Podczas wdrażania zasobów przy użyciu szablonu, ta wartość może służyć do upewnij się, że używany jest odpowiedni szablon. |
| profil apiProfile |Nie | Wersja interfejsu API, która służy jako zbiór wersji interfejsu API dla typów zasobów. Użyj tej wartości, aby uniknąć konieczności określania wersji interfejsu API dla każdego zasobu w szablonie. Po określeniu wersji profilu interfejsu API i nie określaniu wersji interfejsu API dla typu zasobu Menedżer zasobów używa wersji interfejsu API dla tego typu zasobu zdefiniowanego w profilu.<br><br>Właściwość profilu interfejsu API jest szczególnie przydatna podczas wdrażania szablonu w różnych środowiskach, takich jak Usługa Azure Stack i globalna platforma Azure. Użyj wersji profilu interfejsu API, aby upewnić się, że szablon automatycznie używa wersji, które są obsługiwane w obu środowiskach. Aby uzyskać listę bieżących wersji profilu interfejsu API i wersji interfejsu API zasobów zdefiniowanych w profilu, zobacz [Profil interfejsu API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Aby uzyskać więcej informacji, zobacz [Śledzenie wersji przy użyciu profilów interfejsu API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [Parametry](#parameters) |Nie |Wartości, które są dostarczane podczas wdrażania jest wykonywany w celu dostosowania wdrożenia zasobów. |
| [Zmiennych](#variables) |Nie |Wartości, które są używane jako fragmenty JSON w szablonie, aby uprościć wyrażenia języka szablonu. |
| [Funkcje](#functions) |Nie |Funkcje zdefiniowane przez użytkownika, które są dostępne w szablonie. |
| [Zasobów](#resources) |Tak |Typy zasobów, które są wdrażane lub aktualizowane w grupie zasobów lub subskrypcji. |
| [Wyjść](#outputs) |Nie |Wartości, które są zwracane po wdrożeniu. |

Każdy element ma właściwości, które można ustawić. W tym artykule opisano sekcje szablonu bardziej szczegółowo.

## <a name="parameters"></a>Parametry

W sekcji parametry szablonu można określić, które wartości można wprowadzić podczas wdrażania zasobów. W szablonie jest ograniczona liczba parametrów 256. Można zmniejszyć liczbę parametrów przy użyciu obiektów, które zawierają wiele właściwości.

Dostępne właściwości parametru to:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Nazwa elementu | Wymagany | Opis |
|:--- |:--- |:--- |
| nazwa parametru |Tak |Nazwa parametru. Musi być prawidłowym identyfikatorem JavaScript. |
| type |Tak |Typ wartości parametru. Dozwolone typy i wartości to **ciąg**, **securestring**, **int**, **bool**, **object**, **secureObject**i **tablica**. Zobacz [Typy danych](#data-types). |
| Defaultvalue |Nie |Wartość domyślna dla parametru, jeśli dla parametru nie jest podana żadna wartość. |
| Allowedvalues |Nie |Tablica dozwolonych wartości parametru, aby upewnić się, że podana jest właściwa wartość. |
| Minvalue |Nie |Minimalna wartość parametrów typu int, ta wartość jest włącznie. |
| Maxvalue |Nie |Maksymalna wartość parametrów typu int, ta wartość jest włącznie. |
| Minlength |Nie |Minimalna długość dla parametrów ciągu, bezpiecznego ciągu i typu tablicy, ta wartość jest włącznie. |
| Maxlength |Nie |Maksymalna długość dla parametrów ciągu, bezpiecznego ciągu i typu tablicy, ta wartość jest włącznie. |
| description |Nie |Opis parametru, który jest wyświetlany użytkownikom za pośrednictwem portalu. Aby uzyskać więcej informacji, zobacz [Komentarze w szablonach](#comments). |

Aby zapoznać się z przykładami używania parametrów, zobacz [Parametry w szablonach usługi Azure Resource Manager](template-parameters.md).

### <a name="data-types"></a>Typy danych

W przypadku liczby całkowite przekazywane jako parametry wbudowane zakres wartości może być ograniczony przez zestaw SDK lub narzędzie wiersza polecenia używane do wdrożenia. Na przykład podczas wdrażania szablonu przy użyciu programu PowerShell typy całkowite mogą wynosić od -2147483648 do 2147483647. Aby uniknąć tego ograniczenia, należy określić duże wartości całkowite w [pliku parametrów](parameter-files.md). Typy zasobów stosują własne limity dla właściwości liczby całkowitej.

Określając wartości logiczne i całkowite w szablonie, nie otaczaj wartości cudzysłowami. Wartości ciągów początkowych i końcowych z podwójnymi cudzysłowami.

Obiekty zaczynają się od lewego nawiasu klamrowego i kończą prawym nawiasem klamrowym. Tablice zaczynają się od lewego nawiasu, a kończą prawym nawiasem.

Bezpieczne ciągi i bezpieczne obiekty nie mogą być odczytywane po wdrożeniu zasobów.

Aby uzyskać próbki typów danych formatowania, zobacz [Formaty typów parametrów](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Zmienne

W sekcji zmiennych można skonstruować wartości, które mogą być używane w całym szablonie. Nie trzeba definiować zmiennych, ale często upraszczają szablon, zmniejszając złożone wyrażenia.

W poniższym przykładzie przedstawiono dostępne opcje definiowania zmiennej:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Aby uzyskać `copy` informacje na temat używania do tworzenia kilku wartości dla zmiennej, zobacz [Iteracja zmienna](copy-variables.md).

Aby zapoznać się z przykładami używania zmiennych, zobacz [Zmienne w szablonie Usługi Azure Resource Manager](template-variables.md).

## <a name="functions"></a>Funkcje

W szablonie można tworzyć własne funkcje. Te funkcje są dostępne do użycia w szablonie. Zazwyczaj definiuje się skomplikowane wyrażenia, których nie chcesz powtarzać w całym szablonie. Funkcje zdefiniowane przez użytkownika można tworzyć na podstawie wyrażeń i [funkcji](template-functions.md) obsługiwanych w szablonach.

Podczas definiowania funkcji użytkownika istnieją pewne ograniczenia:

* Funkcja nie może uzyskać dostępu do zmiennych.
* Funkcja może używać tylko parametrów, które są zdefiniowane w funkcji. Podczas korzystania z [funkcji parametrów](template-functions-deployment.md#parameters) w ramach funkcji zdefiniowanej przez użytkownika, jesteś ograniczony do parametrów dla tej funkcji.
* Funkcja nie może wywoływać innych funkcji zdefiniowanych przez użytkownika.
* Funkcja nie może korzystać z [funkcji odwołania](template-functions-resource.md#reference).
* Parametry funkcji nie mogą mieć wartości domyślnych.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Nazwa elementu | Wymagany | Opis |
|:--- |:--- |:--- |
| namespace |Tak |Obszar nazw dla funkcji niestandardowych. Służy do unikania konfliktów nazw z funkcjami szablonu. |
| nazwa funkcji |Tak |Nazwa funkcji niestandardowej. Podczas wywoływania funkcji połącz nazwę funkcji z obszarem nazw. Na przykład, aby wywołać funkcję o nazwie uniqueName `"[contoso.uniqueName()]"`w contoso obszaru nazw, należy użyć programu . |
| nazwa parametru |Nie |Nazwa parametru, który ma być używany w ramach funkcji niestandardowej. |
| wartość parametru |Nie |Typ wartości parametru. Dozwolone typy i wartości to **ciąg**, **securestring**, **int**, **bool**, **object**, **secureObject**i **tablica**. |
| typ wyjściowy |Tak |Typ wartości wyjściowej. Wartości wyjściowe obsługują te same typy co parametry wejściowe funkcji. |
| wartość wyjściowa |Tak |Wyrażenie języka szablonu, które jest oceniane i zwracane z funkcji. |

Aby zapoznać się z przykładami używania funkcji niestandardowych, zobacz [Funkcje zdefiniowane przez użytkownika w szablonie Usługi Azure Resource Manager](template-user-defined-functions.md).

## <a name="resources"></a>Resources

W sekcji zasoby można zdefiniować zasoby, które są wdrażane lub aktualizowane.

Zasoby można zdefiniować za pomocą następującej struktury:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nazwa elementu | Wymagany | Opis |
|:--- |:--- |:--- |
| Warunek | Nie | Wartość logiczna, która wskazuje, czy zasób będzie aprowizować podczas tego wdrożenia. Kiedy `true`zasób jest tworzony podczas wdrażania. Kiedy `false`, zasób jest pomijany dla tego wdrożenia. Patrz [warunek](conditional-resource-deployment.md). |
| type |Tak |Typ zasobu. Ta wartość jest kombinacją obszaru nazw dostawcy zasobów i typu zasobu (takiego jak **Microsoft.Storage/storageAccounts**). Aby określić dostępne wartości, zobacz [odwołanie do szablonu](/azure/templates/). W przypadku zasobu podrzędnego format typu zależy od tego, czy jest zagnieżdżony w zasobie nadrzędnym, czy zdefiniowany poza zasobem nadrzędnym. Zobacz [Ustawianie nazwy i typu zasobów podrzędnych](child-resource-name-type.md). |
| apiVersion |Tak |Wersja interfejsu API REST do użycia do tworzenia zasobu. Aby określić dostępne wartości, zobacz [odwołanie do szablonu](/azure/templates/). |
| name |Tak |Nazwa zasobu. Nazwa musi być zgodna z ograniczeniami składników identyfikatora URI zdefiniowanymi w RFC3986. Usługi platformy Azure, które udostępniają nazwę zasobu stronom zewnętrznym, sprawdzają poprawność nazwy, aby upewnić się, że nie jest to próba sfałszowania innej tożsamości. W przypadku zasobu podrzędnego format nazwy zależy od tego, czy jest zagnieżdżona w zasobie nadrzędnym, czy zdefiniowana poza zasobem nadrzędnym. Zobacz [Ustawianie nazwy i typu zasobów podrzędnych](child-resource-name-type.md). |
| Komentarze |Nie |Notatki dotyczące dokumentowania zasobów w szablonie. Aby uzyskać więcej informacji, zobacz [Komentarze w szablonach](template-syntax.md#comments). |
| location |Różna |Obsługiwane lokalizacje geograficzne dostarczonego zasobu. Można wybrać dowolną z dostępnych lokalizacji, ale zazwyczaj warto wybrać jedną z lokalizacji, która jest bliska użytkownikom. Zazwyczaj ma również sens umieszczanie zasobów, które współdziałają ze sobą w tym samym regionie. Większość typów zasobów wymaga lokalizacji, ale niektóre typy (takie jak przypisanie roli) nie wymagają lokalizacji. Zobacz [Ustawianie lokalizacji zasobów](resource-location.md). |
| dependsOn |Nie |Zasoby, które muszą zostać wdrożone przed wdrożeniem tego zasobu. Menedżer zasobów ocenia zależności między zasobami i wdraża je w odpowiedniej kolejności. Gdy zasoby nie są zależne od siebie nawzajem, są one wdrażane równolegle. Wartością może być oddzielona przecinkami lista nazw zasobów lub unikatowych identyfikatorów zasobów. Lista tylko zasobów, które są wdrażane w tym szablonie. Zasoby, które nie są zdefiniowane w tym szablonie musi już istnieć. Unikaj dodawania niepotrzebnych zależności, ponieważ mogą one spowolnić wdrożenie i utworzyć zależności cykliczne. Aby uzyskać wskazówki dotyczące ustawiania zależności, zobacz [Definiowanie zależności w szablonach usługi Azure Resource Manager](define-resource-dependency.md). |
| tags |Nie |Tagi, które są skojarzone z zasobem. Zastosuj tagi, aby logicznie organizować zasoby w ramach subskrypcji. |
| sku | Nie | Niektóre zasoby umożliwiają wdrożenie wartości definiujących jednostkę SKU. Na przykład można określić typ nadmiarowości dla konta magazynu. |
| Rodzaju | Nie | Niektóre zasoby zezwalają na wartość definiuj typ wywożenia zasobu. Na przykład można określić typ usługi Cosmos DB do utworzenia. |
| copy |Nie |Jeśli potrzebne jest więcej niż jedno wystąpienie, liczba zasobów do utworzenia. Tryb domyślny jest równoległy. Określ tryb szeregowy, gdy nie chcesz, aby wszystkie lub zasoby były wdrażane w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Tworzenie kilku wystąpień zasobów w usłudze Azure Resource Manager](copy-resources.md). |
| plan | Nie | Niektóre zasoby zezwalają na wartości, które definiują plan do wdrożenia. Na przykład można określić obraz portalu marketplace dla maszyny wirtualnej. |
| properties |Nie |Ustawienia konfiguracji specyficzne dla zasobów. Wartości właściwości są takie same jak wartości podane w treści żądania dla operacji interfejsu API REST (metoda PUT) w celu utworzenia zasobu. Można również określić tablicę kopiowania, aby utworzyć kilka wystąpień właściwości. Aby określić dostępne wartości, zobacz [odwołanie do szablonu](/azure/templates/). |
| zasoby |Nie |Zasoby podrzędne, które zależą od zdefiniowanego zasobu. Podaj tylko typy zasobów, które są dozwolone przez schemat zasobu nadrzędnego. Zależność od zasobu nadrzędnego nie jest implikowana. Należy jawnie zdefiniować tę zależność. Zobacz [Ustawianie nazwy i typu zasobów podrzędnych](child-resource-name-type.md). |

## <a name="outputs"></a>Dane wyjściowe

W sekcji Dane wyjściowe należy określić wartości, które są zwracane z wdrożenia. Zazwyczaj zwracane wartości z zasobów, które zostały wdrożone.

Poniższy przykład przedstawia strukturę definicji danych wyjściowych:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Nazwa elementu | Wymagany | Opis |
|:--- |:--- |:--- |
| nazwa wyjścia |Tak |Nazwa wartości wyjściowej. Musi być prawidłowym identyfikatorem JavaScript. |
| Warunek |Nie | Wartość logiczna wskazująca, czy zwracana jest ta wartość wyjściowa. Kiedy `true`, wartość jest uwzględniona w danych wyjściowych dla wdrożenia. Kiedy `false`, wartość danych wyjściowych jest pomijany dla tego wdrożenia. Jeśli nie jest określony, `true`wartością domyślną jest . |
| type |Tak |Typ wartości wyjściowej. Wartości wyjściowe obsługują te same typy co parametry wejściowe szablonu. Jeśli określisz **securestring** dla typu wyjściowego, wartość nie jest wyświetlana w historii wdrażania i nie można pobrać z innego szablonu. Aby użyć wartości tajnej w więcej niż jednym szablonie, należy przechowywać klucz tajny w magazynie kluczy i odwoływać się do klucza tajnego w pliku parametrów. Aby uzyskać więcej informacji, zobacz [Przekazywanie wartości bezpiecznego parametru podczas wdrażania za pomocą usługi Azure Key Vault.](key-vault-parameter.md) |
| value |Nie |Wyrażenie języka szablonu, które jest oceniane i zwracane jako wartość wyjściowa. Określ **wartość** lub **kopię**. |
| copy |Nie | Służy do zwracania więcej niż jednej wartości dla danych wyjściowych. Określ **wartość** lub **kopię**. Aby uzyskać więcej informacji, zobacz [Iteracja danych wyjściowych w szablonach usługi Azure Resource Manager](copy-outputs.md). |

Aby zapoznać się z przykładami używania danych wyjściowych, zobacz [Dane wyjściowe w szablonie Usługi Azure Resource Manager](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Komentarze i metadane

Istnieje kilka opcji dodawania komentarzy i metadanych do szablonu.

### <a name="comments"></a>Komentarze

W przypadku komentarzy wbudowanych można `//` `/* ... */` użyć jednej lub jednej z tych składni, która nie działa ze wszystkimi narzędziami. Edytor szablonów portalu nie może używać się do pracy nad szablonami z komentarzami wbudowanymi. Jeśli dodasz ten styl komentarza, upewnij się, że narzędzia, których używasz, obsługują wbudowane komentarze JSON.

> [!NOTE]
> Aby wdrożyć szablony z komentarzami przy użyciu interfejsu wiersza polecenia platformy Azure `--handle-extended-json-format` w wersji 2.3.0 lub starszej, należy użyć przełącznika.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

W programie Visual Studio Code [rozszerzenie Narzędzia usługi Azure Resource Manager](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) może automatycznie wykryć szablon Menedżera zasobów i odpowiednio zmienić tryb języka. Jeśli widzisz **szablon usługi Azure Resource Manager** w prawym dolnym rogu programu VS Code, możesz użyć komentarzy wbudowanych. Komentarze wbudowane nie są już oznaczone jako nieprawidłowe.

![Tryb szablonu usługi Azure Resource Manager kodu programu Visual Studio](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadane

`metadata` Obiekt można dodać niemal wszędzie w szablonie. Menedżer zasobów ignoruje obiekt, ale edytor JSON może ostrzegać, że właściwość jest nieprawidłowa. W obiekcie zdefiniuj potrzebne właściwości.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

W przypadku **parametrów** `metadata` dodaj `description` obiekt z właściwością.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Podczas wdrażania szablonu za pośrednictwem portalu tekst, który podał w opisie, jest automatycznie używany jako wskazówka dla tego parametru.

![Pokaż końcówkę parametru](./media/template-syntax/show-parameter-tip.png)

W **resources**przypadku zasobów `comments` dodaj element lub obiekt metadanych. W poniższym przykładzie pokazano zarówno element komentarza, jak i obiekt metadanych.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

W przypadku **wyjść**dodaj obiekt metadanych do wartości wyjściowej.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Nie można dodać obiektu metadanych do funkcji zdefiniowanych przez użytkownika.

## <a name="multi-line-strings"></a>Ciągi wielowierszowe

Ciąg można podzielić na wiele wierszy. Na przykład zobacz właściwość lokalizacji i jeden z komentarzy w poniższym przykładzie JSON.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Aby wdrożyć szablony z ciągami wielowierszowymi przy użyciu interfejsu wiersza polecenia platformy `--handle-extended-json-format` Azure w wersji 2.3.0 lub starszej, należy użyć przełącznika.

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
* Aby uzyskać szczegółowe informacje na temat funkcji, których można używać w obrębie szablonu, zobacz [Funkcje szablonów usługi Azure Resource Manager](template-functions.md).
* Aby połączyć kilka szablonów podczas wdrażania, zobacz [Używanie połączonych szablonów z usługą Azure Resource Manager](linked-templates.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów, zobacz [Najważniejsze wskazówki dotyczące szablonów usługi Azure Resource Manager](template-best-practices.md).
* Aby uzyskać zalecenia dotyczące tworzenia szablonów Usługi Resource Manager, których można używać we wszystkich środowiskach platformy Azure i usłudze Azure Stack, zobacz [Tworzenie szablonów usługi Azure Resource Manager w celu zapewnienia spójności chmury.](templates-cloud-consistency.md)

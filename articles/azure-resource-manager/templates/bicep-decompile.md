---
title: Konwertowanie szablonów między elementami JSON i Bicep
description: Opisuje polecenia służące do konwertowania szablonów Azure Resource Manager z Bicep na JSON i z formatu JSON na Bicep.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103422230"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Konwertowanie szablonów ARM między elementami JSON i Bicep

W tym artykule opisano sposób konwertowania szablonów Azure Resource Manager (szablony ARM) z formatu JSON na Bicep oraz z Bicep na format JSON.

Aby można było uruchamiać polecenia konwersji, musi być [zainstalowany interfejs wiersza polecenia Bicep](bicep-install.md) .

Polecenia konwersji generują szablony, które są funkcjonalnie równoważne. Jednak mogą nie być dokładnie takie same w implementacji. Konwersja szablonu z formatu JSON na Bicep, a następnie powrót do formatu JSON prawdopodobnie spowoduje utworzenie szablonu z inną składnią niż oryginalny szablon. Po wdrożeniu przekonwertowane szablony dają te same wyniki.

## <a name="convert-from-json-to-bicep"></a>Konwertuj z formatu JSON na Bicep

Interfejs wiersza polecenia Bicep udostępnia polecenie dekompilowania istniejącego szablonu JSON do pliku Bicep. Aby dekompilować plik JSON, użyj:

```azurecli
bicep decompile mainTemplate.json
```

To polecenie udostępnia punkt początkowy dla tworzenia Bicep. Polecenie nie działa dla wszystkich szablonów. Obecnie zagnieżdżone szablony mogą być dekompilowane tylko wtedy, gdy używają zakresu oceny wyrażenia "Inner". Nie można dekompilować szablonów, które używają pętli kopiowania.

## <a name="convert-from-bicep-to-json"></a>Konwertuj z Bicep na JSON

Interfejs wiersza polecenia Bicep udostępnia również polecenie konwersji Bicep na format JSON. Aby skompilować plik JSON, użyj:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Eksportuj szablon i Konwertuj

Możesz wyeksportować szablon dla grupy zasobów, a następnie przekazać go bezpośrednio do polecenia dekompilowania. Poniższy przykład pokazuje, jak dekompilować wyeksportowany szablon.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Wyeksportuj szablon](export-template-portal.md) przy użyciu portalu.

Użyj `bicep decompile <filename>` pobranego pliku.

---

## <a name="side-by-side-view"></a>Widok obok siebie

[Bicep plac zabaw](https://aka.ms/bicepdemo) umożliwia wyświetlanie odpowiedników plików JSON i Bicep obok siebie. Możesz wybrać przykładowy szablon, aby wyświetlić obie wersje. Można też wybrać opcję `Decompile` przekazania własnego szablonu JSON i wyświetlić odpowiedni plik Bicep.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat Bicep, zobacz [samouczek Bicep](./bicep-tutorial-create-first-bicep.md).

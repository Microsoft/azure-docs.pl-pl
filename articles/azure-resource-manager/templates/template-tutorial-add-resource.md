---
title: Samouczek — Dodawanie zasobu do szablonu
description: W tym artykule opisano kroki tworzenia pierwszego szablonu Azure Resource Manager. Dowiesz się więcej na temat składni pliku szablonu i sposobu wdrażania konta magazynu.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 58a6423944abca703a42b68044e58d86187457bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614380"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Samouczek: Dodawanie zasobu do szablonu ARM

W [poprzednim samouczku](template-tutorial-create-first-template.md)przedstawiono sposób tworzenia pustego szablonu i wdrażania go. Teraz możesz przystąpić do wdrażania rzeczywistego zasobu. W tym samouczku dodasz konto magazynu. Ukończenie tego samouczka zajmuje około **9 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy wykonanie [samouczka wprowadzającego dotyczącego szablonów](template-tutorial-create-first-template.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Dodaj zasób

Aby dodać definicję konta magazynu do istniejącego szablonu, zobacz wyróżniony kod JSON w poniższym przykładzie. Zamiast próbować skopiować sekcje szablonu, Skopiuj cały plik i Zastąp jego zawartość.

Zastąp element **{Account-Unique-Name}** (w tym nawiasy klamrowe) unikatową nazwą konta magazynu.

> [!IMPORTANT]
> Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Nazwa może zawierać tylko małe litery lub cyfry. Nie może być dłuższa niż 24 znaki. Możesz spróbować użyć wzorca nazewnictwa, takiego jak **store1** , jako prefiksu, a następnie dodać inicjały i bieżącą datę. Na przykład użyta nazwa może wyglądać jak **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Odgadnięcie unikatowej nazwy konta magazynu nie jest proste i nie działa dobrze w przypadku automatyzowania dużych wdrożeń. W dalszej części tej serii samouczków będziesz używać funkcji szablonów, które ułatwiają tworzenie unikatowej nazwy.

## <a name="resource-properties"></a>Właściwości zasobu

Możesz się zastanawiać, jak znaleźć właściwości do użycia dla każdego typu zasobu. Możesz użyć odwołania do [szablonu ARM](/azure/templates/) , aby znaleźć typy zasobów, które chcesz wdrożyć.

Każdy wdrażany zasób ma co najmniej następujące trzy właściwości:

- **Typ**: typ zasobu. Ta wartość jest kombinacją przestrzeni nazw dostawcy zasobów i typu zasobu (np. Microsoft. Storage/storageAccounts).
- **apiVersion**: wersja interfejsu API REST do użycia podczas tworzenia zasobu. Każdy dostawca zasobów opublikował własne wersje interfejsu API, dlatego ta wartość jest specyficzna dla tego typu.
- **Nazwa**: nazwa zasobu.

Większość zasobów ma również właściwość **Location** , która ustawia region, w którym zasób jest wdrażany.

Inne właściwości różnią się w zależności od typu zasobu i wersji interfejsu API. Ważne jest, aby zrozumieć połączenie między wersją interfejsu API i dostępnymi właściwościami, więc Przyjrzyjmy się bardziej szczegółowo.

W tym samouczku dodano konto magazynu do szablonu. Możesz zobaczyć tę wersję interfejsu API w witrynie [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Zwróć uwagę, że wszystkie właściwości nie zostały dodane do szablonu. Wiele właściwości jest opcjonalnych. Dostawca zasobów Microsoft. Storage może wydać nową wersję interfejsu API, ale wdrażaną wersję nie trzeba zmieniać. Możesz dalej korzystać z tej wersji i wiedzieć, że wyniki wdrożenia będą spójne.

Jeśli zobaczysz starszą wersję interfejsu API, taką jak [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), zobaczysz, że jest dostępny mniejszy zestaw właściwości.

Jeśli zdecydujesz się zmienić wersję interfejsu API dla zasobu, upewnij się, że są oceniane właściwości tej wersji i odpowiednio Dostosuj szablon.

## <a name="deploy-template"></a>Wdrażanie szablonu

Możesz wdrożyć szablon, aby utworzyć konto magazynu. Nadaj wdrożenieowi inną nazwę, aby można je było łatwo znaleźć w historii.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **TemplateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj **pełnego** przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj przełącznika **debugowania** , aby uzyskać więcej informacji na potrzeby debugowania.

Mogą wystąpić dwa możliwe błędy wdrażania:

- Błąd: Code = AccountNameInvalid; Komunikat = {Podaj unikatową nazwę} nie jest prawidłową nazwą konta magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.

    W szablonie Zastąp element **{Account-Unique-Name}** unikatową nazwą konta magazynu.  Zobacz [Dodawanie zasobu](#add-resource).

- Błąd: Code = StorageAccountAlreadyTaken; Komunikat = konto magazynu o nazwie store1abc09092019 jest już zajęte.

    W szablonie spróbuj użyć innej nazwy konta magazynu.

To wdrożenie trwa dłużej niż wdrożenie pustego szablonu, ponieważ konto magazynu zostało utworzone. Może to potrwać około minuty, ale jest zwykle szybszy.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Zobaczysz, że konto magazynu zostało wdrożone.
1. Zauważ, że etykieta wdrożenia brzmi teraz: **wdrożenia: 2**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Utworzono prosty szablon służący do wdrażania konta usługi Azure Storage.  W kolejnych samouczkach dowiesz się, jak dodawać parametry, zmienne, zasoby i dane wyjściowe do szablonu. Te funkcje są blokami konstrukcyjnymi dla znacznie bardziej złożonych szablonów.

> [!div class="nextstepaction"]
> [Dodawanie parametrów](template-tutorial-add-parameters.md)

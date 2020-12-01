---
title: Przetestuj plik definicji interfejsu użytkownika
description: Opisuje sposób testowania środowiska użytkownika w celu utworzenia aplikacji zarządzanej przez platformę Azure za pomocą portalu.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: f76d3b81c2d5425f7bb91c5c86a79faa097794e3
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435004"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Przetestuj interfejs portalu dla Azure Managed Applications

Po [utworzeniu createUiDefinition.jsw pliku](create-uidefinition-overview.md) dla aplikacji zarządzanej należy przetestować środowisko użytkownika. Aby uprościć testowanie, użyj środowiska piaskownicy ładującego plik w portalu. Nie musisz faktycznie wdrażać zarządzanej aplikacji. Piaskownica prezentuje interfejs użytkownika w bieżącym, pełnym środowisku portalu. Można też użyć skryptu do testowania interfejsu. W tym artykule przedstawiono oba podejścia. Piaskownica jest zalecanym sposobem na wyświetlenie podglądu interfejsu.

## <a name="prerequisites"></a>Wymagania wstępne

* **createUiDefinition.js** pliku. Jeśli nie masz tego pliku, skopiuj [plik przykładowy](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="use-sandbox"></a>Korzystanie z piaskownicy

1. Otwórz [piaskownicę Tworzenie definicji interfejsu użytkownika](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Pokaż piaskownicę](./media/test-createuidefinition/show-sandbox.png)

1. Zastąp pustą definicję zawartością createUiDefinition.jspliku. Wybierz pozycję **Podgląd**.

   ![Wybierz podgląd](./media/test-createuidefinition/select-preview.png)

1. Utworzony formularz zostanie wyświetlony. Możesz przejść przez środowisko użytkownika i wypełnić wartości.

   ![Pokaż formularz](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli formularz nie jest wyświetlany po wybraniu **wersji zapoznawczej**, może wystąpić błąd składniowy. Poszukaj czerwonego wskaźnika na prawym pasku przewijania i przejdź do niego.

![Pokaż błąd składniowy](./media/test-createuidefinition/show-syntax-error.png)

Jeśli formularz nie jest wyświetlany, a zamiast tego zobaczysz ikonę chmury z porzuceniem, w formularzu występuje błąd, taki jak Brakująca właściwość. Otwórz Narzędzia deweloperskie sieci Web w przeglądarce. W **konsoli** programu są wyświetlane ważne komunikaty o interfejsie.

![Pokaż błąd](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Użyj skryptu testowego

Aby przetestować interfejs w portalu, skopiuj jeden z następujących skryptów na komputer lokalny:

* [Skrypt ładowania po stronie programu PowerShell — AZ module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Skrypt ładowania po stronie programu PowerShell — moduł platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skrypt ładowania po stronie interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Aby wyświetlić plik interfejsu w portalu, Uruchom pobrany skrypt. Skrypt tworzy konto magazynu w ramach subskrypcji platformy Azure i przekazuje createUiDefinition.jsw pliku na konto magazynu. Konto magazynu jest tworzone podczas pierwszego uruchomienia skryptu lub jeśli konto magazynu zostało usunięte. Jeśli konto magazynu już istnieje w ramach subskrypcji platformy Azure, skrypt ponownie używa go. Skrypt otwiera Portal i ładuje plik z konta magazynu.

Podaj lokalizację dla konta magazynu i określ folder, w którym znajduje się createUiDefinition.jspliku.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Jeśli createUiDefinition.jsw pliku znajduje się w tym samym folderze co skrypt, a konto magazynu zostało już utworzone, nie trzeba podawać tych parametrów.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```bash
./sideload-createuidef.sh
```

Skrypt otwiera nową kartę w przeglądarce. Zostanie wyświetlony portal z interfejsem służącym do tworzenia aplikacji zarządzanej.

Podaj wartości dla pól. Po zakończeniu zobaczysz wartości, które są przesyłane do szablonu, które można znaleźć w konsoli narzędzia deweloperskie w przeglądarce.

![Pokaż wartości](./media/test-createuidefinition/show-json.png)

Możesz użyć tych wartości jako pliku parametrów do testowania szablonu wdrożenia.

Jeśli portal zostanie zasunięty na ekran podsumowania, w sekcji dane wyjściowe może wystąpić błąd. Na przykład można odwołać się do kontrolki, która nie istnieje. Jeśli parametr w danych wyjściowych jest pusty, parametr może odwoływać się do właściwości, która nie istnieje. Na przykład odwołanie do kontrolki jest prawidłowe, ale odwołanie do właściwości jest nieprawidłowe.

## <a name="test-your-solution-files"></a>Testowanie plików rozwiązania

Po zweryfikowaniu, że interfejs portalu działa zgodnie z oczekiwaniami, należy sprawdzić, czy plik createUiDefinition jest prawidłowo zintegrowany z mainTemplate.jsem pliku. Test skryptu weryfikacji można uruchomić w celu przetestowania zawartości plików rozwiązania, w tym pliku createUiDefinition. Skrypt weryfikuje składnię JSON, wyszukuje wyrażenia regularne w polach tekstowych i sprawdza, czy wartości wyjściowe interfejsu portalu pasują do parametrów szablonu. Aby uzyskać informacje na temat uruchamiania tego skryptu, zobacz [run static Validation checks for templates](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Następne kroki

Po zweryfikowaniu interfejsu portalu Dowiedz się więcej o udostępnianiu [aplikacji zarządzanej przez platformę Azure w portalu Marketplace](../../marketplace/create-new-azure-apps-offer.md).
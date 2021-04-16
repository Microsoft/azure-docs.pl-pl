---
title: Testowanie pliku definicji interfejsu użytkownika
description: Opisuje sposób testowania obsługi użytkownika podczas tworzenia aplikacji zarządzanej platformy Azure za pośrednictwem portalu.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: eeef454e4c5706b39d07261ade1c2f0ffbc942ad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478900"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testowanie interfejsu portalu pod Azure Managed Applications

Po [utworzeniu createUiDefinition.jspliku dla](create-uidefinition-overview.md) aplikacji zarządzanej należy przetestować środowisko użytkownika. Aby uprościć testowanie, użyj środowiska piaskownicy, które ładuje plik w portalu. Nie musisz wdrażać aplikacji zarządzanej. Piaskownica przedstawia interfejs użytkownika w bieżącym środowisku portalu pełnoekranowego. Możesz też użyć skryptu do testowania interfejsu. W tym artykule przedstawiono obie metody. Piaskownica jest zalecanym sposobem wyświetlania podglądu interfejsu.

## <a name="prerequisites"></a>Wymagania wstępne

* Plik **createUiDefinition.jspliku.** Jeśli nie masz tego pliku, skopiuj [przykładowy plik](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json).

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="use-sandbox"></a>Korzystanie z piaskownicy

1. Otwórz [piaskownicę Tworzenie definicji interfejsu użytkownika](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Pokaż piaskownicę](./media/test-createuidefinition/show-sandbox.png)

1. Zastąp pustą definicję zawartością pliku createUiDefinition.jspliku. Wybierz pozycję **Podgląd.**

   ![Wybieranie podglądu](./media/test-createuidefinition/select-preview.png)

1. Zostanie wyświetlony utworzony formularz. Możesz przejść przez środowisko użytkownika i wypełnić wartości.

   ![Wyświetlanie formularza](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli formularz nie zostanie wyświetlony po wybraniu **opcji** Podgląd, może wystąpić błąd składniowy. Poszukaj czerwonego wskaźnika na pasku przewijania po prawej stronie i przejdź do niego.

![Wyświetlanie błędu składni](./media/test-createuidefinition/show-syntax-error.png)

Jeśli formularz nie jest wyświetlany, a zamiast tego jest wyświetlana ikona chmury z spadkiem, w formularzu występuje błąd, taki jak brakująca właściwość. Otwórz okno Narzędzia deweloperskie w przeglądarce. Konsola **wyświetla** ważne komunikaty dotyczące interfejsu.

![Pokaż błąd](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Korzystanie ze skryptu testowego

Aby przetestować interfejs w portalu, skopiuj jeden z następujących skryptów na komputer lokalny:

* [Skrypt ładowania bocznego programu PowerShell — moduł Az](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Skrypt ładowania bocznego programu PowerShell — moduł platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Skrypt ładowania bocznego interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Aby wyświetlić plik interfejsu w portalu, uruchom pobrany skrypt. Skrypt tworzy konto magazynu w ramach subskrypcji platformy Azure i createUiDefinition.jspliku na konto magazynu. Konto magazynu jest tworzone przy pierwszym uruchomieniu skryptu lub po usunięciu konta magazynu. Jeśli konto magazynu już istnieje w subskrypcji platformy Azure, skrypt użyje go ponownie. Skrypt otwiera portal i ładuje plik z konta magazynu.

Podaj lokalizację konta magazynu i określ folder, w którym znajduje się createUiDefinition.jspliku.

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

Jeśli plik createUiDefinition.jsznajduje się w tym samym folderze co skrypt, a konto magazynu zostało już utworzone, nie musisz po prostu podaniem tych parametrów.

W przypadku programu PowerShell użyj polecenia:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```bash
./sideload-createuidef.sh
```

Skrypt otwiera nową kartę w przeglądarce. Zostanie wyświetlony portal z interfejsem do tworzenia aplikacji zarządzanej.

Podaj wartości pól. Po zakończeniu zobaczysz wartości, które są przekazywane do szablonu, które można znaleźć w konsoli narzędzi deweloperskiej przeglądarki.

![Pokaż wartości](./media/test-createuidefinition/show-json.png)

Możesz użyć tych wartości jako pliku parametrów do testowania szablonu wdrożenia.

Jeśli portal zawiesza się na ekranie podsumowania, w sekcji danych wyjściowych może wystąpić usterka. Możesz na przykład odwołać się do kontrolki, która nie istnieje. Jeśli parametr w danych wyjściowych jest pusty, parametr może odwoływać się do właściwości, która nie istnieje. Na przykład odwołanie do kontrolki jest prawidłowe, ale odwołanie do właściwości jest nieprawidłowe.

## <a name="test-your-solution-files"></a>Testowanie plików rozwiązania

Po sprawdzeniu, czy interfejs portalu działa zgodnie z oczekiwaniami, należy sprawdzić, czy plik createUiDefinition jest prawidłowo zintegrowany z mainTemplate.jspliku. Możesz uruchomić test skryptu weryfikacji, aby przetestować zawartość plików rozwiązania, w tym plik createUiDefinition. Skrypt weryfikuje składnię JSON, sprawdza wyrażenia regularne w polach tekstowych i sprawdza, czy wartości wyjściowe interfejsu portalu są zgodne z parametrami szablonu. Aby uzyskać informacje na temat uruchamiania tego skryptu, zobacz Run static validation checks for templates (Uruchamianie [statycznych testów walidacji szablonów).](https://aka.ms/arm-ttk)

## <a name="next-steps"></a>Następne kroki

Po zakończeniu sprawdzania interfejsu portalu dowiedz się więcej o tym, jak aplikacja [zarządzana platformy Azure jest dostępna w witrynie Marketplace.](../../marketplace/create-new-azure-apps-offer.md)

---
title: 'Szybki start: tworzenie strategii w portalu'
description: W tym przewodniku Szybki start użyjemy Azure Blueprints tworzenia, definiowania i wdrażania artefaktów za pośrednictwem Azure Portal.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 94555b1b73fdac7368c9ac96fa682fb09afc83eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538961"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Szybki start: definiowanie i przypisywanie strategii w portalu

Gdy dowiesz się, jak tworzyć i przypisywać strategie, możesz zdefiniować typowe wzorce tworzenia konfiguracji wielokrotnego użytku i szybko wdrażalnych na podstawie szablonów usługi Azure Resource Manager (szablonów ARM), zasad, zabezpieczeń i innych. Z tego samouczka dowiesz się, jak używać Azure Blueprints do wykonywania niektórych typowych zadań związanych z tworzeniem, publikowaniem i przypisywaniem strategii w organizacji. Do zadań tych należą:

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. W tym przykładzie utworzysz nową strategia o nazwie **MyBlueprint,** aby skonfigurować przypisania ról i zasad dla subskrypcji. Następnie dodaj nową grupę zasobów i utwórz szablon Resource Manager przypisanie roli w nowej grupie zasobów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz **pozycję Definicje** strategii ze strony po lewej stronie i wybierz **przycisk + Utwórz plan** w górnej części strony.

   Możesz też wybrać **pozycję Utwórz** na stronie **Wprowadzenie,** aby przejść bezpośrednio do tworzenia strategii.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Zrzut ekranu przedstawiający przycisk &quot;Utwórz strategii&quot; na stronie Definicje strategii." border="false":::

1. Wybierz **pozycję Rozpocznij od pustej** strategii na karcie w górnej części listy wbudowanych strategii.

1. Podaj nazwę **strategii,** taką jak **MyBlueprint.** (Użyj maksymalnie 48 liter i cyfr, ale bez spacji ani znaków specjalnych). Na **razie pozostaw** pole Opis strategii puste.

1. W polu **Lokalizacja definicji** wybierz wielokropek po [](../management-groups/overview.md) prawej stronie, wybierz grupę zarządzania lub subskrypcję, w której chcesz zapisać plan, a następnie wybierz **pozycję Wybierz**.

1. Sprawdź, czy informacje są poprawne. Pól **Nazwa strategii** i Lokalizacja **definicji** nie można później zmienić. Następnie wybierz **pozycję Dalej: Artefakty** w dolnej części strony lub **kartę Artefakty** w górnej części strony.

1. Dodaj przypisanie roli na poziomie subskrypcji:

   1. Wybierz wiersz **+ Dodaj artefakt** w obszarze **Subskrypcja**. Po **prawej stronie** przeglądarki zostanie otwarte okno Dodawanie artefaktu.

   1. Wybierz **pozycję Przypisanie roli** dla opcji Typ **artefaktu.**

   1. W **obszarze Rola** wybierz pozycję **Współautor.** Pozostaw **pole wyboru Dodaj użytkownika, aplikację** lub grupę z polem wyboru wskazującym parametr dynamiczny.

   1. Wybierz **pozycję Dodaj,** aby dodać ten artefakt do strategii.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Zrzut ekranu przedstawiający opcje artefaktu przypisania roli służące do dodawania do definicji strategii." border="false":::

   > [!NOTE]
   > Większość artefaktów obsługuje parametry. Parametr, który jest przypisywany do wartości podczas tworzenia strategii, jest _parametrem statycznym_. Jeśli parametr jest przypisywany podczas przypisywania strategii, jest to parametr _dynamiczny_. Aby uzyskać więcej informacji, zobacz [Parametry strategii](./concepts/parameters.md).

1. Dodaj przypisanie zasad na poziomie subskrypcji:

   1. Wybierz wiersz **+ Dodaj artefakt** w obszarze artefaktu przypisania roli.

   1. Wybierz **pozycję Przypisanie zasad** dla opcji Typ **artefaktu.**

   1. Zmień **typ** na **Wbudowany**. W **polu Wyszukaj** wprowadź **tag**.

   1. Zmień fokus poza **obszarem** Wyszukiwanie, aby można było odfiltrować dane. Wybierz **pozycję Dołącz tag i jego wartość domyślną do grup zasobów.**

   1. Wybierz **pozycję Dodaj,** aby dodać ten artefakt do strategii.

1. Wybierz wiersz przypisania zasad Dołącz tag i **jego wartość domyślną do grup zasobów.**

1. Zostanie otwarte okno, w którym można podać parametry do artefaktu jako część definicji strategii i ustawić parametry dla wszystkich przypisań (parametry statyczne) na podstawie tej strategii zamiast ustawiania ich podczas przypisywania (parametry dynamiczne). W tym przykładzie użyto parametrów dynamicznych podczas przypisywania strategii, więc pozostaw wartości domyślne i wybierz pozycję **Anuluj.**

1. Dodaj grupę zasobów na poziomie subskrypcji:

   1. Wybierz wiersz **+ Dodaj artefakt** w obszarze **Subskrypcja**.

   1. Wybierz **pozycję Grupa zasobów dla** opcji Typ **artefaktu.**

   1. Pozostaw **pola Nazwa wyświetlana** artefaktu, Nazwa grupy zasobów i Lokalizacja puste, ale upewnij się, że pole wyboru jest zaznaczone dla każdej właściwości parametru w celu ustawienia ich parametrów dynamicznych.  

   1. Wybierz **pozycję Dodaj,** aby dodać ten artefakt do strategii.

1. Dodaj szablon w grupie zasobów:

   1. Wybierz wiersz **+ Dodaj artefakt** pod **wpisem ResourceGroup.**

   1. Wybierz **Azure Resource Manager w polu** Typ **artefaktu,** ustaw wartość Nazwa **wyświetlana** artefaktu na **StorageAccount** i **pozostaw pole Opis** puste.

   1. Na karcie **Szablon** w oknie edytora wklej następujący szablon usługi ARM. Po wklejeniu szablonu wybierz **kartę Parametry** i zwróć uwagę, że parametry szablonu **storageAccountType** i **lokalizacja** zostały wykryte. Każdy parametr został automatycznie wykryty i wypełniony, ale skonfigurowany jako parametr dynamiczny.

      > [!IMPORTANT]
      > Jeśli importujesz szablon, upewnij się, że plik jest tylko w formacie JSON i nie zawiera kodu HTML. Po wskazaniu adresu URL w usłudze GitHub upewnij się, że wybrano opcję **RAW,** aby pobrać czysty plik JSON, a nie ten, który jest opakowany za pomocą kodu HTML do wyświetlenia w usłudze GitHub. Jeśli zaimportowany szablon nie jest czystym plikiem JSON, wystąpi błąd.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. Wyczyść pole **wyboru storageAccountType** i zwróć uwagę, że lista rozwijana zawiera tylko wartości zawarte w szablonie usługi ARM w obszarze **allowedValues**. Zaznacz pole, aby ustawić je z powrotem na parametr dynamiczny.

   1. Wybierz **pozycję Dodaj,** aby dodać ten artefakt do strategii.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Zrzut ekranu przedstawiający Resource Manager artefaktu szablonu na potrzeby dodawania do definicji strategii." border="false":::

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Zwróć uwagę, że każdy artefakt **_ma x_ z _y_ parametrów wypełnionych** w **kolumnie** Parametry. Parametry dynamiczne są ustawiane każdorazowo podczas przypisywania strategii.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Zrzut ekranu przedstawiający ukończoną definicję strategii z każdym typem artefaktu." border="false":::

1. Teraz, po dodaniu wszystkich zaplanowanych artefaktów, wybierz pozycję **Zapisz** roboczą w dolnej części strony.

## <a name="edit-a-blueprint"></a>Edytowanie strategii

W [polu Tworzenie strategii](#create-a-blueprint)nie podasz opisu ani nie dodasz przypisania roli do nowej grupy zasobów. Aby rozwiązać oba te problemy, należy wykonać następujące czynności:

1. Wybierz **pozycję Definicje** strategii na stronie po lewej stronie.

1. Na liście strategii kliknij prawym przyciskiem myszy tę, która została wcześniej utworzona, a następnie wybierz pozycję **Edytuj strategie.**

1. W polu **Opis strategii** podaj informacje o strategii i artefaktach, które ją tworzą. W tym przypadku wprowadź wartość na przykład: Ta strategia ustawia zasady tagu i przypisanie roli w subskrypcji, tworzy grupę ResourceGroup i wdraża szablon zasobu oraz przypisanie roli do tej **grupy Zasobów.**

1. Wybierz **pozycję Dalej: Artefakty** w dolnej części strony lub **kartę Artefakty** w górnej części strony.

1. Dodaj przypisanie roli w grupie zasobów:

   1. Wybierz wiersz **+ Dodaj artefakt** bezpośrednio pod **wpisem ResourceGroup.**

   1. Wybierz **pozycję Przypisanie roli** dla opcji Typ **artefaktu.**

   1. W **obszarze Rola** wybierz pozycję **Właściciel** i wyczyść pole wyboru w obszarze **Dodaj użytkownika, aplikację lub grupę.**

   1. Wyszukaj i wybierz użytkownika, aplikację lub grupę do dodania. Ten artefakt używa tego samego parametru statycznego ustawionego w każdym przypisaniu tej strategii.

   1. Wybierz **pozycję Dodaj,** aby dodać ten artefakt do strategii.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Zrzut ekranu przedstawiający opcje artefaktu przypisania drugiej roli służące do dodawania do definicji strategii." border="false":::

1. Ukończona strategia powinna wyglądać podobnie do poniższej. Zwróć uwagę, że nowo dodane przypisanie roli **zawiera wypełnione 1 z 1 parametrów**. Oznacza to, że jest to parametr statyczny.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Zrzut ekranu przedstawiający drugą ukończoną definicję strategii z dodatkowym artefaktem przypisania roli." border="false":::

1. Wybierz **pozycję Zapisz roboczą** teraz, gdy została zaktualizowana.

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu wszystkich zaplanowanych artefaktów do strategii czas na jej opublikowanie.
Publikowanie sprawia, że strategia jest dostępna do przypisania do subskrypcji.

1. Wybierz **pozycję Definicje** strategii na stronie po lewej stronie.

1. Na liście strategii kliknij prawym przyciskiem myszy tę, która została wcześniej utworzona, a następnie wybierz pozycję **Opublikuj plan**.

1. W okienku, które  zostanie otwarte, podaj wersję (litery, cyfry i łączniki o maksymalnej długości 20 znaków), taką jak **v1.** Opcjonalnie wprowadź tekst w polu **Uwagi dotyczące zmian,** na przykład **Najpierw opublikuj**.

1. Wybierz **pozycję Publikuj** w dolnej części strony.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu strategii można ją przypisać do subskrypcji. Przypisz utworzoną przez Ciebie strategia do jednej z subskrypcji w hierarchii grupy zarządzania. Jeśli strategia została zapisana w subskrypcji, można ją przypisać tylko do tej subskrypcji.

1. Wybierz **pozycję Definicje** strategii na stronie po lewej stronie.

1. Na liście strategii kliknij prawym przyciskiem myszy tę, która została wcześniej utworzona (lub wybierz wielokropek), a następnie wybierz pozycję **Przypisz strategia**.

1. Na **stronie Przypisywanie** strategii z **listy rozwijanej** Subskrypcja wybierz subskrypcje, w których chcesz wdrożyć tę strategia.

   Jeśli w portalu [Azure Billing](../../cost-management-billing/index.yml) są dostępne obsługiwane oferty dotyczące wersji Enterprise, zostanie uaktywniony link **Utwórz nową** w obszarze **Subskrypcja**. Wykonaj następujące kroki:

   1. Wybierz link **Utwórz nową**, aby utworzyć nową subskrypcję, zamiast wybierać już istniejące.

   1. W polu **Nazwa wyświetlana** podaj nazwę dla nowej subskrypcji.

   1. Wybierz dostępną **ofertę** z listy rozwijanej.

   1. Użyj wielokropka, aby wybrać [grupę zarządzania,](../management-groups/overview.md) dla których subskrypcja będzie podrzędna.

   1. Wybierz pozycję **Utwórz** w dolnej części strony.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Zrzut ekranu przedstawiający okno Tworzenie subskrypcji i opcje dla nowej subskrypcji." border="false":::

      > [!IMPORTANT]
      > Nowa subskrypcja zostanie utworzona natychmiast po wybraniu opcji **Utwórz.**

   > [!NOTE]
   > Przypisanie jest tworzone dla każdej wybranej subskrypcji. Możesz później wprowadzić zmiany w jednym przypisaniu subskrypcji bez wymusania zmian w pozostałych wybranych subskrypcjach.

1. W **polach Nazwa** przypisania podaj unikatową nazwę tego przypisania.

1. W **obszarze** Lokalizacja wybierz region dla obiektu wdrożenia tożsamości zarządzanej i subskrypcji, w którym ma zostać utworzony. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Z listy **rozwijanej Wersja definicji strategii** pozostaw zaznaczenie pozycji **Opublikowane** wersje we **wpisie v1.** (Wartość domyślna to najnowsza opublikowana wersja).

1. Dla opcji **Blokowanie przypisania** pozostaw wartość domyślną **Nie blokuj**. Aby uzyskać więcej informacji, zobacz [Blokowanie zasobów usługi Blueprints.](./concepts/resource-locking.md)

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Zrzut ekranu przedstawiający opcje Blokowanie przypisania i tożsamości zarządzanej dla przypisania strategii." border="false":::

1. W **obszarze Tożsamość zarządzana** pozostaw wartość domyślną **Przypisana przez system**.

1. Dla przypisania roli na poziomie subskrypcji **[Grupa użytkowników lub nazwa aplikacji]: Współautor** wyszukaj i wybierz użytkownika, aplikację lub grupę.

1. W przypadku przypisania zasad na poziomie subskrypcji ustaw **wartość Nazwa tagu** na **CostCenter,** a **wartość tagu** na **ContosoIT**.

1. W **przypadku grupy** zasobów **z** listy rozwijanej podaj nazwę **StorageAccount** i lokalizację Wschodnie usa **2.** 

   > [!NOTE]
   > Dla każdego artefaktu dodanego w grupie zasobów podczas definiowania strategii ten artefakt jest wcięty w celu wyrównania go z grupą zasobów lub obiektem, za pomocą których zostanie wdrożony.
   > Artefakty, które nie mają parametrów lub nie mają parametrów do zdefiniowanych podczas przypisywania, są wyświetlane tylko dla informacji kontekstowych.

1. Na szablonie usługi ARM **StorageAccount** wybierz **Standard_GRS** **parametru storageAccountType.**

1. Przeczytaj pole informacji w dolnej części strony, a następnie wybierz pozycję **Przypisz**.

## <a name="track-deployment-of-a-blueprint"></a>Śledzenie wdrażania strategii

Po przypisaniu strategii do co najmniej jednej subskrypcji dzieją się dwie rzeczy:

- Strategia jest dodawana do **strony Przypisane strategie dla** każdej subskrypcji.
- Rozpocznie się proces wdrażania wszystkich artefaktów zdefiniowanych w strategii.

Teraz, gdy strategia została przypisana do subskrypcji, sprawdź postęp wdrażania:

1. Wybierz **pozycję Przypisane strategie** na stronie po lewej stronie.

1. Na liście strategii kliknij prawym przyciskiem myszy tę, która została wcześniej przypisana, a następnie wybierz pozycję **Wyświetl szczegóły przypisania.**

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Zrzut ekranu przedstawiający menu kontekstowe przypisania strategii z wybraną opcją &quot;Wyświetl szczegóły przypisania&quot;." border="false":::

1. Na stronie **Przypisywanie strategii** sprawdź, czy wszystkie artefakty zostały pomyślnie wdrożone i czy podczas wdrażania nie wystąpiły żadne błędy. Jeśli wystąpiły błędy, zobacz [Troubleshooting blueprints (Rozwiązywanie](./troubleshoot/general.md) problemów ze strategiami), aby określić, co poszło nie tak.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Jeśli przypisanie strategii nie jest już potrzebne, usuń je z subskrypcji. Strategia mogła zostać zastąpiona nowszą strategieą ze zaktualizowanymi wzorcami, zasadami i projektami. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie strategii, wykonaj następujące kroki:

1. Wybierz **pozycję Przypisane strategie** na stronie po lewej stronie.

1. Na liście strategii wybierz strategii, które chcesz usunąć przypisanie. Następnie wybierz **przycisk Cokompuj przypisanie strategii** w górnej części strony.

1. Przeczytaj komunikat potwierdzenia, a następnie wybierz przycisk **OK.**

### <a name="delete-a-blueprint"></a>Usuwanie strategii

1. Wybierz **pozycję Definicje** strategii na stronie po lewej stronie.

1. Kliknij prawym przyciskiem myszy plan, który chcesz usunąć, a następnie wybierz polecenie **Usuń strategie.** Następnie wybierz **pozycję Tak** w oknie dialogowym potwierdzenia.

> [!NOTE]
> Usunięcie strategii w tej metodzie powoduje również usunięcie wszystkich opublikowanych wersji wybranej strategii.
> Aby usunąć pojedynczą wersję, otwórz plan, wybierz kartę **Opublikowane** wersje, wybierz wersję, którą chcesz usunąć, a następnie wybierz pozycję **Usuń tę wersję.** Ponadto nie można usunąć strategii, dopóki nie usunięto całego przypisania strategii tej definicji strategii.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono, przypisano i usunięto plan za pomocą Azure Portal. Aby dowiedzieć się więcej na Azure Blueprints, przejdź do artykułu o cyklu życia strategii.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia strategii](./concepts/lifecycle.md)

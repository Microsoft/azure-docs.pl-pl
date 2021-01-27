---
title: 'Samouczek: Ochrona nowych zasobów przy użyciu blokad'
description: W tym samouczku użyto opcji blokowania zasobów usługi Azure Plans tylko do odczytu i nie usuwaj, aby chronić nowo wdrożone zasoby.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: c671d641982ba833b54586c1b33979a97747396b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915410"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Samouczek: Ochrona nowych zasobów przy użyciu blokad zasobów usługi Azure Plans

Dzięki [zablokowaniu zasobów](../concepts/resource-locking.md)platformy Azure można chronić nowo wdrożone zasoby przed zmianą, nawet przez konto z rolą _właściciela_ . Tę ochronę można dodać w definicjach planów zasobów utworzonych przez artefakt szablonu Azure Resource Manager (szablon ARM).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> - Tworzenie definicji strategii
> - Oznacz swoją definicję planu jako **opublikowaną**
> - Przypisywanie definicji planu do istniejącej subskrypcji
> - Inspekcja nowej grupy zasobów
> - Cofnij przypisanie planu w celu usunięcia blokad

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-a-blueprint-definition"></a>Tworzenie definicji strategii

Najpierw Utwórz definicję planu.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **pierwsze kroki** z lewej strony wybierz pozycję **Utwórz** w obszarze **Utwórz plan**.

1. Znajdź **pustą** przykładową strategię planów w górnej części strony. Wybierz pozycję **Rozpocznij od pustego** planu.

1. Wprowadź te informacje na karcie **podstawowe** :

   - **Nazwa** planu: Podaj nazwę kopii przykładu strategii. W tym samouczku użyjemy nazwy **"locked-storageaccount"**.
   - **Opis** planu: Dodaj opis definicji planu. Służy **do testowania blokowania zasobów planu dla wdrożonych zasobów**.
   - **Lokalizacja definicji**: wybierz przycisk wielokropka (...), a następnie wybierz grupę zarządzania lub subskrypcję, w której ma zostać zapisana definicja planu.

1. Wybierz kartę **artefakty** w górnej części strony lub wybierz pozycję **Dalej: artefakty** w dolnej części strony.

1. Dodaj grupę zasobów na poziomie subskrypcji:
   1. Wybierz pozycję **Dodaj wiersz artefaktu** w obszarze **subskrypcja**.
   1. Wybierz pozycję **Grupa zasobów** w obszarze **Typ artefaktu**.
   1. Ustaw **nazwę wyświetlaną artefaktu** na **RGtoLock**.
   1. Pozostaw puste pola **Nazwa grupy zasobów** i **Lokalizacja** , ale upewnij się, że pole wyboru jest zaznaczone dla każdej właściwości, aby uczynić je **parametrami dynamicznymi**.
   1. Wybierz pozycję **Dodaj** , aby dodać artefakt do planu.

1. Dodaj szablon do grupy zasobów:
   1. Wybierz **Dodaj wiersz artefaktu** w ramach wpisu **RGtoLock** .
   1. Wybierz **szablon Azure Resource Manager** w obszarze **Typ artefaktu**, ustaw **nazwę wyświetlaną artefaktu** na **StorageAccount** i pozostaw pole **Opis** puste.
   1. Na karcie **szablon** wklej następujący szablon ARM do pola Edytor. Po wklejeniu szablonu wybierz pozycję **Dodaj** , aby dodać artefakt do planu.

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
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
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

1. Wybierz pozycję **Zapisz wersję roboczą** w dolnej części strony.

Ten krok powoduje utworzenie definicji planu w wybranej grupie zarządzania lub subskrypcji.

Po **pomyślnym zakończeniu zapisywania definicji** planu zostanie wyświetlone powiadomienie portalu przejdź do następnego kroku.

## <a name="publish-the-blueprint-definition"></a>Publikowanie definicji strategii

Twoja definicja planu została teraz utworzona w Twoim środowisku. Jest on tworzony w trybie **wersji roboczej** i musi być opublikowany, aby można go było przypisać i wdrożyć.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć definicję planu **storageaccount** , a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. W nowym okienku po prawej stronie wprowadź **1,0** jako **wersję**. Ta właściwość jest przydatna, jeśli wprowadzisz zmiany później. Wprowadź **Informacje o zmianach**, takie jak **Pierwsza wersja opublikowana do blokowania zasobów wdrożonych w** planie. Następnie wybierz pozycję **Publikuj** w dolnej części strony.

Ten krok umożliwia przypisanie planu do subskrypcji. Po opublikowaniu definicji planu można nadal wprowadzać zmiany. W przypadku wprowadzenia zmian należy opublikować definicję z nową wartością wersji, aby śledzić różnice między wersjami tej samej definicji planu.

Po **pomyślnym** wyświetleniu powiadomienia portalu publikowanie definicji strategii przejdź do następnego kroku.

## <a name="assign-the-blueprint-definition"></a>Przypisywanie definicji strategii

Po opublikowaniu definicji planu można przypisać ją do subskrypcji w grupie zarządzania, w której zapisano ją. W tym kroku podajesz parametry, aby każde wdrożenie definicji strategii było unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć definicję planu **storageaccount** , a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - **Podstawy**

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, w której zapisano definicję planu. W przypadku wybrania więcej niż jednej subskrypcji zostanie utworzone przypisanie dla każdej subskrypcji przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniana na podstawie nazwy definicji planu. To przypisanie reprezentuje zablokowanie nowej grupy zasobów, dlatego należy zmienić nazwę przypisania na **przypisanie-storageaccount-TestingBPLocks**.
     - **Lokalizacja**: Wybierz region, w którym chcesz utworzyć zarządzaną tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Na potrzeby tego samouczka wybierz pozycję **Wschodnie stany USA 2**.
     - **Wersja definicji** planu: Wybierz opublikowaną wersję **1,0** definicji planu.

   - **Przypisanie blokady**

     Wybierz tryb blokowania **tylko do odczytu** . Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - **Tożsamość zarządzana**

     Użyj opcji domyślnej: **przypisany system**. Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Parametry artefaktu**

     Parametry zdefiniowane w tej sekcji dotyczą artefaktu, w którym są zdefiniowane. Parametry te są [parametrami dynamicznymi](../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Dla każdego artefaktu ustaw wartość parametru na co widać w kolumnie **wartość** .

     |Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Wartość|Opis|
     |-|-|-|-|-|
     |Grupa zasobów RGtoLock|Grupa zasobów|Nazwa|TestingBPLocks|Określa nazwę nowej grupy zasobów, do której mają zostać zastosowane blokady planu.|
     |Grupa zasobów RGtoLock|Grupa zasobów|Lokalizacja|Zachodnie stany USA 2|Określa lokalizację nowej grupy zasobów, do której mają zostać zastosowane blokady planu.|
     |StorageAccount|Szablon usługi Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|Jednostka SKU magazynu. Wartość domyślna to _Standard_LRS_.|

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony.

W tym kroku wdrożono określone zasoby i skonfiguruje wybrane **przypisanie blokady**. Zastosowanie blokad planu może potrwać do 30 minut.

Po wyświetleniu powiadomienia portalu **przypisywanie definicji planu powiodło** się przejdź do następnego kroku.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspekcja zasobów wdrożonych przez przypisanie

Przypisanie tworzy _TestingBPLocks_ grupy zasobów i konto magazynu wdrożone przez artefakt szablonu ARM. Nowa grupa zasobów i wybrany stan blokady są wyświetlane na stronie szczegółów przypisania.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **przypisane plany** po lewej stronie. Użyj filtrów, aby znaleźć przypisanie do planu **storageaccount-TestingBPLocks przypisania** , a następnie wybierz je.

   Na tej stronie można zobaczyć, że przypisanie zakończyło się pomyślnie i że zasoby zostały wdrożone przy użyciu nowego stanu blokady planu. Jeśli przypisanie zostało zaktualizowane, lista rozwijana **operacja przypisywania** zawiera szczegółowe informacje dotyczące wdrożenia każdej wersji definicji. Możesz wybrać grupę zasobów, aby otworzyć stronę właściwości.

1. Wybierz grupę zasobów **TestingBPLocks** .

1. Wybierz stronę **Kontrola dostępu (IAM)** po lewej stronie. Następnie wybierz kartę **przypisania ról** .

   W tym miejscu widzimy, że przypisanie planu _storageaccount-TestingBPLocks_ jest przypisane do roli _właściciela_ . Ta rola ma tę rolę, ponieważ ta rola została użyta do wdrożenia i zablokowania grupy zasobów.

1. Wybierz kartę **Odmów przypisań** .

   Przypisanie planu spowodowało utworzenie [przypisania odmowy](../../../role-based-access-control/deny-assignments.md) w wdrożonej grupie zasobów w celu wymuszenia trybu blokowania **tylko do odczytu** . Przypisanie Odmów uniemożliwia komuś z odpowiednimi prawami na karcie **przypisania roli** w celu podjęcia określonych działań. Przypisanie Odmów ma wpływ na _wszystkich podmiotów zabezpieczeń_.

   Aby uzyskać informacje na temat wykluczania podmiotu zabezpieczeń z przypisania odmowy, zobacz temat [plany blokowanie zasobów](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Zaznacz pozycję Odmów przypisania, a następnie wybierz stronę **odmowa uprawnień** po lewej stronie.

   Przypisanie Odmów uniemożliwia wszystkie operacje z konfiguracją **\* akcji *_ i _*** , ale umożliwia dostęp do odczytu przez wykluczenie **\* /Read** **za pośrednictwem** nostąpes.

1. Na stronie nawigacyjnej Azure Portal wybierz pozycję **Kontrola dostępu TestingBPLocks (IAM)**. Następnie wybierz stronę **Przegląd** po lewej stronie, a następnie przycisk **Usuń grupę zasobów** . Wprowadź nazwę **TestingBPLocks** , aby potwierdzić usunięcie, a następnie wybierz pozycję **Usuń** w dolnej części okienka.

   **Nie powiodło się usunięcie TestingBPLocks grupy zasobów** powiadomienia portalu. Ten błąd oznacza, że mimo że Twoje konto ma uprawnienia do usuwania grupy zasobów, odmowa dostępu przez przypisanie planu. Należy pamiętać, że podczas przypisywania strategii zaznaczono tryb blokowania **tylko do odczytu** . Blokada strategii uniemożliwia konto z uprawnieniami, nawet _właściciel_, od usunięcia zasobu. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

Te kroki pokazują, że nasze wdrożone zasoby są teraz chronione za pomocą blokad planu, które uniemożliwiają niepożądane usuwanie, nawet z konta, które ma uprawnienia do usuwania zasobów.

## <a name="unassign-the-blueprint"></a>Cofnij przypisanie planu

Ostatnim krokiem jest usunięcie przypisania definicji planu. Usunięcie przypisania nie powoduje usunięcia skojarzonych artefaktów.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Wybierz stronę **przypisane plany** po lewej stronie. Użyj filtrów, aby znaleźć przypisanie do planu **storageaccount-TestingBPLocks przypisania** , a następnie wybierz je.

1. Wybierz pozycję Anuluj **przypisanie** planu u góry strony. Zapoznaj się z ostrzeżeniem w oknie dialogowym potwierdzenia, a następnie wybierz przycisk **OK**.

   Po usunięciu przypisania planu blokowane są również blokady. Zasoby mogą być ponownie usuwane przez konto z odpowiednimi uprawnieniami.

1. Wybierz pozycję **grupy zasobów** z menu platformy Azure, a następnie wybierz pozycję **TestingBPLocks**.

1. Wybierz stronę **Kontrola dostępu (IAM)** po lewej stronie, a następnie wybierz kartę **przypisania ról** .

Zabezpieczenia dla grupy zasobów pokazują, że przypisanie planu nie ma już dostępu _właściciela_ .

Po wyświetleniu powiadomienia portalu **usuwanie przypisania strategii powiodło** się przejdź do następnego kroku.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem Usuń następujące zasoby:

- _TestingBPLocks_ grupy zasobów
- _Zablokowana definicja planu — storageaccount_

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się chronić nowe zasoby wdrożone przy użyciu planów platformy Azure. Aby dowiedzieć się więcej na temat planów platformy Azure, przejdź do artykułu dotyczącego cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](../concepts/lifecycle.md)
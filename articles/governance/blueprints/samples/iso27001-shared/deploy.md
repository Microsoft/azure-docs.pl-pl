---
title: Przykład wdrażania planu wspólnych usług ISO 27001
description: Wdróż kroki dla przykładu planu usług udostępnionych ISO 27001, w tym szczegóły parametru artefaktu strategii.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 2eb9ed2199b28ad7cb349e6088c3cc583d8f011d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577328"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Wdróż przykład strategii usług udostępnionych ISO 27001

Aby wdrożyć przykład strategii usług udostępnionych ISO 27001 dla planów platformy Azure, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź **ISO 27001: plan usługi udostępnionej** przykład w _innych próbkach_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowej strategii usług udostępnionych ISO 27001.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów, które składają się na przykład strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

## <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może ją przenieść z normy ISO 27001.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana w przykładowej strategii ISO 27001". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

## <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym **opublikowaniu** kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniona dla Ciebie na podstawie nazwy planu.
       Jeśli chcesz, możesz ją zmienić lub pozostawić.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji** planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Przypisanie blokady

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry strategii

     Parametry zdefiniowane w tej sekcji są używane przez wiele artefaktów w definicji strategii w celu zapewnienia spójności.

     - **Nazwa organizacji**: Podaj krótką nazwę organizacji. Ta właściwość jest używana głównie do określania nazw zasobów.
     - **Prefiks adresu podsieci usług udostępnionych**: Podaj wartość notacji CIDR dla sieci wspólnie wdrożonych zasobów.
     - **Lokalizacja usług udostępnionych**: określa lokalizację, w której są wdrażane artefakty. Nie wszystkie usługi są dostępne we wszystkich lokalizacjach. Artefakty wdrażające takie usługi zapewniają opcję parametru dla lokalizacji, w której ma zostać wdrożony artefakt.
     - **Dozwolona lokalizacja (zasady: plan inicjatywy dla ISO 27001)**: wartość, która wskazuje dozwolone lokalizacje grup zasobów i zasobów.
     - **Log Analytics obszar roboczy dla agentów maszyny wirtualnej (zasady: plan inicjatywy dla ISO 27001)**: Określa identyfikator zasobu obszaru roboczego. Ten parametr używa `concat` funkcji do konstruowania identyfikatora zasobu.

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Parametry te są [parametrami dynamicznymi](../../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

## <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|\[Wersja zapoznawcza \] : wdrażanie log Analytics agenta dla systemu Linux VM Scale Sets (VMSS)|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_.|
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_.|
|\[Wersja zapoznawcza \] : wdrażanie log Analytics agenta dla systemu Windows VM Scale Sets (VMSS)|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_.|
|\[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu|Obowiązkowe Wartość domyślna to _["Brak"]_.|
|Dozwolone typy zasobów|Przypisanie zasad|Dozwolone typy zasobów|Lista typów zasobów, które mogą zostać wdrożone. Ta lista składa się ze wszystkich typów zasobów wdrożonych w usługach udostępnionych.|
|Dozwolone jednostki SKU konta magazynu|Przypisanie zasad|Dozwolone jednostki SKU magazynu|Lista dzienników diagnostycznych — dozwolone jednostki SKU konta magazynu. Wartość domyślna to _["Standard_LRS"]_.|
|Dozwolone jednostki SKU maszyny wirtualnej|Przypisanie zasad|Lista jednostek SKU maszyn wirtualnych, które mogą zostać wdrożone. Wartość domyślna to _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Inicjatywa strategii dla ISO 27001|Przypisanie zasad|Typy zasobów do inspekcji dzienników diagnostycznych|Lista typów zasobów do inspekcji, jeśli nie włączono ustawienia dziennika diagnostycznego. Akceptowalne wartości można znaleźć Azure Monitor w obszarze [schematy dzienników diagnostycznych](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|Grupa zasobów Log Analytics|Grupa zasobów|Nazwa|**Zablokowany** — łączy **nazwę organizacji** z, `-sharedsvsc-log-rg` Aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Log Analytics|Grupa zasobów|Lokalizacja|**Zablokowany** — używa parametru strategii.|
|Szablon Log Analytics|Szablon usługi Resource Manager|Warstwa usług|Ustawia warstwę obszaru roboczego Log Analytics. Wartość domyślna to _PerNode_.|
|Szablon Log Analytics|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon Log Analytics|Szablon usługi Resource Manager|Lokalizacja|Region używany do tworzenia obszaru roboczego Log Analytics. Wartość domyślna to _zachodnie stany USA 2_.|
|Grupa zasobów sieciowych|Grupa zasobów|Nazwa|**Zablokowany** — łączy **nazwę organizacji** z, `-sharedsvcs-net-rg` Aby uczynić grupę zasobów unikatową.|
|Grupa zasobów sieciowych|Grupa zasobów|Lokalizacja|**Zablokowany** — używa parametru strategii.|
|Szablon zapory platformy Azure|Szablon usługi Resource Manager|Prywatny adres IP zapory platformy Azure|Konfiguruje prywatny adres IP [zapory platformy Azure](../../../../firewall/overview.md). Ta wartość jest również używana jako domyślna tabela tras w podsieci usług udostępnionych. Musi być częścią notacji CIDR zdefiniowanej w **prefiksie adresu podsieci zapory platformy Azure**. Wartość domyślna to _10.0.4.4_.|
|Szablon zapory platformy Azure|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon sieciowej grupy zabezpieczeń|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu Virtual Network|Notacja CIDR dla sieci wirtualnej. Wartość domyślna to _10.0.0.0/16_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Włącz Virtual Network ochronę DDoS|Konfiguruje ochronę DDoS dla sieci wirtualnej. Wartość domyślna to _true_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci usług udostępnionych|Notacja CIDR dla podsieci usług udostępnionych. Wartość domyślna to _10.0.0.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci DMZ|Notacja CIDR dla podsieci DMZ. Wartość domyślna to _10.0.1.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci Application Gateway|Notacja CIDR dla podsieci usługi Application Gateway. Wartość domyślna to _10.0.2.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci bramy Virtual Network|Notacja CIDR dla podsieci bramy sieci wirtualnej. Wartość domyślna to _10.0.3.0/24_.|
|Szablon tabeli Virtual Network i trasy|Szablon usługi Resource Manager|Prefiks adresu podsieci zapory platformy Azure|Notacja CIDR dla podsieci [zapory platformy Azure](../../../../firewall/overview.md) . Powinien zawierać parametr **prywatnego adresu IP zapory platformy Azure** .|
|Grupa zasobów Key Vault|Grupa zasobów|Nazwa|**Zablokowany** — łączy **nazwę organizacji** z, `-sharedsvcs-kv-rg` Aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Key Vault|Grupa zasobów|Lokalizacja|**Zablokowany** — używa parametru strategii.|
|Szablon Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera przesiadkowego|Nazwa użytkownika dla serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie serwera przesiadkowego**. Wartość domyślna to _JB-admin-User_.|
|Szablon Key Vault|Szablon usługi Resource Manager|Serwera przesiadkowego administratora klucza SSH lub hasła|Klucz lub hasło dla konta w serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie serwera przesiadkowego**. Brak wartości domyślnej i nie można jej pozostawić pustej.|
|Szablon Key Vault|Szablon usługi Resource Manager|Nazwa użytkownika administratora domeny|Nazwa użytkownika służąca do uzyskiwania dostępu do Active Directory VM i dołączania do domeny innych maszyn wirtualnych. Musi być zgodna z wartością właściwości **użytkownik administrator domeny** w **szablonie Active Directory Domain Services**. Wartość domyślna to _domena-administrator-użytkownik_.|
|Szablon Key Vault|Szablon usługi Resource Manager|Hasło administratora domeny|Hasło administratora domeny. Brak wartości domyślnej i nie można jej pozostawić pustej.|
|Szablon Key Vault|Szablon usługi Resource Manager|Identyfikator obiektu usługi AAD|Identyfikator obiektu usługi AAD konta, które wymaga dostępu do wystąpienia Key Vault. Brak wartości domyślnej i nie można jej pozostawić pustej. Aby zlokalizować tę wartość z Azure Portal, Wyszukaj i wybierz pozycję "Użytkownicy" w obszarze _usługi_. Użyj pola _Nazwa_ , aby odfiltrować nazwę konta, a następnie wybierz to konto. Na stronie _profil użytkownika_ wybierz ikonę "kliknij, aby skopiować" obok _identyfikatora obiektu_.  |
|Szablon Key Vault|Szablon usługi Resource Manager|Przechowywanie dzienników w dniach|Przechowywanie danych w dniach. Wartość domyślna to _365_.|
|Szablon Key Vault|Szablon usługi Resource Manager|Jednostka SKU Key Vault|Określa jednostkę SKU utworzonego Key Vault. Wartość domyślna to _Premium_.|
|Grupa zasobów serwera przesiadkowego|Grupa zasobów|Nazwa|**Zablokowany** — łączy **nazwę organizacji** z, `-sharedsvcs-jb-rg` Aby uczynić grupę zasobów unikatową.|
|Grupa zasobów serwera przesiadkowego|Grupa zasobów|Lokalizacja|**Zablokowany** — używa parametru strategii.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Nazwa użytkownika administratora serwera przesiadkowego|Nazwa użytkownika używana do uzyskiwania dostępu do maszyn wirtualnych serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie Key Vault**. Wartość domyślna to _JB-admin-User_.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Hasło administratora serwera przesiadkowego (identyfikator zasobu Key Vault)|Identyfikator zasobu Key Vault. Użyj "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" i Zastąp ciąg `{subscriptionId}` identyfikatorem subskrypcji i `{orgName}` parametrem " **Nazwa organizacji** ".|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|Hasło administratora serwera przesiadkowego (Key Vault nazwa klucza tajnego)|Nazwa użytkownika administratora serwera przesiadkowego. Musi być zgodna wartość właściwości **szablonu Key Vault** **serwera przesiadkowego nazwa użytkownika administratora**.|
|Szablon serwera przesiadkowego|Szablon usługi Resource Manager|System operacyjny serwera przesiadkowego|Określa system operacyjny maszyny wirtualnej serwera przesiadkowego. Wartość domyślna to _Windows_.|
|Grupa zasobów Active Directory Domain Services|Grupa zasobów|Nazwa|**Zablokowany** — łączy **nazwę organizacji** z, `-sharedsvcs-adds-rg` Aby uczynić grupę zasobów unikatową.|
|Grupa zasobów Active Directory Domain Services|Grupa zasobów|Lokalizacja|**Zablokowany** — używa parametru strategii.|
|Szablon Active Directory Domain Services|Szablon usługi Resource Manager|Nazwa użytkownika administratora domeny|Nazwa użytkownika dla dodaje serwera przesiadkowego. Musi być zgodna z tą samą wartością właściwości w **szablonie Key Vault**. Wartość domyślna to _Dodawanie-administratora-użytkownika_.|
|Szablon Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny (identyfikator zasobu Key Vault)|Identyfikator zasobu Key Vault. Użyj "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" i Zastąp ciąg `{subscriptionId}` identyfikatorem subskrypcji i `{orgName}` parametrem " **Nazwa organizacji** ".|
|Szablon Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny (Key Vault nazwa klucza tajnego)|Nazwa użytkownika administratora domeny. Musi być zgodna wartość właściwości **szablonu Key Vault** **Nazwa użytkownika Administrator domeny**.|
|Szablon Active Directory Domain Services|Szablon usługi Resource Manager|Nazwa domeny|Nazwa Active Directory utworzonego przez przykład. Wartość domyślna to _contoso.com_.|
|Szablon Active Directory Domain Services|Szablon usługi Resource Manager|Administrator domeny|Nazwa użytkownika dla konta administratora i do dołączania urządzeń do domeny usługi AD. Musi być zgodna z wartością właściwości **Nazwa użytkownika administratora usługi AD** w **szablonie Key Vault**. Wartość domyślna to _domena-administrator-użytkownik_.|
|Szablon Active Directory Domain Services|Szablon usługi Resource Manager|Hasło administratora domeny|Ustaw Key Vault szczegóły dotyczące przechowywania hasła. Brak wartości domyślnej i nie można jej pozostawić pustej.|

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu kroków w celu wdrożenia przykładowej usługi wspólnych informacji ISO 27001 zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o architekturze i mapowaniu formantów:

> [!div class="nextstepaction"]
> [Plan usług udostępnionych ISO 27001 — Omówienie](./index.md) 
>  [Mapowanie kontroli dla usług udostępnionych ISO 27001](./control-mapping.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

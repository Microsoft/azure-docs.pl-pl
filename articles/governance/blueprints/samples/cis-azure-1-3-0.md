---
title: Przykłady usługi CIS Microsoft Azure 1.3.0 plan testu porównawczego
description: Omówienie przykładowych 1.3.0 planów testów porównawczych usługi Microsoft Azure CIS. Ten przykład strategii pomaga klientom ocenić określone mechanizmy kontroli.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: 7030b4e9e9ddd3d5bbf1a5a7deaf1e1aa241b38e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566000"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Przykłady usługi CIS Microsoft Azure 1.3.0 plan testu porównawczego

Przykładowa usługa CIS Microsoft Azure 1.3.0 plan testu porównawczego w wersji testowej jest oparta na [Azure Policyach](../../policy/overview.md) , które ułatwiają ocenę konkretnych Microsoft Azurech wykrytych w ramach testów porównawczych usługi CIS v 1.3.0. Ten plan ułatwia klientom wdrożenie podstawowego zestawu zasad dla dowolnej architektury wdrożonej przez platformę Azure, która musi implementować usługi CIS Microsoft Azure zalecenia dotyczące testów porównawczych 1.3.0.

## <a name="recommendation-mapping"></a>Mapowanie zaleceń

[Azure Policy mapowanie rekomendacji](../../policy/samples/cis-azure-1-3-0.md) zawiera szczegółowe informacje dotyczące definicji zasad uwzględnionych w ramach tego planu oraz sposób mapowania tych definicji zasad na **zalecenia** dotyczące usługi CIS Microsoft Azure testów porównawczych 1.3.0. Po przypisaniu do architektury zasoby są oceniane przez Azure Policy w przypadku braku zgodności z przypisanymi definicjami zasad. Aby uzyskać więcej informacji, zobacz [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Wdróż

Aby wdrożyć przykłady planów platformy Azure dla usługi CIS Microsoft Azure, można wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykłady usługi **CIS Microsoft Azure 1.3.0 plan testu porównawczego v** w _innych próbkach_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładowego planu porównawczego usługi CIS Microsoft Azure.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów uwzględnionych w przykładowej strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

### <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacje mogą nie zostać przesunięte z wyrównania z użyciem usług CIS Microsoft Azure przykłady testów porównawczych 1.3.0.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu" Przykłady planów testów usługi CIS Microsoft Azure ". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

### <a name="assign-the-sample-copy"></a>Przypisywanie kopii przykładu

Po pomyślnym **opublikowaniu** kopii przykładowej strategii można ją przypisać do subskrypcji w grupie zarządzania, w której został on zapisany. W tym kroku udostępniane są parametry, dzięki którym każde wdrożenie kopii przykładowej strategii będzie unikatowe.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. Wybierz pozycję **Przypisz strategię** w górnej części strony definicji strategii.

1. Podaj wartości parametrów dla przypisania strategii:

   - Podstawy

     - **Subskrypcje**: Wybierz co najmniej jedną subskrypcję znajdującą się w grupie zarządzania, do której została zapisana kopia przykładu planu. W przypadku wybrania więcej niż jednej subskrypcji dla każdej z nich zostanie utworzone przypisanie przy użyciu wprowadzonych parametrów.
     - **Nazwa przypisania**: nazwa jest wstępnie wypełniona dla Ciebie na podstawie nazwy planu.
       Jeśli chcesz, możesz ją zmienić lub pozostawić.
     - **Lokalizacja**: Wybierz region, w którym ma zostać utworzona zarządzana tożsamość. Usługa Azure Blueprint używa tej tożsamości zarządzanej do wdrażania wszystkich artefaktów w przypisanej strategii. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Wersja definicji** planu: Wybierz **opublikowaną** wersję kopii przykładowej strategii.

   - Przypisanie blokady

     Wybierz ustawienie blokowania strategii dla danego środowiska. Aby uzyskać więcej informacji, zobacz [blokowanie zasobów strategii](../concepts/resource-locking.md).

   - Tożsamość zarządzana

     Pozostaw domyślną opcję zarządzanej tożsamości _przypisanej do systemu_ .

   - Parametry artefaktu

     Parametry zdefiniowane w tej sekcji mają zastosowanie do artefaktu w ramach którego zostały zdefiniowane. Parametry te są [parametrami dynamicznymi](../concepts/parameters.md#dynamic-parameters) , ponieważ są one definiowane podczas przypisywania planu. Aby uzyskać pełną listę lub parametry artefaktów oraz ich opisy, zobacz [tabela parametrów artefaktów](#artifact-parameters-table).

1. Po wprowadzeniu wszystkich parametrów wybierz pozycję **Przypisz** w dolnej części strony. Tworzone jest przypisanie strategii i rozpoczyna się wdrażanie artefaktu. Wdrażanie zajmuje trochę czasu. Aby sprawdzić stan wdrożenia, otwórz przypisanie strategii.

> [!WARNING]
> Usługa Azure Blueprints i wbudowane przykłady strategii są udostępniane **bezpłatnie**. Opłaty za zasoby platformy Azure są [ustalane według produktu](https://azure.microsoft.com/pricing/). Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby oszacować koszt działania zasobów wdrożonych za pomocą tego przykładu strategii.

### <a name="artifact-parameters-table"></a>Tabela parametrów artefaktów

Poniższa tabela zawiera listę parametrów artefaktów strategii:

|Nazwa artefaktu|Typ artefaktu|Nazwa parametru|Opis|
|-|-|-|-|
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Lista rozszerzeń maszyn wirtualnych, które są zatwierdzone do użycia|Rozdzielana średnikami lista rozszerzeń maszyn wirtualnych; Aby wyświetlić pełną listę rozszerzeń, użyj polecenia Azure PowerShell Get-AzVMExtensionImage|
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail na potrzeby otrzymywania raportów skanowania|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w Azure Data Lake Store|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Magazyn kluczy powinien mieć włączoną ochronę przed czyszczeniem|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że aplikacja interfejsu API ma wartość "certyfikaty klienta (przychodzące certyfikaty klienta") "on"|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dotyczący zasad: tożsamość zarządzana powinna być używana w aplikacja funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender dla Key Vault powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: niestandardowe role właściciela subskrypcji nie powinny istnieć|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: klucze powinny mieć ustawioną datę wygaśnięcia|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć Transparent Data Encryption w bazach danych SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Wpływ na zasady: Administrator Azure Active Directory powinien zostać zainicjowany dla serwerów SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender dla App Service powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konta magazynu powinny ograniczać dostęp do sieci przy użyciu reguł sieci wirtualnej|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dotyczący zasad: tożsamość zarządzana powinna być używana w aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: dostęp SSH z Internetu powinien być zablokowany|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: niedołączone dyski powinny być szyfrowane|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: usługa Azure Defender dla magazynu powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konta magazynu powinny ograniczać dostęp do sieci|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w Logic Apps|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w IoT Hub|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: FTPS powinny być wymagane tylko w aplikacja funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń (Microsoft. Security/securitySolutions/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń (Microsoft. Security/securitySolutions/Write)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć bezpieczny transfer do kont magazynu|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne na kontach usługi Batch|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dotyczący zasad: funkcja autoaprowizacji agenta Log Analytics powinna być włączona w ramach subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt zasad: FTPS powinna być wymagana w aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: usługa Azure Defender dla serwerów powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: subskrypcje powinny mieć adres e-mail kontaktu pod kątem problemów z zabezpieczeniami|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: dostęp publiczny do konta magazynu powinien być niedozwolony|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender for Kubernetes powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: ograniczenie połączenia powinno być włączone dla serwerów baz danych PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że aplikacja INTERNETowa ma "certyfikaty klienta (przychodzące certyfikaty klienta") "on"|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender dla serwerów SQL na maszynach powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć powiadomienia E-mail dla alertów o wysokiej ważności.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konto magazynu powinno używać klucza zarządzanego przez klienta do szyfrowania|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji sieci WEB|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w Virtual Machine Scale Sets|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Wpływ na zasady: usługa Azure Defender dla serwerów Azure SQL Database powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w centrum zdarzeń|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: aktualizacje systemu powinny być zainstalowane na maszynach|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: serwery SQL powinny być skonfigurowane z zachowaniem inspekcji na 90 dni lub wyższą.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja "HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: usługi MFA powinny mieć włączone konta z uprawnieniami do zapisu w Twojej subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć uwierzytelnianie w aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: wpisy tajne powinny mieć ustawioną datę wygaśnięcia|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: FTPS powinien być wymagany tylko w aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć inspekcję w programie SQL Server|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami właściciela w ramach subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniu zarządzanym SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt zasad: Role-Based Access Control (RBAC) powinien być używany w usługach Kubernetes Services|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Monitoruj brakujące Endpoint Protection w Azure Security Center|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w usługach wyszukiwania|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w App Services|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. Network/networkSecurityGroups/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. Network/networkSecurityGroups/securityRules/Write)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. Network/networkSecurityGroups/Write)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: alert dotyczący dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. SQL/Servers/firewallRules/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. SQL/Servers/firewallRules/Write)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy zainstalować tylko zatwierdzone rozszerzenia maszyn wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: usługa Azure Defender dla rejestrów kontenerów powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dotyczący zasad: tożsamość zarządzana powinna być używana w aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć uwierzytelnianie w aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji zasad (Microsoft. Authorization/policyAssignments/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji zasad (Microsoft. Authorization/policyAssignments/Write)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć uwierzytelnianie w aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w Data Lake Analytics|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konta magazynu powinny zezwalać na dostęp z zaufanych usług firmy Microsoft|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w Key Vault|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Wymuszaj połączenie SSL powinno być włączone dla serwerów bazy danych PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami do odczytu w ramach subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: dostęp RDP z Internetu powinien być zablokowany|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Wymuś włączenie połączenia SSL dla serwerów baz danych MySQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Upewnij się, że aplikacja funkcji ma wartość "certyfikaty klienta (przychodzące certyfikaty klienta") "on"|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć punkty kontrolne dziennika dla serwerów baz danych PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć połączenia dzienników dla serwerów baz danych PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: odłączenia powinny być rejestrowane dla serwerów bazy danych PostgreSQL.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy używać najnowszej wersji protokołu TLS w aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w Service Bus|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w Azure Stream Analytics|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: w aplikacja funkcji powinna być używana najnowsza wersja protokołu TLS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Efekt dla zasad: konto magazynu zawierające kontener z dziennikami aktywności musi być zaszyfrowane za pomocą BYOK|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](../../policy/concepts/effects.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Uwzględnij klastry AKS podczas przeprowadzania inspekcji w przypadku włączenia dzienników diagnostycznych zestawu skalowania maszyn wirtualnych||
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Najnowsza wersja języka Java dla App Services|Najnowsza obsługiwana wersja języka Java dla App Services|
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Najnowsza wersja języka Python dla systemu Linux dla App Services|Najnowsza obsługiwana wersja języka Python dla App Services|
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Lista regionów, w których należy włączyć Network Watcher|Aby wyświetlić pełną listę regionów, uruchom polecenie programu PowerShell Get-AzLocation|
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Najnowsza wersja języka PHP dla App Services|Najnowsza obsługiwana wersja języka PHP dla App Services|
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Wymagany okres przechowywania dzienników zasobów|Aby uzyskać więcej informacji na temat dzienników zasobów, odwiedź stronę [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md) |
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Nazwa grupy zasobów dla Network Watcher|Nazwa grupy zasobów, w której znajdują się obserwatorzy sieci|
|Usługa CIS Microsoft Azure wykryto testy testowe 1.3.0|Przypisanie zasad|Wymagane ustawienie inspekcji dla serwerów SQL||

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
---
title: Przykład strategii CMMC Level 3
description: Omówienie przykładu planu CMMC Level 3. Ten przykład strategii pomaga klientom ocenić określone mechanizmy kontroli.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572657"
---
# <a name="cmmc-level-3-blueprint-sample"></a>Przykład strategii CMMC Level 3

Przykład planu CMMC Level 3 oferuje funkcję ładu Guard-szyn korzystających z [Azure Policy](../../policy/overview.md) , które ułatwiają ocenę określonych kontrolek [struktury certyfikacji cyberbezpieczeństwa (CMMC) modelu](https://www.acq.osd.mil/cmmc/index.html) . Ten plan ułatwia klientom wdrożenie podstawowego zestawu zasad dla dowolnej architektury wdrożonej na platformie Azure, która musi implementować kontrolki dla poziomu CMMC 3.

## <a name="control-mapping"></a>Mapowanie kontrolek

[Mapowanie formantów Azure Policy](../../policy/samples/cmmc-l3.md) zawiera szczegółowe informacje o definicjach zasad uwzględnionych w ramach tego planu oraz o tym, jak te definicje zasad są mapowane na **kontrolki** w środowisku CMMC. Po przypisaniu do architektury zasoby są oceniane przez Azure Policy w przypadku braku zgodności z przypisanymi definicjami zasad. Aby uzyskać więcej informacji, zobacz [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Wdróż

Aby wdrożyć przykład planu platformy Azure CMMC Level 3, należy wykonać następujące czynności:

> [!div class="checklist"]
> - Tworzenie nowej strategii na podstawie przykładu
> - Oznaczanie swojej kopii przykładu jako **opublikowanej**
> - Przypisywanie kopii strategii do istniejącej subskrypcji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Utwórz plan z przykładu

Najpierw zaimplementuj przykład strategii, tworząc nową strategię w środowisku przy użyciu przykładu jako wzorca.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Na stronie **Wprowadzenie** z lewej strony wybierz przycisk **Utwórz** w obszarze _Tworzenie strategii_.

1. Znajdź przykład " **CMMC Level 3** " pod _innymi próbkami_ i wybierz opcję **Użyj tego przykładu**.

1. Wprowadź _podstawowe_ informacje dotyczące tego przykładu strategii:

   - **Nazwa** planu: Podaj nazwę kopii przykładu planu CMMC Level 3.
   - **Lokalizacja definicji**: Użyj wielokropka i wybierz grupę zarządzania, w której ma zostać zapisana kopia przykładu.

1. Wybierz kartę _Artefakty_ w górnej części strony lub pozycję **Dalej: Artefakty** w dolnej części strony.

1. Zapoznaj się z listą artefaktów uwzględnionych w przykładowej strategii. Wiele artefaktów ma parametry, które zdefiniujemy później. Po zakończeniu przeglądania przykładu strategii wybierz pozycję **Zapisz wersję roboczą**.

### <a name="publish-the-sample-copy"></a>Publikowanie kopii przykładu

Twoja kopia przykładu strategii została utworzona w środowisku. Została ona utworzona w trybie **wersji roboczej** i musi zostać **opublikowana**, zanim będzie można ją przypisać i wdrożyć. Kopię przykładowej strategii można dostosować do środowiska i potrzeb, ale modyfikacja może nie przełączać jej do CMMC poziomu 3.

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz pozycję **Strategie**.

1. Po lewej stronie wybierz stronę **Definicje strategii**. Użyj filtrów, aby znaleźć kopię przykładowej strategii, a następnie wybierz ją.

1. W górnej części strony wybierz pozycję **Publikuj strategię**. Na nowej stronie, po prawej, w polu **Wersja** podaj wersję kopii przykładu strategii. Ta właściwość jest przydatna w przypadku dokonywania późniejszych modyfikacji. Podaj **Informacje o zmianach** , takie jak "Pierwsza wersja opublikowana z przykładu" CMMC Level 3 ". Następnie wybierz pozycję **Publikuj** w dolnej części strony.

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
|Poziom 3 CMMC|Przypisanie zasad|Uwzględnij serwery połączone z łukiem podczas oceny zasad konfiguracji gościa|Po wybraniu wartości "true" wyrażasz zgodę na naliczanie opłat miesięcznie za maszynę połączoną z łukiem Aby uzyskać więcej informacji, odwiedź stronę https://aka.ms/policy-pricing|
|Poziom 3 CMMC|Przypisanie zasad|Lista użytkowników, którzy muszą być wykluczeni z grupy administratorów maszyn wirtualnych systemu Windows|Rozdzielana średnikami lista użytkowników, którzy powinni zostać wykluczeni w lokalnej grupie Administratorzy; Przykład: administrator; Użytkownik1; Do|
|Poziom 3 CMMC|Przypisanie zasad|Lista użytkowników, którzy muszą być uwzględnieni w grupie administratorów maszyn wirtualnych z systemem Windows|Rozdzielana średnikami lista użytkowników, którzy powinni być uwzględnieni w lokalnej grupie Administratorzy; Przykład: administrator; Użytkownik1; Do|
|Poziom 3 CMMC|Przypisanie zasad|Log Analytics identyfikator obszaru roboczego dla raportowania agenta maszyny wirtualnej|Identyfikator (GUID) obszaru roboczego Log Analytics, w którym agenci maszyn wirtualnych powinni zgłosić|
|Poziom 3 CMMC|Przypisanie zasad|Dozwolone nazwy krzywych eliptyczna|Lista dozwolonych nazw krzywych dla certyfikatów kryptograficznych krzywej eliptycznej.|
|Poziom 3 CMMC|Przypisanie zasad|Dozwolone typy kluczy|Lista dozwolonych typów kluczy|
|Poziom 3 CMMC|Przypisanie zasad|Zezwalaj na użycie sieci hosta dla Kubernetes klastrów|Ustaw tę wartość na true, jeśli pod warunkiem, że w przeciwnym razie można używać sieci hosta.|
|Poziom 3 CMMC|Przypisanie zasad|Inspekcja zmiany zasad uwierzytelniania|Określa, czy zdarzenia inspekcji są generowane po wprowadzeniu zmian w zasadach uwierzytelniania. To ustawienie jest przydatne w przypadku śledzenia zmian zaufania na poziomie domeny i lasu, które są przyznawane kontom użytkowników lub grupom.|
|Poziom 3 CMMC|Przypisanie zasad|Inspekcja zmiany zasad autoryzacji|Określa, czy zdarzenia inspekcji są generowane do przypisywania i usuwania praw użytkownika w zasadach właściwych dla użytkownika, zmiany w uprawnieniach obiektu tokenu zabezpieczającego, zmiany atrybutów zasobów oraz centralne zmiany zasad dostępu dla obiektów systemu plików.|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Azure Backup powinna być włączona dla Virtual Machines|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta Cognitive Services powinny ograniczać dostęp do sieci|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: wystąpienia zarządzane SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: usługa Azure API for FHIR powinna używać klucza zarządzanego przez klienta (CMK) do szyfrowania danych przechowywanych w stanie spoczynku|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna być włączona dla usługi Azure front-drzwi|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: ustawienia oceny luk w zabezpieczeniach dla programu SQL Server powinny zawierać adres e-mail na potrzeby otrzymywania raportów skanowania|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp do sieci publicznej powinien być wyłączony dla kont Cognitive Services|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: funkcja CORS nie powinna zezwalać wszystkim zasobom na dostęp do aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zastosowania zasad: zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: powinien być przypisany do subskrypcji więcej niż jeden właściciel|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: szyfrowanie dysków powinno być stosowane na maszynach wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć powiadomienie E-mail do właściciela subskrypcji w celu uzyskania alertów o wysokiej ważności.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Magazyn kluczy powinien mieć włączoną ochronę przed czyszczeniem|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: serwery SQL powinny używać kluczy zarządzanych przez klienta do szyfrowania danych magazynowanych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender dla Key Vault powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć kopię zapasową nadmiarową dla Azure Database for MariaDB|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: mechanizm CORS nie powinien zezwalać każdej domenie na dostęp do interfejsu API dla FHIR|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń "zabezpieczenia sieci"|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zasad: należy zaktualizować reguły dozwolonych w ramach adaptacyjnych zasad kontroli aplikacji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla Application Gateway|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: klucze powinny mieć ustawioną datę wygaśnięcia|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć Transparent Data Encryption w bazach danych SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: profil dziennika Azure Monitor powinien zbierać dzienniki dla kategorii "Write", "Delete" i "Action"|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Magazyn kluczy powinien mieć włączone usuwanie nietrwałe|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Wpływ na zasady: Administrator Azure Active Directory powinien zostać zainicjowany dla serwerów SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć tylko bezpieczne połączenia z pamięcią podręczną platformy Azure dla Redis|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: szyfrowanie infrastruktury powinno być włączone dla serwerów Azure Database for PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender dla App Service powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — zmiana zasad|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta Cognitive Services powinny włączać szyfrowanie danych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp SSH z Internetu powinien być zablokowany|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: niedołączone dyski powinny być szyfrowane|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: usługa Azure Defender dla magazynu powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta magazynu powinny ograniczać dostęp do sieci|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Wdróż zaawansowaną ochronę przed zagrożeniami na kontach magazynu|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: zmienne konta usługi Automation powinny być szyfrowane|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w IoT Hub|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: szyfrowanie infrastruktury powinno być włączone dla serwerów Azure Database for MySQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji zabezpieczeń (Microsoft. Security/securitySolutions/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach konfiguracji w zestawach skalowania maszyn wirtualnych należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — dostęp sieciowy|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć bezpieczny transfer do kont magazynu|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Azure Monitor powinien zbierać dzienniki aktywności ze wszystkich regionów|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna używać określonego trybu dla usługi platformy Azure front-drzwi|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta magazynu powinny mieć szyfrowanie infrastruktury|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: adaptacyjne kontrolki aplikacji do definiowania bezpiecznych aplikacji powinny być włączone na maszynach|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć kopię zapasową nadmiarową dla Azure Database for PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące opcji zabezpieczeń — Kontrola konta użytkownika|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: usługa Azure Defender dla serwerów powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: subskrypcje powinny mieć adres e-mail kontaktu pod kątem problemów z zabezpieczeniami|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp publiczny do konta magazynu powinien być niedozwolony|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: rozwiązanie do oceny luk w zabezpieczeniach powinno być włączone na maszynach wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender for Kubernetes powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Zapora powinna być włączona na Key Vault|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Zapora aplikacji sieci Web (WAF) powinna być włączona dla Application Gateway|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: rejestry kontenerów powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp do sieci publicznej powinien być wyłączony dla elastycznych serwerów PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach Azure Container Registry obrazów należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: klastry Service Fabric powinny mieć Właściwość ClusterProtectionLevel ustawioną na wartość EncryptAndSign|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zastosowania zasad: usługa Azure Defender dla serwerów SQL na maszynach powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta Cognitive Services powinny włączać szyfrowanie danych za pomocą klucza zarządzanego przez klienta|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta przestarzałe powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć powiadomienia E-mail dla alertów o wysokiej ważności.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konto magazynu powinno używać klucza zarządzanego przez klienta do szyfrowania|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka PHP jest najnowsza, jeśli jest używana jako część aplikacji sieci WEB|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że "wersja języka Python" jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: klucze powinny być określonym typem kryptograficznym RSA lub we|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: subskrypcje platformy Azure powinny mieć profil dziennika aktywności|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: systemy operacyjne i dyski danych w klastrach usługi Azure Kubernetes powinny być szyfrowane przez klucze zarządzane przez klienta|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Wpływ na zasady: usługa Azure Defender dla serwerów Azure SQL Database powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: usługa Azure Eksplorator danych Encryption w spoczynku powinna używać klucza zarządzanego przez klienta|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: klucze używające kryptografii RSA powinny mieć określony minimalny rozmiar klucza|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć kopię zapasową nadmiarową dla Azure Database for MySQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Kubernetesy klastrów powinny używać tylko zatwierdzonej sieci hosta i zakresu portów|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: aktualizacje systemu powinny być zainstalowane na maszynach|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące zasad inspekcji systemu — użycie|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: zadania Azure Stream Analytics powinny używać kluczy zarządzanych przez klienta do szyfrowania danych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja "HTTP" jest najnowsza, jeśli jest używana do uruchamiania aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Najnowsza wersja protokołu TLS powinna być używana w aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: usługi MFA powinny mieć włączone konta z uprawnieniami do zapisu w Twojej subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji interfejsu API|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: rozszerzenie Microsoft IaaSAntimalware należy wdrożyć na serwerach z systemem Windows|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja języka Java jest najnowsza, jeśli jest używana jako część aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: wszystkie porty sieciowe powinny być ograniczone dla sieciowych grup zabezpieczeń skojarzonych z maszyną wirtualną|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy wybrać warstwę cenową standardowa Security Center|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja użycia niestandardowych reguł RBAC|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć inspekcję w programie SQL Server|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Agent Log Analytics powinien być zainstalowany na maszynach wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami właściciela w ramach subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Zaawansowane zabezpieczenia danych powinny być włączone w wystąpieniu zarządzanym SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zasad: Role-Based Access Control (RBAC) powinien być używany w usługach Kubernetes Services|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny wirtualne powinny mieć rozszerzenie konfiguracji gościa|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Monitoruj brakujące Endpoint Protection w Azure Security Center|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Dziennik aktywności powinien być zachowywany przez co najmniej jeden rok.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp do sieci publicznej powinien być wyłączony dla serwerów PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Wdróż zaawansowaną ochronę przed zagrożeniami dla kont Cosmos DB|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć dzienniki diagnostyczne w App Services|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. ClassicNetwork/networkSecurityGroups/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. ClassicNetwork/networkSecurityGroups/securityRules/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. Network/networkSecurityGroups/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. Network/networkSecurityGroups/securityRules/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: alert dotyczący dziennika aktywności powinien istnieć dla określonych operacji administracyjnych (Microsoft. SQL/Servers/firewallRules/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Wpływ na zasady: maszyny wirtualne niepołączone z Internetem powinny być chronione przy użyciu sieciowych grup zabezpieczeń|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Windows, które nie mają włączonego ustawienia złożoności hasła|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: usługa Azure Defender dla rejestrów kontenerów powinna być włączona|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: zadania Azure Data Box powinny włączać podwójne szyfrowanie dla danych przechowywanych na urządzeniu|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Usługa Microsoft chroniąca przed złośliwym kodem powinna być skonfigurowana do automatycznego aktualizowania sygnatur ochrony|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Alert dziennika aktywności powinien istnieć dla określonych operacji zasad (Microsoft. Authorization/policyAssignments/Delete)|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp do sieci publicznej powinien być wyłączony dla elastycznych serwerów MySQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta magazynu powinny zezwalać na dostęp z zaufanych usług firmy Microsoft|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: certyfikaty używające kryptografii RSA powinny mieć określony minimalny rozmiar klucza|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: rejestry kontenerów nie powinny zezwalać na nieograniczony dostęp do sieci|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Wymuszaj połączenie SSL powinno być włączone dla serwerów bazy danych PostgreSQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: rozszerzenie konfiguracji gościa należy wdrożyć na maszynach wirtualnych platformy Azure z tożsamością zarządzaną przypisanej przez system|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: długoterminowe geograficznie nadmiarowe kopie zapasowe powinny być włączone dla baz danych Azure SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp do sieci publicznej powinien być wyłączony dla serwerów MySQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Windows, które nie przechowują haseł przy użyciu szyfrowania odwracalnego|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny z systemem Windows powinny spełniać wymagania dotyczące "przypisywania praw użytkownika"|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach konfiguracji na maszynach należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Upewnij się, że wersja protokołu HTTP jest najnowsza, jeśli jest używana do uruchamiania aplikacji funkcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy włączyć usługę MFA na kontach z uprawnieniami do odczytu w ramach subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp RDP z Internetu powinien być zablokowany|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Linux, które nie mają uprawnień do plików haseł ustawionych na 0644|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: podsieci powinny być skojarzone z sieciową grupą zabezpieczeń|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Wymuś włączenie połączenia SSL dla serwerów baz danych MySQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: debugowanie zdalne powinno być wyłączone dla API Apps|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Linux, które zezwalają na połączenia zdalne z kont bez hasła|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta przestarzałe z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zastosowania zasad: należy włączyć podwójne szyfrowanie na platformie Azure Eksplorator danych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Agent Log Analytics powinien być zainstalowany na Virtual Machine Scale Sets|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: należy używać najnowszej wersji protokołu TLS w aplikacji sieci Web|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: szyfrowanie dysków powinno być włączone na platformie Azure Eksplorator danych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: maszyny wirtualne dostępne z Internetu powinny być chronione przy użyciu sieciowych grup zabezpieczeń|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: Inspekcja maszyn z systemem Linux, które mają konta bez hasła|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: obszary robocze usługi Azure Synapse powinny używać kluczy zarządzanych przez klienta do szyfrowania danych przechowywanych w stanie spoczynku|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: w aplikacja funkcji powinna być używana najnowsza wersja protokołu TLS|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: usługi Kubernetes należy uaktualnić do wersji Kubernetes, która nie jest zagrożona.|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: cały ruch internetowy powinien być kierowany za pośrednictwem wdrożonej zapory platformy Azure|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: komputery z systemem Linux powinny spełniać wymagania dotyczące podstawy zabezpieczeń platformy Azure|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: dostęp do sieci publicznej powinien być wyłączony dla serwerów MariaDB|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt zasad: luki w zabezpieczeniach baz danych SQL należy skorygować|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Efekt dla zasad: klucze używające kryptografii krzywej eliptycznej powinny mieć określone nazwy krzywych|Aby uzyskać więcej informacji na temat efektów, odwiedź stronę [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Poziom 3 CMMC|Przypisanie zasad|Przestrzenie nazw wyłączone z oceny zasad: Kubernetes klastrów powinny używać tylko zatwierdzonej sieci hosta i zakresu portów|Lista przestrzeni nazw Kubernetes do wykluczenia z oceny zasad.|
|Poziom 3 CMMC|Przypisanie zasad|Najnowsza wersja języka Java dla App Services|Najnowsza obsługiwana wersja języka Java dla App Services|
|Poziom 3 CMMC|Przypisanie zasad|Najnowsza wersja języka Python dla systemu Linux dla App Services|Najnowsza obsługiwana wersja języka Python dla App Services|
|Poziom 3 CMMC|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Linux do dodania do zakresu podczas inspekcji Log Analytics wdrożenia agenta|Przykładowa wartość: "/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage"|
|Poziom 3 CMMC|Przypisanie zasad|Opcjonalnie: lista obrazów maszyn wirtualnych z obsługiwanym systemem operacyjnym Windows do dodania do zakresu podczas inspekcji Log Analytics wdrożenia agenta|Przykładowa wartość: "/subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/Providers/Microsoft.COMPUTE/images/ContosoStdImage"|
|Poziom 3 CMMC|Przypisanie zasad|Lista regionów, w których należy włączyć Network Watcher|Inspekcja, jeśli Network Watcher nie jest włączona dla regionów.|
|Poziom 3 CMMC|Przypisanie zasad|Lista typów zasobów, dla których włączono obsługę dzienników diagnostycznych||
|Poziom 3 CMMC|Przypisanie zasad|Maksymalna wartość w dozwolonym zakresie portów hosta, która może być używana w przestrzeni nazw sieci hosta|Maksymalna wartość w dozwolonym zakresie portów hosta, która może być używana w przestrzeni nazw sieci hosta.|
|Poziom 3 CMMC|Przypisanie zasad|Minimalny rozmiar klucza RSA dla kluczy|Minimalny rozmiar klucza kluczy RSA.|
|Poziom 3 CMMC|Przypisanie zasad|Minimalny rozmiar klucza RSA certyfikatów|Minimalny rozmiar klucza dla certyfikatów RSA.|
|Poziom 3 CMMC|Przypisanie zasad|Minimalna wersja protokołu TLS dla serwerów sieci Web systemu Windows|Serwery sieci Web systemu Windows z niższymi wersjami protokołu TLS zostaną ocenione jako niezgodne|
|Poziom 3 CMMC|Przypisanie zasad|Minimalna wartość w dozwolonym zakresie portów hosta, która może być używana w przestrzeni nazw sieci hosta|Minimalna wartość w dozwolonym zakresie portów hosta, która może być używana w przestrzeni nazw sieci hosta.|
|Poziom 3 CMMC|Przypisanie zasad|Wymaganie trybu|Tryb wymagany dla wszystkich zasad WAF|
|Poziom 3 CMMC|Przypisanie zasad|Wymaganie trybu|Tryb wymagany dla wszystkich zasad WAF|
|Poziom 3 CMMC|Przypisanie zasad|Dozwolone ścieżki hostów dla woluminów hostPath, które mają być używane|Ścieżki hostów dozwolone dla woluminów hostPath, które mają być używane. Podaj pustą listę ścieżek, aby zablokować wszystkie ścieżki hosta.|
|Poziom 3 CMMC|Przypisanie zasad|Dostęp sieciowy: ścieżki rejestru, do których można uzyskiwać dostęp anonimowo|Określa, które ścieżki rejestru będą dostępne za pośrednictwem sieci, niezależnie od użytkowników lub grup wymienionych na liście kontroli dostępu (ACL) `winreg` klucza rejestru.|
|Poziom 3 CMMC|Przypisanie zasad|Dostęp sieciowy: ścieżki rejestru i ścieżki podrzędne dostępne zdalnie|Określa ścieżki rejestru i ścieżki podrzędne, które będą dostępne za pośrednictwem sieci, niezależnie od użytkowników lub grup wymienionych na liście kontroli dostępu (ACL) `winreg` klucza rejestru.|
|Poziom 3 CMMC|Przypisanie zasad|Dostęp sieciowy: udziały, do których można uzyskiwać dostęp anonimowo|Określa, które udziały sieciowe mają być dostępne dla użytkowników anonimowych. Konfiguracja domyślna tego ustawienia zasad ma niewielki wpływ, ponieważ wszyscy użytkownicy muszą zostać uwierzytelnieni, zanim będą mogli uzyskać dostęp do zasobów udostępnionych na serwerze.|
|Poziom 3 CMMC|Przypisanie zasad|Zabezpieczenia sieci: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos|Określa typy szyfrowania, które mogą być używane przez protokół Kerberos.|
|Poziom 3 CMMC|Przypisanie zasad|Zabezpieczenia sieci: poziom uwierzytelniania LAN Manager|Określ, który protokół uwierzytelniania odpowiedzi na żądanie jest używany do logowania do sieci. Wybór ten dotyczy poziomu protokołu uwierzytelniania używanego przez klientów, negocjowanego poziomu zabezpieczeń sesji oraz poziomu uwierzytelniania akceptowanego przez serwery.|
|Poziom 3 CMMC|Przypisanie zasad|Zabezpieczenia sieci: wymagania podpisywania klienta LDAP|Określ poziom podpisywania danych żądany w imieniu klientów, którzy wysyłają żądania wiązania LDAP.|
|Poziom 3 CMMC|Przypisanie zasad|Zabezpieczenia sieci: minimalne zabezpieczenia sesji dla klientów opartych na NTLM SSP (włączając secure RPC)|Określa, które zachowania są dozwolone przez klientów dla aplikacji korzystających z dostawcy obsługi zabezpieczeń (SSP) NTLM. Interfejs SSP (SSPI) jest używany przez aplikacje, które wymagają usług uwierzytelniania. [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers)Aby uzyskać więcej informacji, zobacz.|
|Poziom 3 CMMC|Przypisanie zasad|Zabezpieczenia sieci: minimalne zabezpieczenia sesji dla serwerów opartych na NTLM SSP (włączając secure RPC)|Określa, które zachowania są dozwolone przez serwery dla aplikacji korzystających z dostawcy obsługi zabezpieczeń (SSP) NTLM. Interfejs SSP (SSPI) jest używany przez aplikacje, które wymagają usług uwierzytelniania.|
|Poziom 3 CMMC|Przypisanie zasad|Najnowsza wersja języka PHP dla App Services|Najnowsza obsługiwana wersja języka PHP dla App Services|
|Poziom 3 CMMC|Przypisanie zasad|Wymagany okres przechowywania (w dniach) dla dzienników diagnostycznych IoT Hub||
|Poziom 3 CMMC|Przypisanie zasad|Nazwa grupy zasobów dla Network Watcher|Nazwa grupy zasobów NetworkWatcher, na przykład NetworkWatcherRG. Jest to grupa zasobów, w której znajdują się obserwatorzy sieci.|
|Poziom 3 CMMC|Przypisanie zasad|Wymagane ustawienie inspekcji dla serwerów SQL||
|Poziom 3 CMMC|Przypisanie zasad|Azure Data Box jednostki SKU obsługujące podwójne szyfrowanie oparte na oprogramowaniu|Lista jednostek SKU Azure Data Box, które obsługują podwójne szyfrowanie oparte na oprogramowaniu|
|Poziom 3 CMMC|Przypisanie zasad|UAC: tryb zatwierdzania przez administratora dla wbudowanego konta administratora|Określa zachowanie trybu zatwierdzania przez administratora dla wbudowanego konta administratora.|
|Poziom 3 CMMC|Przypisanie zasad|UAC: zachowanie monitu o podniesienie uprawnień dla administratorów w trybie zatwierdzania przez administratora|Określa zachowanie monitu o podniesienie uprawnień dla administratorów.|
|Poziom 3 CMMC|Przypisanie zasad|UAC: wykrywanie instalacji aplikacji i monitowanie o podniesienie uprawnień|Określa zachowanie wykrywania instalacji aplikacji dla komputera.|
|Poziom 3 CMMC|Przypisanie zasad|UAC: Uruchom wszystkich administratorów w trybie zatwierdzania przez administratora|Określa zachowanie wszystkich ustawień zasad kontroli konta użytkownika (UAC) dla komputera.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, które mogą wymusić zamknięcie systemu zdalnego|Określa, którzy użytkownicy i grupy mogą zamknąć komputer z lokalizacji zdalnej w sieci.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, którym odmówiono dostępu do tego komputera z sieci|Określa, którzy użytkownicy lub grupy jawnie nie mogą łączyć się z komputerem za pośrednictwem sieci.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, dla których odmówiono logowania lokalnego|Określa, którzy użytkownicy i grupy jawnie nie mogą się zalogować na komputerze.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, dla których odmówiono zalogowania się jako zadanie wsadowe|Określa, którzy użytkownicy i grupy jawnie nie mogą logować się na komputerze jako zadanie wsadowe (tj. zaplanowane zadanie).|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, dla których odmówiono logowania w trybie usługi|Określa, które konta usług jawnie nie mogą zarejestrować procesu jako usługi.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, których odmówiono logowania za poorednictwem Usługi pulpitu zdalnego|Określa, którzy użytkownicy i grupy jawnie nie mogą logować się na komputerze za pośrednictwem usług terminalowych/Pulpit zdalny klienta.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, które mogą przywracać pliki i katalogi|Określa, którzy użytkownicy i grupy mogą ominąć uprawnienia plików, katalogów, rejestru i innych obiektów trwałych podczas przywracania kopii zapasowej plików i katalogów.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy i grupy, które mogą zamknąć system|Określa, którzy użytkownicy i grupy, którzy są zalogowani lokalnie na komputerach w danym środowisku, mogą zamknąć system operacyjny za pomocą polecenia Zamknij.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, które mogą logować się lokalnie|Określa, którzy użytkownicy zdalni w sieci mogą łączyć się z komputerem. Nie obejmuje to Podłączanie pulpitu zdalnego.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, którzy mogą tworzyć kopie zapasowe plików i katalogów|Określa użytkowników i grupy, które mogą obejść uprawnienia plików i katalogów do tworzenia kopii zapasowej systemu.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, którzy mogą zmieniać czas systemowy|Określa, którzy użytkownicy i grupy mogą zmieniać godzinę i datę w zegarze wewnętrznym komputera.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, którzy mogą zmieniać strefę czasową|Określa, którzy użytkownicy i grupy mogą zmieniać strefę czasową komputera.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, którzy mogą tworzyć obiekt tokenu|Określa, którzy użytkownicy i grupy mogą tworzyć token dostępu, który może zapewnić podwyższony poziom uprawnień dostępu do poufnych danych.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, które mogą logować się lokalnie|Określa, którzy użytkownicy lub grupy mogą interaktywnie zalogować się na komputerze. Użytkownicy próbujący zalogować się za pośrednictwem Podłączanie pulpitu zdalnego lub IIS wymagają również tego prawa użytkownika.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy pulpitu zdalnego|Użytkownicy lub grupy, którzy mogą logować się za poorednictwem Usługi pulpitu zdalnego|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, którzy mogą zarządzać inspekcją i dziennikiem zabezpieczeń|Określa, że użytkownicy i grupy mogą zmieniać opcje inspekcji dla plików i katalogów i czyścić dziennik zabezpieczeń.|
|Poziom 3 CMMC|Przypisanie zasad|Użytkownicy lub grupy, które mogą przejąć własność plików lub innych obiektów|Określa, którzy użytkownicy i grupy mogą przejmować na własność pliki, foldery, klucze rejestru, procesy lub wątki. To prawo użytkownika pomija wszelkie uprawnienia, które są stosowane do ochrony obiektów, aby zapewnić własność określonemu użytkownikowi.|

## <a name="next-steps"></a>Następne kroki

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../how-to/update-existing-assignments.md).
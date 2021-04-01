---
title: '& oficjalne kontrolki przykładowe planów NHS w Wielkiej Brytanii'
description: Kontrolowanie mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS. Każda kontrolka jest zamapowana na co najmniej jedną definicję Azure Policy, która pomaga w ocenie.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: bf10bcf7064306094664c1ff560187ef77645d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627570"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Kontrolowanie mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS w Wielkiej Brytanii

W poniższym artykule szczegółowo przedstawiono sposób mapowania przykładowych OFICJALNych i BRYTYJSKIch planów NHS z brytyjskimi i BRYTYJSKImi formantami NHS. Aby uzyskać więcej informacji na temat kontrolek, zobacz [Zjednoczone Królestwo](https://www.gov.uk/government/publications/government-security-classifications).

Następujące mapowania są do **oficjalnych** i **brytyjskich NHS** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz **\[ kontrolę wersji zapoznawczej \] Zjednoczone oficjalne i Zjednoczone Królestwo NHS oraz Wdróż określone rozszerzenia maszyn wirtualnych, aby obsługiwać zasady inspekcji** wbudowanej inicjatywy.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; jednak często nie jest to jeden lub kompletny odpowiednik między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Data w ochronie tranzytowej

Plan pomaga zapewnić, że transfer informacji w ramach usług platformy Azure jest bezpieczny, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję niezabezpieczonych połączeń z kontami magazynu i Redis Cache.

- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Należy włączyć bezpieczny transfer na konta magazynu
- Pokaż wyniki inspekcji z serwerów sieci Web systemu Windows, które nie używają bezpiecznych protokołów komunikacyjnych
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS

## <a name="23-data-at-rest-protection"></a>2,3 danych w ramach ochrony REST

Ten plan pomaga wymusić zasady korzystania z formantów cryptograph, przypisując definicje [Azure Policy](../../../policy/overview.md) , które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych. Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. Zasady przypisane przez ten plan wymagają szyfrowania dla kont usługi Data Lake Storage; Wymagaj przezroczystego szyfrowania danych w bazach danych SQL; Inspekcja braku szyfrowania na kontach magazynu, bazach danych SQL, dyskach maszyn wirtualnych i zmiennych konta usługi Automation; Inspekcja niezabezpieczonych połączeń z kontami magazynu i Redis Cache; Inspekcja niesłabego szyfrowania hasła maszyny wirtualnej; i Przeprowadź inspekcję niezaszyfrowanej komunikacji Service Fabric.

- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Zmienne konta usługi Automation powinny być szyfrowane
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- Należy włączyć Transparent Data Encryption baz danych SQL
- Wdróż przezroczyste szyfrowanie danych SQL DB
- Wymagaj szyfrowania na kontach Data Lake Store
- Dozwolone lokalizacje (zostały trwale zakodowane jako "Południowe Zjednoczone Królestwo" i "ZACHODNIe Zjednoczone Królestwo")
- Dozwolone lokalizacje dla grup zasobów (zostały trwale zakodowane jako "Południowe Zjednoczone Królestwo" i "ZACHODNIe Zjednoczone Królestwo")

## <a name="52-vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach 5,2

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego przez przypisanie definicji [Azure Policy](../../../policy/overview.md) , które monitorują brak programu Endpoint Protection, brakujące aktualizacje systemu operacyjnego, luki w zabezpieczeniach programu SQL i luki w zabezpieczeniach maszyn wirtualnych. Te szczegółowe dane zapewniają informacje o stanie zabezpieczeń wdrożonych zasobów i umożliwiają określanie priorytetów akcji korygowania.

- Monitoruj brakujące Endpoint Protection w Azure Security Center
- Należy zainstalować aktualizacje systemu na maszynach
- Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach
- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL
- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL
- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować
- Zaawansowana ochrona danych powinna być włączona w wystąpieniu zarządzanym SQL
- Zaawansowana ochrona danych powinna być włączona na serwerach SQL

## <a name="53-protective-monitoring"></a>5,3 monitorowanie ochronne

Ten plan pomaga chronić zasoby systemu informacji przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które zapewniają ochronę przed nieograniczonym dostępem, zezwalanie na działanie listy i zagrożenia.

- Konta magazynu powinny ograniczać dostęp do sieci
- Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.
- Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii
- Należy włączyć Standard Azure DDoS Protection
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "All" w zaawansowanych ustawieniach zabezpieczeń danych wystąpienia zarządzanego SQL
- Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server
- Wdrażanie wykrywania zagrożeń na serwerach SQL
- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server

## <a name="9-secure-user-management"></a>9 Zarządzanie bezpiecznymi użytkownikami 

Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów na platformie Azure.
Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan pomaga ograniczyć i kontrolować prawa dostępu, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub odczytu/zapisu i kontami z uprawnieniami właściciela, odczytu i/zapisu, które nie mają włączonej usługi uwierzytelniania wieloskładnikowego.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

## <a name="10-identity-and-authentication"></a>10 tożsamości i uwierzytelniania

Ten plan pomaga ograniczyć i kontrolować prawa dostępu, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont zewnętrznych z uprawnieniami właściciela i/lub odczytu/zapisu i kontami z uprawnieniami właściciela, odczytu i/zapisu, które nie mają włączonej usługi uwierzytelniania wieloskładnikowego.

- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

Ten plan przypisuje definicje Azure Policy do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL i Service Fabric. Korzystanie z uwierzytelniania Azure Active Directory umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft.

- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów

Ten plan przypisuje również definicje Azure Policy do kont inspekcji, dla których należy określić priorytety dla przeglądu, w tym konta z amortyzacją i konta zewnętrzne. W razie potrzeby konta mogą być blokowane przed zalogowaniem (lub usunięciem), które natychmiast usuwa prawa dostępu do zasobów platformy Azure.
Ten plan przypisuje dwie Azure Policy definicje do inspekcji konta amortyzowanego, które należy wziąć pod uwagę podczas usuwania.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji

Ten plan przypisuje również definicję Azure Policy, która przeprowadza inspekcję uprawnień pliku hasła maszyny wirtualnej systemu Linux do alertu, jeśli są ustawione nieprawidłowo. Ten projekt umożliwia podejmowanie działań naprawczych w celu zapewnienia, że uwierzytelniające nie zostały naruszone.

- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które nie mają uprawnień do pliku haseł ustawione na 0644

Ten plan pomaga wymusić silne hasła, przypisując definicje Azure Policy, które umożliwiają inspekcję maszyn wirtualnych z systemem Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących hasła Świadomość maszyn wirtualnych w przypadku naruszenia zasad dotyczących siły haseł ułatwia podejmowanie działań naprawczych w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami.

- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają włączonego ustawienia złożoności hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają maksymalnego wieku hasła wynoszącego 70 dni
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie mają minimalnego wieku hasła wynoszącego 1 dzień
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- Pokaż wyniki inspekcji z maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł

Ten plan pomaga również kontrolować dostęp do zasobów platformy Azure, przypisując definicje Azure Policy. Te zasady przeprowadzają inspekcję użycia typów zasobów i konfiguracji, które mogą zezwalać na dostęp do zasobów. Informacje o zasobach, które naruszają te zasady, mogą pomóc w podejmowaniu działań naprawczych w celu zapewnienia dostępu do zasobów platformy Azure tylko autoryzowanym użytkownikom.

- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które mają konta bez hasła
- Pokaż wyniki inspekcji z maszyn wirtualnych systemu Linux, które zezwalają na połączenia zdalne z kont bez hasła
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager
- Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych

## <a name="11-external-interface-protection"></a>11 ochrona interfejsu zewnętrznego

W przypadku korzystania z więcej niż 25 zasad do odpowiedniego zarządzania bezpiecznymi użytkownikami ten plan pomaga chronić interfejsy usługi przed nieautoryzowanym dostępem, przypisując [Azure Policy](../../../policy/overview.md) definicję, która monitoruje nieograniczone konta magazynu.
Konta magazynu z nieograniczonym dostępem mogą zezwalać na niezamierzony dostęp do informacji zawartych w systemie informacyjnym. Ten plan przypisuje również zasady, które umożliwiają adaptacyjne kontrole aplikacji na maszynach wirtualnych.

- Konta magazynu powinny ograniczać dostęp do sieci
- Na maszynach powinny być włączone adaptacyjne kontrole aplikacji umożliwiające Definiowanie bezpiecznych aplikacji.
- Dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony
- Zalecane zalecenia dotyczące ograniczania przepustowości sieci powinny być stosowane w przypadku maszyn wirtualnych mających dostęp do Internetu.
- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych
- Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci
- Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji
- Debugowanie zdalne powinno być wyłączone dla aplikacji sieci Web
- Zdalne debugowanie powinno zostać wyłączone dla API Apps

## <a name="13-audit-information-for-users"></a>13 informacji o inspekcji dla użytkowników

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure.
Przypisane zasady również przeprowadzają inspekcję, jeśli maszyny wirtualne nie wysyłają dzienników do określonego obszaru roboczego usługi log Analytics.

- Zaawansowana ochrona danych powinna być włączona na serwerach SQL
- Przeprowadzanie inspekcji ustawienia diagnostyki
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Linux
- \[Wersja zapoznawcza \] : wdrażanie agenta log Analytics dla maszyn wirtualnych z systemem Windows
- Wdróż obserwatora sieciowego po utworzeniu sieci wirtualnych


## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli w OFICJALNych i BRYTYJSKIch planach NHS, odwiedź następujące artykuły, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Oficjalne i brytyjskie plany NHS — Omówienie](./index.md) 
>  [Plany NHS oficjalnego i Zjednoczonego Królestwa — wdrażanie kroków](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).

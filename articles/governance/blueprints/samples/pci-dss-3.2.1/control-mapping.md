---
title: Przykładowe kontrolki planu PCI-DSS v 3.2.1
description: Sterowanie mapowaniem przykładu usługi Payment Card Industry Data Security Standard v 3.2.1 do Azure Policy i kontroli RBAC platformy Azure.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 028981e7c6fc6e3886781d33079dcdfa0aebcc39
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386089"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Sterowanie mapowaniem przykładu planu PCI-DSS v 3.2.1

W poniższym artykule szczegółowo opisano sposób, w jaki usługa Azure planuje plan PCI-DSS v 3.2.1, jest mapowana na kontrolki PCI-DSS v 3.2.1. Aby uzyskać więcej informacji na temat kontrolek, zobacz [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Następujące mapowania są do kontrolek **PCI-DSS v 3.2.1:2018 r** . Użyj nawigacji po prawej stronie, aby przejść bezpośrednio do określonego mapowania formantów. Wiele zamapowanych formantów jest implementowanych z inicjatywą [Azure Policy](../../../policy/overview.md) . Aby zapoznać się z pełną inicjatywą, Otwórz **zasady** w Azure Portal i wybierz stronę **definicje** . Następnie Znajdź i wybierz pozycję **PCI v 3.2.1:2018 r** Policy Initiative.

> [!IMPORTANT]
> Każda kontrolka poniżej jest skojarzona z co najmniej jedną definicją [Azure Policy](../../../policy/overview.md) . Te zasady mogą pomóc w [ocenie zgodności](../../../policy/how-to/get-compliance-data.md) z kontrolką; jednak często nie jest to jeden lub kompletny odpowiednik między kontrolką a co najmniej jedną zasadą. W związku z tym **zgodność** w Azure Policy odnosi się tylko do samych zasad; nie gwarantuje to, że jest w pełni zgodne ze wszystkimi wymaganiami formantu. Ponadto Standard zgodności zawiera kontrolki, które nie są obecnie rozwiązywane przez żadną Azure Policy definicje. W związku z tym zgodność w Azure Policy jest tylko częściowym widokiem ogólnego stanu zgodności. Skojarzenia między kontrolkami i definicjami Azure Policy dla tego przykładowego planu zgodności mogą ulec zmianie z upływem czasu. Aby wyświetlić historię zmian, zobacz [historię zatwierdzeń usługi GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 i 1.3.4 granice ochrony

Ten plan ułatwia zarządzanie sieciami i sterowanie nimi przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują grupy zabezpieczeń sieci z regułami ograniczającymi. Reguły, które są zbyt ograniczane, mogą zezwalać na niezamierzony dostęp do sieci i powinny być przeglądane. Ten plan przypisuje jedną Azure Policy definicji, które monitorują niechronione punkty końcowe, aplikacje i konta magazynu. Punkty końcowe i aplikacje, które nie są chronione przez zaporę, i konta magazynu z nieograniczonym dostępem mogą zezwalać na nieograniczony dostęp do informacji zawartych w systemie informacyjnym.

- Inspekcja nieograniczonego dostępu sieciowego do kont magazynu
- Dostęp za poorednictwem punktu końcowego połączonego z Internetem powinien być ograniczony

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4. a, 4,1, 4.1. g, 4.1. h i 6.5.3, ochrona kryptograficzna

Ten plan pomaga wymuszać zasady przy użyciu kontrolek cryptograph, przypisując definicje [Azure Policy](../../../policy/overview.md) , które wymuszają określone kontrolki cryptograph i inspekcji używają słabych ustawień kryptograficznych. Zrozumienie, w jaki sposób zasoby platformy Azure mogą mieć nieoptymalną konfigurację kryptograficzną, może pomóc w podejmowaniu działań naprawczych w celu zapewnienia, że zasoby są skonfigurowane zgodnie z zasadami zabezpieczeń informacji. W szczególnych przypadkach zasady przypisane przez ten plan wymagają przezroczystego szyfrowania danych w bazach danych SQL; Inspekcja braku szyfrowania na kontach magazynu i zmiennych konta usługi Automation. Istnieją również zasady, które umożliwiają przeprowadzenie inspekcji niezabezpieczonych połączeń z kontami magazynu, aplikacjami funkcji, WebApp, API Apps i Redis Cache i Przeprowadź inspekcję niezaszyfrowanej komunikacji Service Fabric.

- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS
- Należy włączyć Transparent Data Encryption baz danych SQL
- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych
- Zmienne konta usługi Automation powinny być szyfrowane
- Należy włączyć tylko bezpieczne połączenia z Redis Cache
- Należy włączyć bezpieczny transfer na konta magazynu
- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign
- Należy włączyć Transparent Data Encryption baz danych SQL
- Wdróż przezroczyste szyfrowanie danych SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5,1, 6,2, 6,6 i 11.2.1 luk w zabezpieczeniach oraz aktualizacje systemu

Ten plan ułatwia zarządzanie lukami w zabezpieczeniach systemu informacyjnego przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które monitorują brakujące aktualizacje systemu, luki w zabezpieczeniach systemu operacyjnego i luki w zabezpieczeniach programu SQL Server w programie Azure Security Center. Azure Security Center udostępnia funkcje raportowania, które umożliwiają wgląd w informacje o stanie zabezpieczeń wdrożonych zasobów platformy Azure w czasie rzeczywistym.

- Monitoruj brakujące Endpoint Protection w Azure Security Center
- Wdróż domyślne rozszerzenie Microsoft IaaSAntimalware dla systemu Windows Server
- Wdrażanie wykrywania zagrożeń na serwerach SQL
- Należy zainstalować aktualizacje systemu na maszynach
- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować
- Luki w zabezpieczeniach baz danych SQL należy skorygować
- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach

## <a name="711-712-and-713-separation-of-duties"></a>ppkt. 7.1.2 i 7.1.3 separacja obowiązków

Posiadanie tylko jednego właściciela subskrypcji platformy Azure nie pozwala na nadmiarowość administracyjną. Bez względu na to, że zbyt wielu właścicieli subskrypcji platformy Azure może zwiększyć prawdopodobieństwo naruszenia za pośrednictwem konta właściciela z naruszeniem. Ten plan pomaga zachować odpowiednią liczbę właścicieli subskrypcji platformy Azure, przypisując definicje [Azure Policy](../../../policy/overview.md) , które przeprowadzają inspekcję liczby właścicieli subskrypcji platformy Azure. Zarządzanie uprawnieniami właściciela subskrypcji może pomóc w zaimplementowaniu odpowiedniego rozdzielenia obowiązków.

- Do subskrypcji powinien być przypisany więcej niż jeden właściciel
- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3,2, 7.2.1, 8.3.1. a i 8.3.1. b zarządzanie prawami dostępu uprzywilejowanego

Ten plan pomaga ograniczyć i kontrolować prawa dostępu uprzywilejowanego, przypisując definicje [Azure Policy](../../../policy/overview.md) do inspekcji kont zewnętrznych przy użyciu uprawnień właściciela, zapisu i/lub odczytu oraz kont pracowników z uprawnieniami właściciela i/lub zapisu, dla których nie włączono usługi uwierzytelniania wieloskładnikowego. Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów platformy Azure. Informacje o tym, gdzie są zaimplementowane niestandardowe reguły RBAC platformy Azure, mogą pomóc w sprawdzeniu potrzeby i poprawnej implementacji, ponieważ niestandardowe reguły RBAC platformy Azure są podatne na błędy. Ten plan przypisuje również definicje [Azure Policy](../../../policy/overview.md) do inspekcji użycia uwierzytelniania Azure Active Directory dla serwerów SQL. Korzystanie z uwierzytelniania Azure Active Directory upraszcza zarządzanie uprawnieniami i scentralizowanie Zarządzanie tożsamościami użytkowników baz danych i innych firmy Microsoft  
services.
 
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji
- Uwierzytelnianie MFA powinno być włączone na kontach z uprawnieniami do zapisu w Twojej subskrypcji
- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji
- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory
- Inspekcja użycia niestandardowych reguł RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 i 8.1.5 najniższe uprawnienia i przegląd praw dostępu użytkownika

Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Za pomocą Azure Portal można sprawdzić, kto ma dostęp do zasobów platformy Azure i ich uprawnień. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) do kont inspekcji, dla których należy określić priorytety dla przeglądu, w tym konta z amortyzacją i konta zewnętrzne z podniesionymi uprawnieniami.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji
- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 usunięcie lub dostosowanie praw dostępu

Kontrola dostępu oparta na rolach (Azure RBAC) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Przy użyciu Azure Active Directory i kontroli RBAC platformy Azure można aktualizować role użytkowników, aby odzwierciedlały zmiany organizacyjne. W razie potrzeby konta mogą być blokowane przed zalogowaniem (lub usunięciem), które natychmiast usuwa prawa dostępu do zasobów platformy Azure. Ten plan przypisuje definicje [Azure Policy](../../../policy/overview.md) do inspekcji konta amortyzowanego, które należy wziąć pod uwagę podczas usuwania.

- Przestarzałe konta powinny zostać usunięte z subskrypcji
- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3. a, b, 8.2.4. a, b i 8.2.5 uwierzytelniania opartego na hasłach

Ten plan pomaga wymusić silne hasła, przypisując definicje [Azure Policy](../../../policy/overview.md) , które umożliwiają inspekcję maszyn wirtualnych z systemem Windows, które nie wymuszają minimalnej siły i innych wymagań dotyczących hasła Świadomość maszyn wirtualnych w przypadku naruszenia zasad dotyczących siły haseł ułatwia podejmowanie działań naprawczych w celu zapewnienia zgodności haseł dla wszystkich kont użytkowników maszyny wirtualnej z zasadami.

- \[Wersja zapoznawcza \] : Inspekcja maszyn wirtualnych z systemem Windows bez maksymalnego wieku hasła 70 dni
- \[Wersja zapoznawcza \] : wdrażanie wymagań w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows bez maksymalnego wieku hasła wynoszącego 70 dni
- \[Wersja zapoznawcza \] : Inspekcja maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Wersja zapoznawcza \] : wdrażanie wymagań w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które nie ograniczają minimalnej długości hasła do 14 znaków
- \[Wersja zapoznawcza \] : Inspekcja maszyn wirtualnych z systemem Windows, które zezwalają na ponowne korzystanie z poprzednich 24 haseł
- \[Wersja zapoznawcza \] : wdrażanie wymagań w celu przeprowadzenia inspekcji maszyn wirtualnych z systemem Windows, które zezwalają na ponowne używanie poprzednich 24 haseł

## <a name="103-and-1054-audit-generation"></a>10,3 i Generowanie inspekcji 10.5.4

Ten plan pomaga zapewnić, że zdarzenia systemowe są rejestrowane przez przypisanie [Azure Policy](../../../policy/overview.md) definicji, które Przeprowadź inspekcję ustawień dziennika w zasobach platformy Azure.
Dzienniki diagnostyczne zapewniają wgląd w operacje wykonywane w ramach zasobów platformy Azure. Dzienniki systemu Azure są oparte na zsynchronizowaniu zegarów wewnętrznych w celu utworzenia skorelowanego rekordu zdarzeń między zasobami.

- Inspekcja powinna być włączona w zaawansowanych ustawieniach zabezpieczeń danych na SQL Server
- Przeprowadzanie inspekcji ustawienia diagnostyki
- Inspekcja ustawień inspekcji poziomu serwera SQL
- Wdrażanie inspekcji na serwerach SQL
- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager
- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>Zabezpieczenia informacji 12.3.6 i 12.3.7

Ten plan ułatwia zarządzanie siecią i sterowanie nią przez przypisanie [Azure Policyych](../../../policy/overview.md) definicji, które przeprowadzają inspekcję akceptowalnych lokalizacji sieciowych i zatwierdzonych produktów firmy dozwolonych dla środowiska. Są one dostosowywane przez każdą firmę za pomocą parametrów zasad w ramach każdej z tych zasad.

- Dozwolone lokalizacje
- Dozwolone lokalizacje grup zasobów

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu mapowania kontroli planu PCI-DSS v 3.2.1 zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat omówienia i sposobu wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Plan PCI-DSS v 3.2.1 — omówienie](./index.md) 
>  [Plan PCI-DSS v 3.2.1 — etapy wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
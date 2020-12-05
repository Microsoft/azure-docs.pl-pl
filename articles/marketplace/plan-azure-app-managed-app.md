---
title: Planowanie aplikacji zarządzanej przez platformę Azure dla oferty aplikacji platformy Azure
description: Dowiedz się, co jest wymagane do utworzenia planu aplikacji zarządzanej dla nowej oferty aplikacji platformy Azure za pomocą portalu komercyjnej witryny Marketplace w centrum partnerskim firmy Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621420"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Planowanie aplikacji zarządzanej przez platformę Azure dla oferty aplikacji platformy Azure

Plan _aplikacji zarządzanej_ przez platformę Azure jest jednym ze sposobów publikowania oferty aplikacji platformy Azure w portalu Azure Marketplace. Jeśli jeszcze tego nie zrobiono, przeczytaj artykuł [Planowanie oferty aplikacji platformy Azure dla komercyjnej witryny Marketplace](plan-azure-application-offer.md).

Aplikacje zarządzane to oferty Transact wdrożone i rozliczane za pomocą witryny Azure Marketplace. Opcja wystaw, którą widzi użytkownik, otrzymuje teraz.

Użyj aplikacji platformy Azure: planu aplikacji zarządzanej, gdy wymagane są następujące warunki:

- Rozwiązanie oparte na subskrypcji zostanie wdrożone dla klienta za pomocą maszyny wirtualnej lub rozwiązania opartego na całej infrastrukturze jako usługi (IaaS).
- Ty lub Twój klient wymaga, aby rozwiązanie było zarządzane przez partnera. Na przykład partner może być integratorem systemów lub dostawcą usług zarządzanych (MSP).

## <a name="managed-application-offer-requirements"></a>Wymagania oferty zarządzanej aplikacji

| Wymagania | Szczegóły |
| ------------ | ------------- |
| Subskrypcja platformy Azure | Zarządzane aplikacje muszą być wdrożone w ramach subskrypcji klienta, ale mogą być zarządzane przez inną firmę. |
| Rozliczenia i pomiary | Zasoby są udostępniane w ramach subskrypcji platformy Azure klienta. Maszyny wirtualne korzystające z modelu płatności zgodnie z rzeczywistym użyciem są transakcyjne dla klienta przez firmę Microsoft i rozliczane za pośrednictwem subskrypcji platformy Azure klienta. <br><br> W przypadku maszyn wirtualnych służących do przenoszenia własnych licencji firma Microsoft rozlicza wszelkie koszty związane z infrastrukturą, które są naliczane w ramach subskrypcji klienta, ale bezpośrednio obciążają opłaty za Licencjonowanie oprogramowania. |
| Wirtualny dysk twardy (VHD) zgodny z platformą Azure | Maszyny wirtualne muszą być wbudowane w system Windows lub Linux. Aby uzyskać więcej informacji, zobacz:<br> • [Utwórz zasób techniczny maszyny wirtualnej platformy Azure](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (dla dysków VHD z systemem Windows).<br> •  [Dystrybucje systemu Linux zatwierdzone na platformie Azure](../virtual-machines/linux/endorsed-distros.md) (dla dysków VHD z systemem Linux). |
| Udział w zakresie użycia przez klienta | Wszystkie nowe oferty aplikacji platformy Azure muszą również obejmować identyfikator GUID [przypisywania użycia klienta przez partnera platformy Azure](azure-partner-customer-usage-attribution.md) . Aby uzyskać więcej informacji o przypisywaniu użycia klienta i sposobach ich włączania, zobacz temat przypisanie [użycia klienta przez partnera platformy Azure](azure-partner-customer-usage-attribution.md). |
| Pakiet wdrożeniowy | Wymagany jest pakiet wdrożeniowy, który umożliwi klientom wdrożenie planu. Jeśli tworzysz wiele planów, które wymagają tej samej konfiguracji technicznej, możesz użyć tego samego pakietu. Aby uzyskać szczegółowe informacje, zobacz następną sekcję: pakiet wdrożeniowy. |
|||

> [!NOTE]
> Zarządzane aplikacje muszą być wdrażane za pomocą witryny Azure Marketplace. Jeśli komunikacja z klientem jest istotna, skontaktuj się z zainteresowanymi klientami po włączeniu udostępniania potencjalnego klienta.

## <a name="deployment-package"></a>Pakiet wdrożeniowy

Pakiet wdrożeniowy zawiera wszystkie pliki szablonów potrzebne dla tego planu, a także dodatkowe zasoby, spakowane jako plik. zip.

Wszystkie aplikacje platformy Azure muszą zawierać te dwa pliki w folderze głównym archiwum. zip:

- Plik szablonu Menedżer zasobów o nazwie [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Ten szablon definiuje zasoby, które mają zostać wdrożone w ramach subskrypcji platformy Azure klienta. Przykłady szablonów Menedżer zasobów można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) lub w odpowiedniej repozytorium [szablonów szybkiego startu: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .
- Definicja interfejsu użytkownika dla środowiska tworzenia aplikacji platformy Azure o nazwie [createUiDefinition.jsna](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). W interfejsie użytkownika należy określić elementy, które umożliwiają klientom podanie wartości parametrów.

Maksymalne obsługiwane rozmiary plików:

- Do 1 GB całkowitego rozmiaru skompresowanego archiwum zip
- Do 1 GB dla dowolnego nieskompresowanego pliku w archiwum zip

Wszystkie nowe oferty aplikacji platformy Azure muszą również obejmować identyfikator GUID [przypisywania użycia klienta przez partnera platformy Azure](azure-partner-customer-usage-attribution.md) .

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Plan można opublikować w regionie publicznym platformy Azure, w regionie Azure Government lub w obu tych przypadkach. Przed opublikowaniem w celu [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)Przetestuj i zweryfikuj swój plan w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby skonfigurować i przetestować plan, zażądaj konta próbnego od [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/).

Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania. Azure Government używa fizycznie wyizolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).

Aby uzyskać listę krajów i regionów obsługiwanych przez komercyjną witrynę Marketplace, zobacz [dostępność geograficzna i obsługa waluty](marketplace-geo-availability-currencies.md).

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków, które je opisują. Mogą to być albo linki do swojej aukcji bezpośrednio w programie, jak i linki do opisów zgodności z ich własnymi witrynami sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

## <a name="choose-who-can-see-your-plan"></a>Wybierz, kto może zobaczyć Twój plan

Każdy plan można skonfigurować tak, aby był widoczny dla wszystkich (publicznych) lub tylko do określonych odbiorców (prywatnych). Możesz utworzyć do 100 planów, a nawet 45 z nich mogą być prywatne. Możesz chcieć utworzyć plan prywatny, aby zaoferować różne opcje cenowe lub konfiguracje techniczne dla określonych klientów.

Możesz udzielić dostępu do planu prywatnego przy użyciu identyfikatorów subskrypcji platformy Azure z opcją dołączenia opisu każdego przypisanego identyfikatora subskrypcji. Możesz dodać maksymalnie 10 identyfikatorów subskrypcji ręcznie lub maksymalnie 10 000 identyfikatorów subskrypcji przy użyciu. Plik CSV. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID i litery muszą być małymi literami.

Plany prywatne nie są obsługiwane w przypadku subskrypcji platformy Azure ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP). Aby uzyskać więcej informacji, zobacz [prywatne oferty w komercyjnym portalu Microsoft Marketplace](private-offers.md).

> [!NOTE]
> W przypadku opublikowania planu prywatnego można później zmienić jego widoczność na publiczną. Jednak po opublikowaniu planu publicznego nie można zmienić jego widoczności na prywatną.

## <a name="define-pricing"></a>Definiowanie cen

Dla każdego planu należy podać cenę miesięcznie. Ta cena jest uzupełnieniem usługi Azure Infrastructure lub kosztów związanych z płatność zgodnie z rzeczywistym użyciem w przypadku zasobów wdrożonych w ramach tego rozwiązania.

Oprócz ceny za miesiąc można także ustawić ceny za użycie jednostek niestandardowych przy użyciu [rozliczeń naliczanych](partner-center-portal/azure-app-metered-billing.md). Cena za miesiąc można ustawić na zero, a opłaty za korzystanie wyłącznie z naliczanych opłat.

Ceny są ustawiane w USD (USD = Stany Zjednoczone dolara) są konwertowane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących kursów wymiany przy zapisywaniu. Można jednak wybrać opcję określenia cen klientów dla każdego rynku.

## <a name="just-in-time-jit-access"></a>Dostęp just in Time (JIT)

Dostęp JIT umożliwia żądanie dostępu z podwyższonym poziomem uprawnień do zasobów aplikacji zarządzanej w celu rozwiązywania problemów lub konserwacji. Zawsze masz dostęp tylko do odczytu do zasobów, ale przez określony okres możesz mieć większy dostęp. Aby uzyskać więcej informacji, zobacz [Włączanie i żądanie dostępu just in Time do Azure Managed Applications](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> Pamiętaj, aby zaktualizować `createUiDefinition.json` plik w celu obsługi tej funkcji.

## <a name="deployment-mode"></a>Tryb wdrożenia

Plan aplikacji zarządzanej można skonfigurować tak, aby korzystał z trybu wdrożenia **kompletnego** lub **przyrostowego** . W trybie kompletnym ponowne wdrożenie aplikacji przez klienta skutkuje usunięciem zasobów w zarządzanej grupie zasobów, jeśli zasoby nie są zdefiniowane w [mainTemplate.jsna](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). W trybie przyrostowym ponowne wdrożenie aplikacji pozostawia istniejące zasoby bez zmian. Aby dowiedzieć się więcej, zobacz [Azure Resource Manager trybu wdrożenia](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>Adres URL punktu końcowego powiadomienia

Opcjonalnie możesz udostępnić punkt końcowy elementu webhook HTTPS, aby otrzymywać powiadomienia o wszystkich operacjach CRUD na wystąpieniach aplikacji zarządzanej planu.

## <a name="customize-allowed-customer-actions-optional"></a>Dostosuj dozwolone akcje klienta (opcjonalnie)

Opcjonalnie można określić, które akcje mogą wykonywać klienci na zarządzanych zasobach oprócz `*/read` akcji, które są domyślnie dostępne.

W przypadku wybrania tej opcji należy podać akcje kontrolki lub dozwolone akcje danych albo oba te elementy. Aby uzyskać więcej informacji, zobacz [Omówienie przypisań Odmów dla zasobów platformy Azure](../role-based-access-control/deny-assignments.md). Aby uzyskać dostępne akcje, zobacz [Azure Resource Manager operacje dostawcy zasobów](../role-based-access-control/resource-provider-operations.md). Na przykład, aby zezwolić użytkownikom na ponowne uruchomienie maszyn wirtualnych, Dodaj `Microsoft.Compute/virtualMachines/restart/action` do dozwolonych akcji.

## <a name="choose-who-can-manage-the-application"></a>Wybierz, kto może zarządzać aplikacją

Należy wskazać, kto może zarządzać zarządzaną aplikacją w każdej z wybranych chmur: _publiczną platformę Azure_ i _Azure Government chmurę_. Zbierz wymienione poniżej informacje.

- **Azure Active Directory identyfikator dzierżawy** — identyfikator dzierżawy usługi Azure AD (znany również jako identyfikator katalogu) zawierający tożsamości użytkowników, grup lub aplikacji, do których chcesz udzielić uprawnień. Identyfikator dzierżawy usługi Azure AD można znaleźć na Azure Portal w obszarze [właściwości Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).
- **Autoryzacje** — Dodaj identyfikator obiektu Azure Active Directory każdego użytkownika, grupy lub aplikacji, którym chcesz nadać uprawnienia do zarządzanej grupy zasobów. Zidentyfikuj użytkownika według identyfikatora podmiotu zabezpieczeń, który można znaleźć w [bloku Azure Active Directory użytkownicy na Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Dla każdego identyfikatora podmiotu zabezpieczeń zostanie skojarzona jedna z wbudowanych ról usługi Azure AD (właściciela lub współautora). Wybrana rola opisuje uprawnienia podmiotu zabezpieczeń w ramach zasobów w ramach subskrypcji klienta. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../role-based-access-control/built-in-roles.md). Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC), zobacz [Rozpoczynanie pracy z funkcją RBAC w Azure Portal](../role-based-access-control/overview.md).

> [!NOTE]
> Mimo że użytkownik może dodać do 100 autoryzacji w regionie świadczenia usługi Azure, zazwyczaj łatwiej jest utworzyć Active Directory grupę użytkowników i określić jej identyfikator w "Identyfikator podmiotu zabezpieczeń". Pozwala to na dodanie większej liczby użytkowników do grupy zarządzania po wdrożeniu planu i zmniejszenie potrzeb aktualizacji planu tylko w celu dodania większej liczby autoryzacji.

## <a name="policy-settings"></a>Ustawienia zasad

[Zasady platformy Azure](../governance/policy/index.yml) można zastosować do aplikacji zarządzanej, aby określić wymagania dotyczące zgodności dla wdrożonego rozwiązania. Definicje zasad i format wartości parametrów podano w artykule [Przykłady dla usługi Azure Policy](../governance/policy/samples/index.md).

Można skonfigurować maksymalnie pięć zasad i tylko jedno wystąpienie każdego typu zasad. Niektóre typy zasad wymagają dodatkowych parametrów.

| Typ zasad | Wymagane parametry zasad |
| ------------ | ------------- |
| Szyfrowanie Azure SQL Database | Nie |
| Ustawienia inspekcji usługi Azure SQL Server | Tak |
| Szyfrowanie Azure Data Lake Store | Nie |
| Inspekcja ustawień diagnostycznych | Tak |
| Inspekcja zgodności lokalizacji zasobów | Nie |
|||

Dla każdego dodawanego typu zasad należy skojarzyć standardowe lub bezpłatne jednostki SKU zasad. Dla zasad inspekcji wymagana jest standardowa jednostka SKU. Nazwy zasad są ograniczone do 50 znaków.

## <a name="next-steps"></a>Następne kroki

- [Jak utworzyć ofertę aplikacji platformy Azure w portalu komercyjnym](create-new-azure-apps-offer.md)

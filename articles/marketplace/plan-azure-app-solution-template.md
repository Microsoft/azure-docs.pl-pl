---
title: Planowanie szablonu rozwiązania dla oferty aplikacji platformy Azure
description: Dowiedz się, co jest wymagane do utworzenia planu szablonu rozwiązania dla nowej oferty aplikacji platformy Azure za pomocą portalu komercyjnej witryny Marketplace w centrum partnerskim firmy Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 3e4d0513808cdc44fc71e182a07fa6b050d182ee
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452519"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Planowanie szablonu rozwiązania dla oferty aplikacji platformy Azure

W tym artykule wyjaśniono wymagania dotyczące publikowania planu szablonu rozwiązania dla oferty aplikacji platformy Azure. Plan szablonu rozwiązania to jeden z dwóch typów planów obsługiwanych przez oferty aplikacji platformy Azure. Aby uzyskać informacje o różnicach między tymi dwoma typami planów, zobacz [typy planów](plan-azure-application-offer.md#plans). Jeśli jeszcze tego nie zrobiono, przeczytaj temat [Planowanie oferty aplikacji platformy Azure](plan-azure-application-offer.md).

Typ planu szablonu rozwiązania wymaga [szablonu Azure Resource Manager (szablon ARM)](../azure-resource-manager/templates/overview.md) , aby automatycznie wdrażać infrastrukturę rozwiązania.

## <a name="solution-template-requirements"></a>Wymagania dotyczące szablonów rozwiązań

| Wymagania | Szczegóły |
| ------------ | ------------- |
| Rozliczenia i pomiary | Plany szablonów rozwiązań nie są transakcyjne, ale mogą służyć do wdrażania płatnych ofert maszyn wirtualnych, które są rozliczane za pomocą komercyjnej witryny Microsoft Marketplace. Zasoby, które wdraża szablon ARM rozwiązania, są konfigurowane w subskrypcji platformy Azure klienta. Maszyny wirtualne z płatnością zgodnie z rzeczywistym użyciem są transakcyjne dla klienta przez firmę Microsoft i rozliczane za pośrednictwem subskrypcji platformy Azure klienta. <br><br> W przypadku rozliczeń związanych z licencją własną (BYOL), chociaż koszty infrastruktury firmy Microsoft, które są naliczane w ramach subskrypcji klienta, są naliczane bezpośrednio przez klienta. |
| Wirtualny dysk twardy (VHD) zgodny z platformą Azure | Maszyny wirtualne muszą być wbudowane w system Windows lub Linux. Aby uzyskać więcej informacji, zobacz:<ul><li>[Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (dla dysków VHD z systemem Windows)</li><li>[Dystrybucje systemu Linux zatwierdzone na platformie Azure](../virtual-machines/linux/endorsed-distros.md) (dla dysków VHD z systemem Linux).</li></ul> |
| Udział w zakresie użycia przez klienta | Włączenie przypisywania użycia klienta jest wymagane we wszystkich szablonach rozwiązań opublikowanych w witrynie Azure Marketplace. Aby uzyskać więcej informacji o przypisywaniu użycia klienta i sposobach ich włączania, zobacz temat przypisanie [użycia klienta przez partnera platformy Azure](azure-partner-customer-usage-attribution.md). |
| Korzystanie z dysków zarządzanych | Usługa [Managed disks](../virtual-machines/managed-disks-overview.md) jest opcją domyślną dla utrwalonych dysków maszyn wirtualnych infrastruktury jako usługi (IaaS) na platformie Azure. W szablonach rozwiązań należy używać dysków zarządzanych.<ul><li>Aby zaktualizować szablony rozwiązań, postępuj zgodnie ze wskazówkami w temacie [używanie dysków zarządzanych w Azure Resource Manager szablonach](../virtual-machines/using-managed-disks-template-deployments.md)i Użyj dostarczonych [przykładów](https://github.com/Azure/azure-quickstart-templates).</li><li>Aby opublikować dysk VHD jako obraz w portalu Azure Marketplace, zaimportuj podstawowy dysk VHD z dysków zarządzanych do konta magazynu przy użyciu [Azure PowerShell](../virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md) lub [interfejsu wiersza polecenia platformy Azure](../virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md)</ul> |
| Pakiet wdrożeniowy | Wymagany jest pakiet wdrożeniowy, który umożliwi klientom wdrożenie planu. Jeśli utworzysz wiele planów, które wymagają tej samej konfiguracji technicznej, możesz użyć tego samego pakietu planu. Aby uzyskać szczegółowe informacje, zobacz następną sekcję: pakiet wdrożeniowy. |
|||

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

> [!NOTE]
> W przypadku opublikowania planu prywatnego można później zmienić jego widoczność na publiczną. Jednak po opublikowaniu planu publicznego nie można zmienić jego widoczności na prywatną.

W przypadku planów szablonów rozwiązań można także wybrać opcję ukrycia planu w portalu Azure Marketplace. Można to zrobić, jeśli plan jest wdrażany tylko pośrednio przez inny szablon rozwiązania lub zarządzaną aplikację.

> [!NOTE]
> Plany prywatne nie są obsługiwane w przypadku subskrypcji platformy Azure ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP).

Aby uzyskać więcej informacji, zobacz [prywatne oferty w komercyjnym portalu Microsoft Marketplace](private-offers.md).

## <a name="next-steps"></a>Następne kroki

- [Jak utworzyć ofertę aplikacji platformy Azure w portalu komercyjnym](create-new-azure-apps-offer.md)
---
title: Koncepcje dotyczące DevTest Labs | Microsoft Docs
description: Zapoznaj się z podstawowymi pojęciami dotyczącymi usługi DevTest Labs oraz możliwością tworzenia i monitorowania maszyn wirtualnych platformy Azure oraz zarządzania nimi
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 43d62d637686a785cafd29aa311ccf20cb942721
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283847"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs — pojęcia
## <a name="overview"></a>Omówienie
Poniższa lista zawiera pojęcia i definicje dotyczące programu Key DevTest Labs:

## <a name="labs"></a>Laboratoria
Laboratorium to infrastruktura obejmująca grupę zasobów, na przykład Virtual Machines (VM), która umożliwia lepsze zarządzanie tymi zasobami przez określenie limitów i przydziałów.

## <a name="virtual-machine"></a>Maszyna wirtualna
Maszyna wirtualna platformy Azure jest jednym z kilku typów [skalowalnych zasobów obliczeniowych dostępnych na żądanie,](/azure/architecture/guide/technology-choices/compute-decision-tree) które są oferowane przez platformę Azure. Maszyny wirtualne platformy Azure zapewniają elastyczność wirtualizacji bez konieczności kupowania i konserwowania fizycznego sprzętu, który go uruchamia, chociaż nadal trzeba obsługiwać maszynę wirtualną przez wykonywanie określonych zadań, takich jak konfigurowanie, stosowanie poprawek i instalowanie oprogramowania, które jest na nim uruchomione.

[Omówienie maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/windows/overview.md) zawiera informacje o tym, co należy wziąć pod uwagę przed utworzeniem maszyny wirtualnej, sposobie jej tworzenia oraz sposobie zarządzania nią.

## <a name="claimable-vm"></a>Maszyna wirtualna do zajmowania
Maszyna wirtualna z funkcją domaganą na platformie Azure jest maszyną wirtualną, która jest dostępna do użycia przez dowolnego użytkownika laboratorium z uprawnieniami. Administrator laboratorium może przygotować maszyny wirtualne z określonymi obrazami podstawowymi i artefaktami, a następnie zapisać je w udostępnionej puli. Użytkownik laboratorium może następnie przejąć działającą maszynę wirtualną z puli, gdy potrzebują ich z tą określoną konfiguracją.

Maszyna wirtualna, do której można uzyskać dostęp, nie jest początkowo przypisana do żadnego konkretnego użytkownika, ale będzie wyświetlana na liście każdego użytkownika w obszarze "maszyny wirtualne objęte usługą". Gdy użytkownik zostanie przejęty przez użytkownika, jest on przenoszony do obszaru "moje maszyny wirtualne" i nie jest już możliwy do zajmowania przez żadnego innego użytkownika.

## <a name="environment"></a>Środowisko
W DevTest Labs środowisko odnosi się do kolekcji zasobów platformy Azure w laboratorium. [Ten wpis w blogu](./devtest-lab-faq.md#blog-post) omawia sposób tworzenia środowisk z obsługą wielu maszyn wirtualnych na podstawie szablonów Azure Resource Manager.

## <a name="base-images"></a>Obrazy podstawowe
Obrazy podstawowe są obrazami maszyn wirtualnych ze wszystkimi narzędziami i ustawieniami wstępnie zainstalowanymi i skonfigurowanymi do szybkiego tworzenia maszyn wirtualnych. Możesz zainicjować obsługę maszyny wirtualnej, wybierając istniejącą bazę i dodając artefakt, aby zainstalować agenta testowego. Można następnie zapisać zainicjowaną maszynę wirtualną jako podstawową, aby można było użyć podstawy bez konieczności ponownej instalacji agenta testowego dla każdej aprowizacji maszyny wirtualnej.

## <a name="artifacts"></a>Artifacts
Artefakty służą do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi maszyny wirtualnej. Artefakty mogą być następujące:

* Narzędzia, które chcesz zainstalować na maszynie wirtualnej, takie jak agenci, programu Fiddler i Visual Studio.
* Akcje, które mają zostać uruchomione na maszynie wirtualnej, takie jak klonowanie repozytorium.
* Aplikacje, które chcesz przetestować.

Artefakty to [Azure Resource Manager](../azure-resource-manager/management/overview.md) pliki JSON, które zawierają instrukcje dotyczące wykonywania wdrożenia i stosowania konfiguracji.

## <a name="artifact-repositories"></a>Repozytoria artefaktów
Repozytoria artefaktów to repozytoria Git, w których są zaewidencjonowane artefakty. Repozytoria artefaktów można dodawać do wielu laboratoriów w organizacji, umożliwiając ponowne użycie i udostępnianie.

## <a name="formulas"></a>Formuły
Formuły, oprócz obrazów podstawowych, zapewniają mechanizm szybkiej aprowizacji maszyn wirtualnych. Formuła w DevTest Labs jest listą domyślnych wartości właściwości używanych do tworzenia maszyny wirtualnej laboratorium.
Dzięki formułom maszyny wirtualne z tym samym zestawem właściwości — takie jak obraz podstawowy, rozmiar maszyny wirtualnej, Sieć wirtualna i artefakty — można utworzyć bez konieczności określania tych właściwości za każdym razem. Podczas tworzenia maszyny wirtualnej na podstawie formuły wartości domyślne mogą być używane jako-jest lub modyfikowane.

## <a name="policies"></a>Zasady
Zasady pomagają w kontrolowaniu kosztów w laboratorium. Można na przykład utworzyć zasady, aby automatycznie zamykać maszyny wirtualne na podstawie zdefiniowanego harmonogramu.

## <a name="caps"></a>Czapki
Cap to mechanizm minimalizowania strat w laboratorium. Na przykład można ustawić limit, aby ograniczyć liczbę maszyn wirtualnych, które mogą być tworzone dla poszczególnych użytkowników lub w środowisku laboratoryjnym.

## <a name="security-levels"></a>Poziomy zabezpieczeń
Dostęp zabezpieczeń jest określany przez kontrolę dostępu opartą na rolach (Azure RBAC). Aby zrozumieć, jak działa dostęp, ułatwia zrozumienie różnic między uprawnieniami, rolą i zakresem zdefiniowanym przez RBAC.

* Uprawnienie — uprawnienie to zdefiniowany dostęp do określonej akcji (np. dostęp do odczytu do wszystkich maszyn wirtualnych).
* Rola — rola to zestaw uprawnień, które mogą być pogrupowane i przypisywane do użytkownika. Na przykład rola *właściciela subskrypcji* ma dostęp do wszystkich zasobów w ramach subskrypcji.
* Zakres — zakres to poziom w hierarchii zasobu platformy Azure, na przykład grupa zasobów, pojedyncze laboratorium lub cała subskrypcja.

W zakresie DevTest Labs istnieją dwa typy ról do definiowania uprawnień użytkowników: właściciel laboratorium i użytkownik laboratorium.

* Właściciel laboratorium — właściciel laboratorium ma dostęp do wszystkich zasobów w środowisku laboratoryjnym. W związku z tym właściciel laboratorium może zmodyfikować zasady, odczytać i napisać wszystkie maszyny wirtualne, zmienić sieć wirtualną itd.
* Użytkownik laboratorium — użytkownik laboratorium może wyświetlać wszystkie zasoby laboratorium, takie jak maszyny wirtualne, zasady i sieci wirtualne, ale nie może modyfikować zasad ani żadnych maszyn wirtualnych utworzonych przez innych użytkowników.

Aby dowiedzieć się, jak utworzyć role niestandardowe w DevTest Labs, zapoznaj się z artykułem [Udziel uprawnień użytkownikom do określonych zasad laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Ponieważ zakresy są hierarchiczne, gdy użytkownik ma uprawnienia w określonym zakresie, są automatycznie udzielane im uprawnienia do każdego zakresu niższego poziomu. Na przykład jeśli użytkownik jest przypisany do roli właściciela subskrypcji, ma dostęp do wszystkich zasobów w ramach subskrypcji, w tym wszystkich maszyn wirtualnych, wszystkich sieci wirtualnych i wszystkich laboratoriów. W związku z tym właściciel subskrypcji automatycznie dziedziczy rolę właściciela laboratorium. Jednak przeciwieństwo nie jest prawdziwe. Właściciel laboratorium ma dostęp do laboratorium, który jest niższym zakresem niż poziom subskrypcji. W związku z tym właściciel laboratorium nie będzie mógł wyświetlać maszyn wirtualnych ani sieci wirtualnych ani żadnych zasobów poza laboratorium.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Wszystkie koncepcje omówione w tym artykule można skonfigurować za pomocą szablonów Azure Resource Manager, które umożliwiają zdefiniowanie infrastruktury/konfiguracji rozwiązania platformy Azure i wielokrotne wdrożenie w spójnym stanie.

[Zrozumienie struktury i składni szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md#template-format) opisuje strukturę szablonu Azure Resource Manager i właściwości, które są dostępne w różnych sekcjach szablonu.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
[Tworzenie laboratorium w usłudze DevTest Labs](devtest-lab-create-lab.md)

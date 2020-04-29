---
title: Zmień jednostkę SKU dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, w jaki sposób do warstwy SKU dla Azure AD Domain Servicesej domeny zarządzanej w przypadku zmiany wymagań firmy
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655585"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Zmień jednostkę SKU dla istniejącej Azure AD Domain Services domeny zarządzanej

W Azure Active Directory Domain Services (Azure AD DS) dostępna wydajność i funkcje są oparte na typie jednostki SKU. Te różnice między funkcjami obejmują częstotliwość tworzenia kopii zapasowych lub maksymalną liczbę jednokierunkowych relacji zaufania między lasami (obecnie w wersji zapoznawczej). Podczas tworzenia domeny zarządzanej można wybrać jednostkę SKU, a jednostki SKU można zmienić w górę lub w dół, gdy zostanie wdrożona domena zarządzana. Zmiany wymagań firmy mogą obejmować konieczność wykonywania kilku często wykonywanych kopii zapasowych lub tworzenia dodatkowych relacji zaufania lasów. Aby uzyskać więcej informacji na temat limitów i cen różnych jednostek SKU, zobacz temat [azure AD DS SKU — koncepcje][concepts-sku] i [cennik usługi Azure AD DS][pricing] .

W tym artykule opisano sposób zmiany jednostki SKU dla istniejącej domeny zarządzanej AD DS platformy Azure przy użyciu Azure Portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby Uzupełnij samouczek, aby [utworzyć i skonfigurować wystąpienie Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Ograniczenia zmiany jednostki SKU

Po wdrożeniu domeny zarządzanej usługi Azure AD DS można zmienić jednostki SKU w górę lub w dół. Jeśli jednak korzystasz z lasu zasobów (obecnie w wersji zapoznawczej) i utworzono jednokierunkowe zaufania lasu wychodzącego z usługi Azure AD DS do środowiska lokalnego AD DS, istnieją pewne ograniczenia dotyczące operacji zmiany jednostki SKU. Jednostki SKU w *warstwie Premium* i *Enterprise* definiują limit liczby relacji zaufania, które można utworzyć. Nie można zmienić jednostki SKU z niższym maksymalnym limitem niż aktualnie skonfigurowany.

Przykład:

* Jeśli utworzono dwa zaufania lasów w jednostce SKU *Premium* , nie można zmienić w dół do *standardowej* jednostki SKU. *Standardowa* jednostka SKU nie obsługuje relacji zaufania lasów.
* Lub, jeśli utworzono siedem relacji zaufania w jednostce SKU *Premium* , nie można zmienić w dół jednostki SKU *przedsiębiorstwa* . Jednostka SKU *przedsiębiorstwa* obsługuje maksymalnie pięć relacji zaufania.

Aby uzyskać więcej informacji na temat tych limitów, zobacz [funkcje i limity usługi Azure AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Wybierz nową jednostkę SKU

Aby zmienić jednostkę SKU dla domeny zarządzanej AD DS platformy Azure przy użyciu Azure Portal, wykonaj następujące czynności:

1. W górnej części Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**. Wybierz domenę zarządzaną z listy, na przykład *aaddscontoso.com*.
1. W menu po lewej stronie strony AD DS platformy Azure wybierz pozycję **ustawienia > jednostka SKU**.

    ![Wybierz opcję menu SKU dla domeny zarządzanej platformy Azure AD DS w Azure Portal](media/change-sku/overview-change-sku.png)

1. Z menu rozwijanego wybierz jednostkę SKU dla domeny zarządzanej platformy Azure AD DS. Jeśli masz Las zasobów, nie możesz wybrać opcji *standardowa* jednostka SKU, ponieważ zaufania lasów są dostępne tylko w jednostce SKU *przedsiębiorstwa* lub wyższej.

    Wybierz żądaną jednostkę SKU z menu rozwijanego, a następnie wybierz pozycję **Zapisz**.

    ![Wybierz żądaną jednostkę SKU z menu rozwijanego w Azure Portal](media/change-sku/change-sku-selection.png)

Zmiana typu jednostki SKU może potrwać minutę lub dwa.

## <a name="next-steps"></a>Następne kroki

Jeśli masz Las zasobów i chcesz utworzyć dodatkowe relacje zaufania po zmianie jednostki SKU, zobacz [Tworzenie zaufania do domeny lokalnej w usłudze Azure AD DS (wersja zapoznawcza)][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/

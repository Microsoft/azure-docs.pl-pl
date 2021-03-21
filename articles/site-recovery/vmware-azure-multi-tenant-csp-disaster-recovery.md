---
title: Skonfiguruj odzyskiwanie po awarii programu VMware na platformie Azure w środowisku z obsługą wielu dzierżawców przy użyciu Site Recovery i programu Cloud Solution Provider (CSP) | Microsoft Docs
description: Opisuje sposób konfigurowania odzyskiwania po awarii programu VMware w środowisku z wieloma dzierżawcami przy użyciu Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654953"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Konfigurowanie odzyskiwania po awarii oprogramowania VMware w środowisku wielodostępnym za pomocą programu Cloud Solution Provider (CSP)

[Program CSP](https://partner.microsoft.com/cloud-solution-provider) wspiera lepsze scenariusze dla usług w chmurze firmy Microsoft, w tym Microsoft 365, pakiet Enterprise Mobility Suite i Microsoft Azure. W przypadku dostawcy usług kryptograficznych partnerzy są właścicielami kompleksowych relacji z klientami i stają się głównym punktem kontaktu z relacją. Partnerzy mogą wdrażać subskrypcje platformy Azure dla klientów i łączyć subskrypcje z własnymi dodanymi, dostosowanymi ofertami.

Za pomocą [Azure Site Recovery](site-recovery-overview.md), jako partnerzy mogą zarządzać odzyskiwaniem po awarii dla klientów bezpośrednio przez dostawcę usług kryptograficznych. Alternatywnie możesz użyć dostawcy usług kryptograficznych, aby skonfigurować środowiska Site Recovery i umożliwić klientom zarządzanie własnymi potrzebami odzyskiwania po awarii w sposób samoobsługowy. W obu scenariuszach partnerzy są kontaktami między Site Recovery i klientami. Partnerzy usługi mogą uzyskać relację z klientem i rozliczać klientów za Site Recovery użycie.

W tym artykule opisano, jak partner może tworzyć subskrypcje dzierżawy i zarządzać nimi za pomocą dostawcy usług kryptograficznych w przypadku scenariusza replikacji VMware z wieloma dzierżawcami.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować replikację VMware, należy wykonać następujące czynności:

- [Przygotuj](tutorial-prepare-azure.md) Zasoby platformy Azure, w tym subskrypcja platformy Azure, Sieć wirtualna platformy Azure i konto magazynu.
- [Przygotuj](vmware-azure-tutorial-prepare-on-premises.md) lokalne serwery i maszyny wirtualne VMware.
- Dla każdej dzierżawy Utwórz oddzielny serwer zarządzania, który może komunikować się z maszynami wirtualnymi dzierżawcy i serwerami vCenter. Tylko użytkownik jako partner powinien mieć prawa dostępu do tego serwera zarządzania. Dowiedz się więcej o [środowiskach z wieloma dzierżawcami](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Utwórz konto dzierżawy

1. Korzystając z [Centrum partnerskiego firmy Microsoft](https://partnercenter.microsoft.com/), zaloguj się do swojego konta programu CSP.
2. W menu **pulpit nawigacyjny** wybierz pozycję **klienci**.
3. Na stronie, która zostanie otwarta, kliknij przycisk **Dodaj klienta** .
4. Na stronie **Nowy klient** wprowadź szczegóły informacji o koncie dla dzierżawy.

    ![Strona informacje o koncie](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Następnie kliknij przycisk **Dalej: subskrypcje**.
6. Na stronie Wybór subskrypcji zaznacz pole wyboru **Microsoft Azure** . Możesz dodać inne subskrypcje teraz lub w dowolnym innym czasie.
7. Na stronie **Przegląd** Potwierdź szczegóły dzierżawy, a następnie kliknij przycisk **Prześlij**.
8. Po utworzeniu konta dzierżawy zostanie wyświetlona strona potwierdzenia wyświetlająca szczegóły konta domyślnego i hasła dla tej subskrypcji. Zapisz informacje i w razie potrzeby zmień hasło, korzystając ze strony logowania Azure Portal.

Te informacje można udostępnić dzierżawie, jeśli jest to konieczne. w razie potrzeby możesz utworzyć osobne konto i udostępnić je.

## <a name="access-the-tenant-account"></a>Dostęp do konta dzierżawy

Możesz uzyskać dostęp do subskrypcji dzierżawcy za pomocą pulpitu nawigacyjnego Centrum partnerskiego firmy Microsoft.

1. Na stronie **klienci** kliknij nazwę konta dzierżawcy.
2. Na stronie **subskrypcje** konta dzierżawy możesz monitorować istniejące subskrypcje kont i dodawać do nich więcej subskrypcji, zgodnie z potrzebami.
3. Aby zarządzać operacjami odzyskiwania po awarii dla dzierżawy, wybierz pozycję **wszystkie zasoby (Azure Portal)**. Dzięki temu można uzyskać dostęp do subskrypcji platformy Azure dla dzierżawy.

    ![Link wszystkie zasoby](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Aby sprawdzić dostęp, kliknij link Azure Active Directory w prawym górnym rogu Azure Portal.

    ![Azure Active Directory łącze](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Teraz można wykonywać wszystkie Site Recovery operacje dla dzierżawy w Azure Portal i zarządzać nimi. Aby uzyskać dostęp do subskrypcji dzierżawy za pomocą dostawcy usług kryptograficznych w celu zarządzania odzyskiwaniem po awarii, wykonaj czynności opisane wcześniej.

## <a name="assign-tenant-access-to-the-subscription"></a>Przypisywanie dostępu dzierżawców do subskrypcji

1. Upewnij się, że skonfigurowano infrastrukturę odzyskiwania po awarii. Partnerzy uzyskują dostęp do subskrypcji dzierżawy za pomocą portalu CSP, niezależnie od tego, czy odzyskiwanie awaryjne jest zarządzane, czy samoobsługowe. Skonfiguruj magazyn i zarejestruj infrastrukturę w ramach subskrypcji dzierżawy.
1. Podaj dzierżawcę przy użyciu [utworzonego konta](#create-a-tenant-account).
1. Nowego użytkownika można dodać do subskrypcji dzierżawy za pomocą portalu CSP w następujący sposób:

    1. Przejdź do strony subskrypcja dostawcy CSP dzierżawy, a następnie wybierz opcję **Użytkownicy i licencje** .

       ![Strona subskrypcji dostawcy CSP dla dzierżawy](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Teraz Utwórz nowego użytkownika, wprowadzając odpowiednie szczegóły i wybierając uprawnienia lub przekazując listę użytkowników w pliku CSV.

    1. Po utworzeniu nowego użytkownika Wróć do Azure Portal. Na stronie **subskrypcja** wybierz odpowiednią subskrypcję.

    1. Wybierz pozycję **Kontrola dostępu (IAM)**, a następnie kliknij pozycję **przypisania ról**.

    1. Kliknij pozycję **Dodaj przypisanie roli** , aby dodać użytkownika z odpowiednim poziomem dostępu. Użytkownicy utworzeni za pomocą portalu CSP są wyświetlani na karcie przypisania ról.

        ![Dodawanie użytkownika](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- W przypadku większości operacji zarządzania jest wystarczająca rola *współautor* . Użytkownicy mający ten poziom dostępu mogą wykonywać wszystkie czynności w ramach subskrypcji, z wyjątkiem poziomów dostępu zmiana (dla których wymagany jest dostęp na poziomie *właściciela*).
- Site Recovery również ma trzy [wstępnie zdefiniowane role użytkownika](site-recovery-role-based-linked-access-control.md), których można użyć do dodatkowego ograniczenia poziomów dostępu zgodnie z potrzebami.

## <a name="multi-tenant-environments"></a>Środowiska z wieloma dzierżawcami

Istnieją trzy główne modele z wieloma dzierżawcami:

* **Udostępniony dostawca usług hostingowych (HSP)**: partner jest właścicielem infrastruktury fizycznej i używa udostępnionych zasobów (vCenter, centrów danych, magazynu fizycznego itd.) do hostowania wielu maszyn wirtualnych dzierżawcy w tej samej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną lub w ramach samoobsługowego rozwiązania.

* **Dedykowany dostawca usług hostingowych**: partner jest właścicielem infrastruktury fizycznej, ale używa dedykowanych zasobów (wielu vCenter, fizycznych magazynów danych itd.) w celu hostowania maszyn wirtualnych każdej dzierżawy w oddzielnej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną, a Dzierżawca może być jego własnością jako samoobsługowe rozwiązanie.

* **Dostawca usług zarządzanych (msp)**: klient jest właścicielem infrastruktury fizycznej, która hostuje maszyny wirtualne, a partner zapewnia obsługę i zarządzanie odzyskiwaniem po awarii.

Konfigurując subskrypcje dzierżawców zgodnie z opisem w tym artykule, można szybko rozpocząć Włączanie klientów w dowolnym z odpowiednich modeli wielodostępnych. Więcej informacji o różnych modelach wielodostępnych i włączeniu lokalnych kontroli dostępu można znaleźć [tutaj](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](site-recovery-role-based-linked-access-control.md) , aby zarządzać wdrożeniami Azure Site Recovery.
- Dowiedz się więcej o [architekturze replikacji](vmware-azure-architecture.md)oprogramowania VMware do platformy Azure.
- [Zapoznaj się z samouczkiem](vmware-azure-tutorial.md) dotyczącym replikowania maszyn wirtualnych VMware na platformę Azure.
Dowiedz się więcej o [środowiskach wielodostępnych](vmware-azure-multi-tenant-overview.md) służących do replikowania maszyn wirtualnych VMware na platformę Azure.

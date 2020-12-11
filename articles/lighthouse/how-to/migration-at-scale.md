---
title: Zarządzanie projektami migracji na dużą skalę za pomocą Azure Migrate
description: Dowiedz się, jak efektywnie korzystać z Azure Migrate na delegowanych zasobach klientów.
ms.date: 12/4/2020
ms.topic: how-to
ms.openlocfilehash: 16b92f3aa4dc3bfcb71eb232170c4df30348f8db
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095393"
---
# <a name="manage-migration-projects-at-scale-with-azure-migrate"></a>Zarządzanie projektami migracji na dużą skalę za pomocą Azure Migrate

Jako dostawca usług możesz dołączyć wielu dzierżawców klientów do [usługi Azure Lighthouse](../overview.md). Usługa Azure Lighthouse umożliwia dostawcom usług wykonywanie operacji na dużą skalę w wielu dzierżawach Azure Active Directory (Azure AD) jednocześnie, co sprawia, że zadania zarządzania są bardziej wydajne.

[Azure Migrate](../../migrate/migrate-services-overview.md) udostępnia scentralizowany centrum do oceny i migracji do lokalnych serwerów, infrastruktury, aplikacji i danych platformy Azure. Zwykle partnerzy, którzy przeprowadzają oceny i migrację na dużą skalę dla wielu klientów, muszą uzyskać dostęp osobno dla każdej subskrypcji klienta przy użyciu [modelu subskrypcji CSP (dostawcy rozwiązań w chmurze)](/partner-center/customers-revoke-admin-privileges) lub przez [utworzenie użytkownika-gościa w dzierżawie klienta](/azure/active-directory/external-identities/what-is-b2b).

Integracja usługi Azure Lighthouse z usługą Azure Migrate umożliwia dostawcom usług wykrywanie, ocenianie i migrowanie obciążeń dla różnych klientów na dużą skalę, a jednocześnie pozwala klientom na pełny wgląd w swoje środowiska i kontrolę nad nimi. Za pośrednictwem zarządzania zasobami delegowanymi przez platformę Azure dostawcy usług mają jeden widok wszystkich projektów Azure Migrate zarządzanych przez wielu dzierżawców klientów.

> [!NOTE]
> Za pośrednictwem usługi Azure Lighthouse partnerzy mogą przeprowadzić odnajdywanie, ocenę i migrację lokalnych maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych i wystąpień AWS/GCP. Dostępne są dwie opcje [migracji maszyn wirtualnych VMware](../../migrate/server-migrate-overview.md). Obecnie podczas pracy nad projektem migracji w delegowanej subskrypcji klienta może być używana tylko Metoda migracji oparta na agentach. Migracja przy użyciu replikacji bez wykorzystania agentów nie jest obecnie obsługiwana przez delegowany dostęp do zakresu klienta.

Ten temat zawiera omówienie sposobu używania [Azure Migrate](../../migrate/migrate-services-overview.md) w skalowalny sposób.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, te wskazówki dotyczą również [przedsiębiorstw korzystających z usługi Azure Lighthouse do zarządzania wieloma dzierżawcami](../concepts/enterprise.md).

W zależności od danego scenariusza warto utworzyć Azure Migrate w dzierżawie klienta lub w dzierżawie zarządzającej. Zapoznaj się z poniższymi zagadnieniami i ustal, który model najlepiej pasuje do potrzeb związanych z migracją klienta.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Tworzenie projektu Azure Migrate w dzierżawie klienta

Jedną z opcji korzystania z usługi Azure Lighthouse jest utworzenie projektu Azure Migrate w dzierżawie klienta. Użytkownicy w dzierżawie zarządzającej mogą następnie wybrać subskrypcję klienta podczas tworzenia projektu migracji. Od dzierżawy zarządzającej dostawca usług może wykonać niezbędne operacje migracji. Może to obejmować wdrożenie urządzenia Azure Migrate w celu odnajdywania obciążeń, oceniania obciążeń przez grupowanie maszyn wirtualnych i obliczanie kosztów związanych z chmurą, przeglądanie gotowości maszyn wirtualnych i przeprowadzanie migracji.

W tym scenariuszu żadne zasoby nie zostaną utworzone i zapisane w dzierżawie zarządzającej, nawet jeśli kroki odnajdywania i oceny mogą być inicjowane i wykonywane z tej dzierżawy. Wszystkie zasoby, takie jak projekty migracji, raporty oceny dla obciążeń Premium i zmigrowane zasoby w docelowym miejscu docelowej, zostaną wdrożone w ramach subskrypcji klienta. Jednak dostawca usług może uzyskać dostęp do wszystkich projektów klientów z własnego środowiska dzierżawców i portalu.

Takie podejście minimalizuje przełączanie kontekstu dla dostawców usług pracujących w wielu klientach, a jednocześnie pozwala klientom na przechowywanie wszystkich zasobów w swoich dzierżawach.

Przepływ pracy dla tego modelu będzie podobny do następującego:

1. Klient jest dołączany [do usługi Azure Lighthouse](onboard-customer.md). Rola wbudowana współautor jest wymagana dla tożsamości, która będzie używana z Azure Migrate. Zobacz przykładowy szablon [delegowani-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) , aby zapoznać się z przykładem z tą rolą.
1. Wskazany użytkownik loguje się do dzierżawy zarządzającej w Azure Portal, a następnie przechodzi do Azure Migrate. Ten użytkownik [tworzy projekt Azure Migrate](/azure/migrate/create-manage-projects), wybierając odpowiednią delegowaną subskrypcję klienta.
1. Następnie użytkownik [wykonuje kroki odnajdywania i oceny](../../migrate/tutorial-discover-vmware.md).

   W przypadku maszyn wirtualnych VMware przed skonfigurowaniem urządzenia można ograniczyć odnajdywanie do vCenter Server centrów danych, klastrów, folderu klastrów, hostów, folderu hostów lub poszczególnych maszyn wirtualnych. Aby ustawić zakres, przypisz uprawnienia do konta używanego przez urządzenie w celu uzyskania dostępu do vCenter Server. Jest to przydatne, jeśli wiele maszyn wirtualnych klientów jest hostowanych w funkcji hypervisor. Nie można ograniczyć zakresu odnajdywania funkcji Hyper-V.

    > [!NOTE]
    > Można odnajdywać i oceniać maszyny wirtualne VMware do migracji przy użyciu Azure Migrate przez delegowany dostęp udostępniony przez usługę Azure Lighthouse. W przypadku migracji maszyn wirtualnych VMware tylko Metoda oparta na agencie jest obecnie obsługiwana podczas pracy nad projektem migracji w delegowanej subskrypcji klienta.

1. Gdy docelowa subskrypcja klienta jest gotowa, Kontynuuj migrację przez dostęp przyznany przez usługę Azure Lighthouse. Projekt migracji zawierający wyniki oceny i zmigrowane zasoby zostaną utworzone w dzierżawie klienta w ramach subskrypcji docelowej.

> [!TIP]
> Przed migracją należy wdrożyć strefę wyładunkowej w celu aprowizacji zasobów infrastruktury programu Foundation i przygotować subskrypcję, do której zostaną zmigrowane maszyny wirtualne. Aby uzyskać dostęp do niektórych zasobów w tej strefie docelowej lub je utworzyć, może być wymagana wbudowana rola właściciela, która nie jest obecnie obsługiwana w usłudze Azure Lighthouse. W takich scenariuszach klient może potrzebować zapewnić rolę dostępu gościa lub delegować dostęp administratora za pośrednictwem modelu subskrypcji CSP. Aby dowiedzieć się, jak tworzyć strefy wyładunkowe z wieloma dzierżawcami, zapoznaj się z [rozwiązaniem demonstracyjnym z wielodostępnymi strefami](https://github.com/Azure/Multi-tenant-Landing-Zones) w witrynie GitHub.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Tworzenie projektu Azure Migrate w dzierżawie zarządzającej

W tym scenariuszu operacje związane z migracją, takie jak odnajdywanie i ocenianie, są nadal wykonywane przez użytkowników w dzierżawie zarządzającej. Jednak projekt migracji i wszystkie odpowiednie zasoby będą znajdować się w dzierżawie partnera, a klient nie będzie miał bezpośredniego wglądu w projekt (w razie potrzeby oceny mogą być udostępniane klientom). Lokalizacją docelową migracji każdego klienta będzie subskrypcja klienta.

Takie podejście umożliwia dostawcom usług szybkie uruchamianie projektów do wykrywania i oceny migracji, a tym samym wstępne kroki od subskrypcji klienta i dzierżawców.

Przepływ pracy dla tego modelu będzie podobny do następującego:

1. Klient jest dołączany [do usługi Azure Lighthouse](onboard-customer.md). Rola wbudowana współautor jest wymagana dla tożsamości, która będzie używana z Azure Migrate. Zobacz przykładowy szablon [delegowani-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) , aby zapoznać się z przykładem z tą rolą.
1. Wskazany użytkownik loguje się do dzierżawy zarządzającej w Azure Portal, a następnie przechodzi do Azure Migrate. Ten użytkownik [tworzy projekt Azure Migrate](/azure/migrate/create-manage-projects) w ramach subskrypcji należącej do dzierżawy zarządzającej.
1. Następnie użytkownik [wykonuje kroki odnajdywania i oceny](../../migrate/tutorial-discover-vmware.md). Lokalne maszyny wirtualne będą odnajdywane i oceniane w projekcie migracji utworzonym w dzierżawie zarządzającej, a następnie migrowane z tej lokalizacji.

   Jeśli zarządzasz wieloma klientami na tym samym hoście funkcji Hyper-V, możesz odnaleźć wszystkie obciążenia jednocześnie. Maszyny wirtualne specyficzne dla klienta można wybrać w tej samej grupie, a następnie można utworzyć ocenę i przeprowadzić migrację, wybierając odpowiednią subskrypcję klienta jako lokalizację docelową. Nie ma potrzeby ograniczania zakresu odnajdywania i można zachować pełną przegląd wszystkich obciążeń klientów w jednym projekcie migracji.

1. Gdy wszystko będzie gotowe, Kontynuuj migrację, wybierając delegowaną subskrypcję klienta jako lokalizację docelową replikacji i migracji obciążeń. Nowo utworzone zasoby będą dostępne w ramach subskrypcji klienta, a dane oceny i zasoby odnoszące się do projektu migracji pozostaną w dzierżawie zarządzającej.

Uwaga: przed wdrożeniem należy zmodyfikować plik parametrów w celu odzwierciedlenia jego środowiska https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Rozpoznawanie partnerów dla migracji klientów

Jako członek [Microsoft Partner Network](https://partner.microsoft.com)można połączyć swój identyfikator partnera z poświadczeniami używanymi do zarządzania delegowanymi zasobami klientów. Za pośrednictwem linku administratora partnera (PAL) firma Microsoft może mieć wpływ na dochody i wykorzystanie platformy Azure dla organizacji na podstawie zadań wykonywanych dla klientów, w tym projektów migracji.

Aby uzyskać więcej informacji, zobacz [Łączenie identyfikatora partnera w celu śledzenia wpływu na delegowane zasoby](partner-earned-credit.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure Migrate](../../migrate/migrate-services-overview.md).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).


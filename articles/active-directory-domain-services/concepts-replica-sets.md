---
title: Replika koncepcji dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jakie zestawy replik znajdują się w Azure Active Directory Domain Services oraz jak zapewnia nadmiarowość dla aplikacji, które wymagają usług Identity Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: joflore
ms.openlocfilehash: 499f4df303993d97ebb4eb38de98828b085aff00
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961072"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services-preview"></a>Funkcja Replica ustawia koncepcje i funkcje Azure Active Directory Domain Services (wersja zapoznawcza)

Podczas tworzenia domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS) należy zdefiniować unikatową przestrzeń nazw. Ta przestrzeń nazw jest nazwą domeny, na przykład *aaddscontoso.com*, a dwa kontrolery domeny (DC) są wdrażane w wybranym regionie platformy Azure. To wdrożenie kontrolerów domeny jest znane jako zestaw replik.

Można rozszerzyć domenę zarządzaną, aby mieć więcej niż jeden zbiór replik dla dzierżawy usługi Azure AD. Zestawy replik można dodawać do dowolnej komunikacji równorzędnej sieci wirtualnej w dowolnym regionie świadczenia usługi Azure, który obsługuje usługę Azure AD DS. Dodatkowe zestawy replik w różnych regionach platformy Azure zapewniają geograficzne odzyskiwanie po awarii dla starszych aplikacji, jeśli region platformy Azure przejdzie w tryb offline.

Zestawy replik są obecnie dostępne w wersji zapoznawczej.

> [!NOTE]
> Zestawy replik nie umożliwiają wdrażania wielu unikatowych domen zarządzanych w jednej dzierżawie platformy Azure. Każdy zestaw replik zawiera te same dane.

## <a name="how-replica-sets-work"></a>Jak działają zestawy replik

Podczas tworzenia domeny zarządzanej, takiej jak *aaddscontoso.com*, zostaje utworzony początkowy zestaw replik. Dodatkowe zestawy replik mają tę samą przestrzeń nazw i konfigurację. Zmiany w usłudze Azure AD DS, w tym konfiguracja, tożsamość użytkownika i poświadczenia, grupy, obiekty zasad grupy, obiekty komputerów i inne zmiany są stosowane do wszystkich zestawów replik w domenie zarządzanej przy użyciu replikacji AD DS.

Każdy zestaw replik można utworzyć w sieci wirtualnej. Każda sieć wirtualna musi być połączona za pomocą komunikacji równorzędnej z każdą inną siecią wirtualną, która hostuje zestaw replik domeny zarządzanej. Ta konfiguracja służy do tworzenia topologii sieci siatki, która obsługuje replikację katalogu. Sieć wirtualna może obsługiwać wiele zestawów replik, pod warunkiem, że każdy zestaw replik znajduje się w innej podsieci wirtualnej.

Wszystkie zestawy replik są umieszczane w tej samej lokacji Active Directory. W związku z tym wszystkie zmiany są propagowane przy użyciu replikacji wewnątrzlokacyjnej na potrzeby szybkiego wywoływania zbieżności.

> [!NOTE]
> Nie można definiować oddzielnych lokacji i definiować ustawień replikacji między zestawami replik.

Na poniższym diagramie przedstawiono domenę zarządzaną z dwoma zestawami replik. Pierwszy zestaw replik jest tworzony z przestrzeni nazw domeny. Drugi zestaw replik jest tworzony po:

![Diagram przykładowej domeny zarządzanej z dwoma zestawami replik](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> Zestawy replik zapewniają dostępność usług uwierzytelniania w regionach, w których skonfigurowano zestaw replik. Aby aplikacja miała geograficzną nadmiarowość w przypadku awarii regionalnej, platforma aplikacji, która opiera się na domenie zarządzanej, musi również znajdować się w innym regionie.
>
> Zestaw replik nie zapewnia odporności innych usług wymaganych do działania aplikacji, takich jak maszyny wirtualne platformy Azure lub usługa Azure App Services. W projekcie dostępności innych składników aplikacji należy wziąć pod uwagę funkcje odporności dla usług, które tworzą aplikację.

Poniższy przykład przedstawia domenę zarządzaną z trzema zestawami replik w celu dodatkowego zapewnienia odporności i zapewnienia dostępności usług uwierzytelniania. W obu przykładach obciążenia aplikacji znajdują się w tym samym regionie, w którym znajduje się zestaw replik domeny zarządzanej:

![Diagram przykładowej domeny zarządzanej z trzema zestawami replik](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Domyślną *jednostką* SKU dla domeny zarządzanej jest jednostka SKU przedsiębiorstwa, która obsługuje wiele zestawów replik. Aby utworzyć dodatkowe zestawy replik w przypadku zmiany *standardowej* jednostki SKU, należy [uaktualnić domenę zarządzaną](change-sku.md) do *wersji Enterprise* lub *Premium*.

Maksymalna liczba zestawów replik obsługiwanych w ramach wersji zapoznawczej to cztery, łącznie z pierwszą repliką utworzoną podczas tworzenia domeny zarządzanej.

Rozliczenia dla każdego zestawu replik bazują na JEDNOSTKAch konfiguracji domeny. Na przykład jeśli masz domenę zarządzaną, która korzysta z jednostki SKU *przedsiębiorstwa* i masz trzy zestawy replik, subskrypcja jest rozliczana za godzinę dla każdego z trzech zestawów replik.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-use-my-production-managed-domain-with-this-preview"></a>Czy mogę używać mojej produkcyjnej domeny zarządzanej z tą wersją zapoznawczą?

Zestawy replik są funkcją publicznej wersji zapoznawczej w Azure AD Domain Services. Można użyć produkcyjnej domeny zarządzanej, ale należy pamiętać o różnicach w zakresie pomocy technicznej istniejących dla funkcji w wersji zapoznawczej. Aby uzyskać więcej informacji o wersjach zapoznawczych, [Azure Active Directory wersji zapoznawczej umowy SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>Czy można utworzyć zestaw replik w ramach subskrypcji innej niż domena zarządzana?

Nie. Zestawy replik muszą znajdować się w tej samej subskrypcji co domena zarządzana.

### <a name="how-many-replica-sets-can-i-create"></a>Ile zestawów replik można utworzyć?

Wersja zapoznawcza jest ograniczona do maksymalnie czterech zestawów replik — początkowej repliki zestawu dla domeny zarządzanej oraz trzech dodatkowych zestawów replik.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>Jak informacje o użytkownikach i grupach są synchronizowane z zestawami replik?

Wszystkie zestawy replik są połączone ze sobą za pomocą komunikacji równorzędnej sieci wirtualnej siatki. Jeden zestaw replik otrzymuje aktualizacje użytkowników i grup z usługi Azure AD. Te zmiany są następnie replikowane do innych zestawów replik za pomocą replikacji wewnątrzlokacyjnej AD DS za pośrednictwem sieci równorzędnej.

Podobnie jak w przypadku AD DS lokalnego, rozszerzony stan rozłączenia może powodować zakłócenia w replikacji. Ponieważ wirtualne sieci równorzędne nie są przechodnie, wymagania projektowe dla zestawów replik wymagają w pełni oczka topologii sieci.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>Jak mogę wprowadzić zmiany w mojej domenie zarządzanej po skonfigurowaniu zestawów replik?

Zmiany w domenie zarządzanej działają tak samo jak wcześniej. Można [utworzyć maszynę wirtualną zarządzania i korzystać z niej przy użyciu narzędzi RSAT, które są dołączone do domeny zarządzanej](tutorial-create-management-vm.md). Do domeny zarządzanej można przyłączyć dowolną liczbę maszyn wirtualnych zarządzania.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z zestawami replik, [Utwórz i skonfiguruj domenę zarządzaną platformy Azure AD DS][tutorial-create-advanced]. Po wdrożeniu [Utwórz i użyj dodatkowych zestawów replik][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md

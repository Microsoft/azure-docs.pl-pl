---
title: Aktualizowanie delegacji
description: Dowiedz się, jak zaktualizować delegowanie dla klienta, który został wcześniej dołączony do usługi Azure Lighthouse.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555765"
---
# <a name="update-a-delegation"></a>Aktualizowanie delegacji

Po dodaniu subskrypcji (lub grupy zasobów) do usługi Azure Lighthouse może zajść konieczność wprowadzenia zmian. Na przykład klient może chcieć wykonać dodatkowe zadania zarządzania, które wymagają innej wbudowanej roli platformy Azure, lub może zajść konieczność zmiany dzierżawy, do której jest delegowana subskrypcja klienta.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą korzystać z tego samego procesu, aby skonfigurować usługę Azure Lighthouse i skonsolidować swoje środowisko zarządzania.

W przypadku dołączenia [klienta za pomocą szablonów Azure Resource Manager (szablony ARM)](onboard-customer.md)dla tego klienta należy wykonać nowe wdrożenie. W zależności od tego, co jest zmieniane, możesz chcieć zaktualizować oryginalną ofertę lub usunąć oryginalną ofertę i utworzyć nową.

- **Jeśli zmieniasz tylko autoryzacje**: możesz zaktualizować delegowanie, zmieniając tylko sekcję **autoryzacji** szablonu ARM.
- **Jeśli zmieniasz dzierżawę zarządzającą**: musisz utworzyć nowy szablon usługi ARM przy użyciu innego **mspOfferName** niż poprzednia oferta.

## <a name="update-your-arm-template"></a>Aktualizowanie szablonu ARM

Aby zaktualizować delegowanie, należy wdrożyć szablon ARM zawierający zmiany, które chcesz wprowadzić.

W przypadku aktualizowania tylko autoryzacji (takich jak dodanie nowej grupy użytkowników z rolą, która nie wcześniej lub zmiana roli istniejącego użytkownika), można użyć tego samego **mspOfferName** jak w [szablonie ARM](onboard-customer.md#create-an-azure-resource-manager-template) , który został użyty dla poprzedniego delegowania. Możesz użyć poprzedniego szablonu jako punktu początkowego. Następnie wprowadź wymagane zmiany, takie jak zastąpienie jednej roli wbudowanej platformy Azure innym lub dodanie zupełnie nowej autoryzacji do szablonu.

Jeśli zmienisz **mspOfferName**, zostanie ona uznana za nową, osobną ofertę. Jest to wymagane w przypadku zmiany dzierżawy zarządzającej.

Nie trzeba zmieniać **mspOfferName** , jeśli dzierżawa zarządzająca pozostaje taka sama. W większości przypadków zaleca się, aby tylko jeden **mspOfferName** używany przez tego samego klienta i zarządzać dzierżawcą. W przypadku wybrania tej opcji należy upewnić się, że poprzednie delegowanie klienta zostało usunięte przed wdrożeniem nowego.

## <a name="remove-the-previous-delegation"></a>Usuń poprzednie delegowanie

Przed przeprowadzeniem nowego wdrożenia możesz chcieć [usunąć dostęp do poprzedniego delegowania](remove-delegation.md). Dzięki temu wszystkie poprzednie uprawnienia zostaną usunięte, co pozwoli na rozpoczęcie czyszczenia z dokładnymi użytkownikami/grupami i rolami, które powinny być stosowane do przodu.

> [!IMPORTANT]
> Jeśli używasz nowego **mspOfferName** i nie chcesz mieć tych samych wartości **principalId** , przed wdrożeniem nowej oferty musisz usunąć dostęp do poprzedniego delegowania. Jeśli nie usuniesz pierwszej oferty, użytkownicy, którzy uzyskali wcześniej uprawnienia, mogą utracić dostęp całkowicie ze względu na przydziały powodujące konflikty.

W przypadku zmiany dzierżawy zarządzającej można pozostawić poprzednią ofertę w miejscu, jeśli chcesz, aby obie dzierżawy nadal miały dostęp. Jeśli chcesz mieć dostęp tylko do nowej dzierżawy zarządzającej, musisz usunąć wcześniejszą ofertę. Można to zrobić przed lub po dodaniu nowej oferty.

Jeśli aktualizujesz ofertę, aby dostosować tylko autoryzacje i zachować te same **mspOfferName**, nie musisz usuwać poprzedniego delegowania. Nowe wdrożenie zamieni poprzednie delegowanie i zostanie zastosowane tylko autoryzacje w najnowszym szablonie.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagram przedstawiający, kiedy należy zmienić mspOfferName i usunąć poprzednie delegowanie.":::

Usuwanie dostępu do delegowania może odbywać się przez dowolnego użytkownika w dzierżawie zarządzającej, który udzielił [roli usuwanie przypisania rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) w ramach oryginalnego delegowania. Jeśli żaden użytkownik w dzierżawie nie ma tej roli, możesz polecić klientowi [usunięcie dostępu do oferty w Azure Portal](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> Jeśli poprzednie delegowanie zostało usunięte zgodnie z powyższymi krokami i nadal nie można wdrożyć nowego szablonu ARM, może być konieczne [całkowite usunięcie definicji rejestracji](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Można to zrobić przez dowolnego użytkownika z rolą, która ma `Microsoft.Authorization/roleAssignments/write` uprawnienie, takie jak [właściciel](../../role-based-access-control/built-in-roles.md#owner), w dzierżawie klienta.  

## <a name="deploy-the-arm-template"></a>Wdrażanie szablonu usługi ARM

Klient może [wdrożyć zaktualizowany szablon](onboard-customer.md#deploy-the-azure-resource-manager-templates) w taki sam sposób, jak wcześniej: w Azure Portal przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Po zakończeniu wdrażania [upewnij się, że zakończyło się pomyślnie](onboard-customer.md#confirm-successful-onboarding). Zaktualizowane autoryzacje będą obowiązywały dla subskrypcji lub grup zasobów, które zostały delegowane przez klienta.

## <a name="updating-managed-service-offers"></a>Aktualizowanie ofert usług zarządzanych

Jeśli klient został dołączony do oferty usługi zarządzanej opublikowanej w portalu Azure Marketplace i chcesz zaktualizować autoryzacje, możesz zaktualizować delegowanie, [publikując nową wersję oferty](../../marketplace/partner-center-portal/update-existing-offer.md) przy użyciu [autoryzacji](../../marketplace/plan-managed-service-offer.md) , które mają zostać zaktualizowane w planie dla tego klienta. Klient będzie mógł następnie zaktualizować do najnowszej wersji w Azure Portal.

Jeśli chcesz zmienić dzierżawę zarządzającą, musisz [utworzyć i opublikować nową ofertę usługi zarządzanej](../../marketplace/plan-managed-service-offer.md) , która zostanie zaakceptowana przez klienta.

> [!TIP]
> Jak wspomniano wcześniej, zalecamy, aby nie używać wielu różnych ofert między tym samym klientem i zarządzaniem dzierżawcą. Jeśli opublikujesz nową ofertę dla tego samego klienta, który używa tej samej dzierżawy zarządzającej, upewnij się, że wcześniejsza oferta została usunięta, zanim klient zaakceptuje nowszą ofertę.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlaj klientów i zarządzaj nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.
- Dowiedz się, jak [usunąć dostęp do delegowania](remove-delegation.md) , który został wcześniej dołączony.

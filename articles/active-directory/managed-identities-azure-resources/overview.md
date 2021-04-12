---
title: Tożsamości zarządzane dla zasobów platformy Azure
description: Przegląd zarządzanych tożsamości dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 04/07/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fabb8bbdb42212dffd3781f4e98204abb518e6b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105582"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Jakie są zarządzane tożsamości dla zasobów platformy Azure?

Typowym wyzwaniem dla deweloperów jest zarządzanie kluczami tajnymi i poświadczeniami używanymi do zabezpieczania komunikacji między różnymi składnikami, które tworzą rozwiązanie. Tożsamości zarządzane eliminują konieczność zarządzania poświadczeniami przez deweloperów. Tożsamości zarządzane zapewniają tożsamość aplikacji, które mają być używane podczas nawiązywania połączenia z zasobami, które obsługują uwierzytelnianie Azure Active Directory (Azure AD). Aplikacje mogą używać tożsamości zarządzanej do uzyskiwania tokenów usługi Azure AD. Na przykład aplikacja może używać tożsamości zarządzanej do uzyskiwania dostępu do zasobów, takich jak [Azure Key Vault](../../key-vault/general/overview.md) , w których deweloperzy mogą przechowywać poświadczenia w bezpieczny sposób lub uzyskiwać dostęp do kont magazynu.

Do czego służy tożsamość zarządzana?</br>

> [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Oto niektóre korzyści wynikające z używania tożsamości zarządzanych:

- Nie musisz zarządzać poświadczeniami. Poświadczenia nie są jeszcze dostępne dla Ciebie.
- Przy użyciu tożsamości zarządzanych można uwierzytelniać się w dowolnym zasobie, który obsługuje [uwierzytelnianie Azure Active Directory](../authentication/overview-authentication.md) , w tym własne aplikacje.
- Tożsamości zarządzane mogą być używane bez dodatkowych kosztów.

> [!NOTE]
> Tożsamości zarządzane dla zasobów platformy Azure to nowa nazwa usługi znanej wcześniej jako Tożsamość usługi zarządzanej (MSI).

## <a name="managed-identity-types"></a>Zarządzane typy tożsamości

Istnieją dwa typy tożsamości zarządzanych:

- **Przypisane przez system** Niektóre usługi platformy Azure umożliwiają włączenie zarządzanej tożsamości bezpośrednio w wystąpieniu usługi. Po włączeniu tożsamości zarządzanej przypisanej przez system tożsamość jest tworzona w usłudze Azure AD, która jest powiązana z cyklem życia tego wystąpienia usługi. W związku z tym po usunięciu zasobu platforma Azure automatycznie usunie tę tożsamość. Po zaprojektowaniu, tylko ten zasób platformy Azure może używać tej tożsamości do żądania tokenów z usługi Azure AD.
- **Przypisane przez użytkownika** Tożsamość zarządzaną można także utworzyć jako autonomiczny zasób platformy Azure. Można [utworzyć tożsamość zarządzaną przypisaną przez użytkownika](how-to-manage-ua-identity-portal.md) i przypisać ją do jednego lub większej liczby wystąpień usługi platformy Azure. W przypadku tożsamości zarządzanych przez użytkownika tożsamość jest zarządzana niezależnie od zasobów, które go używają. </br></br>

> [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

W poniższej tabeli przedstawiono różnice między dwoma typami zarządzanych tożsamości.

|  Właściwość    | Tożsamość zarządzana przypisana przez system | Tożsamość zarządzana przypisana przez użytkownika |
|------|----------------------------------|--------------------------------|
| Tworzenie |  Utworzone w ramach zasobu platformy Azure (na przykład maszyny wirtualnej platformy Azure lub Azure App Service) | Utworzono jako autonomiczny zasób platformy Azure |
| Cykl życiowy | Współużytkowany cykl życia z zasobem platformy Azure, za pomocą którego utworzono tożsamość zarządzaną. <br/> Po usunięciu zasobu nadrzędnego tożsamość zarządzana również jest usuwana. | Niezależny cykl życia. <br/> Musi być jawnie usunięty. |
| Udostępnianie w zasobach platformy Azure | Nie można udostępnić. <br/> Może być skojarzony tylko z jednym zasobem platformy Azure. | Mogą być udostępniane <br/> Ta sama tożsamość zarządzana przypisana przez użytkownika może być skojarzona z więcej niż jednym zasobem platformy Azure. |
| Typowe przypadki użycia | Obciążenia zawarte w pojedynczym zasobie platformy Azure <br/> Obciążenia, dla których są potrzebne niezależne tożsamości. <br/> Na przykład aplikacja działająca na jednej maszynie wirtualnej | Obciążenia działające na wielu zasobach, które mogą współużytkować jedną tożsamość. <br/> Obciążenia wymagające wstępnej autoryzacji do bezpiecznego zasobu w ramach przepływu aprowizacji. <br/> Obciążenia, w których zasoby są często odtwarzane, ale uprawnienia powinny pozostać spójne. <br/> Na przykład obciążenie, w przypadku którego wiele maszyn wirtualnych potrzebuje dostępu do tego samego zasobu |

> [!IMPORTANT]
> Niezależnie od typu tożsamości wybranej tożsamości zarządzanej jest nazwa główna usługi typu specjalnego, która może być używana tylko z zasobami platformy Azure. Po usunięciu tożsamości zarządzanej jest automatycznie usuwana odpowiednia jednostka usługi.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Jak można używać tożsamości zarządzanych dla zasobów platformy Azure?

![Przykłady, w jaki deweloper może używać tożsamości zarządzanych do uzyskiwania dostępu do zasobów z ich kodu bez konieczności zarządzania informacjami o uwierzytelnianiu](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Jakie usługi platformy Azure obsługują funkcję?<a name="which-azure-services-support-managed-identity"></a>

Tożsamości zarządzane dla zasobów platformy Azure mogą służyć do uwierzytelniania w usługach obsługujących uwierzytelnianie usługi Azure AD. Listę usług platformy Azure, które obsługują funkcję tożsamości zarządzanych dla zasobów platformy Azure, można znaleźć w temacie [Services that support managed identities for Azure resources (Usługi, które obsługują tożsamości zarządzane dla platformy Azure)](./services-support-managed-identities.md).

## <a name="which-operations-can-i-perform-using-managed-identities"></a>Które operacje można wykonywać za pomocą tożsamości zarządzanych?

Zasoby obsługujące tożsamości zarządzane przypisane przez system umożliwiają:

- Włączać lub wyłączać tożsamości zarządzane na poziomie zasobów.
- Użyj ról RBAC, aby [przyznać uprawnienia](howto-assign-access-portal.md).
- Wyświetlaj operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w [dziennikach aktywności platformy Azure](../../azure-resource-manager/management/view-activity-logs.md).
- Wyświetl działania związane z logowaniem w [dziennikach logowania](../reports-monitoring/concept-sign-ins.md)usługi Azure AD.

W przypadku wybrania tożsamości zarządzanej przypisanej przez użytkownika:

- Możesz [tworzyć, odczytywać, aktualizować i usuwać](how-to-manage-ua-identity-portal.md) tożsamości.
- Aby [udzielić uprawnień](howto-assign-access-portal.md), można użyć przypisań ról RBAC.
- Tożsamości zarządzane przypisane przez użytkownika mogą być używane dla więcej niż jednego zasobu.
- Operacje CRUD są dostępne do przeglądu w [dziennikach aktywności platformy Azure](../../azure-resource-manager/management/view-activity-logs.md).
- Wyświetl działania związane z logowaniem w [dziennikach logowania](../reports-monitoring/concept-sign-ins.md)usługi Azure AD.

Operacje na tożsamościach zarządzanych można wykonać przy użyciu szablonu Azure Resource Manager (ARM), witryny Azure Portal, interfejsu wiersza polecenia platformy Azure, programu PowerShell i interfejsów API REST.

## <a name="next-steps"></a>Następne kroki

* [Używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Resource Manager](tutorial-windows-vm-access-arm.md)
* [Używanie przypisanej przez system tożsamości zarządzanej na maszynie wirtualnej z systemem Linux do uzyskiwania dostępu do usługi Resource Manager](tutorial-linux-vm-access-arm.md)
* [Jak używać tożsamości zarządzanych do App Service i Azure Functions](../../app-service/overview-managed-identity.md)
* [Jak używać tożsamości zarządzanych z usługą Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implementowanie zarządzanych tożsamości dla zasobów Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).

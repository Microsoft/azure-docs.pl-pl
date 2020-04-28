---
title: Usuń niezgodne błędy katalogów w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, co to jest błąd niezgodnego katalogu i jak go rozwiązać w Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 6ff12ce5fec8fcc49fa21ef5f3009fc2283300c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654822"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Rozwiązywanie niezgodnych błędów katalogów dla istniejących Azure AD Domain Services domen zarządzanych

Jeśli w domenie zarządzanej Azure Active Directory Domain Services (Azure AD DS) zostanie wyświetlony niezgodny błąd dzierżawy, nie będzie można administrować domeną zarządzaną do momentu rozwiązania problemu. Ten błąd występuje, gdy bazowa sieć wirtualna platformy Azure jest przenoszona do innego katalogu usługi Azure AD.

W tym artykule wyjaśniono, dlaczego występuje błąd i jak go rozwiązać.

## <a name="what-causes-this-error"></a>Co powoduje ten błąd?

Niezgodny błąd katalogu występuje, gdy domena zarządzana i Sieć wirtualna platformy Azure AD DS należą do dwóch różnych dzierżaw usługi Azure AD. Na przykład może istnieć domena zarządzana AD DS platformy Azure o nazwie *aaddscontoso.com* , która działa w dzierżawie usługi Azure AD firmy Contoso. Jednak Sieć wirtualna platformy Azure dla domeny zarządzanej jest częścią dzierżawy usługi Azure AD firmy Fabrikam.

Platforma Azure używa kontroli dostępu opartej na rolach (RBAC), aby ograniczyć dostęp do zasobów. Po włączeniu usługi Azure AD DS w dzierżawie usługi Azure AD skróty poświadczeń są synchronizowane z domeną zarządzaną. Ta operacja wymaga, aby być administratorem dzierżawy dla katalogu usługi Azure AD, a dostęp do tych poświadczeń musi być kontrolowany. Aby wdrożyć zasoby w sieci wirtualnej platformy Azure i kontrolować ruch, musisz mieć uprawnienia administracyjne w sieci wirtualnej, w której wdrażasz usługę Azure AD DS.

Aby kontrola RBAC działała spójnie i bezpieczny dostęp do wszystkich zasobów używanych przez usługę Azure AD DS, domena zarządzana i Sieć wirtualna muszą należeć do tej samej dzierżawy usługi Azure AD.

W środowisku Menedżer zasobów są stosowane następujące reguły:

- Katalog usługi Azure AD może mieć wiele subskrypcji platformy Azure.
- Subskrypcja platformy Azure może mieć wiele zasobów, takich jak sieci wirtualne.
- Dla katalogu usługi Azure AD jest włączona jedna Azure AD Domain Services domena zarządzana.
- Azure AD Domain Services domeny zarządzanej można włączyć w sieci wirtualnej należącej do dowolnej subskrypcji platformy Azure w ramach tej samej dzierżawy usługi Azure AD.

### <a name="valid-configuration"></a>Prawidłowa konfiguracja

W poniższym przykładowym scenariuszu wdrażania jest włączona domena zarządzana contoso platformy Azure AD DS w dzierżawie usługi Azure AD firmy Contoso. Domena zarządzana jest wdrażana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Azure AD firmy Contoso. Zarówno domena zarządzana, jak i Sieć wirtualna należą do tej samej dzierżawy usługi Azure AD. Ta Przykładowa konfiguracja jest prawidłowa i jest w pełni obsługiwana.

![Prawidłowa konfiguracja dzierżawy usługi Azure AD DS przy użyciu domeny zarządzanej i sieci wirtualnej należącej do tej samej dzierżawy usługi Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Niezgodna konfiguracja dzierżawy

W tym przykładowym scenariuszu wdrażania jest włączona domena zarządzana contoso Azure AD DS w dzierżawie usługi Azure AD firmy Contoso. Jednak domena zarządzana jest wdrażana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Azure AD firmy Fabrikam. Domena zarządzana i Sieć wirtualna należą do dwóch różnych dzierżaw usługi Azure AD. Ta Przykładowa konfiguracja jest niezgodną dzierżawą i nie jest obsługiwana. Sieć wirtualną należy przenieść do tej samej dzierżawy usługi Azure AD, która jest domeną zarządzaną.

![Niezgodna konfiguracja dzierżawy](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Błąd rozwiązywania niezgodnej dzierżawy

Poniższe dwie opcje rozwiązują błąd niezgodnego katalogu:

* [Usuń domenę zarządzaną AD DS platformy Azure](delete-aadds.md) z istniejącego katalogu usługi Azure AD. [Utwórz zastępczą domenę zarządzaną platformy azure AD DS](tutorial-create-instance.md) w tym samym katalogu usługi Azure AD co sieć wirtualna, której chcesz użyć. Gdy wszystko będzie gotowe, Dołącz wszystkie komputery, które zostały wcześniej dołączone do usuniętej domeny zarządzanej.
* [Przenieś subskrypcję platformy Azure](../cost-management-billing/manage/billing-subscription-transfer.md) zawierającą sieć wirtualną do tego samego katalogu usługi Azure AD jako domenę zarządzaną platformy Azure AD DS.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów z usługą Azure AD DS, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).

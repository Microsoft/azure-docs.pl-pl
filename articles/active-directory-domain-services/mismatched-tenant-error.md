---
title: Usuń niezgodne błędy katalogów w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, co to jest błąd niezgodnego katalogu i jak go rozwiązać w Azure AD Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 0c2a6ee4ee5c92122e896e62485479d50c660c7f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968076"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>Rozwiązywanie niezgodnych błędów katalogów dla istniejących Azure Active Directory Domain Services domen zarządzanych

Jeśli w domenie zarządzanej Azure Active Directory Domain Services (Azure AD DS) zostanie wyświetlony niezgodny błąd dzierżawy, nie będzie można administrować domeną zarządzaną do momentu rozwiązania problemu. Ten błąd występuje, gdy bazowa sieć wirtualna platformy Azure jest przenoszona do innego katalogu usługi Azure AD.

W tym artykule wyjaśniono, dlaczego występuje błąd i jak go rozwiązać.

## <a name="what-causes-this-error"></a>Co powoduje ten błąd?

Niezgodny błąd katalogu występuje, gdy domena zarządzana i Sieć wirtualna platformy Azure AD DS należą do dwóch różnych dzierżaw usługi Azure AD. Na przykład możesz mieć domenę zarządzaną o nazwie *aaddscontoso.com* , która jest uruchamiana w dzierżawie usługi Azure AD firmy Contoso. Jednak Sieć wirtualna platformy Azure dla domeny zarządzanej jest częścią dzierżawy usługi Azure AD firmy Fabrikam.

Kontrola dostępu oparta na rolach (Azure RBAC) służy do ograniczania dostępu do zasobów. Po włączeniu usługi Azure AD DS w dzierżawie usługi Azure AD skróty poświadczeń są synchronizowane z domeną zarządzaną. Ta operacja wymaga, aby być administratorem dzierżawy dla katalogu usługi Azure AD, a dostęp do tych poświadczeń musi być kontrolowany.

Aby wdrożyć zasoby w sieci wirtualnej platformy Azure i kontrolować ruch, musisz mieć uprawnienia administracyjne w sieci wirtualnej, w której wdrażana jest domena zarządzana.

Aby usługa Azure RBAC działała spójnie i bezpieczny dostęp do wszystkich zasobów używanych przez usługę Azure AD DS, domena zarządzana i Sieć wirtualna muszą należeć do tej samej dzierżawy usługi Azure AD.

Do wdrożeń mają zastosowanie następujące reguły:

- Katalog usługi Azure AD może mieć wiele subskrypcji platformy Azure.
- Subskrypcja platformy Azure może mieć wiele zasobów, takich jak sieci wirtualne.
- Dla katalogu usługi Azure AD jest włączona jedna domena zarządzana.
- Domenę zarządzaną można włączyć w sieci wirtualnej należącej do dowolnej subskrypcji platformy Azure w ramach tej samej dzierżawy usługi Azure AD.

### <a name="valid-configuration"></a>Prawidłowa konfiguracja

W poniższym przykładowym scenariuszu wdrażania domena zarządzana contoso jest włączona w dzierżawie usługi Azure AD firmy Contoso. Domena zarządzana jest wdrażana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Azure AD firmy Contoso.

Zarówno domena zarządzana, jak i Sieć wirtualna należą do tej samej dzierżawy usługi Azure AD. Ta Przykładowa konfiguracja jest prawidłowa i jest w pełni obsługiwana.

![Prawidłowa konfiguracja dzierżawy usługi Azure AD DS przy użyciu domeny zarządzanej i sieci wirtualnej należącej do tej samej dzierżawy usługi Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Niezgodna konfiguracja dzierżawy

W tym przykładowym scenariuszu wdrażania domena zarządzana contoso jest włączona w dzierżawie usługi Azure AD firmy Contoso. Jednak domena zarządzana jest wdrażana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Azure AD firmy Fabrikam.

Domena zarządzana i Sieć wirtualna należą do dwóch różnych dzierżaw usługi Azure AD. Ta Przykładowa konfiguracja jest niezgodną dzierżawą i nie jest obsługiwana. Sieć wirtualną należy przenieść do tej samej dzierżawy usługi Azure AD, która jest domeną zarządzaną.

![Niezgodna konfiguracja dzierżawy](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Błąd rozwiązywania niezgodnej dzierżawy

Poniższe dwie opcje rozwiązują błąd niezgodnego katalogu:

* Najpierw [Usuń domenę zarządzaną](delete-aadds.md) z istniejącego katalogu usługi Azure AD. Następnie [Utwórz zastępczą domenę zarządzaną](tutorial-create-instance.md) w tym samym katalogu usługi Azure AD, która jest siecią wirtualną, której chcesz użyć. Gdy wszystko będzie gotowe, Dołącz wszystkie komputery, które zostały wcześniej dołączone do usuniętej domeny zarządzanej.
* [Przenieś subskrypcję platformy Azure](../cost-management-billing/manage/billing-subscription-transfer.md) zawierającą sieć wirtualną do tego samego katalogu usługi Azure AD, co domena zarządzana.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów z usługą Azure AD DS, zobacz [Przewodnik rozwiązywania problemów](troubleshoot.md).

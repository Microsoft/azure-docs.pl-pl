---
title: Konfigurowanie tożsamości laboratorium w Azure DevTest Labs
description: Dowiedz się, jak skonfigurować tożsamość laboratorium w usłudze Azure DevTest.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88719684"
---
# <a name="configure-a-lab-identity"></a>Konfigurowanie tożsamości laboratorium

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Zabezpieczanie poświadczeń to ważne zadanie. W idealnej sytuacji poświadczenia nie są nigdy wyświetlane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. Azure Key Vault zapewnia sposób bezpiecznego przechowywania poświadczeń, wpisów tajnych i innych kluczy, ale kod musi być uwierzytelniany do Key Vault, aby je pobrać. 

Zarządzane tożsamości dla funkcji zasobów platformy Azure w Azure Active Directory (Azure AD) rozwiązują ten problem. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie. Dowiedz się więcej o [zarządzanych tożsamościach na platformie Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Istnieją dwa typy tożsamości zarządzanych: 

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system  

 **Tożsamość zarządzana przypisana przez system**   jest włączona bezpośrednio w wystąpieniu usługi platformy Azure. Po włączeniu tożsamości platforma Azure tworzy tożsamość wystąpienia w dzierżawie usługi Azure AD, która jest zaufaną dzierżawą subskrypcji wystąpienia. Po utworzeniu tożsamości poświadczenia są aprowizowane w wystąpieniu. Cykl życia tożsamości przypisanej do systemu jest bezpośrednio powiązany z wystąpieniem usługi platformy Azure, na którym jest włączona. Usunięcie wystąpienia spowoduje, że platforma Azure automatycznie oczyści poświadczenia i tożsamość w usłudze Azure AD. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Scenariusze korzystania z tożsamości przypisanej do systemu laboratorium  

Każda DevTest Labs jest tworzona przy użyciu tożsamości przypisanej do systemu, która pozostaje ważna przez cały czas trwania laboratorium. Tożsamość przypisana do systemu jest używana w następujących celach:  

- Wszystkie wdrożenia oparte na [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) używane do uruchamiania wielu maszyn wirtualnych i/lub platformy jako środowiska usługi zostaną wykonane przy użyciu tożsamości przypisanej do systemu laboratorium  
- Szyfrowanie dysków dla dysków laboratorium przy użyciu klucza zarządzanego przez klienta jest obsługiwane za pośrednictwem tożsamości przypisanej do systemu laboratorium. Zapewniając jawny dostęp do tożsamości laboratorium w celu uzyskania dostępu do zestawu szyfrowania dysków, laboratorium może zaszyfrować wszystkie dyski maszyny wirtualnej w Twoim imieniu. Dowiedz się więcej na temat [włączania szyfrowania dysków](encrypt-disks-customer-managed-keys.md) na dyskach laboratoryjnych przy użyciu klucza zarządzanego przez klienta.  

### <a name="configure-identity"></a>Konfigurowanie tożsamości

W tej sekcji przedstawiono sposób konfigurowania zasad tożsamości laboratorium.

> [!NOTE]
> Dla laboratoriów utworzonych przed 8/10/2020, tożsamość przypisana do systemu zostanie wyłączona. Jako właściciel laboratorium możesz go włączyć, jeśli zamierzasz używać laboratoriów do celów wymienionych w poprzedniej sekcji.  
>
> W przypadku nowych laboratoriów utworzonych po 8/10/2020, przypisana przez system do laboratorium tożsamość jest domyślnie ustawiona na włączone, a właściciel laboratorium nie będzie w stanie go wyłączyć w cyklu życia laboratorium.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj **DevTest Labs**.
1. Z listy laboratoriów wybierz odpowiednie laboratorium.
1. Wybierz pozycję **Konfiguracja i zasady**  ->  **tożsamość (wersja zapoznawcza)**. 

> [!div class="mx-imgBorder"]
> ![Konfigurowanie tożsamości](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika  

Tożsamość zarządzana przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure. Cykl życia tożsamości przypisanej przez użytkownika jest zarządzany osobno od cyklu życia wystąpień usługi platformy Azure, do których jest przypisany. 

DevTest Labs obsługuje tożsamości przypisane przez użytkownika dla maszyn wirtualnych i środowisk opartych na Azure Resource Manager.  Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Dodawanie tożsamości przypisanej do użytkownika w celu wdrożenia środowisk Azure Resource Manager laboratoryjnych](use-managed-identities-environments.md)
- [Dodawanie tożsamości przypisanych do użytkownika w celu wdrożenia maszyn wirtualnych laboratorium](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Następne kroki

Przegląd [konfigurowania zarządzania kosztami](devtest-lab-configure-cost-management.md)
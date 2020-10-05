---
title: Wybierz sposób autoryzacji dostępu do danych kolejki w Azure Portal
titleSuffix: Azure Storage
description: Gdy uzyskujesz dostęp do danych z kolejki przy użyciu Azure Portal, Portal wysyła żądania do usługi Azure Storage w ramach okładek. Te żądania do usługi Azure Storage mogą być uwierzytelniane i autoryzowane przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozguns
ms.subservice: queues
ms.custom: contperfq1
ms.openlocfilehash: 2593f1b7ea4cfabe0243fe6f830d718896e68473
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715513"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Wybierz sposób autoryzacji dostępu do danych kolejki w Azure Portal

Gdy uzyskujesz dostęp do danych z kolejki przy użyciu [Azure Portal](https://portal.azure.com), Portal wysyła żądania do usługi Azure Storage w ramach okładek. Żądanie do usługi Azure Storage może być autoryzowane przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu. Portal wskazuje, której metody używasz, i umożliwia przełączenie między nimi, jeśli masz odpowiednie uprawnienia.  

## <a name="permissions-needed-to-access-queue-data"></a>Uprawnienia wymagające dostępu do danych kolejki

W zależności od tego, jak chcesz autoryzować dostęp do danych w kolejce w Azure Portal, będziesz potrzebować określonych uprawnień. W większości przypadków te uprawnienia są udostępniane za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Aby uzyskać więcej informacji na temat usługi Azure RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>Korzystanie z klucza dostępu do konta

Aby uzyskać dostęp do danych kolejki przy użyciu klucza dostępu do konta, musisz mieć przypisaną rolę platformy Azure, która obejmuje akcję RBAC platformy Azure **Microsoft. Storage/storageAccounts/ListKeys/Action**. Ta rola platformy Azure może być rolą wbudowaną lub niestandardową. Wbudowane role obsługujące **firmę Microsoft. Storage/storageAccounts/ListKeys/Action** obejmują:

- Rola [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Rola [współautor](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Rola [współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Podczas próby dostępu do danych kolejki w Azure Portal Portal najpierw sprawdza, czy przypisano rolę z użyciem elementu **Microsoft. Storage/storageAccounts/ListKeys/Action**. Jeśli przypisano rolę z tą akcją, Portal używa klucza konta do uzyskiwania dostępu do danych kolejki. Jeśli nie masz przypisanej roli z tą akcją, Portal próbuje uzyskać dostęp do danych przy użyciu konta usługi Azure AD.

> [!NOTE]
> Administrator usług ról i administrator z uprawnieniami administratora klasycznego ma odpowiednik roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Rola **właściciela** obejmuje wszystkie akcje, w tym **Microsoft. Storage/storageAccounts/ListKeys/Action**, dzięki czemu użytkownik z jedną z tych ról administracyjnych może również uzyskać dostęp do danych w kolejce przy użyciu klucza konta. Aby uzyskać więcej informacji, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Korzystanie z konta usługi Azure AD

Aby uzyskać dostęp do danych kolejki z Azure Portal przy użyciu konta usługi Azure AD, obie następujące instrukcje muszą być spełnione:

- Przypisano Ci rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager, co najmniej w zakresie do poziomu konta magazynu lub wyższego. Rola **czytelnika** umożliwia dostęp do najbardziej ograniczonych uprawnień, ale można również uzyskać inną rolę Azure Resource Manager, która udziela dostępu do zasobów zarządzania kontami magazynu.
- Przypisano rolę wbudowaną lub niestandardową, która zapewnia dostęp do danych kolejki.

Przypisanie roli **czytnika** lub inne Azure Resource Manager przypisanie roli jest konieczne, aby użytkownik mógł wyświetlać i nawigować w Azure Portal zasoby zarządzania kontami magazynu. Role platformy Azure, które udzielają dostępu do danych kolejki, nie zapewniają dostępu do zasobów zarządzania kontami magazynu. Aby uzyskać dostęp do danych kolejki w portalu, użytkownik musi mieć uprawnienia do nawigowania po zasobach konta magazynu. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [Przypisywanie roli czytelnika do dostępu do portalu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Wbudowane role obsługujące dostęp do danych w kolejce obejmują:

- [Współautor danych kolejki magazynu](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): uprawnienia do odczytu/zapisu/usuwania dla kolejek.
- [Czytnik danych kolejki magazynu](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): uprawnienia tylko do odczytu dla kolejek.

Role niestandardowe mogą obsługiwać różne kombinacje tych samych uprawnień zapewnianych przez wbudowane role. Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md) i [Opis ról dla zasobów platformy Azure](../../role-based-access-control/role-definitions.md).

Wyświetlanie listy kolejek z rolą klasycznego administratora subskrypcji nie jest obsługiwane. Aby wyświetlić listę kolejek, użytkownik musi mieć przypisane do nich rolę **czytnika** Azure Resource Manager, rolę **czytnika danych kolejki magazynu** lub rolę **współautora danych kolejki magazynu** .

> [!IMPORTANT]
> Wersja zapoznawcza Eksplorator usługi Storage w Azure Portal nie obsługuje korzystania z poświadczeń usługi Azure AD w celu wyświetlania i modyfikowania danych kolejki. Eksplorator usługi Storage w Azure Portal zawsze używa kluczy konta do uzyskiwania dostępu do danych. Aby użyć Eksplorator usługi Storage w Azure Portal, musisz mieć przypisaną rolę, która zawiera element **Microsoft. Storage/storageAccounts/ListKeys/Action**.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Przejdź do kolejek w Azure Portal

Aby wyświetlić dane kolejki w portalu, przejdź do **omówienia** konta magazynu i kliknij linki dla **kolejek**. Alternatywnie możesz przejść do sekcji **Usługa kolejki** w menu.

:::image type="content" source="media/authorize-queue-access-portal/queue-access-portal.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do danych kolejki w Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>Ustal bieżącą metodę uwierzytelniania

Po przejściu do kolejki Azure Portal wskazuje, czy aktualnie używasz klucza dostępu do konta, czy konta usługi Azure AD do uwierzytelniania.

### <a name="authenticate-with-the-account-access-key"></a>Uwierzytelnianie przy użyciu klucza dostępu do konta

W przypadku uwierzytelniania przy użyciu klucza dostępu do konta w portalu zostanie wyświetlony **klucz dostępu** określony jako metoda uwierzytelniania:

:::image type="content" source="media/authorize-queue-access-portal/auth-method-access-key.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do danych kolejki w Azure Portal":::

Aby przełączyć się do korzystania z konta usługi Azure AD, kliknij link wyróżniony na obrazie. Jeśli masz odpowiednie uprawnienia za pośrednictwem przypisanych do Ciebie ról platformy Azure, możesz to zrobić. Jeśli jednak nie masz odpowiednich uprawnień, zobaczysz komunikat o błędzie podobny do następującego:

:::image type="content" source="media/authorize-queue-access-portal/auth-error-azure-ad.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do danych kolejki w Azure Portal":::

Zwróć uwagę, że na liście nie są wyświetlane żadne kolejki, jeśli Twoje konto usługi Azure AD nie ma uprawnień do ich wyświetlania. Kliknij link **Przełącz do klucza dostępu** , aby ponownie użyć klawisza dostępu do uwierzytelniania.

### <a name="authenticate-with-your-azure-ad-account"></a>Uwierzytelnianie za pomocą konta usługi Azure AD

W przypadku uwierzytelniania za pomocą konta usługi Azure AD zobaczysz **konto użytkownika usługi Azure AD** określone jako metoda uwierzytelniania w portalu:

:::image type="content" source="media/authorize-queue-access-portal/auth-method-azure-ad.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do danych kolejki w Azure Portal":::

Aby przełączyć się do korzystania z klucza dostępu do konta, kliknij link wyróżniony na obrazie. Jeśli masz dostęp do klucza konta, będziesz mieć możliwość przejścia. Jeśli jednak nie masz dostępu do klucza konta, Azure Portal wyświetli komunikat o błędzie.

Kolejki nie są wyświetlane w portalu, jeśli nie masz dostępu do kluczy konta. Kliknij link **Przejdź do konta użytkownika usługi Azure AD** , aby ponownie użyć konta usługi Azure AD do uwierzytelniania.

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md)
- [Użyj Azure Portal, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-portal.md)
- [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-cli.md)
- [Użyj modułu Azure PowerShell, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-powershell.md)

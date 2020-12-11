---
title: Wybierz sposób autoryzacji dostępu do danych obiektów BLOB w Azure Portal
titleSuffix: Azure Storage
description: Gdy uzyskujesz dostęp do danych obiektów BLOB przy użyciu Azure Portal, Portal wysyła żądania do usługi Azure Storage w ramach okładek. Te żądania do usługi Azure Storage mogą być uwierzytelniane i autoryzowane przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/08/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: 8c963f11a34217253f02cb5d116d66cdbf8bcc19
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033961"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Wybierz sposób autoryzacji dostępu do danych obiektów BLOB w Azure Portal

Gdy uzyskujesz dostęp do danych obiektów BLOB przy użyciu [Azure Portal](https://portal.azure.com), Portal wysyła żądania do usługi Azure Storage w ramach okładek. Żądanie do usługi Azure Storage może być autoryzowane przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu. Portal wskazuje, której metody używasz, i umożliwia przełączenie między nimi, jeśli masz odpowiednie uprawnienia.  

Możesz również określić sposób autoryzacji pojedynczej operacji przekazywania obiektów BLOB w Azure Portal. Domyślnie Portal używa dowolnej metody, która jest już używana do autoryzacji operacji przekazywania obiektów blob, ale istnieje możliwość zmiany tego ustawienia podczas przekazywania obiektu BLOB.

## <a name="permissions-needed-to-access-blob-data"></a>Uprawnienia wymagające dostępu do danych obiektów BLOB

W zależności od tego, jak chcesz autoryzować dostęp do danych obiektów BLOB w Azure Portal, będziesz potrzebować określonych uprawnień. W większości przypadków te uprawnienia są udostępniane za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Aby uzyskać więcej informacji na temat usługi Azure RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>Korzystanie z klucza dostępu do konta

Aby uzyskać dostęp do danych obiektów BLOB przy użyciu klucza dostępu do konta, musisz mieć przypisaną rolę platformy Azure, która obejmuje akcję RBAC platformy Azure **Microsoft. Storage/storageAccounts/ListKeys/Action**. Ta rola platformy Azure może być rolą wbudowaną lub niestandardową. Wbudowane role obsługujące **firmę Microsoft. Storage/storageAccounts/ListKeys/Action** obejmują:

- Rola [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Rola [współautor](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Rola [współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Gdy próbujesz uzyskać dostęp do danych obiektów BLOB w Azure Portal, Portal najpierw sprawdzi, czy masz przypisaną rolę z **firmą Microsoft. Storage/storageAccounts/ListKeys/Action**. Jeśli przypisano rolę z tą akcją, Portal używa klucza konta do uzyskiwania dostępu do danych obiektów BLOB. Jeśli nie masz przypisanej roli z tą akcją, Portal próbuje uzyskać dostęp do danych przy użyciu konta usługi Azure AD.

> [!NOTE]
> Administrator usług ról klasycznych administrator i Co-Administrator obejmujący odpowiednik roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Rola **właściciela** obejmuje wszystkie akcje, w tym **Microsoft. Storage/storageAccounts/ListKeys/Action**, dzięki czemu użytkownik mający jedną z tych ról administracyjnych może również uzyskać dostęp do danych obiektów BLOB przy użyciu klucza konta. Aby uzyskać więcej informacji, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Korzystanie z konta usługi Azure AD

Aby uzyskać dostęp do danych obiektów blob z Azure Portal przy użyciu konta usługi Azure AD, obie następujące instrukcje muszą być prawdziwe:

- Przypisano Ci rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager, co najmniej w zakresie do poziomu konta magazynu lub wyższego. Rola **czytelnika** umożliwia dostęp do najbardziej ograniczonych uprawnień, ale można również uzyskać inną rolę Azure Resource Manager, która udziela dostępu do zasobów zarządzania kontami magazynu.
- Przypisano rolę wbudowaną lub niestandardową, która zapewnia dostęp do danych obiektów BLOB.

Przypisanie roli **czytnika** lub inne Azure Resource Manager przypisanie roli jest konieczne, aby użytkownik mógł wyświetlać i nawigować w Azure Portal zasoby zarządzania kontami magazynu. Role platformy Azure, które udzielają dostępu do danych obiektów blob, nie zapewniają dostępu do zasobów zarządzania kontami magazynu. Aby uzyskać dostęp do danych obiektów BLOB w portalu, użytkownik musi mieć uprawnienia do nawigowania po zasobach konta magazynu. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [Przypisywanie roli czytelnika do dostępu do portalu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Wbudowane role, które obsługują dostęp do danych obiektów blob, obejmują:

- [Właściciel danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): dla kontroli dostępu POSIX dla Azure Data Lake Storage Gen2.
- [Współautor danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): uprawnienia do odczytu/zapisu/usuwania dla obiektów BLOB.
- [Czytnik danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): uprawnienia tylko do odczytu dla obiektów BLOB.

Role niestandardowe mogą obsługiwać różne kombinacje tych samych uprawnień zapewnianych przez wbudowane role. Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md) i [Opis ról dla zasobów platformy Azure](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> Wersja zapoznawcza Eksplorator usługi Storage w Azure Portal nie obsługuje wyświetlania i modyfikowania danych obiektów BLOB przy użyciu poświadczeń usługi Azure AD. Eksplorator usługi Storage w Azure Portal zawsze używa kluczy konta do uzyskiwania dostępu do danych. Aby użyć Eksplorator usługi Storage w Azure Portal, musisz mieć przypisaną rolę, która zawiera element **Microsoft. Storage/storageAccounts/ListKeys/Action**.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Przejdź do obiektów BLOB w Azure Portal

Aby wyświetlić dane obiektów BLOB w portalu, przejdź do **omówienia** konta magazynu i kliknij linki dla **obiektów BLOB**. Alternatywnie możesz przejść do sekcji **BLOB Service** w menu.

:::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Zrzut ekranu przedstawiający sposób nawigowania do danych obiektów BLOB w Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>Ustal bieżącą metodę uwierzytelniania

Po przejściu do kontenera Azure Portal wskazuje, czy aktualnie używasz klucza dostępu do konta, czy konta usługi Azure AD do uwierzytelniania.

### <a name="authenticate-with-the-account-access-key"></a>Uwierzytelnianie przy użyciu klucza dostępu do konta

W przypadku uwierzytelniania przy użyciu klucza dostępu do konta w portalu zostanie wyświetlony **klucz dostępu** określony jako metoda uwierzytelniania:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Zrzut ekranu przedstawiający użytkowników, którzy aktualnie uzyskują dostęp do kontenerów przy użyciu klucza konta":::

Aby przełączyć się do korzystania z konta usługi Azure AD, kliknij link wyróżniony na obrazie. Jeśli masz odpowiednie uprawnienia za pośrednictwem przypisanych do Ciebie ról platformy Azure, możesz to zrobić. Jeśli jednak nie masz odpowiednich uprawnień, zobaczysz komunikat o błędzie podobny do następującego:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Błąd wyświetlany, jeśli konto usługi Azure AD nie obsługuje dostępu":::

Zwróć uwagę, że na liście nie ma żadnych obiektów blob, jeśli Twoje konto usługi Azure AD nie ma uprawnień do ich wyświetlania. Kliknij link **Przełącz do klucza dostępu** , aby ponownie użyć klawisza dostępu do uwierzytelniania.

### <a name="authenticate-with-your-azure-ad-account"></a>Uwierzytelnianie za pomocą konta usługi Azure AD

W przypadku uwierzytelniania za pomocą konta usługi Azure AD zobaczysz **konto użytkownika usługi Azure AD** określone jako metoda uwierzytelniania w portalu:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Zrzut ekranu przedstawiający obecnie dostęp do kontenerów za pomocą konta usługi Azure AD":::

Aby przełączyć się do korzystania z klucza dostępu do konta, kliknij link wyróżniony na obrazie. Jeśli masz dostęp do klucza konta, będziesz mieć możliwość przejścia. Jeśli jednak nie masz dostępu do klucza konta, zobaczysz komunikat o błędzie podobny do następującego:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="Błąd wyświetlany, jeśli nie masz dostępu do klucza konta":::

Zwróć uwagę, że na liście nie są wyświetlane żadne obiekty blob, jeśli nie masz dostępu do kluczy konta. Kliknij link **Przejdź do konta użytkownika usługi Azure AD** , aby ponownie użyć konta usługi Azure AD do uwierzytelniania.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Określ sposób autoryzacji operacji przekazywania obiektów BLOB

Po przekazaniu obiektu BLOB z Azure Portal można określić, czy uwierzytelnianie i autoryzację tej operacji przy użyciu klucza dostępu do konta lub poświadczeń usługi Azure AD. Domyślnie w portalu jest stosowana bieżąca metoda uwierzytelniania, jak pokazano w temacie [Określanie bieżącej metody uwierzytelniania](#determine-the-current-authentication-method).

Aby określić sposób autoryzacji operacji przekazywania obiektów blob, wykonaj następujące kroki:

1. W Azure Portal przejdź do kontenera, do którego chcesz przekazać obiekt BLOB.
1. Wybierz przycisk **Przekaż**.
1. Rozwiń sekcję **Zaawansowane** , aby wyświetlić zaawansowane właściwości obiektu BLOB.
1. W polu **Typ uwierzytelniania** wskaż, czy chcesz autoryzować operację przekazywania za pomocą konta usługi Azure AD, czy z kluczem dostępu do konta, jak pokazano na poniższej ilustracji:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Zrzut ekranu przedstawiający sposób zmiany metody autoryzacji w przekazywaniu obiektów BLOB":::

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md)
- [Użyj Azure Portal, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-portal.md)
- [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-cli.md)
- [Użyj modułu Azure PowerShell, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-powershell.md)

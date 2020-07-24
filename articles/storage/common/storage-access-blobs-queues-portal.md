---
title: Używanie Azure Portal do uzyskiwania dostępu do danych obiektu BLOB lub kolejki
titleSuffix: Azure Storage
description: Gdy uzyskujesz dostęp do danych obiektu BLOB lub kolejki przy użyciu Azure Portal, Portal wysyła żądania do usługi Azure Storage w ramach okładek. Te żądania do usługi Azure Storage mogą być uwierzytelniane i autoryzowane przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: db65ba904cdce7bec83b851c6b50316c89fcedfd
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128730"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Używanie Azure Portal do uzyskiwania dostępu do danych obiektu BLOB lub kolejki

Gdy uzyskujesz dostęp do danych obiektu BLOB lub kolejki przy użyciu [Azure Portal](https://portal.azure.com), Portal wysyła żądania do usługi Azure Storage w ramach okładek. Żądanie do usługi Azure Storage może być autoryzowane przy użyciu konta usługi Azure AD lub klucza dostępu do konta magazynu. Portal wskazuje, której metody używasz, i umożliwia przełączenie między nimi, jeśli masz odpowiednie uprawnienia.  

Możesz również określić sposób autoryzacji pojedynczej operacji przekazywania obiektów BLOB w Azure Portal. Domyślnie Portal używa dowolnej metody, która jest już używana do autoryzacji operacji przekazywania obiektów blob, ale istnieje możliwość zmiany tego ustawienia podczas przekazywania obiektu BLOB.

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Uprawnienia do uzyskiwania dostępu do danych obiektu BLOB lub kolejki

W zależności od tego, jak chcesz autoryzować dostęp do danych obiektu BLOB lub kolejki w Azure Portal, będziesz potrzebować określonych uprawnień. W większości przypadków te uprawnienia są udostępniane za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md)

### <a name="use-the-account-access-key"></a>Korzystanie z klucza dostępu do konta

Aby uzyskać dostęp do danych obiektów blob i kolejek przy użyciu klucza dostępu do konta, musisz mieć przypisaną rolę RBAC, która obejmuje akcję RBAC **Microsoft. Storage/storageAccounts/ListKeys/Action**. Ta rola RBAC może być rolą wbudowaną lub niestandardową. Wbudowane role obsługujące **firmę Microsoft. Storage/storageAccounts/ListKeys/Action** obejmują:

- Rola [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Rola [współautor](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Rola [współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

W przypadku próby uzyskania dostępu do danych obiektu BLOB lub kolejki w Azure Portal Portal najpierw sprawdza, czy przypisano rolę z **firmą Microsoft. Storage/storageAccounts/ListKeys/Action**. Jeśli przypisano rolę z tą akcją, Portal używa klucza konta do uzyskiwania dostępu do danych obiektów blob i kolejek. Jeśli nie masz przypisanej roli z tą akcją, Portal próbuje uzyskać dostęp do danych przy użyciu konta usługi Azure AD.

> [!NOTE]
> Administrator usług ról i administrator z uprawnieniami administratora klasycznego ma odpowiednik roli [właściciela](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Rola **właściciela** obejmuje wszystkie akcje, w tym **Microsoft. Storage/storageAccounts/ListKeys/Action**, dzięki czemu użytkownik z jedną z tych ról administracyjnych może również uzyskać dostęp do danych obiektów blob i kolejek przy użyciu klucza konta. Aby uzyskać więcej informacji, zobacz [Role klasycznego administratora subskrypcji, role kontroli na podstawie ról (RBAC) platformy Azure i role administratora usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Korzystanie z konta usługi Azure AD

Aby uzyskać dostęp do danych obiektu BLOB lub kolejki z Azure Portal przy użyciu konta usługi Azure AD, obie następujące instrukcje muszą być prawdziwe:

- Przypisano Ci rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager, co najmniej w zakresie do poziomu konta magazynu lub wyższego. Rola **czytelnika** umożliwia dostęp do najbardziej ograniczonych uprawnień, ale można również uzyskać inną rolę Azure Resource Manager, która udziela dostępu do zasobów zarządzania kontami magazynu.
- Przypisano rolę wbudowaną lub niestandardową, która zapewnia dostęp do danych obiektu BLOB lub kolejki.

Przypisanie roli **czytnika** lub inne Azure Resource Manager przypisanie roli jest konieczne, aby użytkownik mógł wyświetlać i nawigować w Azure Portal zasoby zarządzania kontami magazynu. Role RBAC, które udzielają dostępu do danych obiektu BLOB lub kolejki, nie zapewniają dostępu do zasobów zarządzania kontami magazynu. Aby uzyskać dostęp do danych obiektu BLOB lub kolejki w portalu, użytkownik musi mieć uprawnienia do nawigowania po zasobach konta magazynu. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [Przypisywanie roli czytelnika do dostępu do portalu](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Wbudowane role obsługujące dostęp do danych obiektu BLOB lub kolejki obejmują:

- [Właściciel danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): dla kontroli dostępu POSIX dla Azure Data Lake Storage Gen2.
- [Współautor danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): uprawnienia do odczytu/zapisu/usuwania dla obiektów BLOB.
- [Czytnik danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): uprawnienia tylko do odczytu dla obiektów BLOB.
- [Współautor danych kolejki magazynu](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): uprawnienia do odczytu/zapisu/usuwania dla kolejek.
- [Czytnik danych kolejki magazynu](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): uprawnienia tylko do odczytu dla kolejek.

Role niestandardowe mogą obsługiwać różne kombinacje tych samych uprawnień zapewnianych przez wbudowane role. Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md) i [Opis ról dla zasobów platformy Azure](../../role-based-access-control/role-definitions.md).

Wyświetlanie listy kolejek z rolą klasycznego administratora subskrypcji nie jest obsługiwane. Aby wyświetlić listę kolejek, użytkownik musi mieć przypisane do nich rolę **czytnika** Azure Resource Manager, rolę **czytnika danych kolejki magazynu** lub rolę **współautora danych kolejki magazynu** .

> [!IMPORTANT]
> Wersja zapoznawcza Eksplorator usługi Storage w Azure Portal nie obsługuje korzystania z poświadczeń usługi Azure AD w celu wyświetlania i modyfikowania danych obiektów blob i kolejek. Eksplorator usługi Storage w Azure Portal zawsze używa kluczy konta do uzyskiwania dostępu do danych. Aby użyć Eksplorator usługi Storage w Azure Portal, musisz mieć przypisaną rolę, która zawiera element **Microsoft. Storage/storageAccounts/ListKeys/Action**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Nawigowanie do obiektów blob lub kolejek w portalu

Aby wyświetlić dane obiektu BLOB lub kolejki w portalu, przejdź do **omówienia** konta magazynu i kliknij linki dla **obiektów BLOB** lub **kolejek**. Alternatywnie możesz przejść do sekcji **BLOB Service** i **Usługa kolejki** w menu. 

![Przejdź do obiektu BLOB lub kolejki danych w Azure Portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Ustal bieżącą metodę uwierzytelniania

Gdy przejdziesz do kontenera lub kolejki, Azure Portal wskazuje, czy obecnie używasz klucza dostępu do konta lub konta usługi Azure AD do uwierzytelniania.

W przykładach w tej sekcji pokazano, jak uzyskać dostęp do kontenera i jego obiektów blob, ale w portalu jest wyświetlany ten sam komunikat podczas uzyskiwania dostępu do kolejki i jej komunikatów lub wyświetlania listy kolejek.

### <a name="authenticate-with-the-account-access-key"></a>Uwierzytelnianie przy użyciu klucza dostępu do konta

W przypadku uwierzytelniania przy użyciu klucza dostępu do konta w portalu zostanie wyświetlony **klucz dostępu** określony jako metoda uwierzytelniania:

![Trwa uzyskiwanie dostępu do danych kontenera przy użyciu klucza konta](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Aby przełączyć się do korzystania z konta usługi Azure AD, kliknij link wyróżniony na obrazie. Jeśli masz odpowiednie uprawnienia za pośrednictwem przypisanych do Ciebie ról RBAC, będziesz mieć możliwość przejścia. Jeśli jednak nie masz odpowiednich uprawnień, zobaczysz komunikat o błędzie podobny do następującego:

![Błąd wyświetlany, jeśli konto usługi Azure AD nie obsługuje dostępu](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Zwróć uwagę, że na liście nie ma żadnych obiektów blob, jeśli Twoje konto usługi Azure AD nie ma uprawnień do ich wyświetlania. Kliknij link **Przełącz do klucza dostępu** , aby ponownie użyć klawisza dostępu do uwierzytelniania.

### <a name="authenticate-with-your-azure-ad-account"></a>Uwierzytelnianie za pomocą konta usługi Azure AD

W przypadku uwierzytelniania za pomocą konta usługi Azure AD zobaczysz **konto użytkownika usługi Azure AD** określone jako metoda uwierzytelniania w portalu:

![Trwa uzyskiwanie dostępu do danych kontenera za pomocą konta usługi Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Aby przełączyć się do korzystania z klucza dostępu do konta, kliknij link wyróżniony na obrazie. Jeśli masz dostęp do klucza konta, będziesz mieć możliwość przejścia. Jeśli jednak nie masz dostępu do klucza konta, zobaczysz komunikat o błędzie podobny do następującego:

![Błąd wyświetlany, jeśli nie masz dostępu do klucza konta](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Zwróć uwagę, że na liście nie są wyświetlane żadne obiekty blob, jeśli nie masz dostępu do kluczy konta. Kliknij link **Przejdź do konta użytkownika usługi Azure AD** , aby ponownie użyć konta usługi Azure AD do uwierzytelniania.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Określ sposób autoryzacji operacji przekazywania obiektów BLOB

Po przekazaniu obiektu BLOB z Azure Portal można określić, czy uwierzytelnianie i autoryzację tej operacji przy użyciu klucza dostępu do konta lub poświadczeń usługi Azure AD. Domyślnie w portalu jest stosowana bieżąca metoda uwierzytelniania, jak pokazano w temacie [Określanie bieżącej metody uwierzytelniania](#determine-the-current-authentication-method).

Aby określić sposób autoryzacji operacji przekazywania obiektów blob, wykonaj następujące kroki:

1. W Azure Portal przejdź do kontenera, do którego chcesz przekazać obiekt BLOB.
1. Wybierz przycisk **Przekaż**.
1. Rozwiń sekcję **Zaawansowane** , aby wyświetlić zaawansowane właściwości obiektu BLOB.
1. W polu **Typ uwierzytelniania** wskaż, czy chcesz autoryzować operację przekazywania za pomocą konta usługi Azure AD, czy z kluczem dostępu do konta, jak pokazano na poniższej ilustracji:

    :::image type="content" source="media/storage-access-blobs-queues-portal/auth-blob-upload.png" alt-text="Zrzut ekranu przedstawiający sposób zmiany metody autoryzacji w przekazywaniu obiektów BLOB":::

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md)
- [Udzielanie dostępu do kontenerów i kolejek platformy Azure przy użyciu RBAC w Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach przy użyciu interfejsu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [ przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)

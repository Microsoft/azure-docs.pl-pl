---
title: Używanie Azure Portal do przypisywania roli RBAC na potrzeby dostępu do danych
titleSuffix: Azure Storage
description: Dowiedz się, w jaki sposób używać Azure Portal do przypisywania uprawnień do Azure Active Directory podmiotu zabezpieczeń z kontrolą dostępu opartą na rolach (RBAC). Usługa Azure Storage obsługuje wbudowane i niestandardowe role RBAC do uwierzytelniania za pośrednictwem usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b22c653d25dc23bbcb249344affaf83a07f190af
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024902"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Użyj Azure Portal, aby przypisać rolę RBAC na potrzeby dostępu do danych obiektów blob i kolejek

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych obiektu BLOB lub kolejki.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano sposób używania Azure Portal do przypisywania ról RBAC. Azure Portal udostępnia prosty interfejs do przypisywania ról RBAC i zarządzania dostępem do zasobów magazynu. Można także przypisać role RBAC dla zasobów obiektów blob i kolejek przy użyciu narzędzi wiersza polecenia platformy Azure lub interfejsów API zarządzania usługi Azure Storage. Aby uzyskać więcej informacji na temat ról RBAC dla zasobów magazynu, zobacz [uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określ zakres zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Przypisywanie ról RBAC przy użyciu Azure Portal

Po ustaleniu odpowiedniego zakresu przypisania roli przejdź do tego zasobu w Azure Portal. Wyświetl ustawienia **Access Control (IAM)** dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

1. Przypisz odpowiednią rolę RBAC usługi Azure Storage, aby udzielić dostępu do podmiotu zabezpieczeń usługi Azure AD.

1. Przypisz rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager użytkownikom, którzy muszą uzyskać dostęp do kontenerów lub kolejek za pośrednictwem Azure Portal przy użyciu poświadczeń usługi Azure AD. 

W poniższych sekcjach opisano poszczególne kroki bardziej szczegółowo.

> [!NOTE]
> Jako właściciel konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych. Musisz jawnie przypisać rolę RBAC do usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera lub kolejki.
>
> Nie można przypisać roli do kontenera lub kolejki, jeśli konto magazynu ma włączoną hierarchiczną przestrzeń nazw.

### <a name="assign-a-built-in-rbac-role"></a>Przypisywanie wbudowanej roli RBAC

Przed przypisaniem roli do podmiotu zabezpieczeń należy wziąć pod uwagę zakres udzielanych uprawnień. Przejrzyj sekcję [Określanie zakresu zasobów](#determine-resource-scope) , aby określić odpowiedni zakres.

Pokazana tutaj procedura przypisuje rolę w zakresie kontenera, ale można wykonać te same kroki, aby przypisać rolę w zakresie do kolejki:

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu i Wyświetl **Przegląd** dla tego konta.
1. W obszarze usługi wybierz pozycję **obiekty blob**.
1. Znajdź kontener, do którego chcesz przypisać rolę, i Wyświetl ustawienia kontenera.
1. Wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla kontenera. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.

    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do kontenera](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Kliknij przycisk **Dodaj przypisanie roli** , aby dodać nową rolę.
1. W oknie **Dodawanie przypisania roli** wybierz rolę usługi Azure Storage, którą chcesz przypisać. Następnie wyszukaj w celu zlokalizowania podmiotu zabezpieczeń, do którego chcesz przypisać tę rolę.

    ![Zrzut ekranu przedstawiający sposób przypisywania roli RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Kliknij pozycję **Zapisz**. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że dodany użytkownik ma teraz uprawnienia do odczytu danych w kontenerze o nazwie *Sample-Container*.

    ![Zrzut ekranu przedstawiający listę użytkowników przypisanych do roli](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Możesz wykonać podobne kroki, aby przypisać rolę do zakresu konta magazynu, grupy zasobów lub subskrypcji.

### <a name="assign-the-reader-role-for-portal-access"></a>Przypisz rolę czytelnika do dostępu do portalu

Gdy przypiszesz wbudowaną lub niestandardową rolę usługi Azure Storage do podmiotu zabezpieczeń, przyznasz uprawnienia temu podmiotowi zabezpieczeń do wykonywania operacji na danych na koncie magazynu. Wbudowane role **czytnika danych** zapewniają uprawnienia do odczytu danych w kontenerze lub kolejce, natomiast wbudowane role **współautor danych** zapewniają uprawnienia do odczytu, zapisu i usuwania do kontenera lub kolejki. Uprawnienia są ograniczone do określonego zasobu.  
Na przykład, Jeśli rola **współautor danych obiektów blob magazynu** zostanie przypisana do użytkownika Mary na poziomie kontenera o nazwie **Sample-Container**, Mary zostanie udzielony dostęp do odczytu, zapisu i usuwania do wszystkich obiektów BLOB w tym kontenerze.

Jeśli jednak użytkownik Jan chce wyświetlić obiekt BLOB w Azure Portal, wówczas rola **współautor danych obiektów blob magazynu** nie będzie mieć wystarczających uprawnień do nawigowania w portalu do obiektu BLOB w celu wyświetlenia go. Dodatkowe uprawnienia usługi Azure AD są wymagane do nawigowania po portalu i wyświetlania innych zasobów, które są tam widoczne.

Jeśli użytkownicy muszą mieć dostęp do obiektów BLOB w Azure Portal, przypisz im dodatkową rolę RBAC, rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) do tych użytkowników, na poziomie konta magazynu lub nowszym. Rola **czytelnik** jest rolą Azure Resource Manager, która umożliwia użytkownikom wyświetlanie zasobów konta magazynu, ale ich nie należy modyfikować. Nie zapewnia uprawnień do odczytu danych w usłudze Azure Storage, ale tylko do zasobów zarządzania kontami.

Wykonaj następujące kroki, aby przypisać rolę **czytelnika** , aby użytkownik mógł uzyskać dostęp do obiektów blob z Azure Portal. W tym przykładzie przypisanie jest ograniczone do konta magazynu:

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.
1. Wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla konta magazynu. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **czytelnik** . 
1. W polu **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
1. Wyszukaj w celu zlokalizowania podmiotu zabezpieczeń, do którego chcesz przypisać rolę.
1. Zapisz przypisanie roli.

Przypisywanie roli **czytelnik** jest niezbędne tylko dla użytkowników, którzy muszą uzyskać dostęp do obiektów blob lub kolejek przy użyciu Azure Portal.

> [!IMPORTANT]
> Wersja zapoznawcza Eksplorator usługi Storage w Azure Portal nie obsługuje korzystania z poświadczeń usługi Azure AD w celu wyświetlania i modyfikowania danych obiektów blob i kolejek. Eksplorator usługi Storage w Azure Portal zawsze używa kluczy konta do uzyskiwania dostępu do danych. Aby użyć Eksplorator usługi Storage w Azure Portal, musisz mieć przypisaną rolę, która zawiera element **Microsoft. Storage/storageAccounts/ListKeys/Action**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat ról RBAC dla zasobów magazynu, zobacz [uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md). 
- Aby dowiedzieć się więcej na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC)?](../../role-based-access-control/overview.md)
- Aby dowiedzieć się, jak przypisywać i zarządzać przypisaniami ról RBAC przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejek z poziomu aplikacji magazynu, zobacz [Korzystanie z usługi Azure AD z aplikacjami usługi Azure Storage](storage-auth-aad-app.md).

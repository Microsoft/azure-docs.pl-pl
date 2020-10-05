---
title: Użyj Azure Portal, aby przypisać rolę platformy Azure na potrzeby dostępu do danych
titleSuffix: Azure Storage
description: Dowiedz się, w jaki sposób używać Azure Portal do przypisywania uprawnień do Azure Active Directory podmiotu zabezpieczeń za pomocą kontroli dostępu opartej na rolach (Azure RBAC). Usługa Azure Storage obsługuje wbudowaną i niestandardową rolę platformy Azure do uwierzytelniania za pośrednictwem usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e2d577261a1cea0bad9aab549b3669f8fdef5751
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715849"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Użyj Azure Portal, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych obiektu BLOB lub kolejki.

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano sposób używania Azure Portal do przypisywania ról platformy Azure. Azure Portal udostępnia prosty interfejs do przypisywania ról platformy Azure i zarządzania dostępem do zasobów magazynu. Role platformy Azure dla zasobów obiektów blob i Queue można także przypisywać przy użyciu narzędzi wiersza polecenia platformy Azure lub interfejsów API zarządzania usługi Azure Storage. Aby uzyskać więcej informacji na temat ról platformy Azure dla zasobów magazynu, zobacz temat [uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md).

## <a name="azure-roles-for-blobs-and-queues"></a>Role platformy Azure dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określ zakres zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Przypisywanie ról platformy Azure przy użyciu Azure Portal

Po ustaleniu odpowiedniego zakresu przypisania roli przejdź do tego zasobu w Azure Portal. Wyświetl ustawienia **Access Control (IAM)** dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

1. Przypisz odpowiednią rolę usługi Azure Storage w celu udzielenia dostępu do podmiotu zabezpieczeń usługi Azure AD.

1. Przypisz rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager użytkownikom, którzy muszą uzyskać dostęp do kontenerów lub kolejek za pośrednictwem Azure Portal przy użyciu poświadczeń usługi Azure AD. 

W poniższych sekcjach opisano poszczególne kroki bardziej szczegółowo.

> [!NOTE]
> Jako właściciel konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych. Musisz jawnie przypisać sobie rolę platformy Azure dla usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera lub kolejki.
>
> Nie można przypisać roli do kontenera lub kolejki, jeśli konto magazynu ma włączoną hierarchiczną przestrzeń nazw.

### <a name="assign-an-azure-built-in-role"></a>Przypisywanie roli wbudowanej platformy Azure

Przed przypisaniem roli do podmiotu zabezpieczeń należy wziąć pod uwagę zakres udzielanych uprawnień. Przejrzyj sekcję [Określanie zakresu zasobów](#determine-resource-scope) , aby określić odpowiedni zakres.

Pokazana tutaj procedura przypisuje rolę w zakresie kontenera, ale można wykonać te same kroki, aby przypisać rolę w zakresie do kolejki:

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu i Wyświetl **Przegląd** dla tego konta.
1. W obszarze usługi wybierz pozycję **obiekty blob**.
1. Znajdź kontener, do którego chcesz przypisać rolę, i Wyświetl ustawienia kontenera.
1. Wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla kontenera. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="Zrzut ekranu przedstawiający ustawienia kontroli dostępu do kontenera":::

1. Kliknij przycisk **Dodaj przypisanie roli** , aby dodać nową rolę.
1. W oknie **Dodawanie przypisania roli** wybierz rolę usługi Azure Storage, którą chcesz przypisać. Następnie wyszukaj w celu zlokalizowania podmiotu zabezpieczeń, do którego chcesz przypisać tę rolę.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Zrzut ekranu przedstawiający ustawienia kontroli dostępu do kontenera":::

1. Kliknij pozycję **Zapisz**. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono, że dodany użytkownik ma teraz uprawnienia do odczytu danych w kontenerze o nazwie *Sample-Container*.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="Zrzut ekranu przedstawiający ustawienia kontroli dostępu do kontenera":::

Możesz wykonać podobne kroki, aby przypisać rolę do zakresu konta magazynu, grupy zasobów lub subskrypcji.

### <a name="assign-the-reader-role-for-portal-access"></a>Przypisz rolę czytelnika do dostępu do portalu

Gdy przypiszesz wbudowaną lub niestandardową rolę usługi Azure Storage do podmiotu zabezpieczeń, przyznasz uprawnienia temu podmiotowi zabezpieczeń do wykonywania operacji na danych na koncie magazynu. Wbudowane role **czytnika danych** zapewniają uprawnienia do odczytu danych w kontenerze lub kolejce, natomiast wbudowane role **współautor danych** zapewniają uprawnienia do odczytu, zapisu i usuwania do kontenera lub kolejki. Uprawnienia są ograniczone do określonego zasobu.  
Na przykład, Jeśli rola **współautor danych obiektów blob magazynu** zostanie przypisana do użytkownika Mary na poziomie kontenera o nazwie **Sample-Container**, Mary zostanie udzielony dostęp do odczytu, zapisu i usuwania do wszystkich obiektów BLOB w tym kontenerze.

Jeśli jednak użytkownik Jan chce wyświetlić obiekt BLOB w Azure Portal, wówczas rola **współautor danych obiektów blob magazynu** nie będzie mieć wystarczających uprawnień do nawigowania w portalu do obiektu BLOB w celu wyświetlenia go. Dodatkowe uprawnienia usługi Azure AD są wymagane do nawigowania po portalu i wyświetlania innych zasobów, które są tam widoczne.

Jeśli użytkownicy muszą mieć dostęp do obiektów BLOB w Azure Portal, przypisz im dodatkową rolę platformy Azure, rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) do tych użytkowników, na poziomie konta magazynu lub nowszym. Rola **czytelnik** jest rolą Azure Resource Manager, która umożliwia użytkownikom wyświetlanie zasobów konta magazynu, ale ich nie należy modyfikować. Nie zapewnia uprawnień do odczytu danych w usłudze Azure Storage, ale tylko do zasobów zarządzania kontami.

Wykonaj następujące kroki, aby przypisać rolę **czytelnika** , aby użytkownik mógł uzyskać dostęp do obiektów blob z Azure Portal. W tym przykładzie przypisanie jest ograniczone do konta magazynu:

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego konta magazynu.
1. Wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla konta magazynu. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **czytelnik** . 
1. W polu **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
1. Wyszukaj w celu zlokalizowania podmiotu zabezpieczeń, do którego chcesz przypisać rolę.
1. Zapisz przypisanie roli.

Przypisywanie roli **czytelnik** jest niezbędne tylko dla użytkowników, którzy muszą uzyskać dostęp do obiektów blob lub kolejek przy użyciu Azure Portal.

> [!IMPORTANT]
> Wersja zapoznawcza Eksplorator usługi Storage w Azure Portal nie obsługuje korzystania z poświadczeń usługi Azure AD w celu wyświetlania i modyfikowania danych obiektów blob i kolejek. Eksplorator usługi Storage w Azure Portal zawsze używa kluczy konta do uzyskiwania dostępu do danych. Aby użyć Eksplorator usługi Storage w Azure Portal, musisz mieć przypisaną rolę, która zawiera element **Microsoft. Storage/storageAccounts/ListKeys/Action**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat ról platformy Azure dla zasobów magazynu, zobacz temat [uwierzytelnianie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md). 
- Aby dowiedzieć się więcej na temat usługi Azure RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md).
- Aby dowiedzieć się, jak przypisywać przypisania ról platformy Azure i zarządzać nimi za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST, zobacz następujące artykuły:
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu modułu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](../../role-based-access-control/role-assignments-rest.md)
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejek z poziomu aplikacji magazynu, zobacz [Korzystanie z usługi Azure AD z aplikacjami usługi Azure Storage](storage-auth-aad-app.md).

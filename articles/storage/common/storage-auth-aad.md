---
title: Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Active Directory
titleSuffix: Azure Storage
description: Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory (Azure AD). Przypisywanie ról platformy Azure na potrzeby praw dostępu. Dostęp do danych za pomocą konta usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: f681a271dcd64468cb471c81496e489f0ce8413f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713662"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory

Usługa Azure Storage obsługuje używanie Azure Active Directory (Azure AD) do autoryzacji żądań do magazynu obiektów blob i kolejek. Korzystając z usługi Azure AD, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC), aby przyznać uprawnienia podmiotowi zabezpieczeń, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD w celu zwrócenia tokenu OAuth 2,0. Tokenu można następnie użyć do autoryzowania żądania do magazynu obiektów blob lub Queue.

Autoryzacja żądań do usługi Azure Storage za pomocą usługi Azure AD zapewnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z autoryzacją klucza współużytkowanego. Firma Microsoft zaleca używanie autoryzacji usługi Azure AD z obiektami BLOB i kolejkowanie aplikacji, o ile jest to możliwe, aby zminimalizować potencjalne luki w zabezpieczeniach związane z kluczem udostępnionym.

Autoryzacja za pomocą usługi Azure AD jest dostępna dla wszystkich kont ogólnego przeznaczenia i magazynu obiektów BLOB we wszystkich regionach publicznych i w chmurach narodowych. Tylko konta magazynu utworzone za pomocą modelu wdrażania Azure Resource Manager obsługują autoryzację usługi Azure AD.

Usługa BLOB Storage obsługuje dodatkowo tworzenie sygnatur dostępu współdzielonego (SAS), które są podpisane przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do danych za pomocą sygnatur dostępu współdzielonego](storage-sas-overview.md).

Azure Files obsługuje autoryzację za pomocą usługi AD (wersja zapoznawcza) lub Azure AD DS (GA) za pośrednictwem protokołu SMB dla maszyn wirtualnych przyłączonych do domeny. Aby dowiedzieć się więcej o korzystaniu z usług AD (wersja zapoznawcza) lub Azure AD DS (GA) za pośrednictwem protokołu SMB dla Azure Files, zobacz [Azure Files Omówienie obsługi uwierzytelniania opartego na tożsamościach w usłudze SMB](../files/storage-files-active-directory-overview.md).

Autoryzacja za pomocą usługi Azure AD nie jest obsługiwana w przypadku usługi Azure Table Storage. Użyj klucza współużytkowanego, aby autoryzować żądania do magazynu tabel.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Omówienie usługi Azure AD dla obiektów blob i kolejek

Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do zasobu obiektu BLOB lub kolejki, żądanie musi być autoryzowane, chyba że jest on obiektem BLOB dostępnym dla użytkowników anonimowych. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. Następnie token jest przesyłany jako część żądania do obiektu BLOB lub usługa kolejki i używany przez usługę do autoryzacji dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby aplikacja zażądała tokenu dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja Azure Functions, może używać [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) do uzyskiwania dostępu do obiektów blob lub kolejek. Aby dowiedzieć się, jak autoryzować żądania wysyłane przez zarządzaną tożsamość do obiektu blob platformy Azure lub usługa kolejki, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](storage-auth-aad-msi.md).

Krok autoryzacji wymaga, aby co najmniej jedna rola platformy Azure była przypisana do podmiotu zabezpieczeń. Usługa Azure Storage udostępnia role platformy Azure, które obejmują wspólne zestawy uprawnień dla danych obiektów blob i kolejek. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby dowiedzieć się więcej na temat przypisywania ról platformy Azure dla usługi Azure Storage, zobacz [Zarządzanie prawami dostępu do danych magazynu za pomocą usługi Azure RBAC](storage-auth-aad-rbac.md).

Natywne aplikacje i aplikacje sieci Web, które wysyłają żądania do obiektu blob platformy Azure lub usługa kolejki mogą również autoryzować dostęp za pomocą usługi Azure AD. Aby dowiedzieć się, jak zażądać tokenu dostępu i używać go do autoryzacji żądań dotyczących danych obiektów blob lub kolejek, zobacz temat [autoryzowanie dostępu do usługi Azure Storage za pomocą usługi Azure AD z aplikacji usługi Azure Storage](storage-auth-aad-app.md).

## <a name="assign-azure-roles-for-access-rights"></a>Przypisywanie ról platformy Azure na potrzeby praw dostępu

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowanych ról platformy Azure, które obejmują wspólne zestawy uprawnień używane do uzyskiwania dostępu do danych obiektów blob i kolejek. Można także definiować role niestandardowe na potrzeby dostępu do danych obiektów blob i kolejek.

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Wbudowane role platformy Azure dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Aby dowiedzieć się, jak przypisać wbudowaną rolę platformy Azure do podmiotu zabezpieczeń, zobacz jeden z następujących artykułów:

- [Użyj Azure Portal, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-portal.md)
- [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-cli.md)
- [Użyj modułu Azure PowerShell, aby przypisać rolę platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-powershell.md)

Aby uzyskać więcej informacji o tym, jak wbudowane role są zdefiniowane dla usługi Azure Storage, zobacz [Omówienie definicji ról](../../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje na temat tworzenia ról niestandardowych platformy Azure, zobacz [role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Uprawnienia dostępu do operacji na danych

Aby uzyskać szczegółowe informacje na temat uprawnień wymaganych do wywołania określonych operacji obiektu BLOB lub usługa kolejki, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Zakres zasobu

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Uzyskiwanie dostępu do danych za pomocą konta usługi Azure AD

Dostęp do danych obiektów blob lub kolejek za pośrednictwem Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure można autoryzować za pomocą konta usługi Azure AD użytkownika lub za pomocą kluczy dostępu do konta (autoryzacja klucza współdzielonego).

### <a name="data-access-from-the-azure-portal"></a>Dostęp do danych z Azure Portal

Azure Portal może korzystać z konta usługi Azure AD lub kluczy dostępu do konta w celu uzyskiwania dostępu do danych obiektów blob i kolejek na koncie usługi Azure Storage. Który schemat autoryzacji używany przez Azure Portal zależy od ról platformy Azure, które są przypisane do użytkownika.

Gdy próbujesz uzyskać dostęp do danych obiektu BLOB lub kolejki, Azure Portal najpierw sprawdzisz, czy masz przypisaną rolę platformy Azure z **firmą Microsoft. Storage/storageAccounts/ListKeys/Action**. Jeśli przypisano rolę z tą akcją, Azure Portal używa klucza konta do uzyskiwania dostępu do danych obiektów blob i kolejek za pośrednictwem autoryzacji klucza wspólnego. Jeśli nie masz przypisanej roli z tą akcją, Azure Portal próbuje uzyskać dostęp do danych przy użyciu konta usługi Azure AD.

Aby uzyskać dostęp do danych obiektu BLOB lub kolejki z Azure Portal przy użyciu konta usługi Azure AD, musisz mieć uprawnienia dostępu do danych obiektów blob i kolejek, a także potrzebujesz uprawnień do nawigowania po zasobach konta magazynu w Azure Portal. Wbudowane role udostępniane przez usługę Azure Storage zapewniają dostęp do zasobów obiektów blob i kolejek, ale nie udzielają uprawnień do zasobów konta magazynu. Z tego powodu dostęp do portalu wymaga również przypisania roli Azure Resource Manager, takiej jak rola [czytnika](../../role-based-access-control/built-in-roles.md#reader) , w zakresie do poziomu konta magazynu lub wyższego. Rola **czytelnika** umożliwia dostęp do najbardziej ograniczonych uprawnień, ale można również uzyskać inną rolę Azure Resource Manager, która udziela dostępu do zasobów zarządzania kontami magazynu. Aby dowiedzieć się więcej na temat przypisywania uprawnień użytkownikom na potrzeby dostępu do danych w Azure Portal za pomocą konta usługi Azure AD, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-portal.md).

Azure Portal wskazuje, który schemat autoryzacji jest używany podczas przechodzenia do kontenera lub kolejki. Aby uzyskać więcej informacji o dostępie do danych w portalu, zobacz [Wybieranie metody autoryzacji dostępu do danych obiektów BLOB w Azure Portal](../blobs/authorize-blob-access-portal.md) i [Wybieranie metody autoryzacji dostępu do danych w kolejce w Azure Portal](../queues/authorize-queue-access-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Dostęp do danych z programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure i program PowerShell obsługują Logowanie przy użyciu poświadczeń usługi Azure AD. Po zalogowaniu się sesja zostanie uruchomiona w ramach tych poświadczeń. Aby dowiedzieć się więcej, zobacz [Uruchamianie interfejsu wiersza polecenia platformy Azure lub poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki](authorize-active-directory-powershell.md)

## <a name="next-steps"></a>Następne kroki

- [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](storage-auth-aad-msi.md)
- [Autoryzuj przy użyciu Azure Active Directory z aplikacji w celu uzyskania dostępu do obiektów blob i kolejek](storage-auth-aad-app.md)
- [Obsługa usługi Azure Storage na potrzeby kontroli dostępu opartej na Azure Active Directory jest ogólnie dostępna](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

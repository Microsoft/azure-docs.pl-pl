---
title: Korzystanie z dostawcy zasobów usługi Azure Storage w celu uzyskania dostępu do zasobów zarządzania
description: Dostawca zasobów usługi Azure Storage to usługa zapewniająca dostęp do zasobów zarządzania dla usługi Azure Storage. Dostawcy zasobów usługi Azure Storage można użyć do tworzenia, aktualizowania, zarządzania i usuwania zasobów, takich jak konta magazynu, prywatne punkty końcowe i klucze dostępu do konta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 162aa0c382ec22f946d20299fbb990b92481518f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714699"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Korzystanie z dostawcy zasobów usługi Azure Storage w celu uzyskania dostępu do zasobów zarządzania

Usługa Azure Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Dostawca zasobów usługi Azure Storage to usługa oparta na Azure Resource Manager i zapewniająca dostęp do zasobów zarządzania dla usługi Azure Storage. Dostawcy zasobów usługi Azure Storage można użyć do tworzenia, aktualizowania, zarządzania i usuwania zasobów, takich jak konta magazynu, prywatne punkty końcowe i klucze dostępu do konta. Aby uzyskać więcej informacji na temat Azure Resource Manager, zobacz [Azure Resource Manager omówienie](/azure/azure-resource-manager/resource-group-overview).

Dostawcy zasobów usługi Azure Storage można używać do wykonywania akcji, takich jak tworzenie lub usuwanie konta magazynu lub pobieranie listy kont magazynu w ramach subskrypcji. Aby autoryzować żądania względem dostawcy zasobów usługi Azure Storage, użyj Azure Active Directory (Azure AD). W tym artykule opisano sposób przypisywania uprawnień do zasobów zarządzania i wskazuje przykłady, które pokazują, jak wykonywać żądania względem dostawcy zasobów usługi Azure Storage.

## <a name="management-resources-versus-data-resources"></a>Zasoby zarządzania i zasoby danych

Firma Microsoft udostępnia dwa interfejsy API REST do pracy z zasobami usługi Azure Storage. Te interfejsy API stanowią podstawę wszystkich akcji, które można wykonać w odniesieniu do usługi Azure Storage. Interfejs API REST usługi Azure Storage umożliwia korzystanie z danych na koncie magazynu, w tym obiektów blob, kolejek, plików i tabel. Interfejs API REST dostawcy zasobów usługi Azure Storage umożliwia korzystanie z konta magazynu i powiązanych zasobów.

Żądanie, które odczytuje lub zapisuje dane obiektów blob, wymaga innych uprawnień niż żądanie wykonujące operację zarządzania. Usługa Azure RBAC zapewnia szczegółową kontrolę nad uprawnieniami do obu typów zasobów. Gdy przypiszesz rolę platformy Azure do podmiotu zabezpieczeń, upewnij się, że rozumiesz, jakie uprawnienia mają być przyznane. Aby uzyskać szczegółowe informacje dotyczące akcji skojarzonych z każdą wbudowaną rolą platformy Azure, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Usługa Azure Storage obsługuje używanie usługi Azure AD do autoryzowania żądań względem obiektów blob i queue storage. Aby uzyskać informacje na temat ról platformy Azure dla operacji na danych obiektów blob i kolejek, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Przypisywanie uprawnień zarządzania przy użyciu kontroli dostępu opartej na rolach (Azure RBAC)

Każda subskrypcja platformy Azure ma skojarzoną Azure Active Directory, która zarządza użytkownikami, grupami i aplikacjami. Użytkownik, Grupa lub aplikacja jest również nazywana podmiotem zabezpieczeń w kontekście [platformy tożsamości firmy Microsoft](/azure/active-directory/develop/). Dostęp do zasobów w ramach subskrypcji można udzielić podmiotowi zabezpieczeń, który jest zdefiniowany w Active Directory przy użyciu kontroli dostępu opartej na rolach (Azure RBAC).

Po przypisaniu roli platformy Azure do podmiotu zabezpieczeń należy również wskazać zakres, w którym obowiązują uprawnienia przyznane przez rolę. W przypadku operacji zarządzania można przypisać rolę na poziomie subskrypcji, grupy zasobów lub konta magazynu. Rolę platformy Azure można przypisać do podmiotu zabezpieczeń za pomocą [Azure Portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure](../../cli-install-nodejs.md), [programu PowerShell](/powershell/azure/)lub [interfejsu API REST dostawcy zasobów usługi Azure Storage](/rest/api/storagerp).

Aby uzyskać więcej informacji, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)?](../../role-based-access-control/overview.md) role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Wbudowane role dla operacji zarządzania

Platforma Azure udostępnia wbudowane role, które udzielają uprawnień do wywoływania operacji zarządzania. Usługa Azure Storage udostępnia także wbudowane role przeznaczone specjalnie do użytku z dostawcą zasobów usługi Azure Storage.

Wbudowane role, które przyznają uprawnienia do wywoływania operacji zarządzania magazynem, obejmują role opisane w poniższej tabeli:

|    Rola na platformie Azure    |    Opis    |    Czy obejmuje dostęp do kluczy konta?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Właściciel** | Może zarządzać wszystkimi zasobami magazynu i dostępem do zasobów.  | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |
| **Współautor**  | Może zarządzać wszystkimi zasobami magazynu, ale nie może zarządzać przypisaniem do zasobów. | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |
| **Czytelnik** | Można wyświetlić informacje o koncie magazynu, ale nie może wyświetlić kluczy konta. | Nie. |
| **Współautor konta magazynu** | Może zarządzać kontem magazynu, uzyskiwać informacje o grupach zasobów i zasobach subskrypcji oraz tworzyć wdrożenia grup zasobów subskrypcji i zarządzać nimi. | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |
| **Administrator dostępu użytkowników** | Może zarządzać dostępem do konta magazynu.   | Tak, umożliwia podmiotowi zabezpieczeń przypisanie wszelkich uprawnień do siebie i innych. |
| **Współautor maszyny wirtualnej** | Może zarządzać maszynami wirtualnymi, ale nie z konta magazynu, z którym są połączone.   | Tak, zapewnia uprawnienia do wyświetlania i ponownego generowania kluczy konta magazynu. |

Trzecia kolumna w tabeli wskazuje, czy wbudowana rola obsługuje **Microsoft. Storage/storageAccounts/ListKeys/Action**. Ta akcja przyznaje uprawnienia do odczytu i ponownego wygenerowania kluczy konta magazynu. Uprawnienia dostępu do zasobów usługi Azure Storage Management nie obejmują również uprawnień dostępu do danych. Jeśli jednak użytkownik ma dostęp do kluczy konta, może używać kluczy konta do uzyskiwania dostępu do danych usługi Azure Storage za pomocą autoryzacji klucza współużytkowanego.

### <a name="custom-roles-for-management-operations"></a>Role niestandardowe dla operacji zarządzania

Platforma Azure obsługuje także definiowanie ról niestandardowych platformy Azure na potrzeby dostępu do zasobów zarządzania. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe platformy Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Przykłady kodu

Przykłady kodu pokazujące sposób autoryzacji i wywoływania operacji zarządzania z bibliotek zarządzania usługi Azure Storage można znaleźć w następujących przykładach:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager a wdrożenia klasyczne

Model wdrażania przy użyciu usługi Resource Manager i model klasyczny to dwa różne sposoby wdrażania rozwiązań platformy Azure i zarządzania nimi. Firma Microsoft zaleca korzystanie z modelu wdrażania Azure Resource Manager podczas tworzenia nowego konta magazynu. Jeśli to możliwe, firma Microsoft zaleca również ponowne utworzenie istniejących klasycznych kont magazynu z modelem Menedżer zasobów. Chociaż można utworzyć konto magazynu przy użyciu klasycznego modelu wdrażania, model klasyczny jest mniej elastyczny i ostatecznie będzie przestarzały.

Aby uzyskać więcej informacji na temat modeli wdrażania platformy Azure, zobacz [Menedżer zasobów i wdrożenie klasyczne](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
- [Co to jest kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
- [Elementy docelowe skalowalności dla dostawcy zasobów usługi Azure Storage](scalability-targets-resource-provider.md)

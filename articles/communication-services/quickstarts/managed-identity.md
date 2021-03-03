---
title: Korzystanie z tożsamości zarządzanych w usługach komunikacyjnych
titleSuffix: An Azure Communication Services quickstart
description: Zarządzane tożsamości umożliwiają autoryzowanie dostępu do usług Azure Communications Services z aplikacji uruchamianych na maszynach wirtualnych platformy Azure, aplikacjach funkcji i innych zasobach.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657624"
---
# <a name="use-managed-identities"></a>Używanie tożsamości zarządzanych
Rozpocznij pracę z usługami Azure Communications Services przy użyciu tożsamości zarządzanych. Biblioteki usług komunikacyjnych i klienckie programu SMS obsługują uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym przewodniku szybki start przedstawiono sposób autoryzacji dostępu do tożsamości i bibliotek klienckich programu SMS ze środowiska platformy Azure, które obsługuje zarządzane tożsamości. Opisano w nim również sposób testowania kodu w środowisku programistycznym.

## <a name="prerequisites"></a>Wymagania wstępne

 - Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free)
 - Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Konfigurowanie

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej lub w usłudze App Service

Zarządzane tożsamości powinny być włączone dla zasobów platformy Azure, które są autoryzowane. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Przypisywanie ról platformy Azure za pomocą Azure Portal

1. Przejdź do witryny Azure Portal.
1. Przejdź do zasobu usługi komunikacyjnej platformy Azure.
1. Przejdź do menu Access Control (IAM) — > i Dodaj > Dodaj przypisanie roli.
1. Wybierz rolę "Współautor" (jest to jedyna obsługiwana rola).
1. Wybierz pozycję "tożsamość zarządzana przypisana przez użytkownika" (lub "tożsamość zarządzana przez system"), a następnie wybierz żądaną tożsamość. Zapisz swój wybór.

![Rola tożsamości zarządzanej](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Przypisywanie ról platformy Azure za pomocą programu PowerShell

Aby przypisać role i uprawnienia przy użyciu programu PowerShell, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end
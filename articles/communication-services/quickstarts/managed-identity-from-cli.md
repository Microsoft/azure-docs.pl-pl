---
title: Tworzenie aplikacji do zarządzania tożsamościami Azure Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: An Azure Communication Services quickstart
description: Zarządzane tożsamości umożliwiają autoryzowanie dostępu do usług Azure Communications Services z aplikacji uruchamianych na maszynach wirtualnych platformy Azure, aplikacjach funkcji i innych zasobach. Ten przewodnik Szybki Start koncentruje się na zarządzaniu tożsamościami za pomocą interfejsu wiersza polecenia platformy Azure.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: 2ef5a3b162d62fa79ed01a156345070ee12b4862
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110682"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Autoryzuj dostęp z zarządzaną tożsamością do zasobu komunikacji w środowisku programistycznym

Zestaw SDK tożsamości platformy Azure zapewnia obsługę uwierzytelniania tokenów Azure Active Directory (Azure AD) dla zestawu Azure SDK. Najnowsze wersje zestawów SDK usług Azure Communication Services dla platform .NET, Java, Python i JavaScript integrują się z biblioteką tożsamości platformy Azure, aby zapewnić prosty i bezpieczny sposób uzyskiwania tokenu OAuth 2,0 na potrzeby autoryzacji żądań usług Azure Communication Services.

Zaletą zestawu SDK usługi Azure Identity jest możliwość użycia tego samego kodu do uwierzytelniania w wielu usługach, niezależnie od tego, czy aplikacja działa w środowisku programistycznym, czy na platformie Azure. Zestaw Azure Identity SDK uwierzytelnia podmiot zabezpieczeń. Gdy kod jest uruchomiony na platformie Azure, podmiot zabezpieczeń jest zarządzaną tożsamością dla zasobów platformy Azure. W środowisku programistycznym tożsamość zarządzana nie istnieje, więc zestaw SDK uwierzytelnia użytkownika lub zarejestrowanej aplikacji do celów testowych.

## <a name="prerequisites"></a>Wymagania wstępne

 - Interfejs wiersza polecenia platformy Azure. [Przewodnik instalacji](/cli/azure/install-azure-cli)
 - Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Konfigurowanie

Zarządzane tożsamości powinny być włączone dla zasobów platformy Azure, które są autoryzowane. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Zestawy SDK Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Usługi App Services](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Uwierzytelnianie zarejestrowanej aplikacji w środowisku programistycznym

Jeśli środowisko programistyczne nie obsługuje logowania jednokrotnego lub logowania za pośrednictwem przeglądarki sieci Web, można użyć zarejestrowanej aplikacji do uwierzytelniania ze środowiska deweloperskiego.

### <a name="creating-an-azure-active-directory-registered-application"></a>Tworzenie zarejestrowanej aplikacji Azure Active Directory

Aby utworzyć zarejestrowaną aplikację przy użyciu interfejsu wiersza polecenia platformy Azure, musisz zalogować się do konta platformy Azure, na którym mają zostać wykonane operacje. W tym celu możesz użyć `az login` polecenia i wprowadzić swoje poświadczenia w przeglądarce. Po zalogowaniu się do konta platformy Azure z poziomu interfejsu wiersza polecenia możemy wywołać `az ad sp create-for-rbac` polecenie w celu utworzenia zarejestrowanej aplikacji.

Poniższe przykłady używają interfejsu wiersza polecenia platformy Azure do utworzenia nowej zarejestrowanej aplikacji

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac`Polecenie zwróci listę właściwości nazwy głównej usługi w formacie JSON. Skopiuj te wartości, aby można było użyć ich do utworzenia niezbędnych zmiennych środowiskowych w następnym kroku.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Propagowanie przypisań ról platformy Azure może potrwać kilka minut.

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Zestaw SDK tożsamości platformy Azure odczytuje wartości z trzech zmiennych środowiskowych w czasie wykonywania w celu uwierzytelnienia aplikacji. W poniższej tabeli opisano wartość ustawioną dla każdej zmiennej środowiskowej.

|Zmienna środowiskowa|Wartość
|-|-
|`AZURE_CLIENT_ID`|`appId` wartość z wygenerowanego kodu JSON 
|`AZURE_TENANT_ID`|`tenant` wartość z wygenerowanego kodu JSON
|`AZURE_CLIENT_SECRET`|`password` wartość z wygenerowanego kodu JSON

> [!IMPORTANT]
> Po ustawieniu zmiennych środowiskowych Zamknij i ponownie otwórz okno konsoli. Jeśli używasz programu Visual Studio lub innego środowiska programistycznego, może być konieczne jego ponowne uruchomienie w celu zarejestrowania nowych zmiennych środowiskowych.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o uwierzytelnianiu](../concepts/authentication.md)

Możesz również chcieć:

- [Dowiedz się więcej o bibliotece tożsamości platformy Azure](/dotnet/api/overview/azure/identity-readme)
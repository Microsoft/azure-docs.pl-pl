---
title: Konfigurowanie poświadczeń wdrożenia
description: Dowiedz się, jakie typy poświadczeń wdrożenia znajdują się w Azure App Service oraz jak je skonfigurować i jak z nich korzystać.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c7d3c7c8b5da40a4e9ccd9085af5a850b9ebc3dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052351"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Skonfiguruj poświadczenia wdrażania dla Azure App Service
Aby zabezpieczyć wdrożenie aplikacji z komputera lokalnego, [Azure App Service](./overview.md) obsługuje dwa typy poświadczeń na potrzeby wdrożenia w ramach [lokalnego narzędzia Git](deploy-local-git.md) i [wdrożenia protokołu FTP/S](deploy-ftp.md). Te poświadczenia nie są takie same, jak poświadczenia subskrypcji platformy Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> Strona **centrum deweloperskiego (klasyczna)** w Azure Portal, która jest starym środowiskiem wdrażania, będzie przestarzała w marcu 2021. Ta zmiana nie wpłynie na żadne istniejące ustawienia wdrożenia w aplikacji. można nadal zarządzać wdrażaniem aplikacji na stronie **centrum wdrażania** .

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Konfigurowanie poświadczeń zakresu użytkownika

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom polecenie [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) . Zastąp \<username> i nazwą \<password> użytkownika i hasłem dla wdrożenia. 

- Nazwa użytkownika musi być unikatowa w ramach platformy Azure, a w przypadku lokalnych powiadomień wypychanych Git nie może zawierać symbolu "@". 
- Hasło musi składać się z co najmniej ośmiu znaków, a dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON przedstawiają hasło jako `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Nie można skonfigurować poświadczeń zakresu użytkownika przy użyciu Azure PowerShell. Użyj innej metody lub Rozważ [użycie poświadczeń zakresu aplikacji](#appscope). 

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Poświadczenia zakresu użytkownika można skonfigurować na [stronie zasobów](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)dowolnej aplikacji. Niezależnie od tego, która aplikacja zostanie skonfigurowana, ma zastosowanie do wszystkich aplikacji dla wszystkich subskrypcji na koncie platformy Azure. 

W [Azure Portal](https://portal.azure.com)należy mieć co najmniej jedną aplikację, aby można było uzyskać dostęp do strony poświadczeń wdrożenia. Aby skonfigurować poświadczenia zakresu użytkownika:

1. W menu po lewej stronie aplikacji wybierz pozycję > **Deployment Center**  >  **FTPS poświadczenia** lub **poświadczenia lokalnego narzędzia Git/FTPS**.

    ![Pokazuje, w jaki sposób można wybrać pulpit nawigacyjny FTP z centrum wdrażania w usłudze Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Przewiń w dół do **zakresu użytkowników**, skonfiguruj **nazwę użytkownika** i **hasło**, a następnie wybierz pozycję **Zapisz**.

Po ustawieniu poświadczeń wdrożenia można znaleźć nazwę użytkownika wdrożenia *narzędzia Git* na stronie **Przegląd** aplikacji,

![Pokazuje, jak znaleźć nazwę użytkownika wdrożenia Git na stronie Przegląd aplikacji.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Jeśli skonfigurowano wdrożenie usługi git, na stronie zostanie wyświetlona **Nazwa użytkownika narzędzia Git/Deployment**; w przeciwnym razie **Nazwa użytkownika FTP/Deployment**.

> [!NOTE]
> Na platformie Azure nie jest wyświetlane hasło do wdrożenia zakresu użytkownika. Jeśli zapomnisz hasła, możesz zresetować poświadczenia, wykonując kroki opisane w tej sekcji.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Korzystanie z poświadczeń zakresu użytkownika przy użyciu protokołu FTP/FTPS

Uwierzytelnianie w punkcie końcowym FTP/FTPS przy użyciu poświadczeń zakresu użytkownika wymaga nazwy użytkownika w następującym formacie: `<app-name>\<user-name>`

Ponieważ poświadczenia zakresu użytkownika są połączone z użytkownikiem, a nie konkretnym zasobem, nazwa użytkownika musi być w tym formacie, aby skierować akcję logowania do odpowiedniego punktu końcowego aplikacji.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Pobieranie poświadczeń zakresu aplikacji

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uzyskaj poświadczenia zakresu aplikacji za pomocą polecenia [AZ webapp Deployment list-Publishing-profile](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . Na przykład:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

W przypadku [lokalnego wdrożenia narzędzia Git](deploy-local-git.md)można także użyć polecenia [AZ webapp Deployment list-Publishing-Credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) , aby uzyskać zdalny identyfikator URI usługi git dla aplikacji, przy użyciu poświadczeń zakresu aplikacji już osadzonych. Na przykład:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Pobierz poświadczenia zakresu aplikacji za pomocą polecenia [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . Na przykład:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W menu po lewej stronie aplikacji wybierz pozycję **Deployment Center**  >  **FTPS poświadczenia** lub **poświadczenia lokalnego narzędzia Git/FTPS**.

    ![Pokazuje, w jaki sposób można wybrać pulpit nawigacyjny FTP z centrum wdrażania w usłudze Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. W sekcji **zakres aplikacji** wybierz link **Kopiuj** , aby skopiować nazwę użytkownika lub hasło.

-----

## <a name="reset-application-scope-credentials"></a>Resetowanie poświadczeń zakresu aplikacji

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Zresetuj poświadczenia zakresu aplikacji za pomocą polecenia [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) :

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Zresetuj poświadczenia zakresu aplikacji za pomocą polecenia [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) :

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W menu po lewej stronie aplikacji wybierz pozycję **Deployment Center**  >  **FTPS poświadczenia** lub **poświadczenia lokalnego narzędzia Git/FTPS**.

    ![Pokazuje, w jaki sposób można wybrać pulpit nawigacyjny FTP z centrum wdrażania w usłudze Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. W sekcji **zakres aplikacji** wybierz pozycję **Zresetuj**.

-----

## <a name="disable-basic-authentication"></a>Wyłącz uwierzytelnianie podstawowe

Niektóre organizacje muszą spełniać wymagania dotyczące zabezpieczeń i raczej wyłączyć dostęp za pośrednictwem protokołu FTP lub webdeploy. W ten sposób członkowie organizacji mogą uzyskiwać dostęp do App Services za pomocą interfejsów API, które są kontrolowane przez Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Aby wyłączyć dostęp do usługi FTP do witryny, uruchom następujące polecenie interfejsu wiersza polecenia. Zamień symbole zastępcze na grupę zasobów i nazwę lokacji. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Aby upewnić się, że dostęp do usługi FTP jest zablokowany, możesz spróbować uwierzytelnić się przy użyciu klienta FTP, takiego jak FileZilla. Aby pobrać poświadczenia publikowania, przejdź do bloku przegląd witryny, a następnie kliknij pozycję Pobierz profil publikowania. Użyj nazwy hosta FTP, nazwy użytkownika i hasła w celu uwierzytelnienia, a otrzymasz 401 odpowiedź na błąd, wskazując, że nie masz uprawnień.

### <a name="webdeploy-and-scm"></a>Webdeploy i SCM

Aby wyłączyć podstawowy dostęp uwierzytelniania do witryny webdeploy port i SCM, uruchom następujące polecenie interfejsu wiersza polecenia. Zamień symbole zastępcze na grupę zasobów i nazwę lokacji. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Aby upewnić się, że poświadczenia profilu publikowania są blokowane w programie webdeploy, spróbuj [opublikować aplikację sieci Web przy użyciu programu Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Wyłącz dostęp do interfejsu API

Interfejs API w poprzedniej sekcji jest kopią zapasową kontroli dostępu opartej na rolach (Azure RBAC), co oznacza, że można [utworzyć rolę niestandardową](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) i przypisać użytkownikom niższych priveldged do roli, aby nie mogli włączyć uwierzytelniania podstawowego w dowolnych lokacjach. Aby skonfigurować rolę niestandardową, [wykonaj te instrukcje](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

Możesz również użyć [Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) do inspekcji wszelkich pomyślnych żądań uwierzytelnienia i użyć [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) , aby wymusić tę konfigurację dla wszystkich lokacji w ramach subskrypcji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać tych poświadczeń do wdrażania aplikacji z [lokalnego systemu Git](deploy-local-git.md) lub przy użyciu [protokołu FTP/S](deploy-ftp.md).

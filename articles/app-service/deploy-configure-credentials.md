---
title: Konfigurowanie poświadczeń wdrożenia
description: Dowiedz się, jakie typy poświadczeń wdrażania znajdują się Azure App Service oraz jak je konfigurować i używać.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 008bfa58c117fc1b43227ba73902d921cec25795
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830579"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Konfigurowanie poświadczeń wdrożenia dla Azure App Service
Aby zabezpieczyć wdrażanie aplikacji z komputera lokalnego, program [Azure App Service](./overview.md) dwa typy poświadczeń dla lokalnego wdrożenia usługi [Git](deploy-local-git.md) i wdrożenia [FTP/S.](deploy-ftp.md) Te poświadczenia nie są takie same jak poświadczenia subskrypcji platformy Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> Strona **Centrum programowa (klasyczna)** w Azure Portal, która jest starym środowiskom wdrażania, zostanie wycofana w marcu 2021 r. Ta zmiana nie wpłynie na żadne ustawienia wdrażania istniejące w aplikacji i będzie można nadal zarządzać wdrażaniem aplikacji na **stronie Centrum** wdrażania.

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Konfigurowanie poświadczeń zakresu użytkownika

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom [polecenie az webapp deployment user set.](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) Zastąp \<username> i nazwą użytkownika wdrożenia i \<password> hasłem. 

- Nazwa użytkownika musi być unikatowa w obrębie platformy Azure, a w przypadku lokalnych wypchnięć Git nie może zawierać symbolu "@". 
- Hasło musi mieć co najmniej osiem znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON pokazują hasło jako `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Nie można skonfigurować poświadczeń zakresu użytkownika za pomocą Azure PowerShell. Użyj innej metody lub rozważ użycie [poświadczeń zakresu aplikacji.](#appscope) 

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Poświadczenia zakresu użytkownika można skonfigurować na stronie zasobów [dowolnej aplikacji.](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) Niezależnie od tego, w której aplikacji skonfigurujesz te poświadczenia, będzie ona stosowana do wszystkich aplikacji dla wszystkich subskrypcji na koncie platformy Azure. 

Na stronie [Azure Portal](https://portal.azure.com)musisz mieć co najmniej jedną aplikację, aby uzyskać dostęp do strony poświadczeń wdrożenia. Aby skonfigurować poświadczenia zakresu użytkownika:

1. W menu po lewej stronie aplikacji wybierz pozycję > **usługi** FTPS lub lokalne  >   poświadczenia **usługi Git/FTPS.**

    ![Pokazuje, jak można wybrać pulpit nawigacyjny FTP z Centrum wdrażania w usłudze Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Przewiń w dół **do opcji Zakres użytkownika,** skonfiguruj ustawienia **Nazwa użytkownika** i **Hasło,** a następnie wybierz pozycję **Zapisz.**

Po skonfigurowaniu poświadczeń wdrożenia możesz znaleźć nazwę użytkownika wdrożenia *Git* na stronie **Przegląd** aplikacji.

![Pokazuje, jak znaleźć nazwę użytkownika wdrożenia Usługi Git na stronie Przegląd aplikacji.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Jeśli wdrożenie Git jest skonfigurowane, na stronie zostanie pokazana nazwa użytkownika **narzędzia Git/wdrożenia;** w przeciwnym razie nazwa **użytkownika protokołu FTP/wdrożenia.**

> [!NOTE]
> Na platformie Azure hasło wdrożenia w zakresie użytkownika nie jest wyświetlane. Jeśli nie pamiętasz hasła, możesz zresetować poświadczenia, korzystając z kroków w tej sekcji.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Używanie poświadczeń zakresu użytkownika z protokołem FTP/FTPS

Uwierzytelnianie w punkcie końcowym FTP/FTPS przy użyciu poświadczeń zakresu użytkownika wymaga nazwy użytkownika w następującym formacie: `<app-name>\<user-name>`

Ponieważ poświadczenia zakresu użytkownika są połączone z użytkownikiem, a nie określonym zasobem, nazwa użytkownika musi być w tym formacie, aby skierować akcję logowania do odpowiedniego punktu końcowego aplikacji.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Uzyskiwanie poświadczeń zakresu aplikacji

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Pobierz poświadczenia zakresu aplikacji za pomocą polecenia [az webapp deployment list-publishing-profiles.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) Na przykład:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

W przypadku lokalnego wdrożenia usługi [Git](deploy-local-git.md)możesz również użyć polecenia [az webapp deployment list-publishing-credentials,](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) aby uzyskać zdalny adres URI usługi Git dla aplikacji z już osadzonymi poświadczeniami zakresu aplikacji. Na przykład:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Pobierz poświadczenia zakresu aplikacji za pomocą polecenia [Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) Na przykład:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W menu po lewej stronie aplikacji wybierz pozycję **Poświadczenia** FTPS Centrum wdrażania  >   lub Lokalne poświadczenia **usługi Git/FTPS.**

    ![Pokazuje, jak można wybrać pulpit nawigacyjny FTP z Centrum wdrażania w usłudze Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. W sekcji **Zakres aplikacji** wybierz **link** Kopiuj, aby skopiować nazwę użytkownika lub hasło.

-----

## <a name="reset-application-scope-credentials"></a>Resetowanie poświadczeń zakresu aplikacji

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Zresetuj poświadczenia zakresu aplikacji za pomocą [polecenia az resource invoke-action:](/cli/azure/resource#az_resource_invoke_action)

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Zresetuj poświadczenia zakresu aplikacji za pomocą [polecenia Invoke-AzResourceAction:](/powershell/module/az.resources/invoke-azresourceaction)

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W menu po lewej stronie aplikacji wybierz pozycję **Poświadczenia** FTPS Centrum wdrażania  >   lub Lokalne poświadczenia **usługi Git/FTPS.**

    ![Pokazuje, jak można wybrać pulpit nawigacyjny FTP z Centrum wdrażania w usłudze Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. W sekcji **Zakres aplikacji** wybierz pozycję **Resetuj**.

-----

## <a name="disable-basic-authentication"></a>Wyłączanie uwierzytelniania podstawowego

Niektóre organizacje muszą spełniać wymagania dotyczące zabezpieczeń i wolisz wyłączyć dostęp za pośrednictwem protokołu FTP lub WebDeploy. Dzięki temu członkowie organizacji mogą uzyskać dostęp do swoich usług App Services za pośrednictwem interfejsów API, które są kontrolowane przez Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Aby wyłączyć dostęp FTP do witryny, uruchom następujące polecenie interfejsu wiersza polecenia. Zastąp symbole zastępcze nazwą grupy zasobów i witryny. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Aby potwierdzić, że dostęp do protokołu FTP jest zablokowany, możesz spróbować uwierzytelnić się przy użyciu klienta FTP, takiego jak FileZilla. Aby pobrać poświadczenia publikowania, przejdź do bloku przeglądu witryny i kliknij pozycję Pobierz profil publikowania. Użyj nazwy hosta FTP pliku, nazwy użytkownika i hasła, aby się uwierzytelnić. Otrzymasz odpowiedź o błędzie 401 wskazującą, że nie masz autoryzacji.

### <a name="webdeploy-and-scm"></a>WebDeploy i SCM

Aby wyłączyć dostęp uwierzytelniania podstawowego do portu WebDeploy i witryny SCM, uruchom następujące polecenie interfejsu wiersza polecenia. Zastąp symbole zastępcze nazwą grupy zasobów i witryny. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Aby potwierdzić, że poświadczenia profilu publikowania są zablokowane w ramach usługi WebDeploy, spróbuj opublikować aplikację internetową przy [użyciu programu Visual Studio 2019.](/visualstudio/deployment/quickstart-deploy-to-azure)

### <a name="disable-access-to-the-api"></a>Wyłączanie dostępu do interfejsu API

Interfejs API w poprzedniej sekcji jest oparty na kontroli dostępu na podstawie [](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) ról (RBAC) platformy Azure, co oznacza, że można utworzyć rolę niestandardową i przypisać do roli użytkowników o niższym poziomie zabezpieczeń, aby nie można było włączyć podstawowego uwierzytelniania w żadnej lokacji. Aby skonfigurować rolę niestandardową, [postępuj zgodnie z tymi instrukcjami.](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)

Można również użyć usługi [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) inspekcji żądań uwierzytelniania i użyć Azure Policy, aby wymusić tę konfigurację dla wszystkich lokacji w subskrypcji. [](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać tych poświadczeń do wdrażania aplikacji z [lokalnego](deploy-local-git.md) narzędzia Git lub przy użyciu [protokołu FTP/S.](deploy-ftp.md)

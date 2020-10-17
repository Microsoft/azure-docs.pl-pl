---
title: Konfigurowanie poświadczeń wdrożenia
description: Dowiedz się, jakie typy poświadczeń wdrożenia znajdują się w Azure App Service oraz jak je skonfigurować i jak z nich korzystać.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 653110b953b6947254d5063a9e389505d45ea4cb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149027"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Skonfiguruj poświadczenia wdrażania dla Azure App Service
[Azure App Service](./overview.md) obsługuje dwa typy poświadczeń dla [lokalnego wdrożenia git](deploy-local-git.md) i [wdrożenia FTP/S](deploy-ftp.md). Te poświadczenia nie są takie same, jak poświadczenia subskrypcji platformy Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Konfigurowanie poświadczeń na poziomie użytkownika

Poświadczenia na poziomie użytkownika można skonfigurować na [stronie zasobów](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)dowolnej aplikacji. Niezależnie od tego, która aplikacja zostanie skonfigurowana, ma zastosowanie do wszystkich aplikacji i wszystkich subskrypcji na koncie platformy Azure. 

### <a name="in-the-cloud-shell"></a>W Cloud Shell

Aby skonfigurować użytkownika wdrożenia w [Cloud Shell](https://shell.azure.com), uruchom polecenie [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Zastąp \<username> i nazwą \<password> użytkownika i hasłem dla wdrożenia. 

- Nazwa użytkownika musi być unikatowa w ramach platformy Azure, a w przypadku lokalnych powiadomień wypychanych Git nie może zawierać symbolu "@". 
- Hasło musi składać się z co najmniej ośmiu znaków, a dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Dane wyjściowe JSON przedstawiają hasło jako `null` . Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

### <a name="in-the-portal"></a>W portalu

W Azure Portal należy mieć co najmniej jedną aplikację, aby można było uzyskać dostęp do strony poświadczeń wdrożenia. Aby skonfigurować poświadczenia na poziomie użytkownika:

1. W [Azure Portal](https://portal.azure.com)z menu po lewej stronie wybierz pozycję **App Services**  >  **\<any_app>**  >  **Deployment center**  >  **FTP**  >  **pulpit nawigacyjny**FTP programu Deployment Center.

    ![Pokazuje, w jaki sposób można wybrać pulpit nawigacyjny FTP z centrum wdrażania w usłudze Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

    Lub, jeśli wdrożenie usługi git zostało już skonfigurowane, wybierz pozycję **App Services**  >  ** &lt; any_app>**  >  **Deployment center**  >  **FTP/Credentials**.

    ![Pokazuje, w jaki sposób można wybrać pulpit nawigacyjny FTP z centrum wdrażania w usłudze Azure App Services dla skonfigurowanego wdrożenia usługi git.](./media/app-service-deployment-credentials/access-with-git.png)

2. Wybierz pozycję **poświadczenia użytkownika**, skonfiguruj nazwę użytkownika i hasło, a następnie wybierz pozycję **Zapisz poświadczenia**.

Po ustawieniu poświadczeń wdrożenia można znaleźć nazwę użytkownika wdrożenia *narzędzia Git* na stronie **Przegląd** aplikacji,

![Pokazuje, jak znaleźć nazwę użytkownika wdrożenia Git na stronie Przegląd aplikacji.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Jeśli skonfigurowano wdrożenie usługi git, na stronie zostanie wyświetlona **Nazwa użytkownika narzędzia Git/Deployment**; w przeciwnym razie **Nazwa użytkownika FTP/Deployment**.

> [!NOTE]
> Na platformie Azure nie jest wyświetlane hasło do wdrożenia na poziomie użytkownika. Jeśli zapomnisz hasła, możesz zresetować poświadczenia, wykonując kroki opisane w tej sekcji.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Korzystanie z poświadczeń na poziomie użytkownika przy użyciu protokołu FTP/FTPS

Uwierzytelnianie do punktu końcowego FTP/FTPS przy użyciu poświadczeń na poziomie użytkownika nazwa użytkownika w następującym formacie: `<app-name>\<user-name>`

Ponieważ poświadczenia na poziomie użytkownika są połączone z użytkownikiem, a nie konkretnym zasobem, nazwa użytkownika musi być w tym formacie, aby skierować akcję logowania do właściwego punktu końcowego aplikacji.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Pobieranie i resetowanie poświadczeń na poziomie aplikacji
Aby uzyskać poświadczenia na poziomie aplikacji:

1. W [Azure Portal](https://portal.azure.com)z menu po lewej stronie wybierz **App Services**  >  ** &lt; any_app>**  >  **Deployment**  >  **/FTP/poświadczenia**.

2. Wybierz pozycję **poświadczenia aplikacji**, a następnie wybierz link **Kopiuj** , aby skopiować nazwę użytkownika lub hasło.

Aby zresetować poświadczenia na poziomie aplikacji, wybierz pozycję **Zresetuj poświadczenia** w tym samym oknie dialogowym.

## <a name="disable-basic-authentication"></a>Wyłącz uwierzytelnianie podstawowe

Niektóre organizacje muszą spełniać wymagania dotyczące zabezpieczeń i raczej wyłączyć dostęp za pośrednictwem protokołu FTP lub webdeploy. W ten sposób członkowie organizacji mogą uzyskiwać dostęp do App Services za pomocą interfejsów API, które są kontrolowane przez Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Aby wyłączyć dostęp do usługi FTP do witryny, uruchom następujące polecenie interfejsu wiersza polecenia. Zamień symbole zastępcze na grupę zasobów i nazwę lokacji. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Aby upewnić się, że dostęp do usługi FTP jest zablokowany, możesz spróbować uwierzytelnić się przy użyciu klienta FTP, takiego jak FileZilla. Aby pobrać poświadczenia publikowania, przejdź do bloku przegląd witryny, a następnie kliknij pozycję Pobierz profil publikowania. Użyj nazwy hosta FTP, nazwy użytkownika i hasła w celu uwierzytelnienia, a otrzymasz 401 odpowiedź na błąd, wskazując, że nie masz uprawnień.

### <a name="webdeploy-and-scm"></a>Webdeploy i SCM

Aby wyłączyć podstawowy dostęp uwierzytelniania do witryny webdeploy port i SCM, uruchom następujące polecenie interfejsu wiersza polecenia. Zamień symbole zastępcze na grupę zasobów i nazwę lokacji. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Aby upewnić się, że poświadczenia profilu publikowania są blokowane w programie webdeploy, spróbuj [opublikować aplikację sieci Web przy użyciu programu Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

### <a name="disable-access-to-the-api"></a>Wyłącz dostęp do interfejsu API

Interfejs API w poprzedniej sekcji jest kopią zapasową kontroli dostępu opartej na rolach (Azure RBAC), co oznacza, że można [utworzyć rolę niestandardową](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) i przypisać użytkownikom niższych priveldged do roli, aby nie mogli włączyć uwierzytelniania podstawowego w dowolnych lokacjach. Aby skonfigurować rolę niestandardową, [wykonaj te instrukcje](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

Możesz również użyć [Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) do inspekcji wszelkich pomyślnych żądań uwierzytelnienia i użyć [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) , aby wymusić tę konfigurację dla wszystkich lokacji w ramach subskrypcji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać tych poświadczeń do wdrażania aplikacji z [lokalnego systemu Git](deploy-local-git.md) lub przy użyciu [protokołu FTP/S](deploy-ftp.md).
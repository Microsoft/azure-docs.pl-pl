---
title: Rozwiązywanie problemów z logowaniem do rejestru
description: Objawy, przyczyny i rozwiązania typowych problemów podczas logowania się do rejestru kontenerów platformy Azure
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 47186cc8256836e5367ecee520787b67662eb42f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780735"
---
# <a name="troubleshoot-registry-login"></a>Rozwiązywanie problemów z logowaniem do rejestru

Ten artykuł ułatwia rozwiązywanie problemów, które mogą wystąpić podczas logowania się do rejestru kontenerów platformy Azure. 

## <a name="symptoms"></a>Objawy

Może zawierać co najmniej jedną z następujących czynności:

* Nie można zalogować się do rejestru `docker login` przy użyciu , lub `az acr login` obu
* Nie można zalogować się do rejestru i występuje błąd `unauthorized: authentication required` lub `unauthorized: Application not registered with AAD`
* Nie można zalogować się do rejestru i występuje błąd interfejsu wiersza polecenia platformy Azure `Could not connect to the registry login server`
* Nie można wypchnąć lub ściągnąć obrazów i zostanie wyświetlony błąd platformy Docker `unauthorized: authentication required`
* Nie można uzyskać dostępu do rejestru z Azure Kubernetes Service, Azure DevOps lub innej usługi platformy Azure
* Nie można uzyskać dostępu do rejestru i występuje błąd `Error response from daemon: login attempt failed with status: 403 Forbidden` — zobacz Rozwiązywanie problemów z [siecią za pomocą rejestru](container-registry-troubleshoot-access.md)
* Nie można uzyskać dostępu do ustawień rejestru lub wyświetlić ich w Azure Portal lub zarządzać rejestrem przy użyciu interfejsu wiersza polecenia platformy Azure

## <a name="causes"></a>Przyczyny

* W Twoim środowisku nie skonfigurowano prawidłowo platformy Docker — [rozwiązanie](#check-docker-configuration)
* Rejestr nie istnieje lub nazwa jest niepoprawna — [rozwiązanie](#specify-correct-registry-name)
* Poświadczenia rejestru są nieprawidłowe — [rozwiązanie](#confirm-credentials-to-access-registry)
* Poświadczenia nie są autoryzowane do operacji wypychania, ściągania ani Azure Resource Manager — [rozwiązanie](#confirm-credentials-are-authorized-to-access-registry)
* Poświadczenia wygasły — [rozwiązanie](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Dalsza diagnostyka 

Uruchom [polecenie az acr check-health,](/cli/azure/acr#az_acr_check_health) aby uzyskać więcej informacji o kondycji środowiska rejestru i opcjonalnie uzyskać dostęp do rejestru docelowego. Na przykład zdiagnozuj błędy konfiguracji platformy Docker lub Azure Active Directory problemów z logowaniem. 

Zobacz [Sprawdzanie kondycji rejestru kontenerów platformy Azure, aby uzyskać](container-registry-check-health.md) przykłady poleceń. Jeśli zostaną zgłoszone błędy, przejrzyj informacje [o błędach](container-registry-health-error-reference.md) i poniższe sekcje, aby zapoznać się z zalecanymi rozwiązaniami.

Jeśli występują problemy z używaniem rejestru wih Azure Kubernetes Service, uruchom polecenie [az aks check-acr,](/cli/azure/aks#az_aks_check_acr) aby sprawdzić, czy rejestr jest dostępny z klastra usługi AKS.

> [!NOTE]
> Niektóre błędy uwierzytelniania lub autoryzacji mogą również wystąpić, jeśli istnieją konfiguracje zapory lub sieci, które uniemożliwiają dostęp do rejestru. Zobacz [Rozwiązywanie problemów z siecią za pomocą rejestru](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Potencjalne rozwiązania

### <a name="check-docker-configuration"></a>Sprawdzanie konfiguracji platformy Docker

Większość Azure Container Registry uwierzytelniania wymaga lokalnej instalacji platformy Docker, aby można było uwierzytelniać się w rejestrze na potrzeby operacji, takich jak wypychanie i ściąganie obrazów. Upewnij się, że klient i demon interfejsu wiersza polecenia platformy Docker (aparat platformy Docker) są uruchomione w Twoim środowisku. Potrzebny jest klient platformy Docker w wersji 18.03 lub nowszej.

Powiązane linki:

* [Omówienie uwierzytelniania](container-registry-authentication.md#authentication-options)
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Określ poprawną nazwę rejestru

W przypadku korzystania z usługi podaj pełną nazwę serwera logowania rejestru, na `docker login` przykład *myregistry.azurecr.io*. Upewnij się, że używasz tylko małych liter. Przykład:

```console
docker login myregistry.azurecr.io
```

W przypadku [korzystania z polecenia az acr login](/cli/azure/acr#az_acr_login) Azure Active Directory tożsamości, najpierw zaloguj się do interfejsu wiersza polecenia platformy [Azure,](/cli/azure/authenticate-azure-cli)a następnie określ nazwę zasobu platformy Azure dla rejestru. Nazwa zasobu to nazwa podana podczas tworzenia rejestru, na przykład *myregistry* (bez sufiksu domeny). Przykład:

```azurecli
az acr login --name myregistry
```

Powiązane linki:

* [Az acr login succeeds but docker fails with error: unauthorized: authentication required](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>Potwierdzanie poświadczeń w celu uzyskania dostępu do rejestru

Sprawdź poprawność poświadczeń, których używasz w swoim scenariuszu lub które zostały ci dostarczone przez właściciela rejestru. Niektóre możliwe problemy:

* Jeśli używasz jednostki usługi Active Directory, upewnij się, że używasz prawidłowych poświadczeń w dzierżawie usługi Active Directory:
  * Nazwa użytkownika — identyfikator aplikacji jednostki usługi (nazywany również *identyfikatorem klienta*)
  * Hasło — hasło jednostki usługi (nazywane również *kluczem tajnym klienta)*
* W przypadku korzystania z usługi platformy Azure, takiej Azure Kubernetes Service lub Azure DevOps, aby uzyskać dostęp do rejestru, potwierdź konfigurację rejestru dla usługi. 
* Jeśli uruchomiono opcję , która umożliwia logowanie do rejestru bez użycia demona platformy Docker, upewnij się, że `az acr login` uwierzytelniasz się przy użyciu nazwy użytkownika `--expose-token` `00000000-0000-0000-0000-000000000000` .
* Jeśli rejestr jest skonfigurowany [](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)do dostępu anonimowego ściągania, istniejące poświadczenia platformy Docker przechowywane z poprzedniego identyfikatora logowania platformy Docker mogą uniemożliwić dostęp anonimowy. Uruchom `docker logout` przed próbą anonimowej operacji ściągania w rejestrze.

Powiązane linki:

* [Omówienie uwierzytelniania](container-registry-authentication.md#authentication-options)
* [Indywidualne logowanie za pomocą usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Logowanie przy użyciu jednostki usługi](container-registry-auth-service-principal.md)
* [Logowanie przy użyciu tożsamości zarządzanej](container-registry-authentication-managed-identity.md)
* [Logowanie przy użyciu tokenu o zakresie repozytorium](container-registry-repository-scoped-permissions.md)
* [Logowanie przy użyciu konta administratora](container-registry-authentication.md#admin-account)
* [Kody błędów uwierzytelniania i autoryzacji usługi Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az_acr_login) reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Potwierdzanie, że poświadczenia są autoryzowane do uzyskiwania dostępu do rejestru

Potwierdź uprawnienia rejestru skojarzone z poświadczeniami, takie jak rola platformy Azure do ściągania obrazów z rejestru lub rola do `AcrPull` `AcrPush` wypychania obrazów. 

Dostęp do rejestru w portalu lub zarządzania rejestrem przy użyciu interfejsu wiersza polecenia platformy Azure wymaga co najmniej roli lub równoważnych uprawnień do wykonywania `Reader` Azure Resource Manager operacji.

Jeśli ostatnio zmieniono uprawnienia, aby zezwolić na dostęp do rejestru za pomocą portalu, może być konieczne wypróbowanie sesji incognito lub prywatnej w przeglądarce, aby uniknąć nieaktywnej pamięci podręcznej przeglądarki lub plików cookie.

Ty lub właściciel rejestru musi mieć wystarczające uprawnienia w subskrypcji, aby dodawać lub usuwać przypisania ról.

Powiązane linki:

* [Role i uprawnienia platformy Azure — Azure Container Registry](container-registry-roles.md)
* [Logowanie przy użyciu tokenu o zakresie repozytorium](container-registry-repository-scoped-permissions.md)
* [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](../active-directory/develop/howto-create-service-principal-portal.md)
* [Tworzenie nowego klucza tajnego aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Kody uwierzytelniania i autoryzacji usługi Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Sprawdź, czy poświadczenia nie wygasły

Tokeny i poświadczenia usługi Active Directory mogą wygasnąć po upływie zdefiniowanych okresów, uniemożliwiając dostęp do rejestru. Aby włączyć dostęp, może być konieczne zresetowanie lub ponowne wygenerowanie poświadczeń.

* W przypadku używania indywidualnej tożsamości usługi AD, tożsamości zarządzanej lub jednostki usługi do logowania do rejestru token usługi AD wygasa po 3 godzinach. Zaloguj się ponownie do rejestru.  
* Jeśli używasz jednostki usługi AD z wygasłym kluczem tajnym klienta, właściciel subskrypcji lub administrator konta musi zresetować poświadczenia lub wygenerować nową jednostkę usługi.
* W przypadku korzystania [z tokenu](container-registry-repository-scoped-permissions.md)o zakresie repozytorium właściciel rejestru może potrzebować zresetowania hasła lub wygenerowania nowego tokenu.

Powiązane linki:

* [Resetowanie poświadczeń jednostki usługi](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [Ponowne generowanie haseł tokenu](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Indywidualne logowanie za pomocą usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Jeśli [zbieranie dzienników zasobów](container-registry-diagnostics-audit-logs.md) jest włączone w rejestrze, przejrzyj dziennik ContainterRegistryLoginEvents. Ten dziennik przechowuje zdarzenia i stan uwierzytelniania, w tym tożsamość przychodzącą i adres IP. Przeszukaj w [dzienniku zapytanie o błędy uwierzytelniania rejestru.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Powiązane linki:

* [Dzienniki dotyczące oceny diagnostycznej i inspekcji](container-registry-diagnostics-audit-logs.md)
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)
* [Najlepsze rozwiązania dla usługi Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Następne kroki

Jeśli nie rozwiążemy problemu w tym miejscu, zobacz następujące opcje.

* Inne tematy dotyczące rozwiązywania problemów z rejestrem obejmują:
  * [Rozwiązywanie problemów z siecią za pomocą rejestru](container-registry-troubleshoot-access.md)
  * [Rozwiązywanie problemów z wydajnością rejestru](container-registry-troubleshoot-performance.md)
* [Opcje pomocy technicznej społeczności](https://azure.microsoft.com/support/community/)
* [Pytania i odpowiedzi Microsoft](/answers/products/)
* [Otwórz bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/) — na podstawie podanej informacji można uruchomić szybką diagnostykę w przypadku niepowodzeń uwierzytelniania w rejestrze
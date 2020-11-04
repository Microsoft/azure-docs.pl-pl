---
title: Rozwiązywanie problemów z logowaniem do rejestru
description: Objawy, przyczyny i rozwiązywanie typowych problemów podczas logowania do usługi Azure Container Registry
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: a00db5cc34da6d90210a22005f33b0ad1bf20f1b
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348903"
---
# <a name="troubleshoot-registry-login"></a>Rozwiązywanie problemów z logowaniem do rejestru

Ten artykuł pomaga rozwiązywać problemy, które mogą wystąpić podczas logowania do usługi Azure Container Registry. 

## <a name="symptoms"></a>Objawy

Może zawierać co najmniej jedną z następujących czynności:

* Nie można zalogować się do rejestru przy użyciu `docker login` , `az acr login` lub obu
* Nie można zalogować się do rejestru i pojawia się błąd `unauthorized: authentication required` lub `unauthorized: Application not registered with AAD`
* Nie można zalogować się do rejestru i pojawia się błąd interfejsu wiersza polecenia platformy Azure `Could not connect to the registry login server`
* Nie można wypchnąć ani ściągnąć obrazów i odebrać błąd platformy Docker `unauthorized: authentication required`
* Nie można uzyskać dostępu do rejestru z usługi Azure Kubernetes, Azure DevOps lub innej usługi platformy Azure
* Nie można uzyskać dostępu do rejestru i pojawia się błąd `Error response from daemon: login attempt failed with status: 403 Forbidden` — Zobacz [Rozwiązywanie problemów z siecią w rejestrze](container-registry-troubleshoot-access.md)
* Nie można uzyskać dostępu do ustawień rejestru w Azure Portal lub zarządzać rejestrem przy użyciu interfejsu wiersza polecenia platformy Azure

## <a name="causes"></a>Przyczyny

* Platforma Docker nie jest prawidłowo skonfigurowana w Twoim środowisku — [rozwiązanie](#check-docker-configuration)
* Rejestr nie istnieje lub jego nazwa jest niepoprawna — [rozwiązanie](#specify-correct-registry-name)
* Poświadczenia rejestru nie są prawidłowe — [rozwiązanie](#confirm-credentials-to-access-registry)
* Poświadczenia nie są autoryzowane do operacji wypychania, ściągania lub Azure Resource Manager — [rozwiązanie](#confirm-credentials-are-authorized-to-access-registry)
* Wygasłe poświadczenia — [rozwiązanie](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Przeprowadzenia dalszej diagnostyki 

Uruchom polecenie [AZ ACR Check-Health](/cli/azure/acr#az-acr-check-health) , aby uzyskać więcej informacji na temat kondycji środowiska rejestru i opcjonalnego dostępu do rejestru docelowego. Na przykład Diagnozuj błędy konfiguracji platformy Docker lub Azure Active Directory problemy z logowaniem. 

Przykłady poleceń można znaleźć [w temacie Sprawdzanie kondycji usługi Azure Container Registry](container-registry-check-health.md) . W przypadku zgłoszenia błędów Przejrzyj [Informacje o błędzie](container-registry-health-error-reference.md) i poniższe sekcje dotyczące zalecanych rozwiązań.

> [!NOTE]
> Niektóre błędy uwierzytelniania lub autoryzacji mogą również wystąpić, jeśli istnieją konfiguracje zapory lub sieci, które uniemożliwiają dostęp do rejestru. Zobacz [Rozwiązywanie problemów z siecią przy użyciu rejestru](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Potencjalne rozwiązania

### <a name="check-docker-configuration"></a>Sprawdź konfigurację platformy Docker

Większość przepływów uwierzytelniania Azure Container Registry wymaga instalacji lokalnej platformy Docker, aby można było uwierzytelnić się w rejestrze w celu wykonywania operacji, takich jak wypychanie i ściąganie obrazów. Upewnij się, że klient i demon interfejsu wiersza polecenia platformy Docker (aparat platformy Docker) działają w danym środowisku. Wymagany jest klient platformy Docker w wersji 18,03 lub nowszej.

Powiązane linki:

* [Omówienie uwierzytelniania](container-registry-authentication.md#authentication-options)
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Określ poprawną nazwę rejestru

W przypadku korzystania z programu `docker login` Podaj pełną nazwę serwera logowania w rejestrze, taką jak *myregistry.azurecr.IO*. Upewnij się, że używasz tylko małych liter. Przykład:

```console
docker login myregistry.azurecr.io
```

Korzystając z polecenia [AZ ACR login](/cli/azure/acr#az-acr-login) z tożsamością Azure Active Directory, najpierw [Zaloguj się do interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli), a następnie podaj nazwę zasobu platformy Azure dla rejestru. Nazwa zasobu jest nazwą podaną podczas tworzenia rejestru, na przykład z *rejestrem* (bez sufiksu domeny). Przykład:

```azurecli
az acr login --name myregistry
```

Powiązane linki:

* [Operacja AZ ACR login powiedzie się, ale program Docker kończy się niepowodzeniem z powodu błędu: Brak autoryzacji: wymagane jest uwierzytelnianie](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Potwierdź poświadczenia, aby uzyskać dostęp do rejestru

Sprawdź poprawność poświadczeń używanych w danym scenariuszu lub przewidzianych przez właściciela rejestru. Niektóre możliwe problemy:

* W przypadku używania jednostki usługi Active Directory upewnij się, że używasz poprawnych poświadczeń w dzierżawie Active Directory:
  * Nazwa użytkownika — identyfikator aplikacji głównej usługi (nazywany także *identyfikatorem klienta* )
  * Hasło — hasło główne usługi (nazywane także *kluczem tajnym klienta* )
* W przypadku korzystania z usługi platformy Azure, takiej jak Azure Kubernetes Service lub Azure DevOps w celu uzyskania dostępu do rejestru, Potwierdź konfigurację rejestru dla usługi.
* W przypadku uruchomienia `az acr login` z `--expose-token` opcją, która umożliwia logowanie w rejestrze bez użycia demona Docker, należy się upewnić, że uwierzytelniasz się przy użyciu nazwy użytkownika `00000000-0000-0000-0000-000000000000` .
* Jeśli rejestr jest skonfigurowany do [anonimowego dostępu ściągania](container-registry-faq.md#how-do-i-enable-anonymous-pull-access), istniejące poświadczenia platformy Docker przechowywane z poprzedniego logowania platformy Docker mogą uniemożliwić dostęp anonimowy. Uruchom `docker logout` przed podjęciem próby anonimowej operacji ściągania w rejestrze.

Powiązane linki:

* [Omówienie uwierzytelniania](container-registry-authentication.md#authentication-options)
* [Indywidualne logowanie za pomocą usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Zaloguj się przy użyciu nazwy głównej usługi](container-registry-auth-service-principal.md)
* [Zaloguj się przy użyciu tożsamości zarządzanej](container-registry-authentication-managed-identity.md)
* [Logowanie przy użyciu tokenu z zakresem repozytorium](container-registry-repository-scoped-permissions.md)
* [Zaloguj się przy użyciu konta administratora](container-registry-authentication.md#admin-account)
* [Kody błędów autoryzacji i uwierzytelniania usługi Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)
* [AZ ACR login](/cli/azure/acr#az-acr-login) Reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Potwierdź, że poświadczenia są autoryzowane, aby uzyskać dostęp do rejestru

Potwierdź uprawnienia rejestru, które są skojarzone z poświadczeniami, `AcrPull` na przykład rolę platformy Azure w celu ściągania obrazów z rejestru lub `AcrPush` roli do wypychania obrazów. 

Dostęp do rejestru w portalu lub w zarządzaniu rejestrem za pomocą interfejsu wiersza polecenia platformy Azure wymaga co najmniej `Reader` roli do wykonywania Azure Resource Manager operacji.

Aby dodać lub usunąć przypisania ról, właściciel rejestru musi mieć wystarczające uprawnienia w subskrypcji.

Powiązane linki:

* [Role i uprawnienia platformy Azure — Azure Container Registry](container-registry-roles.md)
* [Logowanie przy użyciu tokenu z zakresem repozytorium](container-registry-repository-scoped-permissions.md)
* [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](../active-directory/develop/howto-create-service-principal-portal.md)
* [Tworzenie nowego klucza tajnego aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Uwierzytelnianie i kody autoryzacji usługi Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Sprawdź, czy poświadczenia nie wygasły

Tokeny i poświadczenia Active Directory mogą wygasnąć po określonych okresach, uniemożliwiając dostęp do rejestru. Aby włączyć dostęp, może być konieczne zresetowanie lub ponowne wygenerowanie poświadczeń.

* W przypadku korzystania z pojedynczej tożsamości usługi AD, tożsamości zarządzanej lub nazwy głównej usługi dla logowania do rejestru token usługi AD wygasa po 3 godzinach. Zaloguj się ponownie do rejestru.  
* Jeśli używasz jednostki usługi AD z wygasłym wpisem tajnym klienta, właściciel subskrypcji lub administrator konta musi zresetować poświadczenia lub wygenerować nową nazwę główną usługi.
* W przypadku użycia [tokenu z zakresem repozytorium](container-registry-repository-scoped-permissions.md)może być konieczne zresetowanie hasła lub wygenerowanie nowego tokenu przez właściciela rejestru.

Powiązane linki:

* [Resetuj poświadczenia nazwy głównej usługi](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Ponowne generowanie haseł tokenu](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Indywidualne logowanie za pomocą usługi Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Zaawansowane rozwiązywanie problemów

Jeśli [Kolekcja dzienników zasobów](container-registry-diagnostics-audit-logs.md) jest włączona w rejestrze, przejrzyj dziennik ContainterRegistryLoginEvents. Ten dziennik przechowuje zdarzenia i stan uwierzytelniania, w tym przychodzącą tożsamość i adres IP. Zbadaj dziennik pod kątem [błędów uwierzytelniania rejestru](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Powiązane linki:

* [Dzienniki dotyczące oceny i inspekcji diagnostyki](container-registry-diagnostics-audit-logs.md)
* [Rejestr kontenerów — często zadawane pytania](container-registry-faq.md)
* [Najlepsze rozwiązania dla usługi Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Następne kroki

Jeśli w tym miejscu nie rozwiążesz problemu, zapoznaj się z następującymi opcjami.

* Inne tematy dotyczące rozwiązywania problemów z rejestrem obejmują:
  * [Rozwiązywanie problemów z siecią przy użyciu rejestru](container-registry-troubleshoot-access.md)
  * [Rozwiązywanie problemów z wydajnością rejestru](container-registry-troubleshoot-performance.md)
* Opcje [pomocy technicznej społeczności](https://azure.microsoft.com/support/community/)
* [Pytania i odpowiedzi Microsoft](/answers/products/)
* [Otwórz bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/) na podstawie podanych przez Ciebie informacji, możesz uruchomić szybką diagnostykę w celu uwierzytelnienia w rejestrze.
---
title: Zarządzanie podpisanymi obrazami
description: Dowiedz się, jak włączyć zaufanie do zawartości dla rejestru kontenerów platformy Azure oraz wypychać i ściągać podpisane obrazy. Zaufanie do zawartości implementuje zaufanie do zawartości platformy Docker i jest funkcją warstwy usługi Premium.
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 238908c0075ffa5d2193eda642175a0cfe75b839
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784123"
---
# <a name="content-trust-in-azure-container-registry"></a>Zaufanie do zawartości w usłudze Azure Container Registry

Azure Container Registry implementuje model zaufania do [][docker-content-trust] zawartości platformy Docker, umożliwiając wypychanie i ściąganie podpisanych obrazów. Ten artykuł zawiera wprowadzenie do włączania zaufania do zawartości w rejestrach kontenerów.

> [!NOTE]
> Zaufanie do zawartości jest funkcją warstwy [usługi Premium usługi](container-registry-skus.md) Azure Container Registry.

## <a name="how-content-trust-works"></a>Jak działa zaufanie do zawartości

W każdym systemie rozproszonym zaprojektowanym z myślą o bezpieczeństwie bardzo ważne jest weryfikowanie zarówno *źródła*, jak i *integralności* danych wprowadzanych do systemu. Użytkownicy danych muszą mieć możliwość zweryfikowania wydawcy (źródła) danych, a także pewność, że dane nie zostały zmodyfikowane po publikacji (integralność). 

Jako wydawcy obrazu zaufanie do zawartości umożliwia Ci **podpisywanie** obrazów wypychanych do rejestru. Użytkownicy obrazów (osoby lub systemy ściągające obrazy z rejestru) mogą skonfigurować swoich klientów do ściągania *tylko* podpisanych obrazów. Gdy użytkownik ściąga podpisany obraz, jego klient platformy Docker weryfikuje integralność obrazu. W tym modelu użytkownicy mają pewność, że podpisane obrazy w Twoim rejestrze zostały faktycznie opublikowane przez Ciebie i że nie zostały one zmodyfikowane od momentu publikacji.

### <a name="trusted-images"></a>Zaufane obrazy

Zaufanie do zawartości współdziała z **tagami** w repozytorium. Repozytoria obrazów mogą zawierać obrazy z zarówno podpisanymi, jak i niepodpisanymi tagami. Przykładowo można podpisać tylko obrazy `myimage:stable` i `myimage:latest`, ale nie `myimage:dev`.

### <a name="signing-keys"></a>Klucze podpisywania

Zaufanie do zawartości jest zarządzane przy użyciu zestawu kryptograficznych kluczy podpisywania. Te klucze są skojarzone z konkretnym repozytorium w rejestrze. Istnieje kilka typów kluczy podpisywania, które są używane przez klientów platformy Docker i rejestr do zarządzania zaufaniem dla tagów w repozytorium. Po włączeniu zaufania do zawartości i zintegrowaniu go z publikowaniem kontenerów oraz potokiem zużycia należy ostrożnie zarządzać tymi kluczami. Aby uzyskać więcej informacji, zobacz sekcję [Zarządzanie kluczami](#key-management) w dalszej części tego artykułu oraz artykuł [Manage keys for content trust][docker-manage-keys] (Zarządzanie kluczami dla zaufania do zawartości) w dokumentacji platformy Docker.

> [!TIP]
> To jest bardzo ogólne omówienie modelu zaufania do zawartości platformy Docker. Szczegółowe informacje na temat zaufania do zawartości znajdziesz w artykule [Content trust in Docker][docker-content-trust] (Zaufanie do zawartości na platformie Docker).

## <a name="enable-registry-content-trust"></a>Włączanie zaufania do zawartości rejestru

Pierwszym krokiem jest włączenie zaufania do zawartości na poziomie rejestru. Gdy włączysz zaufanie do zawartości, klienci (użytkownicy lub usługi) będą mogli wypychać podpisane obrazy do Twojego rejestru. Włączenie zaufania do zawartości w rejestrze nie ogranicza użycia rejestru tylko do użytkowników z włączonym zaufaniem do zawartości. Użytkownicy bez włączonego zaufania do zawartości wciąż będą mogli używać Twojego rejestru w zwykły sposób. Jednak użytkownicy z włączony zaufaniem do zawartości w swoich klientach będą widzieć w Twoim rejestrze *tylko* podpisane obrazy.

Aby włączyć zaufanie do zawartości dla rejestru, najpierw przejdź do rejestru w witrynie Azure Portal. W **obszarze Zasady** wybierz pozycję Zaufanie do zawartości **włączone**  >    >  **Zapisz**. Możesz również użyć polecenia [az acr config content-trust update][az-acr-config-content-trust-update] w interfejsie wiersza polecenia platformy Azure.

![Zrzut ekranu przedstawiający włączanie zaufania do zawartości dla rejestru w Azure Portal.][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>Włączanie zaufania do zawartości klienta

Aby pracować z zaufanymi obrazami, zarówno wydawcy obrazów, jak i użytkownicy muszą włączyć zaufanie do zawartości dla swoich klientów platformy Docker. Jako wydawca możesz podpisywać obrazy, które wypychasz do rejestru z włączonym zaufaniem do zawartości. Jako użytkownikowi włączenie zaufania do zawartości ogranicza widok rejestru do wyłącznie podpisanych obrazów. Zaufanie do zawartości jest domyślnie wyłączone w klientach platformy Docker, ale można je włączyć przy użyciu sesji powłoki lub polecenia.

Aby włączyć zaufanie do zawartości dla sesji powłoki, ustaw zmienną środowiskową `DOCKER_CONTENT_TRUST` na wartość **1**. Na przykład w powłoce Bash:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Jeśli zamiast tego chcesz włączyć lub wyłączyć zaufanie do zawartości dla jednego polecenia, kilka poleceń platformy Docker obsługuje argument `--disable-content-trust`. Aby włączyć zaufanie do zawartości dla pojedynczego polecenia:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Jeśli włączono zaufanie do zawartości dla sesji powłoki i chcesz je wyłączyć dla pojedynczego polecenia:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Udzielanie uprawnień do podpisywania obrazów

Tylko użytkownicy lub systemy, którym udzielono uprawnień, mogą wypychać zaufane obrazy do rejestru. Aby udzielić użytkownikowi (lub systemowi korzystającemu z jednostki usługi) uprawnień do wypychania zaufanych obrazów, przydziel jego tożsamości w usłudze Azure Active Directory rolę `AcrImageSigner`. Jest to uzupełnienie `AcrPush` roli (lub równoważnej) wymaganej do wypychania obrazów do rejestru. Aby uzyskać szczegółowe informacje, [zobacz Azure Container Registry ról i uprawnień.](container-registry-roles.md)

> [!IMPORTANT]
> Nie można przyznać uprawnienia do wypychania zaufanego obrazu do następujących kont administracyjnych: 
> * konto [administratora](container-registry-authentication.md#admin-account) rejestru kontenerów platformy Azure
> * konto użytkownika w Azure Active Directory z rolą [klasycznego administratora systemu](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

Szczegóły dotyczące przydzielania roli `AcrImageSigner` w witrynie Azure Portal i interfejsie wiersza polecenia platformy Azure znajdują się poniżej.

### <a name="azure-portal"></a>Azure Portal

Przejdź do rejestru na stronie Azure Portal a następnie wybierz pozycję Kontrola dostępu **(IAM)**  >  **Dodaj przypisanie roli.** W obszarze **Dodawanie przypisania roli** wybierz pozycję `AcrImageSigner` w obszarze **Rola**, a następnie **wybierz** co najmniej jednego użytkownika lub jedną jednostkę usługi, po czym wybierz pozycję **Zapisz**.

W tym przykładzie przypisano rolę dwóch jednostek: jednostki usługi o nazwie `AcrImageSigner` "service-principal" i użytkownika o nazwie "Użytkownik platformy Azure".

![Przyznawanie uprawnień do podpisywania obrazów usługi ACR w Azure Portal][content-trust-02-portal]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby udzielić użytkownikowi uprawnień do podpisywania za pomocą interfejsu wiersza polecenia platformy Azure przypisz użytkownikowi rolę `AcrImageSigner` ograniczoną do rejestru. Format polecenia jest następujący:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Aby na przykład przyznać rolę użytkownikowi inne niż administracyjne, można uruchomić następujące polecenia w sesji uwierzytelnionego interfejsu wiersza polecenia platformy Azure. Zmodyfikuj wartość `REGISTRY`, aby odzwierciedlić nazwę rejestru kontenerów platformy Azure.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)
```

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee azureuser@contoso.com
```

Możesz także udzielić [jednostce usługi](container-registry-auth-service-principal.md) uprawnienia do wypychania zaufanych obrazów do rejestru. Używanie jednostki usługi jest przydatne w przypadku systemów kompilacji oraz innych nienadzorowanych systemów, które mają wypychać zaufane obrazy do rejestru. Format jest podobny jak w przypadku udzielania uprawnień użytkownikowi, ale dla wartości `--assignee` należy określi identyfikator jednostki usługi.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

Identyfikator `<service principal ID>` może być identyfikatorem **appId** lub **objectId** bądź jedną z nazw **servicePrincipalName**. Aby uzyskać więcej informacji na temat pracy z jednostkami usługi i usługą Azure Container Registry, zobacz [Uwierzytelnianie w usłudze Azure Container Registry przy użyciu jednostek usługi](container-registry-auth-service-principal.md).

> [!IMPORTANT]
> Po każdej zmianie roli uruchom program , aby odświeżyć token tożsamości lokalnej dla interfejsu wiersza polecenia platformy Azure, aby `az acr login` nowe role zostały wprowadzone. Aby uzyskać informacje na temat weryfikowania ról dla tożsamości, zobacz Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy [Azure](../role-based-access-control/role-assignments-cli.md) i [Rozwiązywanie problemów z usługą Azure RBAC.](../role-based-access-control/troubleshooting.md)

## <a name="push-a-trusted-image"></a>Wypychanie zaufanego obrazu

Aby wypchnąć tag zaufanego obrazu do rejestru kontenerów, włącz zaufanie do zawartości i wypchnij obraz za pomocą polecenia `docker push`. Po pierwszym zakończeniu wypychania przy użyciu podpisanego tagu zostanie poproszony o utworzenie hasła zarówno dla głównego klucza podpisywania, jak i klucza podpisywania repozytorium. Zarówno klucz główny, jak i klucz repozytorium są generowane i przechowywane lokalnie na maszynie.

```console
$ docker push myregistry.azurecr.io/myimage:v1
[...]
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Po pierwszym użyciu polecenia `docker push` z włączonym zaufaniem do zawartości klient platformy Docker używa tego samego klucza głównego do kolejnych wypchnięć. Przy każdym kolejnym wypchnięciu do tego samego repozytorium wyświetlany jest tylko monit o podanie klucza repozytorium. Przy każdym wypchnięciu zaufanego obrazu do nowego repozytorium wyświetlany jest monit o podanie hasła dla nowego klucza repozytorium.

## <a name="pull-a-trusted-image"></a>Ściąganie zaufanego obrazu

Aby ściągnąć zaufany obraz, włącz zaufanie do zawartości i uruchom polecenie `docker pull` jak zwykle. Aby ściągnąć zaufane obrazy, `AcrPull` rola jest wystarczająca dla zwykłych użytkowników. Nie są wymagane żadne dodatkowe `AcrImageSigner` role, takie jak rola. Użytkownicy z włączonym zaufaniem do zawartości mogą ściągać tylko obrazy z podpisanymi tagami. Oto przykład ściągania podpisanego tagu:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Jeśli klient z włączonym zaufaniem do zawartości próbuje ściągnąć niepodpisany tag, operacja kończy się niepowodzeniem z błędem podobnym do następującego:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
Error: remote trust data does not exist
```

### <a name="behind-the-scenes"></a>Za kulisami

Po uruchomieniu polecenia `docker pull` klient platformy Docker używa tej samej biblioteki co w przypadku [interfejsu wiersza polecenia usługi Notary][docker-notary-cli] do zażądania mapowania skrótu tag-do-SHA-256 dla wypychanego tagu. Po zweryfikowaniu podpisów danych o zaufaniu klient nakazuje aparatowi platformy Docker wykonanie „ściągnięcia przez skrót”. Podczas ściągania aparat używa sumy kontrolnej SHA-256 jako adresu zawartości w celu zażądania i zweryfikowania manifestu obrazu z rejestru kontenerów platformy Azure.

> [!NOTE]
> Azure Container Registry nie obsługuje oficjalnie interfejsu wiersza polecenia notary, ale jest zgodny z interfejsem API serwera notarytowego, który jest dołączony do programu Docker Desktop. Obecnie zalecana jest wersja **Notary 0.6.0.**

## <a name="key-management"></a>Zarządzanie kluczami

Jak określono w danych wyjściowych polecenia `docker push` podczas wypychania pierwszego zaufanego obrazu, klucz główny jest najbardziej wrażliwy. Pamiętaj, aby utworzyć kopię zapasową klucza głównego i przechowywać ją w bezpiecznym miejscu. Domyślnie klient platformy Docker przechowuje klucze podpisywania w następującym katalogu:

```sh
~/.docker/trust/private
```

Należy wrócić do kopii zapasowej kluczy głównych i kluczy repozytorium, kompresując je w archiwum i przechowując w bezpiecznej lokalizacji. Na przykład w powłoce Bash:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Wraz z lokalnie wygenerowanym kluczem głównym i kluczem repozytorium generowanych jest kilka innych i są one zapisywane przez usługę Azure Container Registry podczas wypychania zaufanego obrazu. Szczegółowe omówienie różnych kluczy w implementacji zaufania do zawartości platformy Docker, w tym dodatkowe wskazówki dotyczące zarządzania, znajdziesz w artykule [Manage keys for content trust][docker-manage-keys] (Zarządzanie kluczami dla zaufania do zawartości) w dokumentacji platformy Docker.

### <a name="lost-root-key"></a>Utracenie klucza głównego

Jeśli utracisz dostęp do klucza głównego, utracisz dostęp do podpisanych tagów w każdym repozytorium, którego tagi były podpisane przy użyciu tego klucza. Usługa Azure Container Registry nie może przywrócić dostępu do tagów obrazów podpisanych przy użyciu utraconego klucza głównego. Aby usunąć wszystkie dane o zaufaniu (podpisy) dla rejestru, najpierw wyłącz, a następnie ponownie włącz zaufanie do zawartości dla rejestru.

> [!WARNING]
> Wyłączenie i ponowne włączenie zaufania do zawartości w rejestrze spowoduje **usunięcie całości danych o zaufaniu dla wszystkich podpisanych tagów w każdym repozytorium w rejestrze**. Ta akcja jest nieodwracalna — usługa Azure Container Registry nie może odzyskać usuniętych danych o zaufaniu. Wyłączenie zaufania do zawartości nie powoduje usunięcia samych obrazów.

Aby wyłączyć zaufanie do zawartości dla rejestru, przejdź do rejestru w witrynie Azure Portal. W **obszarze Zasady** wybierz pozycję Zaufanie do zawartości **wyłączone**  >    >  **Zapisz**. Zostanie wyświetlone ostrzeżenie dotyczące utraty wszystkich podpisów w rejestrze. Wybierz przycisk **OK**, aby trwale usunąć wszystkie podpisy w rejestrze.

![Wyłączanie zaufania do zawartości dla rejestru w witrynie Azure Portal][content-trust-03-portal]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać dodatkowe informacje na temat zaufania do zawartości, w tym polecenia zaufania platformy [Docker](https://docs.docker.com/engine/reference/commandline/trust/) i delegowania zaufania, zobacz Content trust in Docker (Zaufanie [do zawartości na platformie Docker).](https://docs.docker.com/engine/security/trust/trust_delegation/) [][docker-content-trust] Mimo iż w tym artykule poruszono kilka kluczowych kwestii, zaufanie do zawartości to obszerny temat i omówiono go dogłębniej w dokumentacji platformy Docker.

* Zobacz [dokumentację Azure Pipelines](/azure/devops/pipelines/build/content-trust) przykład użycia zaufania do zawartości podczas kompilowania i wypychania obrazu platformy Docker.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az_acr_config_content_trust_update

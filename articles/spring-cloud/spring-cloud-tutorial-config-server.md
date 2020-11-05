---
title: Konfigurowanie wystąpienia serwera konfiguracji w chmurze Azure wiosennej
description: Dowiedz się, jak skonfigurować wystąpienie serwera konfiguracji w chmurze wiosny dla chmury wiosennej platformy Azure na Azure Portal
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 06d5196e612bcf20e11f17634b32db028cd5bc88
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378095"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Skonfiguruj wystąpienie serwera konfiguracji chmury wiosny dla swojej usługi

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

W tym artykule opisano sposób nawiązywania połączenia ze sprężynowym wystąpieniem serwera konfiguracji chmury z usługą Azure wiosną w chmurze.

Konfiguracja chmury wiosennej zapewnia obsługę serwera i klienta na potrzeby konfiguracji zewnętrznej w systemie rozproszonym. W przypadku wystąpienia serwera konfiguracji istnieje centralne miejsce do zarządzania właściwościami zewnętrznymi aplikacji we wszystkich środowiskach. Aby uzyskać więcej informacji, zobacz [informacje dotyczące serwera konfiguracji chmury wiosennej](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Została już zainicjowana i uruchomiona usługa w chmurze Azure wiosną. Aby skonfigurować i uruchomić usługę Azure wiosny w chmurze, zobacz [Szybki Start: uruchamianie aplikacji ze sprężyną Java za pomocą interfejsu wiersza polecenia platformy Azure](spring-cloud-quickstart.md).

## <a name="restriction"></a>Ograniczenie

W przypadku korzystania z serwera konfiguracji z zapleczem usługi git istnieją pewne ograniczenia. Niektóre właściwości są automatycznie wprowadzane do środowiska aplikacji w celu uzyskania dostępu do serwera konfiguracji i odnajdowania usług. W przypadku skonfigurowania tych właściwości również z plików serwera konfiguracji mogą wystąpić konflikty i nieoczekiwane zachowanie. Właściwości obejmują: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> Zdecydowanie zalecamy, aby _nie_ umieszczać powyższych właściwości w plikach aplikacji serwera konfiguracji.

## <a name="create-your-config-server-files"></a>Tworzenie plików serwera konfiguracji

Chmura sprężynowa platformy Azure obsługuje usługi Azure DevOps, GitHub, GitLab i Bitbucket do przechowywania plików konfiguracji serwera. Po przygotowaniu repozytorium Utwórz pliki konfiguracji z poniższymi instrukcjami i Zapisz je w tym miejscu.

Ponadto niektóre konfigurowalne właściwości są dostępne tylko dla niektórych typów. W poniższych podsekcjach wymieniono właściwości dla każdego typu repozytorium.

### <a name="public-repository"></a>Repozytorium publiczne

W przypadku korzystania z repozytorium publicznego właściwości, które można konfigurować, są bardziej ograniczone.

Wszystkie konfigurowalne właściwości, które są używane do konfigurowania publicznego repozytorium git, są wymienione w poniższej tabeli:

> [!NOTE]
> Używanie łącznika (-) do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład można użyć *etykiety default* , ale nie *defaultLabel*.

| Właściwość        | Wymagany | Cechy                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Tak    | Identyfikator URI repozytorium git, który jest używany jako zaplecze serwera konfiguracji, rozpoczyna się od *http://* , *https://* , *git@* lub *SSH://*. |
| `default-label` | Nie     | Domyślna etykieta repozytorium git powinna być *nazwą gałęzi* , *nazwą tagu* lub *identyfikatorem zatwierdzenia* repozytorium. |
| `search-paths`  | Nie     | Tablica ciągów, które są używane do przeszukiwania podkatalogów repozytorium git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Prywatne repozytorium z uwierzytelnianiem SSH

Wszystkie konfigurowalne właściwości używane do konfigurowania prywatnego repozytorium git przy użyciu protokołu SSH są wymienione w poniższej tabeli:

> [!NOTE]
> Używanie łącznika (-) do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład można użyć *etykiety default* , ale nie *defaultLabel*.

| Właściwość                   | Wymagany | Cechy                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Tak    | Identyfikator URI repozytorium git używany jako zaplecze serwera konfiguracji powinien być uruchamiany z *http://* , *https://* , *git@* lub *SSH://*. |
| `default-label`            | Nie     | Domyślna etykieta repozytorium git powinna być *nazwą gałęzi* , *nazwą tagu* lub *identyfikatorem zatwierdzenia* repozytorium. |
| `search-paths`             | Nie     | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium git. |
| `private-key`              | Nie     | Prywatny klucz SSH, aby uzyskać dostęp do repozytorium git, _wymagany_ , gdy identyfikator URI zaczyna się od *git@* lub *SSH://*. |
| `host-key`                 | Nie     | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty przez `host-key-algorithm` . |
| `host-key-algorithm`       | Nie     | Algorytm klucza hosta powinien mieć wartość *SSH-DSS* , *SSH-RSA* , *ECDSA-algorytmu SHA2-nistp256* , *ECDSA-algorytmu SHA2-nistp384* lub *ECDSA-algorytmu SHA2-nistp521*. *Wymagane* tylko wtedy, gdy `host-key` istnieje. |
| `strict-host-key-checking` | Nie     | Wskazuje, czy uruchomienie wystąpienia serwera konfiguracji zakończy się niepowodzeniem podczas korzystania z prywatnego `host-key` . Powinna mieć *wartość true* (wartość domyślna) lub *Fałsz*. |

> [!NOTE]
> `master`Jeśli nie zostanie określony, serwer konfiguracji przyjmuje (sam model OM Git) jako domyślną etykietę. Ale w serwisie GitHub zmieniono domyślne rozgałęzienie z `master` na `main` ostatnio. Aby uniknąć niepowodzenia serwera konfiguracji chmury ze sprężyną na platformie Azure, należy zwrócić uwagę na domyślną etykietę podczas konfigurowania serwera konfiguracji z usługą GitHub, szczególnie w przypadku nowo utworzonych repozytoriów.

-----

### <a name="private-repository-with-basic-authentication"></a>Prywatne repozytorium z uwierzytelnianiem podstawowym

Poniżej wymieniono wszystkie konfigurowalne właściwości używane do konfigurowania prywatnego repozytorium git z uwierzytelnianiem podstawowym.

> [!NOTE]
> Używanie łącznika (-) do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład użyj *etykiety default, a* nie *defaultLabel*.

| Właściwość        | Wymagany | Cechy                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Tak    | Identyfikator URI repozytorium git używany jako zaplecze serwera konfiguracji powinien być uruchamiany z *http://* , *https://* , *git@* lub *SSH://*. |
| `default-label` | Nie     | Domyślna etykieta repozytorium git powinna być *nazwą gałęzi* , *nazwą tagu* lub *identyfikatorem zatwierdzenia* repozytorium. |
| `search-paths`  | Nie     | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium git. |
| `username`      | Nie     | Nazwa użytkownika, która jest używana do uzyskiwania dostępu do serwera repozytorium git, _wymagana_ w przypadku obsługi serwera repozytorium git `Http Basic Authentication` . |
| `password`      | Nie     | Hasło używane do uzyskiwania dostępu do serwera repozytorium git, _wymagane_ w przypadku obsługi serwera repozytorium git `Http Basic Authentication` . |

> [!NOTE]
> Wiele `Git` serwerów repozytorium obsługuje używanie tokenów zamiast haseł do uwierzytelniania podstawowego http. Niektóre repozytoria, takie jak GitHub, zezwalają na utrwalanie tokenów na czas nieokreślony. Jednak niektóre serwery repozytorium git, w tym Azure DevOps, wymuszanie wygaśnięcia tokenów w ciągu kilku godzin. Repozytoria, które powodują wygaśnięcie tokenów, nie powinny używać uwierzytelniania opartego na tokenach w chmurze Azure wiosennej.

### <a name="git-repositories-with-pattern"></a>Repozytoria Git ze wzorcem

Poniżej znajdują się wszystkie konfigurowalne właściwości używane do konfigurowania repozytoriów Git ze wzorcem.

> [!NOTE]
> Używanie łącznika (-) do oddzielenia słów jest jedyną konwencją nazewnictwa, która jest obecnie obsługiwana. Na przykład użyj *etykiety default, a* nie *defaultLabel*.

| Właściwość                           | Wymagany         | Cechy                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nie             | Mapa składająca się z ustawień repozytorium git o podaną nazwę. |
| `repos."uri"`                      | Tak na `repos` | Identyfikator URI repozytorium git używany jako zaplecze serwera konfiguracji powinien być uruchamiany z *http://* , *https://* , *git@* lub *SSH://*. |
| `repos."name"`                     | Tak na `repos` | Nazwa do identyfikacji w repozytorium git, _wymagana_ tylko wtedy, gdy `repos` istnieje. Na przykład *zespół-A* , *zespół-B*. |
| `repos."pattern"`                  | Nie             | Tablica ciągów używanych do dopasowania nazwy aplikacji. Dla każdego wzorca Użyj `{application}/{profile}` formatu z symbolami wieloznacznymi. |
| `repos."default-label"`            | Nie             | Domyślna etykieta repozytorium git powinna być *nazwą gałęzi* , *nazwą tagu* lub *identyfikatorem zatwierdzenia* repozytorium. |
| `repos."search-paths`"             | Nie             | Tablica ciągów służąca do przeszukiwania podkatalogów repozytorium git. |
| `repos."username"`                 | Nie             | Nazwa użytkownika, która jest używana do uzyskiwania dostępu do serwera repozytorium git, _wymagana_ w przypadku obsługi serwera repozytorium git `Http Basic Authentication` . |
| `repos."password"`                 | Nie             | Hasło używane do uzyskiwania dostępu do serwera repozytorium git, _wymagane_ w przypadku obsługi serwera repozytorium git `Http Basic Authentication` . |
| `repos."private-key"`              | Nie             | Klucz prywatny SSH do uzyskiwania dostępu do repozytorium git, _wymagany_ , gdy identyfikator URI rozpoczyna się od *git@* lub *SSH://*. |
| `repos."host-key"`                 | Nie             | Klucz hosta serwera repozytorium Git nie powinien zawierać prefiksu algorytmu, który został objęty przez `host-key-algorithm` . |
| `repos."host-key-algorithm"`       | Nie             | Algorytm klucza hosta powinien mieć wartość *SSH-DSS* , *SSH-RSA* , *ECDSA-algorytmu SHA2-nistp256* , *ECDSA-algorytmu SHA2-nistp384* lub *ECDSA-algorytmu SHA2-nistp521*. *Wymagane* tylko wtedy, gdy `host-key` istnieje. |
| `repos."strict-host-key-checking"` | Nie             | Wskazuje, czy uruchomienie wystąpienia serwera konfiguracji zakończy się niepowodzeniem podczas korzystania z prywatnego `host-key` . Powinna mieć *wartość true* (wartość domyślna) lub *Fałsz*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Dołącz repozytorium serwera konfiguracji do chmury Azure wiosny

Teraz, gdy pliki konfiguracji są zapisywane w repozytorium, należy połączyć z nim chmurę sieciową platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do strony **omówienia** chmury Azure wiosennej.

1. Wybierz usługę do skonfigurowania.

1. W lewym okienku strony usługi w obszarze **Ustawienia** wybierz kartę **serwer konfiguracji** .

![Okno konfiguracji serwera](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Wprowadź informacje o repozytorium bezpośrednio do Azure Portal

#### <a name="default-repository"></a>Repozytorium domyślne

* **Repozytorium publiczne** : w sekcji **repozytorium domyślne** w polu **Identyfikator URI** wklej identyfikator URI repozytorium.  Ustaw **etykietę** na **config**. Upewnij się, że ustawienie **uwierzytelniania** jest **publiczne** , a następnie wybierz pozycję **Zastosuj** do końca. 

* **Repozytorium prywatne** : chmura sprężynowa platformy Azure obsługuje podstawowe uwierzytelnianie oparte na hasłach i tokenach.

    * **Uwierzytelnianie podstawowe** : w sekcji **repozytorium domyślne** w polu **Identyfikator URI** wklej identyfikator URI repozytorium, a następnie wybierz przycisk **uwierzytelniania** (ikona ołówka). W okienku **Edycja uwierzytelniania** na liście rozwijanej **Typ uwierzytelniania** wybierz pozycję **http Basic** , a następnie wprowadź nazwę użytkownika i hasło/token, aby udzielić dostępu do chmury Azure wiosennej. Wybierz przycisk **OK** , a następnie wybierz pozycję **Zastosuj** , aby zakończyć konfigurowanie wystąpienia serwera konfiguracji.

    ![Uwierzytelnianie podstawowe okienka Edycja uwierzytelniania](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Niektóre serwery repozytorium git, takie jak GitHub, używają *tokenu osobistego* lub *tokenu dostępu* , takiego jak hasło, na potrzeby **uwierzytelniania podstawowego**. Tego rodzaju tokenu można używać jako hasła w chmurze Azure wiosennej, ponieważ nigdy nie wygasa. Jednak w przypadku innych serwerów repozytorium git, takich jak BitBucket i Azure DevOps, *token dostępu* wygasa w ciągu jednej lub dwóch godzin. Oznacza to, że ta opcja nie jest opłacalna w przypadku korzystania z tych serwerów repozytorium w chmurze Azure wiosennej.

    * **SSH** : w sekcji **repozytorium domyślne** w polu **Identyfikator URI** wklej identyfikator URI repozytorium, a następnie wybierz przycisk **uwierzytelniania** (ikona "ołówek"). W okienku **Edycja uwierzytelniania** na liście rozwijanej **Typ uwierzytelniania** wybierz pozycję **SSH** , a następnie wprowadź **klucz prywatny**. Opcjonalnie określ **klucz hosta** i **algorytm klucza hosta**. Upewnij się, że klucz publiczny został uwzględniony w repozytorium serwera konfiguracji. Wybierz przycisk **OK** , a następnie wybierz pozycję **Zastosuj** , aby zakończyć konfigurowanie wystąpienia serwera konfiguracji.

    ![Uwierzytelnianie SSH w okienku Edycja](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repozytorium wzorców

Jeśli chcesz użyć opcjonalnego **repozytorium wzorców** w celu skonfigurowania usługi, określ **Identyfikator URI** i **uwierzytelnianie** tak samo jak w przypadku **repozytorium domyślnego**. Upewnij się, że dołączono **nazwę** wzorca, a następnie wybierz pozycję **Zastosuj** , aby dołączyć ją do wystąpienia. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Wprowadź informacje o repozytorium do pliku YAML

Jeśli zapisano plik YAML z ustawieniami repozytorium, możesz zaimportować go bezpośrednio z komputera lokalnego do chmury z systemem Azure wiosną. Prosty plik YAML dla repozytorium prywatnego z uwierzytelnianiem podstawowym będzie wyglądać następująco:

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Wybierz przycisk **Importuj ustawienia** , a następnie wybierz plik YAML z katalogu projektu. Wybierz pozycję **Importuj** , a następnie przejdź do `async` okna z **powiadomieniami** . Po 1-2 minutach powinna być zgłaszana sukces.

![Okienko powiadomienia serwera konfiguracji](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Informacje z pliku YAML powinny być wyświetlane w Azure Portal. Wybierz pozycję **Zastosuj** , aby zakończyć. 

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Używanie Azure Repos do konfiguracji chmury z użyciem usługi Azure wiosennej

Chmura sprężynowa platformy Azure ma dostęp do repozytoriów Git, które są publiczne, zabezpieczone przez SSH lub zabezpieczone przy użyciu uwierzytelniania podstawowego protokołu HTTP. Ta ostatnia opcja zostanie użyta, ponieważ ułatwia tworzenie i zarządzanie za pomocą Azure Repos.

### <a name="get-repo-url-and-credentials"></a>Pobierz adres URL i poświadczenia repozytorium
1. W portalu Azure Repos projektu kliknij przycisk "Klonuj":

    ![Przycisk klonowania](media/spring-cloud-tutorial-config-server/clone-button.png)

1. Skopiuj adres URL klonowania z pola tekstowego. Ten adres URL będzie zazwyczaj miał postać:

    ```Text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Usuń wszystko po `https://` i przed `dev.azure.com` , włącznie z `@` . Otrzymany adres URL powinien mieć postać:

    ```Text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Zapisz ten adres URL do użycia w następnej sekcji.

1. Kliknij pozycję "Generuj poświadczenia git". Zostanie wyświetlona nazwa użytkownika i hasło. Zapisz je do użycia w następnej sekcji.


### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Konfigurowanie usługi Azure Spring Cloud pod kątem uzyskiwania dostępu do repozytorium Git

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do strony **omówienia** chmury Azure wiosennej.

1. Wybierz usługę do skonfigurowania.

1. W lewym okienku strony usługi w obszarze **Ustawienia** wybierz kartę **serwer konfiguracji** . Skonfiguruj wcześniej utworzone repozytorium:
   - Dodawanie adresu URL repozytorium zapisanego w poprzedniej sekcji
   - Kliknij pozycję włączone `Authentication` i wybierz pozycję `HTTP Basic`
   - __Nazwa__ użytkownika jest nazwą użytkownika zapisaną w poprzedniej sekcji
   - __Hasło__ jest hasłem zapisanym w poprzedniej sekcji
   - Kliknij przycisk "Zastosuj" i poczekaj na pomyślne wykonanie operacji

   ![Spring Cloud Config Server](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-app-configuration"></a>Usuwanie konfiguracji aplikacji

Po zapisaniu pliku konfiguracji przycisk **Usuń konfigurację aplikacji** zostanie wyświetlony na karcie **Konfiguracja** . Wybranie tego przycisku spowoduje całkowite wymazanie istniejących ustawień. Należy wybrać tę opcję, jeśli chcesz połączyć wystąpienie serwera konfiguracji z innym źródłem, na przykład przechodzenie z witryny GitHub do usługi Azure DevOps.



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i konfigurowania wystąpienia serwera konfiguracji chmury wiosennej. Aby dowiedzieć się więcej o zarządzaniu aplikacją, zobacz [skalowanie aplikacji w chmurze Azure wiosennej](spring-cloud-tutorial-scale-manual.md).

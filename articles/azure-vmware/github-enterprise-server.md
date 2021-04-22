---
title: Konfigurowanie serwera GitHub Enterprise w Azure VMware Solution prywatnej
description: Dowiedz się, jak skonfigurować GitHub Enterprise Server w Azure VMware Solution prywatnej.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 0ff7ab87d7401cd3faaecf149fb1b07be3f8db42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862943"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Konfigurowanie serwera GitHub Enterprise w Azure VMware Solution prywatnej

Ten artykuł zawiera instrukcje dotyczące konfiguracji programu GitHub Enterprise Server w wersji "on-premises" programu [GitHub.com](https://github.com/)w Azure VMware Solution prywatnej. Scenariusz, który ominiemy, to wystąpienie programu GitHub Enterprise Server, które może obsługiwać maksymalnie 3000 deweloperów, którzy uruchamiają do 25 zadań na minutę na GitHub Actions. Obejmuje on konfigurację (w momencie pisania) funkcji w wersji *zapoznawczej,* takich jak GitHub Actions. Aby dostosować konfigurację do konkretnych potrzeb, zapoznaj się z wymaganiami wymienionymi w te tematu Installing GitHub Enterprise Server on VMware (Instalowanie [programu GitHub Enterprise Server w programie VMware).](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

GitHub Enterprise Server wymaga prawidłowego klucza licencji. Możesz zarejestrować się, aby uzyskać [licencję próbną.](https://enterprise.github.com/trial) Jeśli chcesz rozszerzyć możliwości aplikacji GitHub Enterprise Server za pośrednictwem integracji, możesz zakwalifikować się do uzyskania bezpłatnej licencji dewelopera z pięcioma miejscami. Złóż wniosek o tę licencję [za pośrednictwem programu partnerskiego usługi GitHub.](https://partner.github.com/)

## <a name="installing-github-enterprise-server-on-vmware"></a>Instalowanie GitHub Enterprise Server w programie VMware

Pobierz [bieżącą wersję programu GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) for VMware ESXi/vSphere (OVA) i wd wdrażaj pobrany szablon [OVA.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Wybierz uruchamianie usługi GitHub lokalnie lub w chmurze.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Wdrażanie szablonu OVA.":::  

Podaj rozpoznawalną nazwę nowej maszyny wirtualnej, taką jak GitHubEnterpriseServer. Nie musisz uwzględniać szczegółów wydania w nazwie maszyny wirtualnej, ponieważ te szczegóły stają się nieaktualne podczas uaktualnienia wystąpienia. Wybierz na razie wszystkie wartości domyślne (wkrótce dokonamy edycji tych szczegółów) i poczekaj na zaimportowanie pliku OVA.

Po zaimportowaniu [dostosuj konfigurację sprzętu](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) do swoich potrzeb. W naszym przykładzie będziemy potrzebować następującej konfiguracji.

| Zasób | Konfiguracja standardowa | Konfiguracja standardowa + "Funkcje w wersji beta" (akcje) |
| --- | --- | --- |
| Procesory wirtualne | 4 | 8 |
| Pamięć | 32 GB | 61 GB |
| Dołączony magazyn | 250 GB | 300 GB |
| Magazyn główny | 200 GB | 200 GB |

Jednak twoje potrzeby mogą się różnić. Zapoznaj się ze wskazówkami na temat kwestii sprzętowych w [te tematu Installing GitHub Enterprise Server on VMware (Instalowanie programu GitHub Enterprise Server w programie VMware).](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations) Zobacz też [Dodawanie zasobów procesora CPU lub pamięci dla oprogramowania VMware,](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) aby dostosować konfigurację sprzętu w zależności od sytuacji.

## <a name="configuring-the-github-enterprise-server-instance"></a>Konfigurowanie wystąpienia GitHub Enterprise Server

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Zainstaluj GitHub Enterprise.":::  

Po włączeniu nowo aprowizowana maszyna wirtualna skonfiguruj ją [za pośrednictwem przeglądarki.](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance) Konieczne będzie przekazanie pliku licencji i ustawienie hasła konsoli zarządzania. Pamiętaj, aby zapisać to hasło w bezpiecznym miejscu.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Uzyskaj dostęp do powłoki administracyjnej za pośrednictwem programu SSH.":::    

Zalecamy co najmniej wykonać następujące czynności:

1. Przekaż publiczny klucz SSH do konsoli zarządzania, aby uzyskać dostęp do powłoki administracyjnej za [pośrednictwem programu SSH.](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh) 

2. [Skonfiguruj w wystąpieniu usługę TLS,](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) aby można było użyć certyfikatu podpisanego przez zaufany urząd certyfikacji.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Konfigurowanie wystąpienia.":::

Zastosuj ustawienia.  Po ponownym uruchomieniu wystąpienia możesz przejść do następnego kroku, **Configuring Blob Storage for GitHub Actions**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Utwórz konto administratora.":::

Po ponownym uruchomieniu wystąpienia możesz utworzyć nowe konto administratora w tym wystąpieniu. Zanotuj również hasło tego użytkownika.

### <a name="other-configuration-steps"></a>Inne kroki konfiguracji

Aby zabezpieczyć wystąpienie do użycia w środowisku produkcyjnym, zalecane są następujące opcjonalne kroki konfiguracji:

1. Skonfiguruj [wysoką dostępność w](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) celu ochrony przed:

    - Awarie oprogramowania (na poziomie systemu operacyjnego lub aplikacji)
    - Awarie sprzętowe (magazyn, procesor, pamięć RAM i tak dalej)
    - Błędy systemu hosta wirtualizacji
    - Logicznie lub fizycznie odcięcie sieci

2. [Skonfiguruj](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [narzędzia do tworzenia kopii zapasowych,](https://github.com/github/backup-utils)udostępniając wersjonarowane migawki do odzyskiwania po awarii hostowane w dostępności, które są oddzielone od wystąpienia podstawowego.
3. [Skonfiguruj izolację poddomeny](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)przy użyciu prawidłowego certyfikatu TLS, aby ograniczyć wykonywanie skryptów między witrynami i inne powiązane luki w zabezpieczeniach.

## <a name="configuring-blob-storage-for-github-actions"></a>Konfigurowanie magazynu obiektów blob na GitHub Actions

> [!NOTE]
> GitHub Actions jest obecnie dostępna w ograniczonej wersji beta na [GitHub Enterprise Server w wersji 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Zewnętrzny magazyn obiektów blob jest niezbędny do włączenia GitHub Actions na GitHub Enterprise Server (obecnie dostępnej jako funkcja "beta"). Ten zewnętrzny magazyn obiektów blob jest używany przez akcje do przechowywania artefaktów i dzienników. Akcje na GitHub Enterprise Server [Azure Blob Storage jako dostawca magazynu](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (i inni). Dlatego aprowiz będziemy aprowizowanie nowego konta usługi Azure Storage z [typem konta magazynu](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Aprowizuj Azure Blob Storage konto.":::

Po zakończeniu wdrażania nowego zasobu BlobStorage skopiuj i zanotuj ciąg połączenia (dostępne w obszarze Klucze dostępu). Wkrótce będzie potrzebny ten ciąg.

## <a name="setting-up-the-github-actions-runner"></a>Konfigurowanie GitHub Actions runnera

> [!NOTE]
> GitHub Actions jest obecnie dostępna jako ograniczona wersja beta w [wersji GitHub Enterprise Server 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

W tym momencie powinno być uruchomione wystąpienie GitHub Enterprise Server z utworzonym kontem administratora. Należy również mieć zewnętrzny magazyn obiektów blob, który będzie GitHub Actions na użytek trwałości.

Teraz utwórzmy coś, co GitHub Actions uruchomić; ponownie użyjemy Azure VMware Solution.

Najpierw zaaprowizujmy nową maszynę wirtualną w klastrze. Naszą maszynę wirtualną będziemy opierać na [najnowszej wersji systemu Ubuntu Server.](http://releases.ubuntu.com/20.04.1/)

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Aprowizuj nową maszynę wirtualną.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Aprowizuj nową maszynę wirtualną w kroku 2.":::

Po utworzeniu maszyny wirtualnej należy ją włączyć i nawiązać z nim połączenie za pośrednictwem połączenia SSH.

Następnie zainstaluj aplikację [uruchamianą przez program uruchamiacy](https://github.com/actions/runner) akcje, która uruchamia zadanie z GitHub Actions przepływu pracy. Zidentyfikuj i pobierz najbardziej aktualną wersję [](https://github.com/actions/runner/releases) systemu Linux x64 w programie Actions Runner na stronie wydań lub przez uruchomienie następującego szybkiego skryptu. Ten skrypt wymaga, aby na maszynie wirtualnej było obecne zarówno curl, jak i [jq.](https://stedolan.github.io/jq/)

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Plik powinien być teraz dostępny lokalnie na maszynie wirtualnej actions-runner-linux-arm64-tar.gz. \* Wyodrębnij tę tarball lokalnie:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

To wyodrębnianie rozpakowuje kilka plików lokalnie, w tym skrypt i , do którego wkrótce `config.sh` `run.sh` wrócimy.

## <a name="enabling-github-actions"></a>Włączanie GitHub Actions

> [!NOTE]
> GitHub Actions jest obecnie dostępna jako ograniczona wersja beta w [wersji GitHub Enterprise Server 2.22.](https://docs.github.com/en/enterprise/admin/github-actions)

Prawie tam! Skonfigurujmy i włączmy GitHub Actions w wystąpieniu GitHub Enterprise Server. Musimy uzyskać dostęp do powłoki administracyjnej wystąpienia GitHub Enterprise Server za pośrednictwem [SSH, a](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)następnie uruchomić następujące polecenia:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Następne uruchomienie:

`ghe-actions-check -s blob`

Powinny zostać wyświetlony dane wyjściowe: "Blob Storage jest w dobrej kondycji".

Teraz, GitHub Actions skonfigurowana, włącz ją dla użytkowników. Zaloguj się do wystąpienia GitHub Enterprise Server jako administrator i wybierz ![ ikonę Rakieta.](media/github-enterprise-server/rocket-icon.png) w prawym górnym rogu dowolnej strony. Na lewym pasku bocznym wybierz pozycję Przegląd dla **przedsiębiorstw,** a następnie pozycję **Zasady,** **Akcje** i wybierz opcję włączenia opcji **Akcje dla wszystkich organizacji.**

Następnie skonfiguruj swój program wykonuj na **karcie Self-hosted runners (Własne).** Wybierz **pozycję Dodaj nowy,** a następnie **pozycję Nowy** runner z listy rozwijanej.

Na następnej stronie zostanie przedstawiony zestaw poleceń do uruchomienia. Wystarczy skopiować polecenie  w celu skonfigurowania uruchamiacza, na przykład:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Skopiuj polecenie `config.sh` i wklej je do sesji w programie Actions Runner (utworzonym wcześniej).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Actions runner (Runner akcji).":::

Użyj polecenia run.sh, aby *uruchomić program* uruchamiacy:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Uruchom program uruchamiacy.":::

Aby udostępnić ten program do edycji dla organizacji w przedsiębiorstwie, edytuj jego dostęp do organizacji:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Edytowanie dostępu do programu runner.":::

W tym miejscu udostępnimy go wszystkim organizacjom, ale możesz ograniczyć dostęp do podzbioru organizacji, a nawet do określonych repozytoriów.

## <a name="optional-configuring-github-connect"></a>(Opcjonalnie) Konfigurowanie narzędzia GitHub Connect

Mimo że ten krok jest opcjonalny, zalecamy użycie akcji typu open source dostępnych w GitHub.com. Umożliwia on tworzenie na podstawie pracy innych osób, odwołując się do tych akcji wielokrotnego użytku w przepływach pracy.

Aby włączyć program GitHub Connect, wykonaj kroki opisane w te [tematu Enabling automatic access to GitHub.com actions using GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)(Włączanie automatycznego dostępu do akcji przy użyciu narzędzia GitHub Connect).

Po włączeniu narzędzia GitHub Connect wybierz opcję Serwer, aby używać akcji z GitHub.com **w przebiegach przepływu** pracy.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Włącz używanie akcji z GitHub.com w przebiegach przepływu pracy.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Konfigurowanie i uruchamianie pierwszego przepływu pracy

Teraz, po skonfigurowaniu funkcji Actions i narzędzia GitHub Connect, wykorzystajmy wszystkie te czynności. Oto przykładowy przepływ pracy, który odwołuje się do doskonałego [biblioteki octokit/request-action,](https://github.com/octokit/request-action)dzięki czemu możemy "skrypować" usługę GitHub za pośrednictwem interakcji przy użyciu interfejsu API usługi GitHub obsługiwanego przez GitHub Actions.

W tym podstawowym przepływie pracy użyjemy polecenia , aby otworzyć problem w `octokit/request-action` usłudze GitHub przy użyciu interfejsu API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Przykładowy przepływ pracy.":::

>[!NOTE]
>GitHub.com hostuje akcję, ale gdy jest uruchamiana na  serwerze GitHub Enterprise Server, automatycznie używa interfejsu API GitHub Enterprise Server.

Jeśli nie chcesz włączać narzędzia GitHub Connect, możesz użyć następującego alternatywnego przepływu pracy.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternatywny przykładowy przepływ pracy.":::

Przejdź do repo w swoim wystąpieniu i dodaj powyższy przepływ pracy jako: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Inny przykładowy przepływ pracy.":::

Na karcie **Akcje** dla swojego repo zaczekaj na wykonanie przepływu pracy.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Wykonany przykładowy przepływ pracy.":::

Możesz również obejrzeć, jak jest on przetwarzany przez runnera.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Przepływ pracy przetwarzany przez runnera.":::

Jeśli wszystko przebiegło pomyślnie, w twoim repocie powinien zostać wyświetlony nowy problem zatytułowany "Hello world".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Przykład w repo.":::

Gratulacje! Właśnie ukończono pierwszy przepływ pracy akcji na GitHub Enterprise Server, uruchomiony w Azure VMware Solution prywatnej.

W tym artykule skonfigurujemy nowe wystąpienie serwera GitHub Enterprise Server, własnego odpowiednika usługi GitHub.com, na podstawie Azure VMware Solution chmury prywatnej. To wystąpienie obejmuje obsługę GitHub Actions i używa Azure Blob Storage do utrwalania dzienników i artefaktów. Ale to tylko poszybowanie powierzchni tego, co można zrobić za pomocą GitHub Actions. Zapoznaj się z listą akcji w witrynie Marketplace usługi [GitHub](https://github.com/marketplace)lub [utwórz własny .](https://docs.github.com/en/actions/creating-actions)

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu serwera GitHub Enterprise w Azure VMware Solution prywatnej, możesz dowiedzieć się więcej na temat: 

- [Jak rozpocząć pracę z GitHub Actions](https://docs.github.com/en/actions)
- [Jak dołączyć do programu w wersji beta](https://resources.github.com/beta-signup/)
- [Administracja serwerem GitHub Enterprise Server](https://githubtraining.github.io/admin-training/#/00_getting_started)

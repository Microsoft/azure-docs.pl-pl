---
title: Konfigurowanie serwera usługi GitHub Enterprise w chmurze prywatnej rozwiązania Azure VMware
description: Dowiedz się, jak skonfigurować serwer usługi GitHub Enterprise w chmurze prywatnej rozwiązania Azure VMware.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 53e5264eed761909217c2e3a902c9fee9faaffaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91345185"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Konfigurowanie serwera usługi GitHub Enterprise w chmurze prywatnej rozwiązania Azure VMware

W tym artykule omówiono procedurę konfigurowania programu GitHub Enterprise Server, czyli "lokalnego" w wersji [GitHub.com](https://github.com/), w chmurze prywatnej rozwiązania platformy Azure VMware. Scenariusz opisany w tym przewodniku dotyczy wystąpienia serwera z systemem GitHub Enterprise, które może obsłużyć do 3 000 deweloperów z maksymalnie 25 zadaniami na minutę w ramach akcji usługi GitHub. Obejmuje on konfigurację (w czasie pisania) funkcji w *wersji zapoznawczej* , na przykład akcji usługi GitHub. Aby dostosować konfigurację do konkretnych potrzeb, należy zapoznać się z wymaganiami wymienionymi w temacie [Instalowanie serwera GitHub Enterprise Server w oprogramowaniu VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Serwer usługi GitHub Enterprise wymaga ważnego klucza licencji. Możesz zarejestrować się w celu uzyskania [licencji na wersję próbną](https://enterprise.github.com/trial). Jeśli chcesz zwiększyć możliwości programu GitHub Enterprise Server za pośrednictwem integracji, możesz zakwalifikować się do korzystania z bezpłatnej licencji na pięć miejsc. Zastosuj tę licencję w [programie partnerskim usługi GitHub](https://partner.github.com/).

## <a name="installing-github-enterprise-server-on-vmware"></a>Instalowanie programu GitHub Enterprise Server w oprogramowaniu VMware

Pobierz [bieżącą wersję programu GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download) dla VMware ESXi/vSphere (komórki jajowe) i [Wdróż pobrany szablon komórki jajowe](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) .

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze." (akcje) |
| --- | --- | --- |
| Procesory wirtualne | 4 | 8 |
| Pamięć | 32 GB | 61 GB |
| Dołączony magazyn | 250 GB | 300 GB |
| Magazyn główny | 200 GB | 200 GB |

Jednak Twoje potrzeby mogą się różnić. Zapoznaj się ze wskazówkami dotyczącymi zagadnień związanych z sprzętem podczas [instalowania programu GitHub Enterprise Server w oprogramowaniu VMware](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations). Zapoznaj się również z tematem [Dodawanie zasobów procesora lub pamięci dla programu VMware](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) , aby dostosować konfigurację sprzętu w zależności od sytuacji.

## <a name="configuring-the-github-enterprise-server-instance"></a>Konfigurowanie wystąpienia usługi GitHub Enterprise Server

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::  

Po włączeniu nowo zainicjowanej maszyny wirtualnej (VM) [skonfiguruj ją za pośrednictwem przeglądarki](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Konieczne będzie przekazanie pliku licencji i ustawienie hasła konsoli zarządzania. Pamiętaj o zapisaniu tego hasła w bezpiecznym miejscu.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::    

Zalecamy wykonanie następujących czynności:

1. Przekaż publiczny klucz SSH do konsoli zarządzania, aby [uzyskać dostęp do powłoki administracyjnej za pośrednictwem protokołu SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh). 

2. [Skonfiguruj protokół TLS w wystąpieniu](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) , aby można było używać certyfikatu podpisanego przez zaufany urząd certyfikacji.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Zastosuj ustawienia.  Po ponownym uruchomieniu wystąpienia można przejść do następnego kroku, **konfigurując BLOB Storage na potrzeby akcji usługi GitHub**.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Po ponownym uruchomieniu wystąpienia Utwórz nowe konto administratora w wystąpieniu. Pamiętaj, aby również zanotować hasło tego użytkownika.

### <a name="additional-configuration-steps"></a>Dodatkowe kroki konfiguracji

Aby zabezpieczyć wystąpienie do użycia w środowisku produkcyjnym, zalecane są następujące kroki instalacji opcjonalnej:

1. Skonfiguruj [wysoką dostępność](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) dla ochrony przed:

    - Awarie oprogramowania (poziom systemu operacyjnego lub aplikacji)
    - Awarie sprzętu (magazyn, procesor CPU, pamięć RAM itd.)
    - Awarie systemu hosta wirtualizacji
    - Logicznie lub fizycznie Poprzednia sieć

2. [Konfigurowanie](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [kopii zapasowych — narzędzia](https://github.com/github/backup-utils)udostępniające migawki z wersjami odzyskiwania po awarii, hostowane w dostępności, które są niezależne od wystąpienia podstawowego.
3. Należy [skonfigurować izolację poddomen](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)przy użyciu prawidłowego certyfikatu protokołu TLS, aby ograniczyć skrypty między lokacjami i inne powiązane luki w zabezpieczeniach.

## <a name="configuring-blob-storage-for-github-actions"></a>Konfigurowanie usługi BLOB Storage na potrzeby akcji usługi GitHub

> [!NOTE]
> Akcje usługi GitHub są [obecnie dostępne w ramach ograniczonej wersji beta w witrynie GitHub Enterprise Server w wersji 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Zewnętrzny magazyn obiektów BLOB jest wymagany do włączenia akcji GitHub na serwerze z systemem GitHub Enterprise (obecnie dostępnym jako funkcja "beta"). Zewnętrzny magazyn obiektów BLOB jest używany przez akcje do przechowywania artefaktów i dzienników. Akcje w witrynie GitHub Enterprise Server [obsługują platformę Azure Blob Storage jako dostawcę magazynu](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (i inne). Dlatego będziemy udostępniać nowe konto usługi Azure Storage z [typem konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#types-of-storage-accounts) BlobStorage:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Po zakończeniu wdrażania nowego zasobu BlobStorage skopiuj i zanotuj parametry połączenia (dostępne w obszarze klucze dostępu). Wkrótce będziemy potrzebować tego ciągu.

## <a name="setting-up-the-github-actions-runner"></a>Konfigurowanie modułu uruchamiającego akcje GitHub

> [!NOTE]
> Akcje usługi GitHub są [obecnie dostępne w ramach ograniczonej wersji beta w witrynie GitHub Enterprise Server w wersji 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Na tym etapie powinno być uruchomione wystąpienie serwera usługi GitHub dla przedsiębiorstw z kontem administratora. Należy również mieć zewnętrzną przestrzeń dyskową BLOB, która będzie używana przez akcje GitHub na potrzeby trwałości.

Teraz Utwórzmy tutaj akcje usługi GitHub do uruchomienia; ponownie korzystamy z rozwiązań VMware platformy Azure.

Najpierw Zainicjuj obsługę nowej maszyny wirtualnej w klastrze. Nasza maszyna wirtualna zostanie utworzona na podstawie [najnowszej wersji serwera Ubuntu](http://releases.ubuntu.com/20.04.1/).

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Po utworzeniu maszyny wirtualnej Włącz ją i Połącz z nią za pośrednictwem protokołu SSH.

Następnie zainstaluj aplikację [uruchamiającego akcje](https://github.com/actions/runner) , która uruchamia zadanie z przepływu pracy akcji usługi GitHub. Należy zidentyfikować i pobrać najnowszą wersję systemu Linux x64 dla programu uruchamiającego akcje, na [stronie wydania](https://github.com/actions/runner/releases) lub przez uruchomienie następującego szybkiego skryptu. Ten skrypt wymaga, aby elementy zwinięcie i [JQ](https://stedolan.github.io/jq/) były obecne na maszynie wirtualnej.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Plik powinien być teraz lokalnie na maszynie wirtualnej, akcje-Runner-Linux-arm64- \* . tar. gz. Wyodrębnij tę plik tar lokalnie:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

To wyodrębnienie powoduje rozpakowanie kilku plików lokalnie, w tym `config.sh` skryptu i, które powrócimy `run.sh` do wkrótce.

## <a name="enabling-github-actions"></a>Włączanie akcji GitHub

> [!NOTE]
> Akcje usługi GitHub są [obecnie dostępne w ramach ograniczonej wersji beta w witrynie GitHub Enterprise Server w wersji 2,22](https://docs.github.com/en/enterprise/admin/github-actions).

Prawie tam! Skonfigurujmy akcje usługi GitHub i włączamy je w wystąpieniu serwera usługi GitHub dla przedsiębiorstw. Będziemy musieli [uzyskać dostęp do powłoki administracyjnej wystąpienia serwera Enterprise w serwisie GitHub za pośrednictwem protokołu SSH](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh), a następnie uruchomić następujące polecenia:

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

Powinna zostać wyświetlona wartość wyjściowa: "Blob Storage jest w dobrej kondycji".

Teraz, gdy akcje usługi GitHub są skonfigurowane, włącz je dla użytkowników. Zaloguj się do swojego wystąpienia usługi GitHub Enterprise Server jako administrator i wybierz ![ ikonę rakiet.](media/github-enterprise-server/rocket-icon.png) w prawym górnym rogu dowolnej strony. Na lewym pasku bocznym wybierz pozycję **Przegląd przedsiębiorstwa**, a następnie pozycję **zasady**, **Akcje**i wybierz opcję **włączenia akcji dla wszystkich organizacji**.

Następnie skonfiguruj moduł uruchamiający z poziomu karty **modułu uruchamiającego własne** . Wybierz pozycję **Dodaj nowy** , a następnie **Nowy** moduł uruchamiający z listy rozwijanej.

Na następnej stronie zostanie wyświetlony zestaw poleceń do uruchomienia. wystarczy skopiować polecenie w celu **skonfigurowania** modułu uruchamiającego, na przykład:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Skopiuj `config.sh` polecenie i wklej je do sesji w module uruchamiającego akcje (utworzony wcześniej).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Użyj polecenia run.sh, aby *uruchomić moduł uruchamiający* :

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Aby udostępnić ten moduł uruchamiający organizacjom w przedsiębiorstwie, Edytuj dostęp do swojej organizacji:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

W tym miejscu udostępnimy ją wszystkim organizacjom, ale możesz również ograniczyć dostęp do podzbioru organizacji, a nawet do określonych repozytoriów.

## <a name="optional-configuring-github-connect"></a>Obowiązkowe Konfigurowanie połączenia GitHub

Chociaż ten krok jest opcjonalny, zalecamy go, jeśli planujesz korzystać z akcji Open Source dostępnych w witrynie GitHub.com. Dzięki temu można kompilować inne osoby, odwołując się do tych akcji wielokrotnego użytku w przepływach pracy.

Aby włączyć łączenie z usługą GitHub, wykonaj kroki opisane w temacie [Włączanie automatycznego dostępu do akcji GitHub.com przy użyciu programu GitHub Connect](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect).

Po włączeniu opcji Połącz z usługą GitHub wybierz **serwer, na którym mają być używane akcje z GitHub.com w przebiegu przepływu pracy** .

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

## <a name="setting-up-and-running-your-first-workflow"></a>Konfigurowanie i uruchamianie pierwszego przepływu pracy

Teraz, po skonfigurowaniu akcji i połączenia z usługą GitHub, przyjrzyjmy się tej pracy. Oto przykładowy przepływ pracy, który odwołuje się do doskonałej [akcji octokit/żądanie](https://github.com/octokit/request-action), umożliwiając nam "skrypt" usługi GitHub za pośrednictwem interakcji z użyciem interfejsu API usługi GitHub obsługiwanego przez akcje usługi GitHub.

W tym podstawowym przepływie pracy będziemy mogli `octokit/request-action` otworzyć problem w usłudze GitHub przy użyciu interfejsu API.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

>[!NOTE]
>GitHub.com hostuje akcję, ale gdy jest uruchomiona na serwerze usługi GitHub Enterprise, *automatycznie* korzysta z interfejsu API serwera usługi GitHub Enterprise.

W przypadku wybrania opcji nie Zezwalaj na łączenie z usługą GitHub można użyć poniższego alternatywnego przepływu pracy.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Przejdź do repozytorium na swoim wystąpieniu i Dodaj powyższy przepływ pracy jako: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Na karcie **Akcje** dla repozytorium Zaczekaj na wykonanie przepływu pracy.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Możesz również obejrzeć, że jest on przetwarzany przez moduł uruchamiający.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Jeśli wszystko zostało wykonane pomyślnie, zobaczysz nowy problem w repozytorium, zatytułowany "Hello World".

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Wybierz, aby uruchomić usługi GitHub lokalnie lub w chmurze.":::

Gratulacje! Wykonano właśnie pierwszy przepływ pracy w usłudze GitHub Enterprise Server uruchomiony w chmurze prywatnej rozwiązania Azure VMware.

Po prostu nakreślę powierzchnię tego, co możesz zrobić za pomocą akcji usługi GitHub. Aby uzyskać więcej informacji, zapoznaj się z listą akcji w [witrynie GitHub Marketplace](https://github.com/marketplace)lub [Utwórz własne](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Następne kroki

W tym artykule skonfigurujemy nowe wystąpienie programu GitHub Enterprise Server, samodzielnie hostowane GitHub.com, w oparciu o chmurę prywatną rozwiązania Azure VMware. To wystąpienie obejmuje obsługę akcji GitHub i używa platformy Azure Blob Storage do trwałości dzienników i artefaktów. Jest to świetna kombinacja dla nowoczesnego, współpracy i bezpiecznego środowiska tworzenia oprogramowania. Jest ona oparta na solidnych podstawach rozwiązań VMware platformy Azure, co pozwala na wykorzystanie zasobów w chmurze w znanym ustawieniu.

Aby uzyskać więcej informacji, zobacz następujące zasoby:

- [Wprowadzenie do akcji usługi GitHub](https://docs.github.com/en/actions)
- [Dołącz do programu beta](https://resources.github.com/beta-signup/)
- [Dowiedz się więcej o administrowaniu serwerem usługi GitHub Enterprise](https://githubtraining.github.io/admin-training/#/00_getting_started)

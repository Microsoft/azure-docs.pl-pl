---
title: Dostosowywanie Azure HDInsight klastra za pomocą akcji skryptu
description: Dodawanie składników niestandardowych do klastrów usługi HDInsight przy użyciu akcji skryptu. Akcje skryptu to skrypty powłoki Bash, których można użyć do dostosowania konfiguracji klastra. Możesz też dodać dodatkowe usługi i narzędzia, takie jak Hue, Solr lub R.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: d5500c04b4299c215eba843530dc84932fa10894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775047"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Dostosowywanie Azure HDInsight klastra za pomocą akcji skryptu

Azure HDInsight metodę konfiguracji o nazwie **akcje skryptu,** które wywołują niestandardowe skrypty w celu dostosowania klastra. Te skrypty służą do instalowania dodatkowych składników i zmieniania ustawień konfiguracji. Akcje skryptu mogą być używane podczas tworzenia klastra lub po jego utworzeniu.

Akcje skryptu mogą być również publikowane w skrypcie Azure Marketplace jako aplikacja HDInsight. Aby uzyskać więcej informacji na temat aplikacji HDInsight, zobacz [Publikowanie aplikacji HDInsight w Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="understand-script-actions"></a>Opis akcji skryptu

Akcja skryptu to skrypt powłoki Bash uruchamiany w węzłach w klastrze usługi HDInsight. Cechy i funkcje akcji skryptu są następujące:

- Aby uzyskać dostęp do pliku, musi być dostępny za pośrednictwem dostawcy zasobów usługi HDInsight i klastra.
- Poniżej przedstawiono możliwe lokalizacje przechowywania:

   - W przypadku zwykłych klastrów (innych niż ESP):
     - Obiekt blob na koncie usługi Azure Storage, który jest podstawowym lub dodatkowym kontem magazynu dla klastra usługi HDInsight. Usługa HDInsight uzyskuje dostęp do obu tych typów kont magazynu podczas tworzenia klastra.
    
       > [!IMPORTANT]  
       > Nie obracaj klucza magazynu na tym koncie usługi Azure Storage, ponieważ spowoduje to niepowodzenie kolejnych akcji skryptu ze zapisanymi tam skryptami.

     - Data Lake Storage Gen1: usługa HDInsight używa jednostki usługi Data Lake Storage musi mieć dostęp do odczytu skryptu. Format URI skryptu powłoki Bash to `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . 

     - Data Lake Storage Gen2 nie jest zalecane do użycia dla akcji skryptu. `abfs://` Nie jest obsługiwany w przypadku URI skryptu powłoki Bash. `https://` Możliwe są interfejsy URI, ale działają one w przypadku kontenerów, które mają dostęp publiczny, i zapora otwarta dla dostawcy zasobów usługi HDInsight i dlatego nie jest zalecana.

     - Publiczna usługa udostępniania plików dostępna za pośrednictwem `https://` ścieżek. Przykłady to Azure Blob, GitHub lub OneDrive. Na przykład URIs [(Przykładowe skrypty akcji skryptu).](#example-script-action-scripts)

  - W przypadku klastrów z esp, `wasb://` lub `wasbs://` lub są `http[s]://` obsługiwane.

- Akcje skryptu można ograniczyć do uruchamiania tylko dla niektórych typów węzłów. Przykłady to węzły główne lub węzły procesu roboczego.
- Akcje skryptu mogą być utrwalane lub *ad hoc.*

  - Utrwalone akcje skryptu muszą mieć unikatową nazwę. Utrwalone skrypty służą do dostosowywania nowych węzłów procesu roboczego dodawanych do klastra za pomocą operacji skalowania. Utrwalony skrypt może również zastosować zmiany do innego typu węzła podczas wykonywania operacji skalowania. Przykładem jest węzeł główny.
  - *Skrypty ad hoc* nie są utrwalane. Akcje skryptu używane podczas tworzenia klastra są automatycznie utrwalane. Nie są one stosowane do węzłów procesu roboczego dodanych do klastra po uruchomieniu skryptu. Następnie można promować *skrypt ad hoc* do utrwalonego skryptu lub zdegradować utrwalony skrypt do skryptu ad *hoc.* Skrypty, które się nie powiodą, nie są utrwalane, nawet jeśli wyraźnie wskazujesz, że powinny być.

- Akcje skryptu mogą akceptować parametry, które są używane przez skrypt podczas wykonywania.
- Akcje skryptu są uruchamiane z uprawnieniami na poziomie głównym w węzłach klastra.
- Akcje skryptu mogą być używane za pośrednictwem interfejsu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub zestawu .NET SDK usługi HDInsight.
- Akcje skryptu, które usuwają lub modyfikują pliki usługi na maszynie wirtualnej, mogą mieć wpływ na kondycję i dostępność usługi.

Klaster przechowuje historię wszystkich uruchomionych skryptów. Historia pomaga znaleźć identyfikator skryptu na potrzeby operacji podsumuj lub zwęzienia.

> [!IMPORTANT]  
> Nie ma automatycznego sposobu na cofnięcie zmian wprowadzonych przez akcję skryptu. Ręcznie odwróć zmiany lub udostępnij skrypt, który je odwraca.

## <a name="permissions"></a>Uprawnienia

W przypadku przyłączony do domeny klaster usługi HDInsight istnieją dwa uprawnienia Apache Ambari, które są wymagane w przypadku używania akcji skryptu z klastrem:

* **AMBARI. URUCHOM \_ POLECENIE \_ NIESTANDARDOWE.** Rola administratora systemu Ambari domyślnie ma to uprawnienie.
* **KLASTER. URUCHOM \_ POLECENIE \_ NIESTANDARDOWE.** Zarówno administrator klastra usługi HDInsight, jak i administrator systemu Ambari domyślnie mają to uprawnienie.

Aby uzyskać więcej informacji na temat pracy z uprawnieniami przy użyciu przyłączone do domeny usługi HDInsight, zobacz [Manage HDInsight clusters with pakiet Enterprise Security (Zarządzanie klastrami usługi HDInsight](./domain-joined/apache-domain-joined-manage.md)za pomocą usługi pakiet Enterprise Security ).

## <a name="access-control"></a>Kontrola dostępu

Jeśli nie jesteś administratorem ani właścicielem subskrypcji platformy Azure, Twoje konto musi mieć co najmniej dostęp do grupy zasobów, która `Contributor` zawiera klaster usługi HDInsight.

Ktoś z dostępem co najmniej współautora do subskrypcji platformy Azure musi wcześniej zarejestrować dostawcę. Rejestracja dostawcy ma miejsce, gdy użytkownik z dostępem współautora do subskrypcji tworzy zasób. Aby uzyskać informacje dotyczące bez tworzenia zasobu, [zobacz Rejestrowanie dostawcy przy użyciu usługi REST.](/rest/api/resources/providers#Providers_Register)

Uzyskaj więcej informacji na temat pracy z zarządzaniem dostępem:

- [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
- [Przypisywanie ról platformy Azure w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Metody używania akcji skryptu

Istnieje możliwość skonfigurowania akcji skryptu do uruchamiania podczas tworzenia klastra po raz pierwszy lub uruchamiania jej w istniejącym klastrze.

### <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w procesie tworzenia klastra

Akcje skryptu używane podczas tworzenia klastra różnią się nieco od akcji skryptu uruchamianych w istniejącym klastrze:

- Skrypt jest automatycznie utrwalany.
- Błąd w skrypcie może spowodować niepowodzenie procesu tworzenia klastra.

Na poniższym diagramie pokazano, kiedy akcja skryptu jest uruchamiana podczas procesu tworzenia:


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="Etapy tworzenia klastra" border="false":::

Skrypt jest uruchamiany podczas konfigurowania usługi HDInsight. Skrypt jest uruchamiany równolegle na wszystkich określonych węzłach w klastrze. Jest on uruchamiany z uprawnieniami głównymi w węzłach.

Możesz wykonać operacje, takie jak zatrzymywanie i uruchamianie usług, w tym usług związanych z usługami Apache Hadoop. Jeśli zatrzymasz usługi, upewnij się, że ambari i inne usługi związane z usługą Hadoop są uruchomione przed zakończeniem działania skryptu. Te wymagane usługi określają kondycję i stan klastra podczas jego tworzenia.

Podczas tworzenia klastra można używać wielu akcji skryptu jednocześnie. Te skrypty są wywoływane w kolejności, w której zostały określone.

> [!IMPORTANT]  
> Akcje skryptu muszą zakończyć się w ciągu 60 minut lub przejdą one w czasie. Podczas aprowizowania klastra skrypt jest uruchamiany współbieżnie z innymi procesami instalacji i konfiguracji. Rywalizacja o zasoby, takie jak czas procesora CPU lub przepustowość sieci, może spowodować, że wykonywanie skryptu będzie trwać dłużej niż w środowisku dewelopera.
>
> Aby zminimalizować czas uruchamiania skryptu, należy unikać zadań, takich jak pobieranie i kompilowanie aplikacji ze źródła. Prekompilowanie aplikacji i przechowywanie danych binarnych w usłudze Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Akcja skryptu w uruchomionym klastrze

Błąd skryptu w już uruchomionym klastrze nie powoduje automatycznego zmiany klastra na stan niepowodzenia. Po zakończeniu działania skryptu klaster powinien powrócić do stanu uruchomienia. Nawet jeśli klaster ma stan uruchomienia, działanie skryptu, który zakończył się niepowodzeniem, może spowodować uszkodzone działanie. Na przykład skrypt może usuwać pliki wymagane przez klaster.

Akcje skryptów są uruchamiane z uprawnieniami głównymi. Przed zastosowaniem skryptu do klastra upewnij się, że rozumiesz, jak działa skrypt.

Po zastosowaniu skryptu do klastra stan klastra zmieni się z Uruchomiony **na** **Zaakceptowany.** Następnie zmienia się w **konfigurację usługi HDInsight,** a na koniec z powrotem do ustawienia **Uruchomione** w celu pomyślnego uruchomienia skryptów. Stan skryptu jest rejestrowany w historii akcji skryptu. Te informacje zawierają informacje o tym, czy skrypt zakończył się powodzeniem, czy niepowodzeniem. Na przykład polecenie `Get-AzHDInsightScriptActionHistory` cmdlet programu PowerShell wyświetla stan skryptu. Zwraca informacje podobne do następującego tekstu:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Jeśli zmienisz użytkownika klastra, administratora, hasło po utworzeniu klastra, akcje skryptu uruchamiane w tym klastrze mogą się nie powieść. Jeśli masz jakiekolwiek utrwalone akcje skryptu, które są kierowane do węzłów procesu roboczego, te skrypty mogą się nie powieść podczas skalowania klastra.

## <a name="example-script-action-scripts"></a>Przykładowe skrypty akcji skryptu

Skrypty akcji mogą być używane za pośrednictwem następujących narzędzi:

* Azure Portal
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure
* HDInsight .NET SDK

Usługa HDInsight udostępnia skrypty do instalowania następujących składników w klastrach usługi HDInsight:

| Nazwa | Skrypt |
| --- | --- |
| Dodawanie konta usługi Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight.](hdinsight-hadoop-add-storage.md) |
| Instalowanie aplikacji Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zobacz Install and use Hue on HDInsight Hadoop clusters (Instalowanie i używanie aplikacji [Hue w klastrach hadoop usługi HDInsight).](hdinsight-hadoop-hue-linux.md) |
| Wstępne ładowanie bibliotek Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zobacz [Dodawanie niestandardowych bibliotek Apache Hive podczas tworzenia klastra usługi HDInsight.](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="script-action-during-cluster-creation"></a>Akcja skryptu podczas tworzenia klastra

W tej sekcji wyjaśniono różne sposoby używania akcji skryptu podczas tworzenia klastra usługi HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Używanie akcji skryptu podczas tworzenia klastra z Azure Portal

1. Rozpocznij tworzenie klastra zgodnie z opisem w temacie [Create Linux-based clusters in HDInsight by using the Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md). Na karcie **Konfiguracja i cennik** wybierz pozycję + Dodaj **akcję skryptu.**

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Azure Portal akcji skryptu klastra":::

1. Użyj pozycji __Wybierz skrypt,__ aby wybrać wstępnie spreprecyzowy skrypt. Aby użyć niestandardowego skryptu, wybierz pozycję __Niestandardowy.__ Następnie podaj nazwę __i__ adres __URI skryptu powłoki Bash__ dla skryptu.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Dodawanie skryptu w formularzu wybierania skryptu":::

   W poniższej tabeli opisano elementy formularza:

   | Właściwość | Wartość |
   | --- | --- |
   | Wybieranie skryptu | Aby użyć własnego skryptu, wybierz pozycję __Niestandardowy.__ W przeciwnym razie wybierz jeden z podanych skryptów. |
   | Nazwa |Określ nazwę akcji skryptu. |
   | URI skryptu powłoki Bash |Określ URI skryptu. |
   | Head/Worker/ZooKeeper |Określ węzły, na których jest uruchamiany skrypt: **Head**, **Worker** lub **ZooKeeper.** |
   | Parametry |Określ parametry, jeśli są wymagane przez skrypt. |

   Użyj wpisu __akcji Utrwal ten__ skrypt, aby upewnić się, że skrypt jest stosowany podczas operacji skalowania.

1. Wybierz __pozycję Utwórz,__ aby zapisać skrypt. Następnie możesz użyć polecenia __+ Prześlij nowy,__ aby dodać kolejny skrypt.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="Wiele akcji skryptu w umacie HDInsight":::

   Po dodaniu skryptów wrócisz do karty **Konfiguracja i** cennik.

1. Wykonaj pozostałe kroki tworzenia klastra w zwykły sposób.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Używanie akcji skryptu z Azure Resource Manager szablonów

Akcje skryptu mogą być używane z Azure Resource Manager szablonów. Przykład można znaleźć w temacie Create HDInsight Linux Cluster and run a script action (Tworzenie klastra [usługi HDInsight w systemie Linux i uruchamianie akcji skryptu).](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)

W tym przykładzie akcja skryptu jest dodawana przy użyciu następującego kodu:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Uzyskaj więcej informacji na temat wdrażania szablonu:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/templates/deploy-powershell.md)
- [Wdrażanie zasobów przy użyciu szablonów Resource Manager i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Używanie akcji skryptu podczas tworzenia klastra z Azure PowerShell

W tej sekcji użyjemy polecenia cmdlet [Add-AzHDInsightScriptAction,](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) aby wywołać skrypty w celu dostosowania klastra. Przed rozpoczęciem upewnij się, że instalujesz i konfigurujesz Azure PowerShell. Aby użyć tych poleceń programu PowerShell, potrzebny jest [moduł AZ .](/powershell/azure/)

Poniższy skrypt pokazuje, jak zastosować akcję skryptu podczas tworzenia klastra przy użyciu programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Zanim klaster zostanie utworzony, może mi potrwać kilka minut.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Używanie akcji skryptu podczas tworzenia klastra z zestawu .NET SDK usługi HDInsight

Zestaw .NET SDK usługi HDInsight udostępnia biblioteki klienckie, które ułatwiają pracę z usługą HDInsight z aplikacji .NET. Aby uzyskać przykładowy kod, zobacz [Akcje skryptu](/dotnet/api/overview/azure/hdinsight#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Akcja skryptu w uruchomionym klastrze

W tej sekcji wyjaśniono, jak stosować akcje skryptu w uruchomionym klastrze.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Stosowanie akcji skryptu do uruchomionego klastra z Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i znajdź klaster.

1. W widoku domyślnym w obszarze **Ustawienia wybierz** pozycję **Akcje skryptu.**

1. W górnej części strony **Akcje skryptu** wybierz pozycję + **Prześlij nowy**.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="Dodawanie skryptu do uruchomionego klastra":::

1. Użyj pozycji __Wybierz skrypt,__ aby wybrać wstępnie spreprecyzowy skrypt. Aby użyć niestandardowego skryptu, wybierz pozycję __Niestandardowy.__ Następnie podaj nazwę __i__ adres __URI skryptu powłoki Bash__ dla skryptu.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Dodawanie skryptu w formularzu wybierania skryptu":::

   W poniższej tabeli opisano elementy formularza:

   | Właściwość | Wartość |
   | --- | --- |
   | Wybieranie skryptu | Aby użyć własnego skryptu, wybierz niestandardowy __.__ W przeciwnym razie wybierz podany skrypt. |
   | Nazwa |Określ nazwę akcji skryptu. |
   | URI skryptu powłoki Bash |Określ URI skryptu. |
   | Head/Worker/Zookeeper |Określ węzły, na których jest uruchamiany skrypt: **Head**, **Worker** lub **ZooKeeper.** |
   | Parametry |Określ parametry, jeśli są wymagane przez skrypt. |

   Użyj wpisu __akcji Utrwal__ ten skrypt, aby upewnić się, że skrypt jest stosowany podczas operacji skalowania.

1. Na koniec wybierz przycisk **Utwórz,** aby zastosować skrypt do klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Stosowanie akcji skryptu do uruchomionego klastra z Azure PowerShell

Do korzystania z tych poleceń programu PowerShell potrzebny jest [moduł AZ](/powershell/azure/). W poniższym przykładzie pokazano, jak zastosować akcję skryptu do uruchomionego klastra:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po zakończeniu operacji otrzymasz informacje podobne do następującego tekstu:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Stosowanie akcji skryptu do uruchomionego klastra z interfejsu wiersza polecenia platformy Azure

Przed rozpoczęciem upewnij się, że interfejs wiersza polecenia platformy Azure został zainstalowany i skonfigurowany. Upewnij się, że masz najnowszą wersję. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

1. Uwierzytelnianie w subskrypcji platformy Azure:

   ```azurecli
   az login
   ```

1. Zastosuj akcję skryptu do uruchomionego klastra:

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   Prawidłowe role to `headnode` , `workernode` , , `zookeepernode` `edgenode` . Jeśli skrypt powinien być stosowany do kilku typów węzłów, należy oddzielić role spacją. Na przykład `--roles headnode workernode`.

   Aby utrwalić skrypt, `--persist-on-success` dodaj . Skrypt można również utrwalić później przy użyciu polecenia `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Stosowanie akcji skryptu do uruchomionego klastra przy użyciu interfejsu API REST

Zobacz [Interfejs API REST klastra w Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Stosowanie akcji skryptu do uruchomionego klastra z zestawu .NET SDK usługi HDInsight

Aby uzyskać przykład zastosowania skryptów do klastra przy użyciu zestawu SDK platformy .NET, zobacz Apply [a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)(Stosowanie akcji skryptu względem działającego klastra usługi HDInsight opartego na systemie Linux).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Wyświetlanie historii oraz podszycie i obniżanie akcji skryptu

### <a name="the-azure-portal"></a>Witryna Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i znajdź klaster.

1. W widoku domyślnym w obszarze **Ustawienia wybierz** pozycję **Akcje skryptu.**

1. Historia skryptów dla tego klastra jest wyświetlana w sekcji akcji skryptu. Te informacje zawierają listę utrwalonych skryptów. Poniższy zrzut ekranu pokazuje, że skrypt Solr został uruchomiony w tym klastrze. Zrzut ekranu nie pokazuje żadnych utrwalonych skryptów.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="Historia przesyłania akcji skryptu portalu":::

1. Wybierz skrypt z historii, aby wyświetlić **sekcję Właściwości** dla tego skryptu. W górnej części ekranu możesz ponownie uruchomić skrypt lub go podniesieć.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="Podniesienie właściwości akcji skryptu":::

1. Możesz również wybrać wielokropek **...** z prawej strony wpisów w sekcji akcji skryptu, aby wykonać akcje.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="Usuwanie utrwalonych akcji skryptu":::

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funkcja |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Pobieranie informacji o utrwalonych akcjach skryptu. To polecenie cmdlet nie powoduje cofnięcia akcji wykonanej przez skrypt— usuwa tylko utrwaloną flagę.|
| `Get-AzHDInsightScriptActionHistory` |Pobieranie historii akcji skryptu zastosowanych do klastra lub szczegółów dla określonego skryptu. |
| `Set-AzHDInsightPersistedScriptAction` |Podość akcji `ad hoc` skryptu do utrwalonej akcji skryptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Obniżanie utrwalonej akcji skryptu na `ad hoc` akcję. |

Poniższy przykładowy skrypt demonstruje użycie polecenia cmdlet w celu podniesienia, a następnie obniżania jego wersji.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

| Polecenie | Opis |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_delete) |Usuwa określoną utrwaloną akcję skryptu klastra. To polecenie nie powoduje cofnięcia akcji wykonanej przez skrypt— usuwa tylko utrwaloną flagę.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_execute)|Wykonaj akcje skryptu w określonym klastrze usługi HDInsight.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list) |Wyświetla listę wszystkich utrwalonych akcji skryptu dla określonego klastra. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list_execution_history)|Wyświetla listę historii wykonywania wszystkich skryptów dla określonego klastra.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_promote)|Podniesie określone wykonanie skryptu ad hoc do utrwalonego skryptu.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_show_execution_details)|Pobiera szczegóły wykonywania skryptu dla danego identyfikatora wykonania skryptu.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Aby uzyskać przykład użycia zestawu SDK platformy .NET do pobierania historii skryptów z klastra, podsumuj lub obniżania poziom skryptów, zobacz Apply [ a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)(Stosowanie akcji skryptu względem działającego klastra usługi HDInsight opartego na systemie Linux).

> [!NOTE]  
> W tym przykładzie pokazano również sposób instalowania aplikacji usługi HDInsight przy użyciu zestawu SDK platformy .NET.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie skryptów akcji skryptu dla hdinsight](hdinsight-hadoop-script-actions-linux.md)
* [Dodawanie dodatkowego magazynu do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md)
* [Rozwiązywanie problemów z akcjami skryptu](troubleshoot-script-action.md)

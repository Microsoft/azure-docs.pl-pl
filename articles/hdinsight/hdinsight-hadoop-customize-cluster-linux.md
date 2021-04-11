---
title: Dostosowywanie klastrów usługi Azure HDInsight za pomocą akcji skryptu
description: Dodaj niestandardowe składniki do klastrów usługi HDInsight za pomocą akcji skryptu. Akcje skryptu są skryptami bash, których można użyć do dostosowania konfiguracji klastra. Lub Dodaj dodatkowe usługi i narzędzia, takie jak odcień, Solr lub R.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: efd145732ecc119e2fdf9b73ca59729232a37d4c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109526"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Dostosowywanie klastrów usługi Azure HDInsight za pomocą akcji skryptu

Usługa Azure HDInsight udostępnia metodę konfiguracji o nazwie **Akcje skryptu** , które wywołują skrypty niestandardowe w celu dostosowania klastra. Skrypty te służą do instalowania dodatkowych składników i zmieniania ustawień konfiguracji. Akcji skryptu można używać podczas tworzenia klastra lub po nim.

Akcje skryptu można również publikować w portalu Azure Marketplace jako aplikację usługi HDInsight. Aby uzyskać więcej informacji na temat aplikacji HDInsight, zobacz [publikowanie aplikacji usługi HDInsight w portalu Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="understand-script-actions"></a>Opis akcji skryptu

Akcja skryptu to skrypt bash, który działa w węzłach klastra usługi HDInsight. Właściwości i funkcje skryptu są następujące:

- Identyfikator URI skryptu bash (lokalizacja uzyskiwania dostępu do pliku) musi być dostępny z poziomu dostawcy zasobów usługi HDInsight i klastra.
- Możliwe są następujące lokalizacje magazynu:

   - W przypadku regularnych klastrów (innych niż ESP):
     - Obiekt BLOB na koncie magazynu platformy Azure, który jest podstawowym lub dodatkowym kontem magazynu dla klastra usługi HDInsight. Usługa HDInsight ma dostęp do obu typów kont magazynu podczas tworzenia klastra.
    
       > [!IMPORTANT]  
       > Nie obracaj klucza magazynu na tym koncie usługi Azure Storage, ponieważ spowoduje to, że kolejne akcje skryptu z zapisanymi skryptami nie powiodą się.

     - Data Lake Storage Gen1: główna Usługa HDInsight używa do uzyskiwania dostępu do Data Lake Storage musi mieć dostęp do odczytu do skryptu. Format identyfikatora URI skryptu bash to `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . 

     - Nie zaleca się stosowania Data Lake Storage Gen2 dla akcji skryptu. `abfs://` nie jest obsługiwany dla identyfikatora URI skryptu bash. `https://` Możliwe są identyfikatory URI, ale te działają w przypadku kontenerów mających dostęp publiczny, a Zapora jest otwarta dla dostawcy zasobów usługi HDInsight. w związku z tym nie jest to zalecane.

     - Publiczna usługa udostępniania plików dostępna za pomocą `https://` ścieżek. Przykłady to Azure Blob, GitHub lub OneDrive. Przykładowo identyfikatory URI, zobacz [przykładowe skrypty akcji skryptu](#example-script-action-scripts).

  - W przypadku klastrów z ESP `wasb://` `wasbs://` `http[s]://` obsługiwane są identyfikatory URI lub lub.

- Akcje skryptu mogą być ograniczone do uruchamiania tylko dla określonych typów węzłów. Przykłady to węzły główne lub węzły procesu roboczego.
- Akcje skryptu mogą być utrwalone lub *ad hoc*.

  - Akcje utrwalonego skryptu muszą mieć unikatową nazwę. Utrwalone skrypty są używane do dostosowywania nowych węzłów procesu roboczego dodanych do klastra za pomocą operacji skalowania. Utrwalony skrypt może również zastosować zmiany do innego typu węzła w przypadku wystąpienia operacji skalowania. Przykładem jest węzeł główny.
  - Skrypty *ad hoc* nie są utrwalane. Akcje skryptu używane podczas tworzenia klastra są automatycznie utrwalane. Nie są one stosowane do węzłów procesu roboczego dodanych do klastra po uruchomieniu skryptu. Następnie można podwyższyć poziom skryptu *ad hoc* do utrwalonego skryptu lub obniżyć utrwalony skrypt do skryptu *ad hoc* . Skrypty, które nie są utrwalane, nawet jeśli wskazują, że powinny być.

- Akcje skryptu mogą akceptować parametry, które są używane przez skrypt podczas wykonywania.
- Akcje skryptu są uruchamiane z uprawnieniami poziomu głównego w węzłach klastra.
- Akcje skryptu mogą być używane w ramach zestawu .NET SDK Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub usługi HDInsight.
- Akcje skryptów, które usuwają lub modyfikują pliki usługi na maszynie wirtualnej, mogą mieć wpływ na kondycję usługi i dostępność.

Klaster przechowuje historię wszystkich uruchomionych skryptów. Historia jest pomocna w przypadku konieczności znalezienia identyfikatora skryptu dla operacji podwyższania lub obniżania poziomu.

> [!IMPORTANT]  
> Nie ma automatycznej metody cofania zmian dokonanych przez akcję skryptu. Ręcznie odwrócić zmiany lub podaj skrypt, który go odwraca.

## <a name="permissions"></a>Uprawnienia

W przypadku klastra usługi HDInsight przyłączonego do domeny istnieją dwa uprawnienia Apache Ambari, które są wymagane w przypadku korzystania z akcji skryptów w klastrze:

* **AMBARI. Uruchom \_ \_ polecenie niestandardowe**. To uprawnienie jest domyślnie objęte rolą administratora Ambari.
* **Klaster. Uruchom \_ \_ polecenie niestandardowe**. Zarówno administrator klastra usługi HDInsight, jak i administrator Ambari domyślnie mają to uprawnienie.

Aby uzyskać więcej informacji na temat pracy z uprawnieniami w usłudze HDInsight przyłączonych do domeny, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu pakiet Enterprise Security](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Kontrola dostępu

Jeśli nie jesteś administratorem ani właścicielem subskrypcji platformy Azure, Twoje konto musi mieć co najmniej `Contributor` dostęp do grupy zasobów zawierającej klaster usługi HDInsight.

Ktoś, który ma co najmniej dostęp współautora do subskrypcji platformy Azure, musiał wcześniej zarejestrować dostawcę. Rejestracja dostawcy odbywa się, gdy użytkownik z dostępem współautora do subskrypcji tworzy zasób. W przypadku programu bez tworzenia zasobu Zobacz temat [Rejestrowanie dostawcy za pomocą usługi REST](/rest/api/resources/providers#Providers_Register).

Uzyskaj więcej informacji na temat pracy z zarządzaniem dostępem:

- [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../role-based-access-control/overview.md)
- [Przypisywanie ról platformy Azure w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Metody używania akcji skryptu

Istnieje możliwość skonfigurowania akcji skryptu do uruchamiania podczas pierwszego tworzenia klastra lub uruchamiania jej w istniejącym klastrze.

### <a name="script-action-in-the-cluster-creation-process"></a>Akcja skryptu w procesie tworzenia klastra

Akcje skryptu używane podczas tworzenia klastra są nieco inne niż akcje skryptu uruchamiane w istniejącym klastrze:

- Skrypt jest automatycznie utrwalany.
- Awaria skryptu może spowodować niepowodzenie procesu tworzenia klastra.

Na poniższym diagramie przedstawiono, kiedy Akcja skryptu jest uruchamiana podczas procesu tworzenia:


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="Etapy podczas tworzenia klastra" border="false":::

Skrypt jest uruchamiany podczas konfigurowania usługi HDInsight. Skrypt jest uruchamiany równolegle na wszystkich określonych węzłach w klastrze. Jest on uruchamiany z uprawnieniami głównymi w węzłach.

Można wykonywać operacje, takie jak zatrzymywanie i uruchamianie usług, w tym usługi związane z Apache Hadoop. Jeśli zatrzymasz usługi, upewnij się, że Ambari i inne usługi związane z usługą Hadoop są uruchomione przed zakończeniem działania skryptu. Te wymagane usługi określają kondycję i stan klastra podczas jego tworzenia.

Podczas tworzenia klastra można używać wielu akcji skryptów jednocześnie. Te skrypty są wywoływane w kolejności, w jakiej zostały określone.

> [!IMPORTANT]  
> Akcje skryptu muszą zakończyć się w ciągu 60 minut lub przekroczyć limit czasu. Podczas aprowizacji klastra skrypt jest uruchamiany współbieżnie z innymi procesami instalacji i konfiguracji. Konkurencja zasobów, takich jak czas procesora CPU lub przepustowość sieci, może spowodować, że wykonanie skryptu trwa dłużej niż w środowisku deweloperskim.
>
> Aby zminimalizować czas potrzebny na uruchomienie skryptu, należy unikać zadań, takich jak pobieranie i kompilowanie aplikacji ze źródła. Wstępne Kompilowanie aplikacji i przechowywanie plików binarnych w usłudze Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Akcja skryptu w uruchomionym klastrze

Awaria skryptu na już uruchomionym klastrze nie powoduje automatycznego zmiany stanu klastra na niepowodzenie. Po zakończeniu działania skryptu klaster powinien powrócić do stanu uruchomienia. Nawet jeśli klaster ma stan uruchomiony, uszkodzony skrypt może być uszkodzony. Na przykład skrypt może usunąć pliki potrzebne przez klaster.

Akcje skryptów są uruchamiane z uprawnieniami głównymi. Zapoznaj się z treścią skryptu przed zastosowaniem go w klastrze.

Po zastosowaniu skryptu do klastra stan klastra zmienia się z **uruchomione** na **zaakceptowane**. Następnie zmieni się on na **Konfiguracja usługi HDInsight** , a wreszcie ponownie, aby **uruchomić** skrypty. Stan skryptu jest rejestrowany w historii akcji skryptu. Te informacje informują o tym, czy skrypt zakończył się powodzeniem, czy niepowodzeniem. Na przykład `Get-AzHDInsightScriptActionHistory` polecenie cmdlet programu PowerShell pokazuje stan skryptu. Zwraca informacje podobne do następującego tekstu:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> W przypadku zmiany użytkownika klastra, administratora, hasła po utworzeniu klastra akcje skryptu uruchamiane względem tego klastra mogą zakończyć się niepowodzeniem. Jeśli masz jakiekolwiek akcje utrwalonego skryptu, które są przeznaczone dla węzłów procesu roboczego, te skrypty mogą kończyć się niepowodzeniem podczas skalowania klastra.

## <a name="example-script-action-scripts"></a>Przykładowe skrypty akcji skryptu

Skrypty akcji skryptu mogą być używane w następujących narzędziach:

* Azure Portal
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure
* Zestaw SDK usługi HDInsight dla platformy .NET

Usługa HDInsight udostępnia skrypty umożliwiające zainstalowanie następujących składników w klastrach usługi HDInsight:

| Nazwa | Skrypt |
| --- | --- |
| Dodawanie konta usługi Azure Storage |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zobacz [Dodawanie dodatkowych kont magazynu do usługi HDInsight](hdinsight-hadoop-add-storage.md). |
| Zainstaluj odcień |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zobacz [Instalowanie i używanie odcienia w klastrach usługi HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Wstępne ładowanie bibliotek Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zobacz [Dodawanie niestandardowych bibliotek Apache Hive podczas tworzenia klastra usługi HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Akcja skryptu podczas tworzenia klastra

W tej sekcji opisano różne sposoby używania akcji skryptu podczas tworzenia klastra usługi HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Użyj akcji skryptu podczas tworzenia klastra z Azure Portal

1. Rozpocznij tworzenie klastra zgodnie z opisem w temacie [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md). Na karcie **Konfiguracja i Cennik** wybierz pozycję **+ Dodaj akcję skryptu**.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Akcja skryptu Azure Portal klastra":::

1. Użyj wpisu __skryptu__ , aby wybrać utworzony skrypt. Aby użyć niestandardowego skryptu, wybierz opcję __niestandardowy__. Podaj __nazwę__ i __Identyfikator URI skryptu bash__ dla skryptu.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Dodawanie skryptu w formularzu wybierania skryptu":::

   W poniższej tabeli opisano elementy w formularzu:

   | Właściwość | Wartość |
   | --- | --- |
   | Wybierz skrypt | Aby użyć własnego skryptu, wybierz opcję __niestandardowy__. W przeciwnym razie wybierz jeden z podanych skryptów. |
   | Nazwa |Określ nazwę akcji skryptu. |
   | Identyfikator URI skryptu bash |Określ identyfikator URI skryptu. |
   | Kierownik/proces roboczy/dozorcy |Określ węzły, w których skrypt jest uruchamiany: **główna**, **proces roboczy** lub **dozorcy**. |
   | Parametry |Określ parametry, jeśli są wymagane przez skrypt. |

   Aby upewnić się, że skrypt jest stosowany podczas operacji skalowania, należy użyć wpisu __akcji Utrwalaj ten skrypt__ .

1. Wybierz pozycję __Utwórz__ , aby zapisać skrypt. Następnie możesz użyć __+ Prześlij nowy__ , aby dodać kolejny skrypt.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="Operacje wielu skryptów usługi HDInsight":::

   Po zakończeniu dodawania skryptów Wróć do karty **Konfiguracja i Cennik** .

1. Wykonaj pozostałe kroki tworzenia klastra w zwykły sposób.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Używanie akcji skryptu z szablonów Azure Resource Manager

Akcji skryptu można używać z szablonami Azure Resource Manager. Aby zapoznać się z przykładem, zobacz [Tworzenie klastra usługi HDInsight w systemie Linux i uruchamianie akcji skryptu](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

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
- [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Użyj akcji skryptu podczas tworzenia klastra z Azure PowerShell

W tej sekcji należy użyć polecenia cmdlet [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) w celu wywołania skryptów w celu dostosowania klastra. Przed rozpoczęciem upewnij się, że zainstalowano i skonfigurowano Azure PowerShell. Aby można było używać tych poleceń programu PowerShell, należy użyć polecenia [AZ module](/powershell/azure/).

Poniższy skrypt pokazuje, jak zastosować akcję skryptu podczas tworzenia klastra przy użyciu programu PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Utworzenie klastra może potrwać kilka minut.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Używanie akcji skryptu podczas tworzenia klastra z poziomu zestawu .NET SDK usługi HDInsight

Zestaw SDK platformy .NET dla usługi HDInsight zawiera biblioteki klienckie, które ułatwiają pracę z usługą HDInsight z poziomu aplikacji .NET. Aby uzyskać przykład kodu, zobacz [Akcje skryptu](/dotnet/api/overview/azure/hdinsight#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Akcja skryptu w uruchomionym klastrze

W tej sekcji wyjaśniono, jak zastosować akcje skryptu w uruchomionym klastrze.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Zastosuj akcję skryptu do działającego klastra z Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Znajdź swój klaster.

1. W widoku domyślnym w obszarze **Ustawienia** wybierz pozycję **Akcje skryptu**.

1. W górnej części strony **Akcje skryptu** wybierz pozycję **+ Prześlij nowy**.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="Dodawanie skryptu do działającego klastra":::

1. Użyj wpisu __skryptu__ , aby wybrać utworzony skrypt. Aby użyć niestandardowego skryptu, wybierz opcję __niestandardowy__. Podaj __nazwę__ i __Identyfikator URI skryptu bash__ dla skryptu.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Dodawanie skryptu w formularzu wybierania skryptu":::

   W poniższej tabeli opisano elementy w formularzu:

   | Właściwość | Wartość |
   | --- | --- |
   | Wybierz skrypt | Aby użyć własnego skryptu, wybierz opcję __niestandardowy__. W przeciwnym razie wybierz podany skrypt. |
   | Nazwa |Określ nazwę akcji skryptu. |
   | Identyfikator URI skryptu bash |Określ identyfikator URI skryptu. |
   | Kierownik/proces roboczy/dozorcy |Określ węzły, w których skrypt jest uruchamiany: **główna**, **proces roboczy** lub **dozorcy**. |
   | Parametry |Określ parametry, jeśli są wymagane przez skrypt. |

   Użyj wpisu __Akcja Utrwalaj ten skrypt__ , aby upewnić się, że skrypt jest stosowany podczas operacji skalowania.

1. Na koniec wybierz przycisk **Utwórz** , aby zastosować skrypt do klastra.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Zastosuj akcję skryptu do działającego klastra z Azure PowerShell

Aby można było używać tych poleceń programu PowerShell, należy użyć polecenia [AZ module](/powershell/azure/). Poniższy przykład pokazuje, jak zastosować akcję skryptu do działającego klastra:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Po zakończeniu operacji otrzymujesz informacje podobne do następującego tekstu:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Stosowanie akcji skryptu do działającego klastra z poziomu interfejsu wiersza polecenia platformy Azure

Przed rozpoczęciem upewnij się, że zainstalowano i skonfigurowano interfejs wiersza polecenia platformy Azure. Upewnij się, że masz najnowszą wersję. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Uwierzytelnianie w ramach subskrypcji platformy Azure:

   ```azurecli
   az login
   ```

1. Zastosuj akcję skryptu do działającego klastra:

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   Prawidłowe role to `headnode` , `workernode` , `zookeepernode` , `edgenode` . Jeśli skrypt ma być stosowany do kilku typów węzłów, rozdziel role przez miejsce. Na przykład `--roles headnode workernode`.

   Aby zachować skrypt, Dodaj `--persist-on-success` . Możesz również utrzymać skrypt później za pomocą polecenia `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Stosowanie akcji skryptu do działającego klastra przy użyciu interfejsu API REST

Zobacz [interfejs API REST klastra w usłudze Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Stosowanie akcji skryptu do działającego klastra z zestawu .NET SDK usługi HDInsight

Aby zapoznać się z przykładem użycia zestawu SDK platformy .NET do zastosowania skryptów do klastra, zobacz temat [stosowanie akcji skryptu względem działającego klastra usługi HDInsight opartego na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Wyświetlanie historii i promowanie i obniżanie poziomu akcji skryptu

### <a name="the-azure-portal"></a>Witryna Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Znajdź swój klaster.

1. W widoku domyślnym w obszarze **Ustawienia** wybierz pozycję **Akcje skryptu**.

1. Historia skryptów dla tego klastra jest wyświetlana w sekcji Akcje skryptu. Te informacje obejmują listę utrwalonych skryptów. Poniższy zrzut ekranu pokazuje, że skrypt Solr został uruchomiony w tym klastrze. Zrzut ekranu nie pokazuje żadnych utrwalonych skryptów.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="Historia przesyłania akcji skryptu portalu":::

1. Wybierz skrypt z historii, aby wyświetlić sekcję **Właściwości** tego skryptu. W górnej części ekranu możesz ponownie uruchomić skrypt lub go podwyższyć.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="Podwyższanie poziomu właściwości akcji skryptu":::

1. Możesz również wybrać wielokropek, **...**, z prawej strony w sekcji Akcje skryptu do wykonania akcji.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="Usuwanie akcji utrwalonego skryptu":::

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funkcja |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Pobierz informacje o akcjach utrwalonego skryptu. To polecenie cmdlet nie cofa akcji wykonywanych przez skrypt. usuwa tylko flagę PERSISTED.|
| `Get-AzHDInsightScriptActionHistory` |Pobierz historię akcji skryptu zastosowanych do klastra lub szczegółowych informacji dotyczących określonego skryptu. |
| `Set-AzHDInsightPersistedScriptAction` |Podnieś `ad hoc` akcję skryptu do utrwalonego działania skryptu. |
| `Remove-AzHDInsightPersistedScriptAction` |Obniż poziom utrwalonej akcji skryptu do `ad hoc` akcji. |

Poniższy przykładowy skrypt demonstruje użycie poleceń cmdlet do podniesienia poziomu i obniżenia poziomu skryptu.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

| Polecenie | Opis |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |Usuwa określoną akcję utrwalonego skryptu w klastrze. To polecenie nie cofa akcji wykonywanych przez skrypt. usuwa tylko flagę PERSISTED.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|Wykonaj akcje skryptu w określonym klastrze usługi HDInsight.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |Wyświetla wszystkie akcje utrwalonego skryptu dla określonego klastra. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|Wyświetla historię wykonywania wszystkich skryptów dla określonego klastra.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|Promuje określone wykonywanie skryptu ad hoc do utrwalonego skryptu.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|Pobiera szczegóły wykonania skryptu dla danego identyfikatora wykonywania skryptu.|

### <a name="hdinsight-net-sdk"></a>Zestaw SDK usługi HDInsight dla platformy .NET

Aby zapoznać się z przykładem użycia zestawu SDK platformy .NET do pobierania historii skryptów z klastra, podniesienia lub obniżenia poziomu skryptów, zobacz temat [ stosowanie akcji skryptu względem uruchomionego klastra usługi HDInsight opartego na systemie Linux](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> W tym przykładzie pokazano również, jak zainstalować aplikację usługi HDInsight przy użyciu zestawu .NET SDK.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie skryptów akcji skryptu dla usługi HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Dodawanie dodatkowego magazynu do klastra usługi HDInsight](hdinsight-hadoop-add-storage.md)
* [Rozwiązywanie problemów z akcjami skryptu](troubleshoot-script-action.md)

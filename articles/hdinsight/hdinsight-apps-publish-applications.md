---
title: Publikowanie aplikacji usługi Azure HDInsight
description: Dowiedz się, jak utworzyć aplikację usługi HDInsight, a następnie opublikować ją w portalu Azure Marketplace.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 05/14/2018
ms.openlocfilehash: d432d20660cd1b7ee59c9ef72f9db04bc0009481
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306589"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Publikowanie aplikacji usługi HDInsight w portalu Azure Marketplace
Aplikację Azure HDInsight można zainstalować w klastrze usługi HDInsight opartym na systemie Linux. W tym artykule dowiesz się, jak opublikować aplikację usługi HDInsight w portalu Azure Marketplace. Aby uzyskać ogólne informacje o publikowaniu w portalu Azure Marketplace, zobacz temat [Publikowanie oferty w portalu Azure Marketplace](../marketplace/overview.md).

Aplikacje usługi HDInsight wykorzystują model *BYOL* . W scenariuszu BYOL Dostawca aplikacji jest odpowiedzialny za Licencjonowanie aplikacji użytkownikom aplikacji. Użytkownicy aplikacji są rozliczani wyłącznie za utworzone przez siebie zasoby platformy Azure, takie jak klaster usługi HDInsight, oraz maszyny wirtualne i węzły klastra. Obecnie naliczanie opłat za samą aplikację nie odbywa się na platformie Azure.

Aby uzyskać więcej informacji, zobacz następujące artykuły dotyczące aplikacji usługi HDInsight:

* [Instalowanie aplikacji usługi HDInsight](hdinsight-apps-install-applications.md). Dowiedz się, jak zainstalować aplikację usługi HDInsight w klastrach.
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md). Dowiedz się, jak instalować i testować niestandardowe aplikacje usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przesłać aplikację niestandardową w portalu Marketplace, najpierw [Utwórz i przetestuj aplikację niestandardową](hdinsight-apps-install-custom-applications.md).

Należy również zarejestrować konto dewelopera. Aby uzyskać więcej informacji, zobacz temat [Publikowanie oferty w portalu Azure Marketplace](../marketplace/overview.md) i [Tworzenie konta dewelopera Microsoft](../marketplace/overview.md).

## <a name="define-the-application"></a>Definiowanie aplikacji
Dwa kroki są związane z publikowaniem aplikacji w portalu Marketplace. Najpierw Zdefiniuj *createUiDef.js* pliku. createUiDef.jsw pliku wskazuje, które klastry są zgodne z aplikacją. Następnie opublikuj szablon na podstawie Azure Portal. Oto przykład createUiDef.jsw pliku:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Pole | Opis | Możliwe wartości |
| --- | --- | --- |
| types (typy) |Typy klastrów, z którymi dana aplikacja jest zgodna. |Hadoop, HBase, burza, Spark (lub dowolną ich kombinację) |
| versions (wersje) |Typy klastrów usługi HDInsight, z którymi dana aplikacja jest zgodna. |3.4 |

## <a name="application-installation-script"></a>Skrypt instalacji aplikacji
Gdy aplikacja jest zainstalowana w klastrze (w istniejącym klastrze lub w nowym), zostaje utworzony węzeł brzegowy. Skrypt instalacji aplikacji jest uruchamiany w węźle brzegowym.

  > [!IMPORTANT]  
  > Nazwa skryptu instalacji aplikacji musi być unikatowa dla określonego klastra. Nazwa skryptu musi mieć następujący format:
  > 
  > "name": "[concat (" odcień-Install-VO ","-", uniquestring (" applicationName ")]"
  > 
  > Nazwa skryptu ma trzy części:
  > 
  > * Prefiks nazwy skryptu, który musi zawierać nazwę aplikacji lub nazwę odpowiednią dla aplikacji.
  > * Łącznik, dla czytelności.
  > * Unikatowa funkcja ciągu z nazwą aplikacji jako parametrem.
  > 
  > Powyższy przykład na liście akcji utrwalonego skryptu jest wyświetlany jako **odcień-Install-VO-4wkahss55hlas**. Zobacz [przykładowy ładunek JSON](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Skrypt instalacji musi mieć następującą charakterystykę:
* Skrypt jest idempotentne. Wiele wywołań skryptu daje ten sam wynik.
* Skrypt ma prawidłową wersję. Użyj innej lokalizacji dla skryptu podczas uaktualniania lub testowania zmian. Dzięki temu Klienci instalujący aplikację nie mają wpływ na aktualizacje ani testy. 
* Skrypt ma odpowiednie rejestrowanie w każdym punkcie. Zazwyczaj dzienniki skryptów są jedynym sposobem debugowania problemów z instalacją aplikacji.
* Wywołania usług zewnętrznych lub zasobów mają odpowiednie ponowne próby, aby nie dotyczyły przejściowych problemów z siecią.
* Jeśli skrypt uruchamia usługi w węzłach, usługi są monitorowane i konfigurowane do automatycznego uruchamiania w przypadku wystąpienia ponownego uruchomienia węzła.

## <a name="package-the-application"></a>Pakowanie aplikacji
Utwórz plik zip, który zawiera wszystkie pliki wymagane do zainstalowania aplikacji usługi HDInsight. Aby opublikować aplikację, należy użyć pliku zip. Plik zip zawiera następujące pliki:

* createUiDefinition.jsna
* mainTemplate.jsna (Aby uzyskać przykład, zobacz [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md)).
* Wszystkie wymagane skrypty

> [!NOTE]  
> Pliki aplikacji (w tym pliki aplikacji sieci Web) mogą być hostowane w dowolnym publicznie dostępnym punkcie końcowym.

## <a name="publish-the-application"></a>Publikowanie aplikacji
Aby opublikować aplikację usługi HDInsight:

1. Zaloguj się do publikowania na platformie Azure.

2. W menu po lewej stronie wybierz pozycję **Szablony rozwiązań**.
3. Wprowadź tytuł, a następnie wybierz pozycję **Utwórz nowy szablon rozwiązania**.
4. Jeśli Twoja organizacja nie została jeszcze zarejestrowana, wybierz pozycję **Utwórz konto Centrum deweloperów i Dołącz do programu Azure**.  Aby uzyskać więcej informacji, zobacz [Tworzenie konta dewelopera Microsoft](../marketplace/overview.md).
5. Wybierz pozycję **Zdefiniuj pewne topologie, aby rozpocząć pracę**. Szablon rozwiązania jest "nadrzędny" dla wszystkich jego topologii. Można zdefiniować wiele topologii w jednej ofercie lub szablonie rozwiązania. Gdy oferta jest wypychana do przemieszczania, jest wypychana do wszystkich jej topologii. 
6. Wprowadź nazwę topologii, a następnie wybierz pozycję **+** .
7. Wprowadź nową wersję, a następnie wybierz pozycję **+** .
8. Przekaż plik zip, który został utworzony podczas pakowania aplikacji.  
9. Wybierz pozycję **Żądaj certyfikacji**. Zespół certyfikacji firmy Microsoft przegląda pliki i poświadcza topologię.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [instalować aplikacje usługi HDInsight](hdinsight-apps-install-applications.md) w klastrach.
* Dowiedz się, jak [zainstalować niestandardowe aplikacje usługi HDInsight](hdinsight-apps-install-custom-applications.md) i wdrożyć nieopublikowaną aplikację usługi HDInsight w usłudze HDInsight.
* Dowiedz się, jak [używać akcji skryptu do dostosowywania klastrów usługi HDInsight opartych na systemie Linux](hdinsight-hadoop-customize-cluster-linux.md) i dodawania większej liczby aplikacji. 
* Informacje na temat [tworzenia klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight przy użyciu szablonów Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
* Dowiedz się, jak [używać pustego węzła krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md) , aby uzyskiwać dostęp do klastrów usługi HDInsight, testować aplikacje usługi HDInsight i hostować aplikacje usługi HDInsight.

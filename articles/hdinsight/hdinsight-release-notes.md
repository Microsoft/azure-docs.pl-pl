---
title: Informacje o wersji usługi Azure HDInsight
description: Najnowsze informacje o wersji usługi Azure HDInsight. Uzyskaj porady deweloperskie i szczegóły dotyczące usługi Hadoop, Spark, R Server, Hive i innych.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 88e2161cfddf95f7f250b8b76c067d045f1529da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092238"
---
# <a name="azure-hdinsight-release-notes"></a>Informacje o wersji usługi Azure HDInsight

Ten artykuł zawiera informacje **o najnowszych aktualizacjach wersji usługi Azure** HDInsight. Aby uzyskać informacje dotyczące wcześniejszych wersji, zobacz [archiwum informacji o wersji usługi HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Podsumowanie

Usługa Azure HDInsight to jedna z najpopularniejszych usług dla klientów korporacyjnych na potrzeby analiz typu open source na platformie Azure.

Jeśli chcesz subskrybować informacje o wersji, zobacz wersje w [tym repozytorium GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Data wydania: 11/18/2020

Ta wersja dotyczy zarówno usługi HDInsight 3,6, jak i usługi HDInsight 4,0. Wersja usługi HDInsight jest udostępniana wszystkim regionom przez kilka dni. Data wydania wskazuje na datę wydania pierwszego regionu. Jeśli nie widzisz poniżej zmian, poczekaj na zakończenie wydania w Twoim regionie w kilka dni.

## <a name="new-features"></a>Nowe funkcje
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Funkcja autorotacji kluczy zarządzanych przez klienta w czasie spoczynku
Począwszy od tej wersji, klienci mogą korzystać z adresów URL kluczy szyfrowania bez systemu Azure KeyValut na potrzeby szyfrowania kluczy zarządzanych przez klienta. Usługa HDInsight automatycznie przeniesie klucze w miarę ich wygaśnięcia lub zastąpione nowymi wersjami. Więcej informacji znajdziesz [tutaj](./disk-encryption.md).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Możliwość wybierania różnych dozorcy rozmiary maszyn wirtualnych dla usług Spark, Hadoop i ML
Usługa HDInsight wcześniej nie obsługiwała dostosowywania rozmiaru węzła dozorcy dla typów klastrów usługi Spark, Hadoop i ML. Domyślnie A2_v2/a2 rozmiary maszyn wirtualnych, które są dostępne bezpłatnie. W tej wersji można wybrać dozorcy rozmiar maszyny wirtualnej, który jest najbardziej odpowiedni dla danego scenariusza. Dozorcy węzły z maszyną wirtualną o rozmiarze innym niż A2_v2/a2 będą obciążane opłatami. Maszyny wirtualne A2_v2 i a2 są nadal udostępniane bezpłatnie.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Przechodzenie do zestawów skalowania maszyn wirtualnych platformy Azure
Usługa HDInsight teraz używa maszyn wirtualnych platformy Azure do aprowizacji klastra. Począwszy od tej wersji, usługa stopniowo przeprowadzi migrację do [zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/overview.md). Cały proces może potrwać miesiące. Po przeprowadzeniu migracji regionów i subskrypcji nowo utworzone klastry usługi HDInsight będą uruchamiane w zestawach skalowania maszyn wirtualnych bez akcji klienta. Nie oczekiwano zmiany krytycznej.

## <a name="deprecation"></a>Przestarzałe
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Wycofanie klastra usług HDInsight 3,6 ML
Typ klastra usług HDInsight 3,6 ML ma koniec wsparcia z grudnia 31 2020. Klienci nie będą mogli tworzyć nowych klastrów usług 3,6 ML po 31 2020 grudnia. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Sprawdź w [tym miejscu](./hdinsight-component-versioning.md#available-versions)wygaśnięcie pomocy technicznej dotyczącej wersji i typów klastrów usługi HDInsight.

### <a name="disabled-vm-sizes"></a>Wyłączone rozmiary maszyn wirtualnych
Począwszy od listopada 16 2020, Usługa HDInsight będzie blokować nowym klientom tworzenie klastrów przy użyciu standand_A8, standand_A9, standand_A10 i standand_A11 maszyn wirtualnych. Nie ma to wpływu na istniejących klientów, którzy korzystali z tych rozmiarów maszyn wirtualnych w ciągu ostatnich trzech miesięcy. Począwszy od stycznia 9 2021, Usługa HDInsight będzie blokować wszystkim klientom tworzenie klastrów przy użyciu standand_A8, standand_A9, standand_A10 i standand_A11 rozmiary maszyn wirtualnych. Istniejące klastry będą działać w postaci, w jakiej jest. Rozważ przeniesienie do usługi HDInsight 4,0, aby uniknąć potencjalnych przerw w działaniu systemu/obsługi.

## <a name="behavior-changes"></a>Zmiany zachowania
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>Dodaj sprawdzanie reguły sieciowej grupy zabezpieczeń przed operacją skalowania
Usługa HDInsight dodała sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i trasy zdefiniowane przez użytkownika (UDR) przy użyciu operacji skalowania. Takie samo sprawdzanie poprawności jest wykonywane na potrzeby skalowania klastra Poza tworzeniem klastra. Ta weryfikacja pomaga zapobiegać nieprzewidywalnym błędom. Jeśli walidacja nie zostanie przekazana, skalowanie kończy się niepowodzeniem. Dowiedz się więcej o tym, jak poprawnie skonfigurować sieciowych grup zabezpieczeń i UDR, zapoznaj się z tematem [adresy IP zarządzania usługą HDInsight](./hdinsight-management-ip-addresses.md).

## <a name="upcoming-changes"></a>Nadchodzące zmiany
Następujące zmiany zostaną wykonane w przyszłych wydaniach.

### <a name="breaking-change-for-net-for-apache-spark-100"></a>Nieprzerwana zmiana dla programu .NET dla Apache Spark 1.0.0
Usługa HDInsight wprowadzi pierwsze główne oficjalne wydanie programu .NET dla Apache Spark w kolejnej wersji. Zapewnia kompletność interfejsu API Dataframe dla platformy Spark 2.4. x i Spark 3.0. x wraz z innymi funkcjami. Istnieją istotne zmiany w tej wersji głównej. Zapoznaj się z [tym identyfikatorem GUID migracji](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , aby poznać kroki wymagane do zaktualizowania kodu i potoków. Dowiedz się więcej [tutaj](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>Domyślny rozmiar maszyny wirtualnej klastra zostanie zmieniony na rodzinę EV3
Począwszy od następnego wydania (z końcem stycznia), domyślne rozmiary maszyn wirtualnych klastra zostaną zmienione z rodziny D na rodzinę EV3. Ta zmiana dotyczy węzłów głównych i węzłów procesu roboczego. Aby uniknąć tej zmiany, Określ rozmiary maszyn wirtualnych, które mają być używane w szablonie ARM.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Domyślna wersja klastra zostanie zmieniona na 4,0
Od lutego 2021 domyślna wersja klastra usługi HDInsight zostanie zmieniona z 3,6 na 4,0. Aby uzyskać więcej informacji na temat dostępnych wersji, zobacz [dostępne wersje](./hdinsight-component-versioning.md#available-versions). Dowiedz się więcej na temat Nowości w usłudze [HDInsight 4,0](./hdinsight-version-release.md)

### <a name="os-version-upgrade"></a>Uaktualnienie wersji systemu operacyjnego
Usługa HDInsight uaktualnia wersję systemu operacyjnego z 16,04 do 18,04. Uaktualnienie zakończyło się przed 2021 kwietnia.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Usługa HDInsight 3,6 koniec wsparcia z 30 2021 czerwca
Usługa HDInsight 3,6 zostanie zakończona. Od czerwca 30 2021 klienci nie mogą tworzyć nowych klastrów usługi HDInsight 3,6. Istniejące klastry będą działać bez pomocy technicznej firmy Microsoft. Rozważ przeniesienie do usługi HDInsight 4,0, aby uniknąć potencjalnych przerw w działaniu systemu/obsługi.

## <a name="bug-fixes"></a>Poprawki błędów
Usługa HDInsight kontynuuje zwiększanie niezawodności i wydajności klastrów. 

## <a name="component-version-change"></a>Zmiana wersji składnika
Brak zmian wersji składnika dla tej wersji. Bieżące wersje składników usługi HDInsight 4,0 i HDInsight 3,6 można znaleźć w [tym dokumencie](./hdinsight-component-versioning.md).

## <a name="known-issues"></a>Znane problemy
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>Zapobieganie okresowemu ponownemu uruchamianiu maszyn wirtualnych klastra usługi HDInsight

Począwszy od połowy listopada 2020, można zauważyć, że maszyny wirtualne klastrów usługi HDInsight są regularnie uruchamiane ponownie. Może to być spowodowane przez:

1.  ClamAV jest włączona w klastrze. Nowy pakiet azsec-clamav zużywa dużą ilość pamięci, która wyzwala ponowny rozruch węzła. 
2.  Zadanie firmy CRONUS jest planowane codziennie, które monitoruje zmiany do listy urzędów certyfikacji używanych przez usługi platformy Azure. Po udostępnieniu nowego certyfikatu urzędu certyfikacji skrypt dodaje certyfikat do magazynu zaufania JDK i planuje ponowne uruchomienie.

Usługa HDInsight wdraża poprawki i stosuje poprawkę dla wszystkich uruchomionych klastrów w przypadku obu problemów. Aby natychmiast zastosować poprawkę i uniknąć nieoczekiwanych ponownych uruchomień maszyn wirtualnych, można uruchomić poniższe akcje skryptu na wszystkich węzłach klastra jako akcje trwałego skryptu. Usługa HDInsight wyśle kolejne powiadomienie po zakończeniu naprawy i poprawek.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```
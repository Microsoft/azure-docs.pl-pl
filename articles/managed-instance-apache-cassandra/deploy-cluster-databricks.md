---
title: Szybki Start — wdrażanie zarządzanego klastra Apache Spark z Azure Databricks
description: W tym przewodniku szybki start pokazano, jak wdrożyć zarządzany Klaster Apache Spark z Azure Databricks przy użyciu Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748708"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Szybki Start: wdrażanie zarządzanego klastra Apache Spark (wersja zapoznawcza) za pomocą Azure Databricks

Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra zapewnia zautomatyzowane operacje wdrażania i skalowania dla zarządzanych centrów danych programu Apache Cassandra w środowiskach open source, przyspieszanie scenariuszy hybrydowych i zmniejszanie trwającej konserwacji.

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal utworzyć w pełni zarządzany Klaster Apache Spark w usłudze Azure Virtual Network wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra. Klaster Spark zostanie utworzony w Azure Databricks. Później możesz tworzyć lub dołączać notesy do klastra, odczytywać dane z różnych źródeł danych i analizować szczegółowe informacje.

Możesz również dowiedzieć się więcej ze szczegółowymi instrukcjami dotyczącymi [wdrażania Azure Databricks w Virtual Network platformy Azure (iniekcja Virtual Network)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Tworzenie klastra Azure Databricks

Wykonaj następujące kroki, aby utworzyć klaster Azure Databricks w Virtual Network, który ma wystąpienie zarządzane platformy Azure dla oprogramowania Apache Cassandra:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W lewym okienku nawigacji zlokalizuj **grupy zasobów** i przejdź do grupy zasobów zawierającej Virtual Network, w którym wdrożono wystąpienie zarządzane.

1. Otwórz zasób **Virtual Network** i zanotuj **przestrzeń adresową**:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Pobierz przestrzeń adresową Virtual Network." border="true":::

1. W obszarze Grupa zasobów wybierz pozycję **Dodaj** i Wyszukaj **Azure Databricks** w polu wyszukiwania:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Wyszukaj Azure Databricks." border="true":::

1. Wybierz pozycję **Utwórz** , aby utworzyć konto Azure Databricks:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Utwórz konto Azure Databricks." border="true":::

1. Wypełnij następujące wartości:

   * **Nazwa obszaru roboczego** — Podaj nazwę obszaru roboczego datakostki.
   * **Region** — upewnij się, że wybrano ten sam region, w którym znajduje się Virtual Network.
   * **Warstwa cenowa** — wybierz opcję standardowa, Premium lub wersja próbna. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Wypełnij pola Nazwa obszaru roboczego, region i warstwa cenowa dla konta datakostki." border="true":::

1. Następnie wybierz kartę **Sieć** i wypełnij następujące informacje:

   * **Wdróż Azure Databricks obszar roboczy w Virtual Network (VNET)** — wybierz pozycję **tak**.
   * **Virtual Network** — z listy rozwijanej wybierz Virtual Network, gdzie istnieje wystąpienie zarządzane.
   * **Nazwa podsieci publicznej** — wprowadź nazwę podsieci publicznej.
   * **Zakres CIDR podsieci publicznej** — Wprowadź zakres adresów IP dla podsieci publicznej.
   * **Nazwa podsieci prywatnej** — wprowadź nazwę podsieci prywatnej.
   * **Zakres CIDR podsieci prywatnej** — Wprowadź zakres adresów IP dla podsieci prywatnej.

   Aby uniknąć kolizji zakresu, upewnij się, że wybrano opcję wyższe zakresy. W razie potrzeby użyj [kalkulatora podsieci wizualizacji](https://www.fryguy.net/wp-content/tools/subnets.html) , aby podzielić zakresy:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Użyj kalkulatora podsieci Virtual Network." border="true":::

   Poniższy zrzut ekranu przedstawia przykładowe szczegóły w okienku sieć:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Określ nazwy podsieci publicznych i prywatnych." border="true":::

1. Wybierz pozycję **Przejrzyj i Utwórz** , a następnie **Utwórz** , aby wdrożyć obszar roboczy.

1. **Uruchom obszar roboczy** po jego utworzeniu.

1. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **nowy klaster**.

1. W okienku **nowy klaster** zaakceptuj wartości domyślne dla wszystkich pól innych niż następujące pola:

   * **Nazwa klastra** — wprowadź nazwę klastra.
   * **Databricks Runtime wersja** — wybierz pozycję Scala 2,11 lub starszą wersję obsługiwaną przez łącznik Cassandra.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Wybierz wersję środowiska uruchomieniowego datakostki i klaster Spark." border="true":::

1. Rozwiń węzeł **Opcje zaawansowane** i Dodaj następującą konfigurację. Pamiętaj, aby zastąpić węzeł adresy IP i poświadczenia:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. Na karcie **biblioteki** Zainstaluj najnowszą wersję łącznika Spark dla Cassandra (*Spark-Cassandra-Connector*) i uruchom ponownie klaster:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Zainstaluj łącznik Cassandra." border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tego klastra wystąpienia zarządzanego, usuń go z następujących kroków:

1. W menu po lewej stronie Azure Portal wybierz pozycję **grupy zasobów**.
1. Z listy wybierz grupę zasobów utworzoną dla tego przewodnika Szybki Start.
1. W okienku **Przegląd** grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
3. W następnym oknie wprowadź nazwę grupy zasobów do usunięcia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia w pełni zarządzanego klastra Apache Spark wewnątrz Virtual Network wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra. Następnie można dowiedzieć się, jak zarządzać klastrem i zasobami centrum danych: 

> [!div class="nextstepaction"]
> [Zarządzanie wystąpieniem zarządzanym platformy Azure dla zasobów Apache Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)


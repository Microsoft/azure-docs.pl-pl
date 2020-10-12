---
title: Bezpieczne zarządzanie zależnościami jar — Azure HDInsight
description: W tym artykule omówiono najlepsze rozwiązania dotyczące zarządzania zależnościami archiwum Java (JAR) dla aplikacji usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: b5b8c014a7150ad83875b9fd361c3538d865d153
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064154"
---
# <a name="safely-manage-jar-dependencies"></a>Bezpieczne zarządzanie zależnościami plików jar

Składniki zainstalowane w klastrach usługi HDInsight mają zależności od bibliotek innych firm. Zwykle do konkretnej wersji wspólnych modułów, takich jak Guava, odwołują się te wbudowane składniki. Gdy przesyłana jest aplikacja z zależnościami, może to spowodować konflikt między różnymi wersjami tego samego modułu. Jeśli wersja składnika, do której odwołuje się pierwsza ścieżka klasy, składniki wbudowane mogą generować wyjątki z powodu niezgodności wersji. Jeśli jednak składniki wbudowane wymagają najpierw dodania ich do ścieżki klas, aplikacja może zgłosić błędy takie jak `NoSuchMethod` .

Aby uniknąć konfliktu wersji, należy rozważyć cieniowanie zależności aplikacji.

## <a name="what-does-package-shading-mean"></a>Co oznacza cieniowanie pakietu?
Cieniowanie zapewnia sposób dołączania i zmieniania nazw zależności. Odszuka klasy i ponownie zapisuje na nich kod bajtowy i zasoby, aby utworzyć prywatną kopię zależności.

## <a name="how-to-shade-a-package"></a>Jak cieniować pakiet?

### <a name="use-uber-jar"></a>Korzystanie z Uber-jar
Uber-JAR to pojedynczy plik JAR, który zawiera zarówno aplikację jar, jak i jej zależności. Zależności w Uber-jar są domyślnie niecieniowane. W niektórych przypadkach może to spowodować konflikt wersji, jeśli inne składniki lub aplikacje odwołują się do innej wersji tych bibliotek. Aby tego uniknąć, można skompilować plik Uber-Jar z pewnymi (lub wszystkie) zależności zacienionych.

### <a name="shade-package-using-maven"></a>Wycieniowanie pakietu przy użyciu Maven
Maven mogą tworzyć aplikacje, które są zapisywane zarówno w języku Java, jak i Scala. Maven-cieniowanie — wtyczka może pomóc w łatwym tworzeniu cieniowanych Uber-jar.

W poniższym przykładzie przedstawiono plik `pom.xml` , który został zaktualizowany w celu odcieniowania pakietu przy użyciu funkcji Maven-odcieni-plugin.  Sekcja XML `<relocation>…</relocation>` przenosi klasy z pakietu `com.google.guava` do pakietu `com.google.shaded.guava` przez przeniesienie odpowiednich wpisów w pliku JAR i ponowne zapisanie kodu bajtowego, którego to dotyczy.

Po zmianie `pom.xml` można wykonać `mvn package` w celu skompilowania zacieniowanego Uber-jar.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Wycieniowanie pakietu przy użyciu SBT
SBT jest również narzędziem kompilacji dla Scala i Java. SBT nie ma wtyczki do cieniowania, takiej jak Maven-decień-wtyczka. Można zmodyfikować `build.sbt` plik w celu cieniowania pakietów. 

Na przykład, aby odcieniować `com.google.guava` , można dodać poniższe polecenie do `build.sbt` pliku:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Następnie można uruchomić `sbt clean` i `sbt assembly` utworzyć cieniowany plik JAR. 

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z narzędzi usługi HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Tworzenie aplikacji Scala Maven dla platformy Spark w IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)

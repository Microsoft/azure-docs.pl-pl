---
title: Konfigurowanie interfejsu wiersza polecenia usługi Azure Service Fabric siatki
description: Interfejs wiersza polecenia usługi Service Fabric Mesh jest wymagany do wdrażania zasobów i zarządzania nimi lokalnie oraz w usłudze Azure Service Fabric Mesh. Poniżej przedstawiono sposób konfigurowania tego programu.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: f8a9c26e65ef911ad85806c72c7946947379ab72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613345"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Interfejs wiersza polecenia usługi Service Fabric Mesh jest wymagany do wdrażania zasobów i zarządzania nimi lokalnie oraz w usłudze Azure Service Fabric Mesh. Poniżej przedstawiono sposób konfigurowania tego programu.

Istnieją trzy typy interfejsów wiersza polecenia, które mogą być używane. Ich podsumowanie znajduje się w poniższej tabeli.

| Moduł interfejsu wiersza polecenia | Środowisko docelowe |  Opis | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Podstawowy interfejs wiersza polecenia umożliwiający wdrażanie aplikacji i zarządzanie zasobami w ramach środowiska Azure Service Fabric Mesh. 
| sfctl | Klastry lokalne | Interfejs wiersza polecenia usługi Service Fabric umożliwiający wdrażanie i testowanie zasobów usługi Service Fabric w ramach klastrów lokalnych.  
| Interfejs wiersza polecenia programu Maven | Klastry lokalne i usługa Azure Service Fabric Mesh | Otoka w `az mesh` programie i umożliwiająca `sfctl` deweloperom języka Java używanie znanego środowiska wiersza polecenia na potrzeby środowiska projektowania lokalnego i platformy Azure.  

Na potrzeby wersji zapoznawczej interfejs wiersza polecenia usługi Azure Service Fabric mesh jest napisany jako rozszerzenie interfejsu wiersza polecenia platformy Azure. Zainstalować go można w usłudze Azure Cloud Shell lub w lokalnej instalacji wiersza polecenia platformy Azure. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.67 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalowanie interfejsu wiersza polecenia usługi Azure Service Fabric Mesh

Jeśli jeszcze tego nie zrobiono, zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric siatkę przy użyciu następującego polecenia: 
 
```azurecli-interactive
az extension add --name mesh
```

Jeśli jest już zainstalowana, zaktualizuj istniejący moduł interfejsu wiersza polecenia usługi Azure Service Fabric w sieci Web przy użyciu następującego polecenia:

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalowanie interfejsu wiersza polecenia usługi Service Fabric (sfctl) 

Postępuj zgodnie z instrukcjami w sekcji [Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric](../service-fabric/service-fabric-cli.md). Modułu **sfctl** można używać do wdrażania aplikacji opartych na modelu zasobów w ramach klastrów usługi Service Fabric na maszynie lokalnej. 

## <a name="install-the-maven-cli"></a>Instalowanie interfejsu wiersza polecenia programu Maven 

Aby korzystać z interfejsu wiersza polecenia programu Maven, na maszynie muszą być zainstalowane następujące elementy: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Usługa Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Interfejs wiersza polecenia usługi Azure Mesh (az mesh) — pod kątem usługi Azure Service Fabric Mesh 
* Interfejs SFCTL (sfctl) — pod kątem klastrów lokalnych 

Interfejs wiersza polecenia programu Maven dla usługi Service Fabric jest nadal w wersji zapoznawczej. 

Aby używać wtyczki programu Maven w aplikacji Java programu Maven, dodaj poniższy fragment kodu do pliku pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Zapoznaj się z sekcją zawierającą [dokumentację interfejsu wiersza polecenia programu Maven](service-fabric-mesh-reference-maven.md), aby uzyskać informacje dotyczące szczegółów użycia.

## <a name="next-steps"></a>Następne kroki

Skonfigurować możesz również [środowisko deweloperskie w systemie Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Znajdź odpowiedzi na [typowe pytania i problemy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli

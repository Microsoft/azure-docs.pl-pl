---
title: Konfigurowanie interfejsu wiersza polecenia usługi Azure Service Fabric siatki
description: Interfejs wiersza polecenia usługi Service Fabric Mesh jest wymagany do wdrażania zasobów i zarządzania nimi lokalnie oraz w usłudze Azure Service Fabric Mesh. Poniżej przedstawiono sposób konfigurowania tego programu.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: fb059fe5dc4e64df104e026983e51f799236f916
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842805"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh
Interfejs wiersza polecenia usługi Service Fabric Mesh jest wymagany do wdrażania zasobów i zarządzania nimi lokalnie oraz w usłudze Azure Service Fabric Mesh. Poniżej przedstawiono sposób konfigurowania tego programu.

Istnieją trzy typy interfejsów wiersza polecenia, które mogą być używane. Ich podsumowanie znajduje się w poniższej tabeli.

| Moduł interfejsu wiersza polecenia | Środowisko docelowe |  Opis | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Podstawowy interfejs wiersza polecenia umożliwiający wdrażanie aplikacji i zarządzanie zasobami w ramach środowiska Azure Service Fabric Mesh. 
| sfctl | Klastry lokalne | Interfejs wiersza polecenia usługi Service Fabric umożliwiający wdrażanie i testowanie zasobów usługi Service Fabric w ramach klastrów lokalnych.  
| Interfejs wiersza polecenia programu Maven | Klastry lokalne i usługa Azure Service Fabric Mesh | Otoka w `az mesh` programie i umożliwiająca `sfctl` deweloperom języka Java używanie znanego środowiska wiersza polecenia na potrzeby środowiska projektowania lokalnego i platformy Azure.  

Na potrzeby wersji zapoznawczej interfejs wiersza polecenia usługi Azure Service Fabric mesh jest napisany jako rozszerzenie interfejsu wiersza polecenia platformy Azure. Zainstalować go można w usłudze Azure Cloud Shell lub w lokalnej instalacji wiersza polecenia platformy Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalowanie interfejsu wiersza polecenia usługi Azure Service Fabric Mesh
1. Musisz zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Aby zainstalować najnowszą wersję interfejsu wiersza polecenia lub uaktualnić do niej, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

2. Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh za pomocą poniższego polecenia. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Zaktualizuj istniejący moduł interfejsu wiersza polecenia usługi Azure Service Fabric Mesh za pomocą poniższego polecenia.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalowanie interfejsu wiersza polecenia usługi Service Fabric (sfctl) 

Postępuj zgodnie z instrukcjami w sekcji [Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric](../service-fabric/service-fabric-cli.md). Modułu **sfctl** można używać do wdrażania aplikacji opartych na modelu zasobów w ramach klastrów usługi Service Fabric na maszynie lokalnej. 

## <a name="install-the-maven-cli"></a>Instalowanie interfejsu wiersza polecenia programu Maven 

Aby korzystać z interfejsu wiersza polecenia programu Maven, na maszynie muszą być zainstalowane następujące elementy: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
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

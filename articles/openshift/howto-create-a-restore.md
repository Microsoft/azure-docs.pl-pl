---
title: Tworzenie aplikacji klastra usługi Azure Red Hat OpenShift 4 przy użyciu Velero
description: Dowiedz się, jak utworzyć Przywracanie aplikacji klastra Red Hat OpenShift platformy Azure przy użyciu usługi Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 0cd6797bcdfadca807e25f8b3decf34bd553fc56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470055"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Tworzenie aplikacji klastra usługi Azure Red Hat OpenShift 4

W tym artykule opisano przygotowanie środowiska w celu utworzenia przywracania aplikacji klastra usługi Azure Red Hat OpenShift 4. Omawiane tematy:

> [!div class="checklist"]
> * Skonfiguruj wymagania wstępne i Zainstaluj niezbędne narzędzia
> * Tworzenie przywracania aplikacji na platformie Azure Red Hat OpenShift 4

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Tworzenie kopii zapasowej aplikacji usługi Azure Red Hat OpenShift 4

Aby utworzyć kopię zapasową aplikacji Azure Red Hat OpenShift 4, zobacz [Tworzenie kopii zapasowej usługi Azure Red Hat OpenShift 4](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Przywracanie aplikacji Azure Red Hat OpenShift 4

Te kroki umożliwią przywrócenie aplikacji, której kopia zapasowa została wcześniej utworzona przy użyciu Velero.
Możesz sprawdzić listę kopii zapasowych, które są aktualnie rozpoznawane przez klaster, aby zobaczyć, jakie kopie zapasowe są dostępne do przywrócenia.  Aby wykonać ten krok, należy wykonać następujące polecenie:

_(W tym kroku przyjęto założenie, że zainstalowano Velero w projekcie o nazwie "Velero")_

```bash
oc get backups -n velero
```

Po utworzeniu kopii zapasowej, którą chcesz przywrócić, należy wykonać przywracanie przy użyciu następującego polecenia:

```bash
velero restore create --from-backup <name of backup from above output list>
```

Ten krok spowoduje utworzenie obiektów Kubernetes, których kopia zapasowa została utworzona w poprzednim kroku podczas tworzenia kopii zapasowej.

Aby sprawdzić stan przywracania, wykonaj następujące czynności:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Gdy zostanie wyświetlona faza `Completed` , aplikacja Azure Red Hat 4 powinna zostać przywrócona.

## <a name="next-steps"></a>Następne kroki

W tym artykule została przywrócona aplikacja klastra Red Hat OpenShift 4 platformy Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie aplikacji klastra OpenShift v4 przy użyciu Velero


Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat obsługiwanych zasobów usługi Azure Red Hat OpenShift 4.

* [Obsługiwane zasoby usługi Azure Red Hat OpenShift v4](supported-resources.md)
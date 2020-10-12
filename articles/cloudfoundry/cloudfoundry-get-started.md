---
title: Wprowadzenie z Cloud Foundry na Microsoft Azure
description: Uruchom Cloud Foundry OSS lub Pivoting na Microsoft Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 8645afac5734cdbffb8df6f341529e688ca526d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84754595"
---
# <a name="cloud-foundry-on-azure"></a>Usługa Cloud Foundry na platformie Azure

Cloud Foundry to platforma jako usługa (PaaS) typu „open source” służąca do tworzenia, wdrażania i obsługi aplikacji 12-factor opracowanych w różnych językach i strukturach. W tym dokumencie opisano opcje, które można uruchamiać Cloud Foundry na platformie Azure oraz jak rozpocząć pracę.

## <a name="cloud-foundry-offerings"></a>Oferty Cloud Foundry

Istnieją dwie formy Cloud Foundry dostępne do uruchamiania na platformie Azure: Cloud Foundry typu Open Source (OSS CF) i Cloud Foundry Pivot (PCF). OSS CF to całkowicie wersja typu ["open source"](https://github.com/cloudfoundry) Cloud Foundry zarządzana przez Cloud Foundry Foundation. Cloud Foundry Pivot to dystrybucja w przedsiębiorstwie Cloud Foundry od oprogramowania z oprogramowaniem firmy Pivot. Zapoznajmy się z niektórymi różnicami między obiema ofertami.

### <a name="open-source-cloud-foundry"></a>Cloud Foundry Open Source

Cloud Foundry można wdrożyć na platformie Azure, wdrażając dyrektora BOSH, a następnie wdrażając Cloud Foundry, korzystając z [instrukcji dostarczonych w witrynie GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Aby dowiedzieć się więcej o korzystaniu z usługi OSS CF, zapoznaj się z [dokumentacją](https://docs.cloudfoundry.org/) dostarczoną przez Cloud Foundry Foundation.

Firma Microsoft zapewnia najlepszą pomoc techniczną dla usługi OSS CF przez następujące kanały społecznościowe:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>Bosh — kanał Azure-CPI w [Cloud Foundry zapasu czasu](https://slack.cloudfoundry.org/)
- [Lista adresowa CF-Bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemy z usługą GitHub dotyczące usługi [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) i programu [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Poziom obsługi zasobów platformy Azure, takich jak maszyny wirtualne, na których uruchomiono Cloud Foundry, jest oparty na umowie pomocy technicznej systemu Azure. Najlepsza pomoc techniczna dla społeczności dotyczy wyłącznie składników Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Cloud Foundry przestawne

Cloud Foundry pivotd obejmuje tę samą platformę podstawową co dystrybucja OSS wraz z zestawem własnych narzędzi do zarządzania i pomocy technicznej dla przedsiębiorstw. Aby uruchomić program PCF na platformie Azure, musisz uzyskać licencję od firmy Pivot. Oferta PCF z portalu Azure Marketplace obejmuje 90-dniową licencję próbną.

Narzędzia te obejmują [Operations Manager przestawne](https://docs.pivotal.io/pivotalcf/customizing/), aplikację sieci Web, która upraszcza wdrażanie i zarządzanie programem Cloud Foundry Foundation oraz [menedżerem](https://docs.pivotal.io/pivotalcf/console/)aplikacji sieci Web, który umożliwia zarządzanie użytkownikami i aplikacjami.

Oprócz kanałów pomocy technicznej wymienionych dla usługi OSS CF powyżej licencja PCF uprawnia do skontaktowania się z działem Pivot w celu uzyskania pomocy. Firma Microsoft i wystawcy mogą również obsługiwać przepływy pracy, które umożliwiają kontaktowanie się z każdą ze stron w celu uzyskania pomocy oraz odpowiednie kierowanie zapytań w zależności od tego, gdzie występuje problem.

## <a name="azure-service-broker"></a>Service Broker platformy Azure

Cloud Foundry zachęca do metody ["12-Factoring"](https://12factor.net/) , która promuje czyste rozdzielenie procesów aplikacji bezstanowych i stanowych usług zapasowych. [Brokerzy usług](https://docs.cloudfoundry.org/services/api.html) oferują spójny sposób udostępniania i powiązania usług pomocniczych z aplikacjami. [Usługa Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) udostępnia niektóre z najważniejszych usług platformy Azure za pośrednictwem tego kanału, w tym usługi Azure Storage i Azure SQL.

W przypadku korzystania z Cloud Foundry Pivot Service Broker jest również [dostępny jako kafelek](https://docs.pivotal.io/azure-sb/installing.html) z sieci Pivot.

## <a name="related-resources"></a>Powiązane zasoby

### <a name="azure-devops-services-plugin"></a>Wtyczka Azure DevOps Services

Cloud Foundry doskonale nadaje się do tworzenia oprogramowania Agile, w tym korzystania z ciągłej integracji (CI) i ciągłego dostarczania (CD). Jeśli używasz Azure DevOps Services do zarządzania projektami i chcesz skonfigurować Cloud Foundry potok ciągłej integracji/ciągłego wdrażania, możesz użyć [rozszerzenia Azure DevOps Services Cloud Foundry kompilacja](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Wtyczka ułatwia konfigurowanie i Automatyzowanie wdrożeń do Cloud Foundry, niezależnie od tego, czy działa na platformie Azure, czy też w innym środowisku.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie Cloud Foundry Pivot z poziomu portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Wdrażanie aplikacji do Cloud Foundry na platformie Azure](./cloudfoundry-deploy-your-first-app.md)

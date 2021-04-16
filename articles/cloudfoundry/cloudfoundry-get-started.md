---
title: Wprowadzenie z Cloud Foundry na Microsoft Azure
description: Uruchamianie systemu operacyjnego lub narzędzia Pivotal Cloud Foundry na Microsoft Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 2f80f07cad0e82ee35fe71223e1282ea24f791bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530930"
---
# <a name="cloud-foundry-on-azure"></a>Usługa Cloud Foundry na platformie Azure

Cloud Foundry to platforma jako usługa (PaaS) typu „open source” służąca do tworzenia, wdrażania i obsługi aplikacji 12-factor opracowanych w różnych językach i strukturach. W tym dokumencie opisano opcje uruchamiania aplikacji Cloud Foundry platformie Azure oraz sposób rozpoczęcia pracy.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry ofert

Na platformie Azure Cloud Foundry dostępne dwie formy aplikacji: open source Cloud Foundry (OSS CF) i Pivotal Cloud Foundry (PCF). OSS CF to całkowicie [open source](https://github.com/cloudfoundry) wersja usługi Cloud Foundry zarządzana przez Cloud Foundry Foundation. Pivotal Cloud Foundry to dystrybucja w przedsiębiorstwie Cloud Foundry firmy Pivotal Software Inc. Przyjrzymy się niektórym różnicom między tymi dwiema ofertami.

### <a name="open-source-cloud-foundry"></a>Open source Cloud Foundry

Możesz wdrożyć narzędzia OSS Cloud Foundry na platformie Azure, najpierw wdrażając katalog BOSH, a następnie wdrażając Cloud Foundry zgodnie z instrukcjami podanymi w [witrynie GitHub.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md) Aby dowiedzieć się więcej na temat korzystania z usługi OSS CF, zobacz [dokumentację](https://docs.cloudfoundry.org/) dostarczaną przez Cloud Foundry Foundation.

Firma Microsoft zapewnia najlepszą pomoc techniczną dla usługi OSS CF za pośrednictwem następujących kanałów społeczności:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>Kanał bosh-azure-cpi w [usłudze Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [cf-bosh mailing list](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemy z usługą GitHub związane [z cpi](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) i [brokerem usług](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Poziom pomocy technicznej dla zasobów platformy Azure, takich jak maszyny wirtualne, na których jest uruchamiany Cloud Foundry, jest oparty na umowie pomocy technicznej platformy Azure. Pomoc społeczności z najlepszym wysiłkiem ma zastosowanie tylko do Cloud Foundry określonych składników.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Rozwiązanie Pivotal Cloud Foundry tę samą platformę podstawową co dystrybucja systemu operacyjnego, a także zestaw zastrzeżonych narzędzi do zarządzania i pomoc techniczną dla przedsiębiorstw. Aby uruchomić platformę PCF na platformie Azure, musisz uzyskać licencję od firmy Pivotal. Oferta PCF z witryny Azure Marketplace obejmuje 90-dniową licencję próbną.

Narzędzia te obejmują [usługę Pivotal Operations Manager](https://docs.pivotal.io/ops-manager/2-10/install/), aplikację internetową, która upraszcza wdrażanie podstaw usługi Cloud Foundry i zarządzanie nimi, oraz [usługę Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/), aplikację internetową do zarządzania użytkownikami i aplikacjami.

Oprócz kanałów pomocy technicznej wymienionych powyżej dla OSS CF licencja PCF uprawnia do skontaktowania się z platformą Pivotal w celu otrzymania pomocy technicznej. Firmy Microsoft i Pivotal włączyły również przepływy pracy pomocy technicznej, które umożliwiają kontaktowanie się z jedną ze stron w celu uzyskania pomocy i odpowiednie rozsyłanie Twojego zapytania w zależności od tego, gdzie występuje problem.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry wspiera metodologię ["12-factor app",](https://12factor.net/) która wspiera czystą separację bez stanowych procesów aplikacji i usług stanowych. [Brokerzy usług](https://docs.cloudfoundry.org/services/api.html) oferują spójny sposób aprowizowania i powiązania usług obsługi zapasowej z aplikacjami. Broker [usług platformy Azure udostępnia](https://github.com/Azure/meta-azure-service-broker) za pośrednictwem tego kanału niektóre kluczowe usługi platformy Azure, w tym azure storage i Azure SQL.

Jeśli używasz usługi Pivotal Cloud Foundry, usługa Service Broker jest również dostępna [jako kafelek](https://docs.pivotal.io/azure-sb/installing.html) w usłudze Pivotal Network.

## <a name="related-resources"></a>Powiązane zasoby

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services wtyczki

Cloud Foundry doskonale nadaje się do elastycznego tworzenia oprogramowania, w tym korzystania z ciągłej integracji i ciągłego dostarczania (CD). Jeśli używasz usługi Azure DevOps Services do zarządzania projektami i chcesz skonfigurować potok cicha/cd przeznaczony dla Cloud Foundry, możesz użyć rozszerzenia [Azure DevOps Services Cloud Foundry kompilacji.](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension) Wtyczka ułatwia konfigurowanie i automatyzowanie wdrożeń do Cloud Foundry, niezależnie od tego, czy są uruchomione na platformie Azure, czy w innym środowisku.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie rozwiązania Pivotal Cloud Foundry z Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Wdrażanie aplikacji na platformie Cloud Foundry platformie Azure](./cloudfoundry-deploy-your-first-app.md)

---
title: Skonfiguruj skrytkę dla Azure Data Box
description: Dowiedz się, jak używać Skrytka klienta z Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124915"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Użyj Skrytka klienta dla Azure Data Box (wersja zapoznawcza)

Azure Data Box służy do transferowania danych klienta do i z platformy Azure. Istnieją wystąpienia, w których pomoc techniczna firmy Microsoft mogą być potrzebne do uzyskiwania dostępu do danych klienta podczas Support request. Za pomocą Skrytka klienta jako interfejsu można przeglądać i zatwierdzać lub odrzucać te żądania dostępu do danych. 

W tym artykule opisano, jak Skrytka klienta żądania inicjowane i śledzone na potrzeby urządzenie Data Box importowania oraz eksportowania zamówień. Artykuł dotyczy zarówno urządzeń Azure Data Box, jak i urządzeń Azure Data Box Heavy. 

## <a name="devops-workflow-for-data-access"></a>Przepływ pracy DevOps na potrzeby dostępu do danych

Zespół operacyjny pomocy technicznej i urządzenie Data Box w firmie Microsoft zazwyczaj nie uzyskuje dostępu do danych klienta. Próbują rozwiązać problemy przy użyciu standardowych narzędzi i telemetrii. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Jeśli problemy nie mogą zostać rozwiązane i wymagają pomoc techniczna firmy Microsoft, aby zbadać lub naprawić dane, żądają dostępu z podwyższonym poziomem uprawnień za pośrednictwem portalu just in Time (JIT). Portal JIP sprawdza poprawność poziomu uprawnień, zapewnia uwierzytelnianie wieloskładnikowe i obejmuje także zatwierdzenie od wewnętrznych osób zatwierdzających firmy Microsoft. Na przykład osoba zatwierdzająca może być DevOps Manager. 

Po zatwierdzeniu żądania dostępu z podwyższonym poziomem uprawnień za pośrednictwem portalu JIT w przypadku włączenia skrytki firma Microsoft będzie także wymagała wyraźnej zgody na dostęp do danych. Dostęp jest żądany i śledzony za pośrednictwem usługi Skrytka klienta w portalu. 

Jeśli nie włączono skrytki, zgoda nie jest wymagana do uzyskania dostępu do danych.


## <a name="prerequisites-for-access-request"></a>Wymagania wstępne dotyczące żądania dostępu

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

1. Utworzono kolejność Azure Data Box zgodnie z instrukcjami w temacie:
    1. [Samouczek: zamówienie Azure Data Box](data-box-deploy-ordered.md) w przypadku zamówień importu.
    1. [Samouczek: zamówienie Azure Data Box](data-box-deploy-export-ordered.md) w przypadku zamówień eksportu.

2. Dla urządzenie Data Box skonfigurowano Skrytka klienta. Jest to usługa opcjonalna. 

    1. Skrytka klienta jest obecnie w wersji zapoznawczej dla usługi urządzenie Data Box. Aby włączyć Skrytka klienta dla urządzenie Data Box organizacji, zarejestruj się, aby uzyskać [skrytka klienta dla publicznej wersji zapoznawczej platformy Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu).
    2. Skrytka klienta jest automatycznie dostępna dla wszystkich klientów z planem pomocy technicznej platformy Azure o minimalnym poziomie dewelopera. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Żądanie obsługi lub bilet pomocy technicznej jest już otwarty dla tego problemu. Aby uzyskać informacje na temat biletu pomocy technicznej, zobacz [plik a Service Request for urządzenie Data Box](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>Śledzenie, zatwierdzanie żądania za pośrednictwem skrytki

Aby śledzić i zatwierdzać żądanie dostępu do danych klienta, wykonaj następujące kroki:

1. Firma Microsoft wykrywa, że wystąpił problem podczas przekazywania lub pobierania danych z platformy Azure. Na przykład porządek urządzenie Data Box jest zatrzymywany podczas etapu **kopiowania danych** . 

    Inżynier pomocy technicznej nawiązuje połączenie z urządzenie Data Box za pośrednictwem sesji pomocy technicznej i próbuje rozwiązać problem przy użyciu standardowych narzędzi i telemetrii. Jeśli urządzenie Data Box dyski są zablokowane, a udziały nie są dostępne, inżynier pomocy technicznej tworzy żądanie skrytki. 
 
2. Po utworzeniu żądania zwykle jest to powiadomienie do administratora subskrypcji, ale można również skonfigurować grupę do powiadomień. 

3. Żądanie skrytki można zobaczyć w Azure Portal na potrzeby zatwierdzenia. 

    ![Żądanie w Azure Portal](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Aby zatwierdzić żądanie skrytki z portalu, możesz wybrać pozycję **Zatwierdź**.

    ![Zatwierdź żądanie](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    Po zatwierdzeniu żądania dyski urządzeń są odblokowane, a udziały są dostępne w sesji obsługi.

4. Inżynier pomocy technicznej rozwiązuje problem z przekazywaniem, a następnie wyłącza sesję pomocy technicznej.

Po rozwiązaniu problemu zadanie kopiowania danych zakończy się w toku.


## <a name="next-steps"></a>Następne kroki

- [Funkcja Skrytka klienta dla platformy Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->
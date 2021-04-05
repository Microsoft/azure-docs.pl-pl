---
title: Szybki start — pobieranie raportów logowania w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak pobrać raport logowania przy użyciu witryny Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011918"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Szybki start: pobieranie raportu logowania przy użyciu witryny Azure Portal

W tym przewodniku Szybki start dowiesz się, jak pobrać dane dotyczące logowania dla swojej dzierżawy z ostatnich 24 godzin. Możesz pobrać do 250 000 rekordów z Azure Portal. Rekordy są domyślnie sortowane według najnowszych, dlatego można pobrać najnowsze 250 000 rekordów. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure Active Directory z licencją Premium, dla której ma być wyświetlony raport aktywności związanej z logowaniem. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). Pamiętaj, że jeśli przed uaktualnieniem nie istniały żadne dane działań, po przejściu na licencję premium może minąć kilka dni, zanim te dane pojawią się w raportach.
* Użytkownik mający rolę **administratora zabezpieczeń**, **czytelnika zabezpieczeń**, **czytelnika raportów** lub **administratora globalnego** dla dzierżawy. Ponadto każdy użytkownik w dzierżawie może uzyskiwać dostęp do informacji o swoim logowaniu.

## <a name="quickstart-download-a-sign-in-report"></a>Szybki start: pobieranie raportu logowania

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Wybierz usługę **Azure Active Directory** z okienka nawigacji po lewej stronie i za pomocą przycisku **Przełącz katalog** wybierz swój katalog Active Directory.
3. Na pulpicie nawigacyjnym wybierz pozycję **Azure Active Directory**, a następnie **Logowania**. 
4. Wybierz pozycję **Ostatnie 24 godziny** z listy rozwijanej filtru **Data** i wybierz przycisk **Zastosuj**, aby wyświetlić logowania z ostatnich 24 godzin. 
5. Wybierz przycisk **Pobierz** , wybierz opcję **CSV** jako format pliku i określ nazwę pliku, aby pobrać plik CSV zawierający filtrowane rekordy. 

![Raportowanie](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Następne kroki

* [Raporty dotyczące logowań w portalu Azure Active Directory](concept-sign-ins.md)
* [Azure Active Directory reporting retention (Przechowywanie raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md)
* [Azure Active Directory reporting latencies](reference-reports-latencies.md) (Opóźnienia raportowania w usłudze Azure Active Directory)

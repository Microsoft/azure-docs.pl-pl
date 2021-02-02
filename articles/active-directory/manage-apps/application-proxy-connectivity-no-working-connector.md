---
title: Nie znaleziono grupy łączników roboczych dla aplikacji serwera proxy aplikacji
description: Rozwiązywanie problemów, które mogą wystąpić w przypadku braku działającego łącznika w grupie łączników dla aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cec028dec2bf2e6295f59aae9c2e243da2209827
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257038"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nie znaleziono grupy łączników roboczych dla aplikacji serwera proxy aplikacji

Ten artykuł pomaga w rozwiązywaniu typowych problemów występujących w sytuacji, gdy nie wykryto łącznika dla aplikacji serwera proxy aplikacji zintegrowanej z Azure Active Directory.

## <a name="overview-of-steps"></a>Przegląd kroków
Jeśli w grupie łączników dla aplikacji nie istnieje łącznik roboczy, istnieje kilka sposobów rozwiązania problemu:

-   Jeśli nie masz łączników w grupie, możesz:

    -   Pobierz nowy łącznik z prawej strony na serwerze lokalnym, a następnie przypisz go do tej grupy

    -   Przenoszenie aktywnego łącznika do grupy

-   Jeśli w grupie nie ma aktywnych łączników, możesz:

    -   Identyfikowanie przyczyny nieaktywności łącznika i jego rozwiązania

    -   Przenoszenie aktywnego łącznika do grupy

Aby ustalić problem, otwórz menu "serwer proxy aplikacji" w aplikacji i Sprawdź komunikat ostrzegawczy grupy łączników. Jeśli w grupie nie ma łączników, komunikat ostrzegawczy określa, że grupa musi mieć co najmniej jeden łącznik. Jeśli nie masz aktywnych łączników, komunikat ostrzegawczy wyjaśnia, że. Często istnieją łączniki nieaktywne. 

   ![Wybór grupy łączników w Azure Portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Aby uzyskać szczegółowe informacje na temat każdej z tych opcji, zapoznaj się z odpowiednią sekcją poniżej. W instrukcjach przyjęto założenie, że zaczynasz od strony zarządzania łącznikiem. Jeśli widzisz komunikat o błędzie powyżej, możesz przejść do tej strony, klikając komunikat ostrzegawczy. Możesz również przejść do strony, przechodząc do **Azure Active Directory**, klikając pozycję **aplikacje dla przedsiębiorstw**, a następnie **serwer proxy aplikacji.**

   ![Zarządzanie grupami łączników w Azure Portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Pobierz nowy łącznik

Aby pobrać nowy łącznik, użyj przycisku "Pobierz łącznik" w górnej części strony.

Zainstaluj łącznik na komputerze z bezpośrednią linią wglądu w aplikację zaplecza. Zwykle łącznik jest instalowany na tym samym serwerze co aplikacja. Po pobraniu łącznik powinien pojawić się w tym menu. Kliknij łącznik i Użyj listy rozwijanej "Grupa łączników", aby upewnić się, że należy ona do odpowiedniej grupy. Zapisz zmianę.

   ![Pobierz łącznik z Azure Portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Przenoszenie aktywnego łącznika

Jeśli masz aktywny łącznik, który powinien należeć do grupy i ma linię wglądu w docelową aplikację zaplecza, możesz przenieść Łącznik do przypisanej grupy. Aby to zrobić, kliknij łącznik. W polu "Grupa łączników" Użyj listy rozwijanej, aby wybrać poprawną grupę, a następnie kliknij przycisk Zapisz.

## <a name="resolve-an-inactive-connector"></a>Rozwiązywanie nieaktywnego łącznika

Jeśli jedyne łączniki w grupie są nieaktywne, jest to możliwe na komputerze, który nie ma wszystkich nieblokowanych portów.

zobacz dokument Rozwiązywanie problemów z portami, aby uzyskać szczegółowe informacje na temat badania tego problemu.

## <a name="next-steps"></a>Następne kroki
[Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)



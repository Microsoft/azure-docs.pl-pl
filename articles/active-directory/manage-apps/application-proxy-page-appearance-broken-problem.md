---
title: Strona aplikacji nie jest poprawnie wyświetlana dla aplikacji serwera proxy aplikacji | Microsoft Docs
description: Wskazówki dotyczące nieprawidłowego wyświetlania strony w aplikacji serwera proxy aplikacji zintegrowanej z usługą Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381416"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Strona aplikacji nie jest wyświetlana prawidłowo dla aplikacji serwera proxy aplikacji

Ten artykuł pomaga w rozwiązywaniu problemów z serwer proxy aplikacji usługi Azure Active Directory aplikacjami podczas przechodzenia do strony, ale coś na stronie nie wygląda poprawnie.

## <a name="overview"></a>Omówienie
Po opublikowaniu aplikacji serwera proxy aplikacji podczas uzyskiwania dostępu do aplikacji są dostępne tylko strony znajdujące się w katalogu głównym. Jeśli strona nie jest wyświetlana prawidłowo, wewnętrzny adres URL używany przez aplikację może nie zawierać niektórych zasobów strony. Aby rozwiązać ten problem, upewnij się, że *wszystkie* zasoby strony zostały opublikowane jako część aplikacji.

Aby sprawdzić, czy brak zasobów jest problemem, można otworzyć narzędzie do śledzenia sieci (na przykład programu Fiddler lub F12 Tools in Internet Explorer/Microsoft Edge), załadować stronę i wyszukać błędy 404. Oznacza to, że aktualnie nie można znaleźć stron i należy je opublikować.

Przykładem takiej sytuacji jest założenie, że aplikacja wydatków została opublikowana przy użyciu wewnętrznego adresu `http://myapps/expenses`URL, ale aplikacja korzysta z arkusza `http://myapps/style.css`stylów. W takim przypadku arkusz stylów nie jest publikowany w aplikacji, więc załadowanie aplikacji wydatków Zgłoś błąd 404 podczas próby załadowania pliku style. css. W tym przykładzie problem został rozwiązany przez opublikowanie aplikacji z wewnętrznym adresem URL `http://myapp/`.

## <a name="problems-with-publishing-as-one-application"></a>Problemy z publikowaniem jako jednej aplikacji

Jeśli nie jest możliwe opublikowanie wszystkich zasobów w ramach tej samej aplikacji, należy opublikować wiele aplikacji i włączyć linki między nimi.

W tym celu zalecamy korzystanie z rozwiązania [domen niestandardowych](application-proxy-configure-custom-domain.md) . To rozwiązanie wymaga jednak posiadania certyfikatu dla domeny, a Twoje aplikacje używają w pełni kwalifikowanych nazw domen (FQDN). Inne opcje można znaleźć w [dokumentacji Rozwiązywanie uszkodzonych linków](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Następne kroki
[Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)

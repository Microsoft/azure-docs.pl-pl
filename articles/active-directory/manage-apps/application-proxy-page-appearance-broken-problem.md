---
title: Strona aplikacji nie jest poprawnie wyświetlana dla aplikacji serwera proxy aplikacji | Microsoft Docs
description: Wskazówki dotyczące nieprawidłowego wyświetlania strony w aplikacji serwera proxy aplikacji zintegrowanej z usługą Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
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
ms.openlocfilehash: 99b09e7b15427eb33e1e85edd89f4c8a37c4a3eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254658"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Strona aplikacji nie jest wyświetlana prawidłowo dla aplikacji serwera proxy aplikacji

Ten artykuł pomaga w rozwiązywaniu problemów z serwer proxy aplikacji usługi Azure Active Directory aplikacjami podczas przechodzenia do strony, ale coś na stronie nie wygląda poprawnie.

## <a name="overview"></a>Omówienie
Po opublikowaniu aplikacji serwera proxy aplikacji podczas uzyskiwania dostępu do aplikacji są dostępne tylko strony znajdujące się w katalogu głównym. Jeśli strona nie jest wyświetlana prawidłowo, wewnętrzny adres URL używany przez aplikację może nie zawierać niektórych zasobów strony. Aby rozwiązać ten problem, upewnij się, że *wszystkie* zasoby strony zostały opublikowane jako część aplikacji.

Aby sprawdzić, czy brak zasobów jest problemem, można otworzyć narzędzie do śledzenia sieci (na przykład programu Fiddler lub F12 Tools in Internet Explorer/Microsoft Edge), załadować stronę i wyszukać błędy 404. Oznacza to, że aktualnie nie można znaleźć stron i należy je opublikować.

Przykładem takiej sytuacji jest założenie, że aplikacja wydatków została opublikowana przy użyciu wewnętrznego adresu URL `http://myapps/expenses` , ale aplikacja korzysta z arkusza stylów `http://myapps/style.css` . W takim przypadku arkusz stylów nie jest publikowany w aplikacji, więc załadowanie aplikacji wydatków Zgłoś błąd 404 podczas próby załadowania pliku style. css. W tym przykładzie problem został rozwiązany przez opublikowanie aplikacji z wewnętrznym adresem URL `http://myapp/` .

## <a name="problems-with-publishing-as-one-application"></a>Problemy z publikowaniem jako jednej aplikacji

Jeśli nie jest możliwe opublikowanie wszystkich zasobów w ramach tej samej aplikacji, należy opublikować wiele aplikacji i włączyć linki między nimi.

W tym celu zalecamy korzystanie z rozwiązania [domen niestandardowych](application-proxy-configure-custom-domain.md) . To rozwiązanie wymaga jednak posiadania certyfikatu dla domeny, a Twoje aplikacje używają w pełni kwalifikowanych nazw domen (FQDN). Inne opcje można znaleźć w [dokumentacji Rozwiązywanie uszkodzonych linków](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Następne kroki
[Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
